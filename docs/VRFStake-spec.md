# VRFStake Specification

## 1. Overview

This document defines the **VRFStake-based primary selection policy** used in the PoN / VoW consensus flow.

VRFStake does not replace VoW candidate construction. Instead, it determines how the **primary proposer** is selected and moved to the front of an already constructed VoW candidate list.

The design goals are:

```text
1. Preserve the existing VoW candidate construction flow.
2. Select the primary proposer using VRFStake.
3. Bind VRFStake evaluation to the correct blockNumber / parentHash pair.
4. Allow legacy fallback during the stabilization phase.
5. Track and eliminate fallback usage during validation.
```

---

## 2. Scope

This specification covers:

```text
- VRFStake proposer resolution
- VoW primary reordering
- blockNumber / parentHash rules
- fallback behavior
- generous-mode interaction
- simulator validation criteria
- representative failure cases
```

This document does not define the internal VRF proof algorithm, stake weight calculation, or warrant membership encoding itself. Those are treated as implementation dependencies of `ExpectedVRFStakeProposer(...)`.

---

## 3. Core Invariant

### 3.1 Required blockNumber / parentHash Pairing

The most important VRFStake invariant is:

```text
If blockNumber = N,
parentHash must be the hash of block N-1.
```

In other words, proposer selection for block `N` must be evaluated using the **parent block hash and parent state**.

### 3.2 Current Block N

When validating or processing an already materialized block `N`:

```go
blockNumber := block.NumberU64()
parentHash := block.ParentHash()
```

### 3.3 Next Block N+1

When computing the expected proposer before creating the next block:

```go
blockNumber := parent.NumberU64() + 1
parentHash := parent.Hash()
```

### 3.4 Invalid Pairing

The following pairing is invalid:

```go
blockNumber := block.NumberU64()
parentHash := block.Hash() // invalid
```

This incorrectly drives the resolver toward a lookup equivalent to:

```text
GetBlock(block.Hash(), block.NumberU64()-1)
```

and can result in:

```text
missing parent for VRFStake primary
```

---

## 4. Resolver Interface

VoW does not compute VRFStake directly. Instead, a resolver is injected into VoW.

```go
type VRFStakePrimaryResolver func(
    bNum uint64,
    parentHash NBytes,
    candidates SNBytes,
) (common.Address, error)

func (v *VoW) SetVRFStakePrimaryResolver(resolver VRFStakePrimaryResolver) {
    if v == nil {
        return
    }

    v.mu.Lock()
    defer v.mu.Unlock()

    v.vrfStakePrimaryResolver = resolver
}
```

### 4.1 Resolver Contract

For every invocation:

```text
bNum = N
parentHash = hash of block N-1
candidates = sorted or sortable VoW candidate set for block N
```

The resolver returns:

```text
- selected proposer address, or
- an error if proposer resolution failed
```

---

## 5. ExpectedVRFStakeProposer Contract

The canonical proposer calculation function is:

```go
func ExpectedVRFStakeProposer(
    chainID *big.Int,
    bc *BlockChain,
    blockNumber uint64,
    parentHash common.Hash,
    activeWBNumber uint64,
    epochLength uint64,
) (common.Address, error)
```

Its core parent lookup is:

```go
parent := bc.GetBlock(parentHash, blockNumber-1)
```

Therefore, the caller must always preserve the pairing rule:

```text
blockNumber = N
parentHash = block(N-1).Hash()
```

### 5.1 Validation for an Existing Block

```go
expected, err := core.ExpectedVRFStakeProposer(
    chainID,
    bc,
    block.NumberU64(),
    block.ParentHash(),
    block.ActiveWBNumberU64(),
    core.DefaultVRFStakeEpochLength,
)
```

### 5.2 Prediction for the Next Block

```go
parent := bc.CurrentBlock()
blockNumber := parent.NumberU64() + 1

expected, err := core.ExpectedVRFStakeProposer(
    chainID,
    bc,
    blockNumber,
    parent.Hash(),
    activeWBNumber,
    core.DefaultVRFStakeEpochLength,
)
```

---

## 6. VoW Integration Model

VRFStake is applied after candidate collection and sorting.

### 6.1 Common Reordering Flow

```text
1. Build the candidate list.
2. sort.Sort(candidate)
3. Resolve the expected proposer using VRFStake.
4. If the proposer exists in the candidate list, move it to the front.
5. If resolution fails, use the configured fallback path.
```

### 6.2 Integration Points

The same reordering rule applies to:

```text
- MakeVoW()
- CommitVoW()
- MakeBlockBasedVoW()
- rearrange()
```

---

## 7. rearrange() Policy

`rearrange()` is the central function responsible for primary positioning.

Recommended implementation:

```go
func (v *VoW) rearrange(bNum uint64, temp SNBytes, parentHash NBytes) SNBytes {
    if temp.Len() == 0 {
        log.Warn("[CONSENSUS] VoW rearrange failed: empty candidates",
            "section", "CONSENSUS",
            "bNum", bNum,
            "agreeB", ponp.AgreeB,
            "parentHash", parentHash.TerminalString(),
        )
        return nil
    }

    if temp.Len() < ponp.AgreeB {
        log.Warn("[CONSENSUS] VoW rearrange failed: insufficient candidates",
            "section", "CONSENSUS",
            "bNum", bNum,
            "candidates", temp.Len(),
            "agreeB", ponp.AgreeB,
            "parentHash", parentHash.TerminalString(),
        )
        return nil
    }

    sort.Sort(temp)

    if v.vrfStakePrimaryResolver != nil {
        primary, err := v.vrfStakePrimaryResolver(bNum, parentHash, temp)
        if err != nil {
            log.Warn("[CONSENSUS] VoW rearrange VRFStake resolver failed",
                "section", "CONSENSUS",
                "bNum", bNum,
                "parentHash", parentHash.TerminalString(),
                "candidates", temp.Len(),
                "err", err,
            )
        } else if primary != (common.Address{}) {
            for i, id := range temp {
                if common.BytesToAddress(id[:]) == primary {
                    log.Debug(log.DF_VOW, "[CONSENSUS] VoW rearrange by VRFStake",
                        "section", "CONSENSUS",
                        "bNum", bNum,
                        "primary", primary.Hex(),
                        "index", i,
                        "candidates", temp.Len(),
                    )
                    return temp.MoveToFront(i)
                }
            }

            log.Warn("[CONSENSUS] VoW rearrange VRFStake primary not found",
                "section", "CONSENSUS",
                "bNum", bNum,
                "primary", primary.Hex(),
                "parentHash", parentHash.TerminalString(),
                "candidates", temp.Len(),
            )
        }
    }

    source := int(binary.BigEndian.Uint32(parentHash[len(parentHash)-4:]))
    skip := source % temp.Len()

    log.Debug(log.DF_VOW, "[CONSENSUS] VoW rearrange by fallback",
        "section", "CONSENSUS",
        "bNum", bNum,
        "candidates", temp.Len(),
        "agreeB", ponp.AgreeB,
        "skip", skip,
        "parentHash", parentHash.TerminalString(),
    )

    return temp.MoveToFront(skip)
}
```

### 7.1 Fallback Rule

During the current stabilization phase:

```text
- VRFStake failure does not immediately stop consensus.
- The legacy fallback path is allowed.
- Every fallback occurrence must be counted and reviewed.
```

Validation target:

```text
fallback count == 0
```

Strict mode may be introduced after the standard VoW / VRFStake path is proven stable.

---

## 8. MakeVoW() Policy

`MakeVoW()` constructs GROUP_B candidates and then delegates primary positioning to `rearrange()`.

### 8.1 Required Behavior

```text
1. Call generous() exactly once, outside the candidate loop.
2. If allowGenerous=true, set YellowNum=0 and include the node.
3. If allowGenerous=false, include only non-Yellow nodes.
4. Treat nil result or nil Front() as failure.
```

### 8.2 Reference Implementation

```go
func (v *VoW) MakeVoW(bNum uint64, wnodes *list.List, parentHash NBytes) SNBytes {
    temp := make(SNBytes, 0, ponp.Total)

    allowGenerous := v.generous()

    for e := wnodes.Front(); e != nil; e = e.Next() {
        w := e.Value.(*WNode)

        if w.Group != GROUP_B {
            continue
        }

        if w.State != CON_ESTABLISHED && w.State != CON_MINE {
            continue
        }

        if allowGenerous {
            w.YellowNum = 0
            temp = append(temp, w.SymId)
            continue
        }

        if !w.Yellow(bNum) {
            temp = append(temp, w.SymId)
            continue
        }
    }

    result := v.rearrange(bNum, temp, parentHash)
    if result == nil || result.Front() == nil {
        log.Warn("[CONSENSUS] VoW MakeVoW failed: rearrange failed",
            "section", "CONSENSUS",
            "bNum", bNum,
            "candidates", temp.Len(),
            "agreeB", ponp.AgreeB,
            "generous", allowGenerous,
            "parentHash", parentHash.TerminalString(),
        )
        return nil
    }

    return result
}
```

---

## 9. CommitVoW() Policy

`CommitVoW()` commits an agreed VoW result and must not corrupt the active Wlist on rearrange failure.

### 9.1 Required Behavior

```text
1. Reject nil vow.
2. Reject vow.Front()==nil.
3. Keep the existing Wlist if rearrange() fails.
4. Update Wlist only after successful rearrangement.
5. Update lastCommit only after successful commit.
```

### 9.2 Reference Implementation

```go
func (v *VoW) CommitVoW(bNum uint64, vow SNBytes, parentHash NBytes) bool {
    v.mu.Lock()
    defer v.mu.Unlock()

    if vow == nil || vow.Front() == nil {
        return false
    }

    rearranged := v.rearrange(bNum, vow, parentHash)
    if rearranged == nil || rearranged.Front() == nil {
        log.Warn("[CONSENSUS] CommitVoW failed: rearrange failed; keeping previous Wlist",
            "section", "CONSENSUS",
            "bNum", bNum,
            "vowLen", vow.Len(),
            "agreeB", ponp.AgreeB,
            "parentHash", parentHash.TerminalString(),
        )
        return false
    }

    v.Wlist = rearranged

    now := time.Now()
    v.IsSync = true
    v.pBlock = bNum
    v.lastTime = now
    v.lastVoW = now
    v.lastCommit = now
    v.pTime = now

    return true
}
```

---

## 10. MakeBlockBasedVoW() Policy

`MakeBlockBasedVoW()` reorders Wlist after a block becomes available.

### 10.1 Required Behavior

```text
1. parentHash must match the parent of blockNumber.
2. Keep the existing Wlist if rearrange() fails.
3. Update Wlist only after successful rearrangement.
```

### 10.2 Correct Invocation

```go
bNum := cBlock.NumberU64()
parentHash := cBlock.ParentHash().Bytes()

ns.ActiveVoW().MakeBlockBasedVoW(bNum, parentHash)
```

### 10.3 Invalid Invocation

```go
bNum := cBlock.NumberU64()
bHash := cBlock.Hash().Bytes()

ns.ActiveVoW().MakeBlockBasedVoW(bNum, bHash) // invalid
```

---

## 11. feVoW() Parent Hash Rule

If `feVoW()` uses:

```go
bNum := cBlock.NumberU64()
```

then both suggestion sending and commit must use:

```go
parentHash := cBlock.ParentHash().Bytes()
```

### 11.1 Correct Form

```go
bNum := cBlock.NumberU64()
parentHash := cBlock.ParentHash().Bytes()

s.sendSuggestion(SO_SUGGEST, bNum, parentHash)
ok := vow.CommitVoW(bNum, nVoW, parentHash)
```

### 11.2 Invalid Form

```go
s.sendSuggestion(SO_SUGGEST, bNum, cBlock.Hash().Bytes()) // invalid
```

---

## 12. generous() Policy

`generous()` is not part of VRFStake itself. It is a Yellow-node recovery policy used during candidate construction.

### 12.1 Semantics

```text
If the elapsed time since the last successful main block commit
exceeds VoWGenerous,
Yellow-excluded GROUP_B warrant nodes may be reintroduced.
```

### 12.2 Recommended Implementation

```go
func (v *VoW) generous() bool {
    if ponp.VoWGenerous <= 0 {
        return false
    }

    diff := time.Since(v.lastCommit).Nanoseconds() / int64(time.Millisecond)
    if diff > ponp.VoWGenerous {
        log.Debug(log.DF_VOW, "[CONSENSUS] VoW generous threshold exceeded",
            "section", "CONSENSUS",
            "diff", diff,
            "threshold", ponp.VoWGenerous,
            "lastCommit", v.lastCommit.Format(time.StampMilli),
        )
        return true
    }

    return false
}
```

### 12.3 Required Constraints

```text
- Do not use lastGenerous.
- Do not mutate lastCommit inside generous().
- Do not call generous() from Init().
- Compute allowGenerous once per MakeVoW() invocation, outside the loop.
```

---

## 13. Simulator Validation Requirements

The simulator must verify more than simple block creation success.

### 13.1 Required Assertions

```text
1. ExpectedVRFStakeProposer(...) returns without error.
2. The returned proposer is not the empty address.
3. The proposer exists in the candidate list.
4. block.Primary() equals the expected proposer.
5. fallback count equals 0.
```

### 13.2 Pre-Creation Expected Proposer Calculation

```go
parent := env.BC.CurrentBlock()
blockNumber := genb.Number().Uint64()

expected, err := core.ExpectedVRFStakeProposer(
    env.Cfg.ChainID,
    env.BC,
    blockNumber,
    parent.Hash(),
    activeWBNumber,
    core.DefaultVRFStakeEpochLength,
)
```

### 13.3 Post-Creation Block Validation

```go
block := env.BC.GetBlockByNumber(blockNumber)

expected, err := core.ExpectedVRFStakeProposer(
    env.Cfg.ChainID,
    env.BC,
    block.NumberU64(),
    block.ParentHash(),
    block.ActiveWBNumberU64(),
    core.DefaultVRFStakeEpochLength,
)

if block.Primary() != expected {
    return fmt.Errorf("primary mismatch")
}
```

---

## 14. Expected Logs

### 14.1 Successful VRFStake Reordering

A successful VRFStake application should emit:

```text
[CONSENSUS] VoW rearrange by VRFStake
```

Example:

```text
[CONSENSUS] VoW rearrange by VRFStake section=CONSENSUS bNum=3826 primary=0x0004544c453E372f0002 index=1 candidates=4
```

### 14.2 Fallback Path

The following logs indicate that VRFStake was not applied and fallback was used:

```text
[CONSENSUS] VoW rearrange VRFStake resolver failed
[CONSENSUS] VoW rearrange by fallback
```

During validation:

```text
- Keep fallback-related logs at WARN.
- Lower them to DEBUG only after the fallback path is proven unnecessary in normal operation.
```

---

## 15. Representative Failure Cases

### 15.1 `missing parent for VRFStake primary`

#### Symptom

```text
missing parent for VRFStake primary: block=N parentHash=<hash>
```

#### Cause

```text
bNum=N was paired with block N hash instead of block N-1 hash.
```

#### Fix

For an existing block:

```go
parentHash := block.ParentHash().Bytes()
```

For the next block:

```go
bNum := parent.NumberU64() + 1
parentHash := parent.Hash().Bytes()
```

---

### 15.2 `MakeVoW returned nil`

Possible causes:

```text
1. candidates == 0
2. candidates < AgreeB
3. rearrange() failed internally
4. result.Front() == nil
5. VRFStake strict mode rejected resolver failure
```

Recommended diagnostic fields:

```text
bNum
candidates
AgreeB
generous
parentHash
```

---

### 15.3 `FE agreed=false`

#### Symptom

```text
responderTotal < MinAgree
majority=false
agreed=false
nVoWLen=0
nVoWPrimary=<nil>
```

#### Meaning

The FE sheet did not collect enough valid responses to form a VoW agreement.

#### Inspect

```text
AppendFeSheet
SsData receive path
bNum / opinion / SI / group classification
duplicate suppression
peer receive state
```

---

## 16. Logging Policy

### 16.1 Validation-Phase Logs

```text
[CONSENSUS] VoW rearrange by VRFStake
[CONSENSUS] VoW rearrange VRFStake resolver failed
[CONSENSUS] VoW rearrange VRFStake primary not found
[CONSENSUS] CommitVoW completed
[CONSENSUS] VoW FE completed
```

### 16.2 Recommended Runtime Flags

```bash
--verbosity 3 \
--vflag "vow=6,ss=5,ssdata=4,n2n=4,peer=4" \
--vmodule "vow.go=6,pon.go=5,ss_sheet_vow.go=5,wnode_set.go=4"
```

### 16.3 Post-Stabilization Severity

```text
- Normal repeated logs: DEBUG
- Failures, fallback, mismatch: WARN
- Block commit summary: INFO
```

---

## 17. Implementation Checklist

```text
[ ] MakeVoW / CommitVoW / MakeBlockBasedVoW / rearrange use parentHash naming consistently.
[ ] For bNum=N, callers pass block(N).ParentHash().
[ ] For next block N+1 prediction, callers pass block(N).Hash().
[ ] No call site combines cBlock.Hash() with cBlock.NumberU64().
[ ] rearrange() failure never overwrites Wlist with nil.
[ ] generous() uses lastCommit only.
[ ] generous=true resets YellowNum=0 before appending candidates.
[ ] Init() does not invoke generous().
[ ] VRFStake resolver failure falls back during stabilization.
[ ] Simulator validates block.Primary() == ExpectedVRFStakeProposer(...).
[ ] Fallback count is tracked.
[ ] Final validation requires fallback count == 0.
```

---

## 18. Summary

VRFStake integration follows one central rule:

```text
Candidate construction remains VoW-driven.
Primary placement becomes VRFStake-driven.
```

Correctness depends primarily on preserving:

```text
blockNumber = N
parentHash = block(N-1).Hash()
```

Once this invariant is maintained consistently across `MakeVoW()`, `CommitVoW()`, `MakeBlockBasedVoW()`, `feVoW()`, and simulator validation, VRFStake proposer selection becomes deterministic, testable, and auditable.

# VRFStake Specification

## 1. Overview

**VRFStake** is the stake-weighted proposer selection rule used by the SymVerse PoN / VoW consensus flow.

Its purpose is to select exactly one expected proposer for a target block from the currently active warrant membership, while ensuring that:

- all nodes derive the same result from the same chain state;
- stake influences proposer probability;
- proposer selection is tied to the finalized parent block;
- block generation, block verification, and simulation share one common rule.

At a high level:

```text
VRFStake selects one proposer
from the active warrant snapshot
using parent-state stake
and a deterministic block-specific seed.
```

---

## 2. Terminology Note

The current implementation named **VRFStake** uses a deterministic, hash-derived, stake-weighted score selection rule.

It is **verifiable** in the sense that every node can recompute the same expected proposer from public chain state.  
However, the implementation shown in this specification does **not** introduce a separate cryptographic VRF proof object, VRF public key, or VRF proof verification step.

Therefore, in this document:

```text
VRFStake = deterministic, verifiable, stake-weighted proposer selection
```

The protocol may later evolve toward a proof-carrying VRF design, but the rule specified here is the current consensus proposer-selection algorithm.

---

## 3. Design Goals

VRFStake is designed to satisfy the following goals.

### 3.1 Deterministic Agreement

Every node that has the same:

- chain ID,
- target block number,
- parent block hash,
- active warrant block,
- parent state,

must compute the same proposer.

### 3.2 Stake-Aware Selection

A larger deposit amount should improve proposer-selection chance, but should not create an overly dominant linear advantage.

The current implementation uses a **logarithmic deposit-weight rule**:

```text
weight = floor(log2(depositAmount)) + 1
```

This means a larger deposit amount increases proposer advantage, but at a diminishing rate.

### 3.3 Parent-State Finality

The proposer for block `N` must be computed only from information available at block `N-1`.

```text
Target block: N
Stake source: parent state of block N-1
Entropy source: parent hash of block N-1
```

The current block state must never be used to select the current block proposer.

### 3.4 Compatibility with VoW

VRFStake does not replace VoW.  
VoW still builds the candidate list. VRFStake determines which eligible candidate should be treated as the expected primary by moving that proposer to the front.

---

## 4. Position in the Consensus Flow

The consensus flow is:

```text
1. Load active warrant membership.
2. Build the VRFStake snapshot from eligible warrant members.
3. Read each member's stake from the parent block state.
4. Compute the target block's proposer using VRFStake scoring.
5. VoW constructs its candidate list.
6. If the VRFStake proposer is present in that candidate list,
   move it to the front.
7. The first candidate becomes the expected primary.
8. Block verification checks that the actual primary matches
   the expected VRFStake proposer.
```

---

## 5. Inputs

For a target block `N`, proposer selection uses:

| Input | Description |
|---|---|
| `chainID` | Identifies the network |
| `blockNumber` | Target block number `N` |
| `parentHash` | Hash of block `N-1` |
| `activeWBNumber` | Active warrant block number |
| `parentState` | State root resolved from block `N-1` |
| `deposit amount` | Economic stake amount of each Group B proposer candidate |
| `epochLength` | Epoch parameter retained for protocol compatibility |

---

## 6. Fundamental Parent Rule

The most important rule is:

```text
For block N:
    blockNumber = N
    parentHash  = hash(block N-1)
```

### Correct

```text
blockNumber = 3826
parentHash  = hash(block 3825)
```

### Incorrect

```text
blockNumber = 3826
parentHash  = hash(block 3826)
```

The incorrect case attempts to use the current block’s own hash as the parent reference.  
That breaks parent lookup and causes proposer computation failure.

---

## 7. Proposer Eligibility: Warrant Group B

VRFStake does not select from all accounts in the network.  
It selects from a deterministic snapshot built from the **active warrant membership**.

The warrant membership distinguishes two groups:

| Group value | Group name | Role in VRFStake |
|---:|---|---|
| `0` | Group A | Not eligible as a block proposer |
| `1` | Group B | Eligible proposer population |

Only **Group B** members may become block proposers under VRFStake.

A warrant entry is included in the VRFStake proposer snapshot only when all of the following are true:

```text
1. the warrant entry exists;
2. its SymID exists and is not the zero address;
3. its Group value is exactly 1, meaning Group B;
4. the parent state reports a deposit amount greater than 0.
```

The snapshot therefore consists of:

```text
active Group B warrant members
with a non-zero deposit amount
```

This creates a clear separation:

```text
Group A:
    participates in the wider warrant structure,
    but is not selected as a VRFStake block proposer.

Group B:
    forms the proposer-eligible population
    used by the VRFStake algorithm.
```

---

## 8. Stake Model: Deposit Amount

In VRFStake, `Stake` means the **deposit amount** recorded for a proposer-eligible warrant member.

Formally:

```text
stake_i = deposit amount of candidate i
```

The implementation loads this amount from the parent state:

```text
stake_i = parentState.GetStake(address_i)
```

Therefore, for block `N`:

```text
stake used for selection
    = deposit amount recorded in the state of block N-1
```

This is consensus-critical.

### 8.1 Why deposit amount matters

The deposit amount represents the economic weight used by the proposer-selection algorithm.

A Group B member with a larger deposit amount receives a higher selection weight, but the advantage is **logarithmic**, not linear:

```text
weight = floor(log2(depositAmount)) + 1
```

This means:

- larger deposits improve proposer-selection competitiveness;
- doubling the deposit increases the weight gradually;
- the selection rule avoids giving proportional linear dominance to the largest depositor.

### 8.2 Zero deposit rule

A Group B member whose deposit amount is zero is excluded from the VRFStake snapshot.

```text
deposit amount == 0
    → not eligible for VRFStake proposer selection
```

### 8.3 Why parent state?

Using parent state ensures:

- proposer selection does not depend on the yet-to-be-produced current block;
- every node can calculate the proposer before block `N` is finalized;
- selection cannot be altered by transactions inside block `N` itself.

---

## 9. Snapshot Ordering

After eligible warrant members are collected, they are sorted by address bytes in ascending order.

```text
snapshot = sorted eligible warrant addresses
```

This sort order is important because the algorithm uses a **1-based snapshot index**.

```text
snapshot[1], snapshot[2], ..., snapshot[k]
```

If nodes used different snapshot ordering, they would compute different scores and could disagree on the proposer.

---

## 10. Stake Snapshot Alignment

The stake array is aligned with the address snapshot.

```text
snapshot[i] = address_i
stake[i]    = stake_i
```

The implementation requires:

```text
len(snapshot) == len(stake)
```

A mismatch is invalid because proposer scores are computed using both the address position and the associated stake value.

---

## 11. Seed Derivation

For a target block, VRFStake derives a deterministic block-specific seed:

```text
seed = H(chainID || blockNumber || parentHash)
```

Where:

- `H` is `SHA3-256`;
- `blockNumber` is encoded as 8-byte big-endian;
- `parentHash` is the hash of block `N-1`.

This seed changes when:

- the target block number changes;
- the parent block hash changes;
- the chain ID changes.

---

## 12. Candidate Base Score

Each snapshot member is evaluated using its 1-based index.

For candidate index `i`:

```text
baseScore_i = H(seed || snapshotIndex_i)
```

Where:

- `H` is `SHA3-256`;
- `snapshotIndex_i` is encoded as 8-byte big-endian.

The base score is then interpreted as a 256-bit unsigned integer.

---

## 13. Stake Weight

Each candidate’s stake is converted into a logarithmic weight:

```text
weight_i = floor(log2(stake_i)) + 1
```

Equivalent implementation behavior:

```text
weight_i = bitLength(stake_i)
```

### Examples

| Stake | Weight |
|---:|---:|
| 1 | 1 |
| 2 | 2 |
| 3 | 2 |
| 4 | 3 |
| 7 | 3 |
| 8 | 4 |
| 16 | 5 |
| 1024 | 11 |

This means:

- higher stake increases proposer competitiveness;
- doubling stake increases weight by exactly 1;
- very large stake does not create a proportional linear monopoly.

---

## 14. Weighted Score

For each candidate:

```text
effectiveScore_i = baseScore_i / weight_i
```

The lower the effective score, the better.

Because larger stake yields larger weight, it tends to reduce the effective score and therefore increases the chance of being selected.

---

## 15. Proposer Selection Rule

The selected proposer is the candidate with the smallest weighted score:

```text
proposer = snapshot[argmin(effectiveScore_i)]
```

Expanded form:

```text
For every candidate i:
    seed_i            = H(chainID || blockNumber || parentHash)
    baseScore_i       = H(seed_i || snapshotIndex_i)
    weight_i          = floor(log2(stake_i)) + 1
    effectiveScore_i  = baseScore_i / weight_i

Select the candidate with the smallest effectiveScore_i.
```

---

## 16. Tie-Breaking

The implementation updates the best proposer only when a score is **strictly smaller** than the current best score.

Therefore, if two candidates produce exactly equal effective scores:

```text
the candidate encountered first in the sorted snapshot remains selected
```

Since the snapshot is sorted by address, ties are resolved deterministically.

Although an exact tie is expected to be extremely rare, the rule remains well-defined.

---

## 17. Worked Example

Assume the active warrant snapshot is already sorted:

| Index | Address | Stake |
|---:|---|---:|
| 1 | A | 1 |
| 2 | B | 4 |
| 3 | C | 16 |

### Step 1 — Compute weights

| Address | Stake | Weight |
|---|---:|---:|
| A | 1 | 1 |
| B | 4 | 3 |
| C | 16 | 5 |

### Step 2 — Compute candidate base scores

Illustrative values:

| Address | Base Score |
|---|---:|
| A | 900 |
| B | 600 |
| C | 1000 |

### Step 3 — Divide by weight

| Address | Calculation | Effective Score |
|---|---|---:|
| A | 900 / 1 | 900 |
| B | 600 / 3 | 200 |
| C | 1000 / 5 | 200 |

### Step 4 — Select proposer

B and C tie at 200.  
Because B appears earlier in the sorted snapshot, B remains selected.

```text
proposer = B
```

---

## 18. Relationship with VoW Candidate Reordering

VRFStake selects a proposer from the active warrant snapshot.  
VoW separately constructs its runtime candidate list.

When the VRFStake proposer exists in the VoW candidate list:

```text
that candidate is moved to the front
```

### Example

Original VoW candidate list:

```text
[A, C, B, D]
```

VRFStake proposer:

```text
B
```

Reordered VoW list:

```text
[B, A, C, D]
```

Thus:

```text
Primary = B
```

---

## 19. Candidate-Set Consistency Requirement

For normal operation, the proposer selected by VRFStake should also appear in the VoW candidate list.

If not, the system detects a policy mismatch between:

```text
VRFStake proposer eligibility
and
VoW runtime candidate eligibility
```

This can happen if:

- a warrant is in the active stake snapshot but absent from the current VoW list;
- temporary connectivity or runtime filtering excludes a selected proposer;
- Yellow / generous recovery logic alters the VoW candidate set;
- active membership state and runtime candidate state diverge.

This condition is not merely a cosmetic error.  
It indicates that proposer-selection policy and candidate-availability policy are not fully aligned.

---

## 20. Block Verification Rule

The expected proposer is not only used during block production.  
It is also a verification target.

For a produced block:

```text
actualProposer == expectedVRFStakeProposer
```

In the current integration:

```text
actualProposer = block.Primary()
```

Verification therefore checks:

```text
block.Primary()
    == ExpectedVRFStakeProposer(
           chainID,
           blockNumber,
           parentHash,
           activeWBNumber,
           epochLength
       )
```

A mismatch means that the block primary does not follow the VRFStake proposer-selection rule.

---

## 21. Epoch Handling

The implementation accepts an `epochLength` parameter and computes:

```text
epoch = floor(blockNumber / epochLength)
```

At present, the snapshot itself is fixed for the active warrant context and the epoch parameter is preserved for compatibility with epoch-aware callers.

In other words:

```text
current implementation:
    epoch is accepted and computed,
    but snapshot selection does not yet vary by epoch.
```

This leaves room for future epoch-aware snapshot or stake policy refinements without changing the external function structure.

---

## 22. Fallback Policy

During stabilization, proposer-resolution failure may fall back to the legacy candidate-rearrangement rule.

### VRFStake path

```text
1. compute proposer;
2. find proposer in VoW candidate list;
3. move proposer to the front.
```

### Fallback path

Fallback may be used when:

- parent block lookup fails;
- parent state cannot be loaded;
- active warrant block is missing;
- the stake snapshot is empty;
- proposer computation fails;
- the proposer is not found in the VoW candidate list.

### Operational target

Fallback is a temporary stabilization tool.  
The final validation target is:

```text
fallback count == 0
```

A system that frequently falls back is not operating in a fully effective VRFStake mode.

---

## 23. Failure Cases

### 23.1 Missing Parent

Cause:

```text
blockNumber and parentHash do not identify the real parent block
```

Typical issue:

```text
For block N,
the hash of block N was passed
instead of the hash of block N-1.
```

Consequence:

```text
expected proposer cannot be derived
```

---

### 23.2 Empty Warrant Membership

Cause:

```text
the active warrant block contains no warrant members
```

Consequence:

```text
VRFStake cannot build a snapshot
```

---

### 23.3 Empty VRFStake Snapshot

Cause:

The active warrant block may exist, but no entry survives the eligibility filter:

```text
- not group 1;
- zero address;
- missing SymID;
- zero or missing parent-state stake.
```

Consequence:

```text
no proposer can be selected
```

---

### 23.4 Snapshot / Stake Length Mismatch

Cause:

```text
the address snapshot and stake snapshot are not aligned
```

Consequence:

```text
the VRFStake state is invalid
```

---

### 23.5 Proposer Not Found in VoW Candidate List

Cause:

```text
VRFStake selected a valid snapshot member,
but VoW does not currently include it in its runtime candidate list.
```

Consequence:

```text
VRFStake ordering cannot be applied directly
```

---

### 23.6 Invalid Block Primary

Cause:

```text
block.Primary() differs from the expected VRFStake proposer
```

Consequence:

```text
the block violates the proposer-selection rule
```

---

## 24. Effect on Consensus and Node Behavior

VRFStake changes the following aspects of consensus behavior.

### 24.1 Group B Becomes the Proposer-Election Domain

VRFStake proposer selection is restricted to **Group B**.

```text
Group A:
    not selected as a proposer

Group B:
    eligible for proposer scoring and final selection
```

This makes Group B the protocol-defined proposer-election domain.


### 24.2 Primary Selection Becomes Deposit-Aware

The primary is no longer decided solely by a simple hash modulo rearrangement.  
It is selected through a deterministic stake-weighted score calculation.

### 24.3 Parent-State Dependence Becomes Explicit

The proposer for block `N` is linked to:

```text
state of block N-1
```

This defines exactly when stake changes can influence proposer selection.

### 24.4 Deposit Changes Affect Future Blocks, Not the Current Block

If a deposit amount change is included in block `N`, it cannot affect proposer selection for block `N`.

It may affect later proposer selections only after that new state becomes part of the parent state of a subsequent block.

### 24.5 Candidate Availability Still Matters

Even if VRFStake chooses a proposer mathematically, the runtime consensus layer must be able to include that proposer in the candidate list.

Thus, network liveness and proposer-selection policy remain connected.

### 24.6 Block Validation Gains a New Consensus Check

Nodes can independently verify that:

```text
the actual block primary matches
the expected VRFStake-selected proposer
```

---

## 25. Comparison with Legacy Hash-Based Rearrangement

| Aspect | Legacy Rearrangement | VRFStake |
|---|---|---|
| Input | Parent hash-derived modulo | Chain ID, block number, parent hash, stake snapshot |
| Deposit-aware | No | Yes |
| Candidate scoring | No | Yes |
| Selection mode | Index skip / reorder | Argmin weighted score |
| Higher deposit advantage | No | Yes, logarithmic |
| Parent-state deposit use | No | Yes |
| Verifiable expected proposer | Limited | Explicit |

The legacy path is simpler, but it does not encode stake into proposer choice.  
VRFStake introduces a richer, protocol-defined proposer rule while preserving deterministic recomputation.

---

## 26. Security and Fairness Considerations

### 26.1 Deterministic Verifiability

Every honest node can recompute the expected proposer from chain data.  
This prevents hidden proposer selection behavior.

### 26.2 Parent Hash as Dynamic Seed Material

Because the seed includes the parent hash, proposer selection changes with chain progression and is not a static fixed ordering.

### 26.3 Logarithmic Deposit Weight

The use of:

```text
floor(log2(depositAmount)) + 1
```

gives larger depositors an advantage, but with diminishing returns.

This design avoids both extremes:

- no stake influence at all;
- purely linear dominance by the largest stake.

### 26.4 Snapshot Determinism

Address sorting and 1-based indices ensure that the same membership state produces the same score inputs.

### 26.5 Scope of Randomness

The algorithm is deterministic after the parent block is known.  
It is not a hidden private lottery; it is a publicly recomputable protocol rule.

---

## 27. Reference Pseudocode

```text
Input:
    chainID
    blockNumber N
    parentHash H(N-1)
    active warrant membership
    parent state

BuildSnapshot():
    snapshot = []
    for warrant in activeWarrants:
        if warrant is invalid:
            continue
        if warrant.group != 1:
            continue

        addr = warrant.symID
        depositAmount = parentState.GetStake(addr)

        if depositAmount <= 0:
            continue

        snapshot.append(addr, depositAmount)

    sort snapshot by address ascending
    return snapshot

SelectProposer():
    seed = SHA3(chainID || N || H(N-1))

    best = none

    for each candidate i in snapshot using 1-based index:
        baseScore = SHA3(seed || i)
        weight = floor(log2(depositAmount_i)) + 1
        effectiveScore = baseScore / weight

        if best is none or effectiveScore < best.score:
            best = candidate_i

    return best.address
```

---

## 28. Validation Checklist

A VRFStake implementation is correct only if:

```text
[ ] Block N uses hash(block N-1) as parentHash.
[ ] Deposit amount is read from the parent state, not current block state.
[ ] Snapshot includes only valid Group B (`Group == 1`) warrant members.
[ ] Group A (`Group == 0`) members are excluded from proposer selection.
[ ] Zero-deposit members are excluded.
[ ] Snapshot is sorted deterministically by address.
[ ] Snapshot index is 1-based.
[ ] Seed = H(chainID || blockNumber || parentHash).
[ ] Base score = H(seed || snapshotIndex).
[ ] Weight = floor(log2(depositAmount)) + 1.
[ ] Effective score = baseScore / weight.
[ ] Proposer = candidate with minimum effective score.
[ ] Tie behavior is deterministic.
[ ] Actual block primary equals expected proposer.
[ ] Fallback occurrence is monitored.
[ ] Stable target is fallback count == 0.
```

---

## 29. Summary

VRFStake is the current SymVerse proposer-selection rule that combines:

- active warrant membership,
- Group B proposer eligibility,
- parent-state deposit amounts,
- deterministic parent-hash-derived seed material,
- logarithmic deposit weighting,
- verifiable minimum-score selection.

Its essential formula is:

```text
seed             = H(chainID || blockNumber || parentHash)
baseScore_i      = H(seed || snapshotIndex_i)
weight_i         = floor(log2(depositAmount_i)) + 1
effectiveScore_i = baseScore_i / weight_i
proposer         = argmin(effectiveScore_i)
```

The most important operational rule remains:

```text
For block N,
VRFStake must use the parent hash and parent state of block N-1.
```

This ensures that proposer selection is deterministic, stake-aware, and independently verifiable by every consensus participant.

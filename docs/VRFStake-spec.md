# VRFStake Specification

## 1. Overview

**VRFStake** is a proposer-selection mechanism used in the SymVerse PoN / VoW consensus flow.

Its purpose is to determine the next block primary in a way that is:

- **Deterministic**: every node computes the same expected proposer from the same chain state
- **State-aware**: proposer selection reflects the active warrant membership and stake-related information
- **Hard to bias in advance**: the selection input includes the previous block hash
- **Compatible with the existing VoW process**: it does not replace VoW candidate construction, but reorders the already agreed candidate set

In simple terms:

> VRFStake decides **which eligible candidate should be placed first as the expected primary** for a block.

---

## 2. Motivation

Before VRFStake, the primary ordering was based on a simpler hash-derived rearrangement rule.  
That method was deterministic and lightweight, but it did not directly reflect stake-oriented selection policy.

VRFStake improves this by introducing a proposer calculation that depends on:

1. The target block number
2. The parent block hash
3. The active warrant set
4. Stake-related selection logic
5. The current epoch context

This allows proposer selection to be aligned with the network’s validator or warrant state while preserving deterministic consensus behavior.

---

## 3. Scope

VRFStake affects **primary selection**, not the entire consensus process.

It does **not**:

- Create the VoW candidate list
- Replace FE voting
- Replace block validation
- Change transaction execution
- Change block structure by itself

It **does**:

- Compute the expected proposer for a block
- Reorder the VoW candidate list so that the expected proposer becomes the first candidate
- Provide a shared deterministic rule for block-primary verification
- Allow simulators and nodes to verify whether the selected primary matches protocol expectations

---

## 4. Core Idea

For a target block `N`, VRFStake uses the **previous block**, namely block `N-1`, as the entropy and state reference.

```text
Target block: N
Reference block: N - 1
Reference hash: hash of block N - 1
```

Therefore:

```text
blockNumber = N
parentHash  = hash of block N - 1
```

This rule is essential.

The proposer for block `N` must be calculated from the parent state of block `N`, not from the hash of block `N` itself.

---

## 5. Why the Parent Hash Matters

The parent hash serves as a common, already-finalized input known to all nodes before block `N` is produced.

This has two important properties.

### 5.1 Deterministic Agreement

All honest nodes that have the same chain head use the same:

- block number
- parent hash
- parent state

As a result, they compute the same expected proposer.

### 5.2 Reduced Predictability

Because the parent hash changes from block to block, the proposer selection input also changes continuously.

This makes the proposer order less static than a fixed round-robin or identity-based selection scheme.

---

## 6. Selection Inputs

VRFStake proposer computation is based on the following logical inputs.

| Input | Meaning |
|---|---|
| `chainID` | Identifies the target network |
| `blockNumber` | The block whose proposer is being selected |
| `parentHash` | Hash of the direct parent block |
| `activeWBNumber` | Active warrant block reference |
| `epochLength` | Epoch granularity used by the selection policy |
| active membership / stake state | Eligibility and weighting basis |

These inputs ensure that proposer selection is tied to both:

- **chain context**, and
- **current consensus membership state**

---

## 7. High-Level Calculation Flow

The protocol-level calculation can be described as follows.

```text
1. Determine the target block number N.
2. Read the parent block hash H(N-1).
3. Load the parent-chain state associated with H(N-1).
4. Determine the active warrant / membership context.
5. Derive the VRFStake selection result for block N.
6. Return the expected proposer address.
7. Move that proposer to the front of the VoW candidate list, if present.
```

Conceptually:

```text
ExpectedProposer(N)
    = VRFStake(
        chain context,
        block number N,
        parent hash H(N-1),
        active membership,
        stake policy,
        epoch policy
      )
```

---

## 8. Relationship with VoW

VRFStake does not replace VoW candidate generation.

The order of operations is:

```text
1. VoW constructs the eligible candidate set.
2. Candidates are sorted into a deterministic baseline order.
3. VRFStake computes the expected proposer.
4. If that proposer exists in the candidate set:
      move it to the front.
5. The first candidate becomes the expected primary.
```

### Example

Suppose the candidate list is:

```text
[A, B, C, D]
```

If VRFStake selects `C`, the reordered list becomes:

```text
[C, A, B, D]
```

In this case:

```text
Primary = C
```

---

## 9. Effect on Block Production

VRFStake influences **who is expected to lead block production** for a given height.

For each target block:

- every node calculates the same expected proposer;
- the VoW candidate order is adjusted consistently;
- the block primary should match the VRFStake result.

This allows the network to verify:

```text
block.Primary == ExpectedVRFStakeProposer
```

If the values match, the block’s primary selection is consistent with the protocol rule.

---

## 10. Effect on Consensus Safety

VRFStake is designed to preserve the deterministic nature of consensus.

Its safety contribution is based on three rules:

### 10.1 Shared Input Rule

Every node must use the same parent-hash rule:

```text
For block N, use parent hash of block N-1.
```

### 10.2 Shared Membership Rule

Every node must evaluate proposer eligibility from the same active membership / warrant state.

### 10.3 Shared Reordering Rule

Once the proposer is computed, the same candidate-reordering rule must be applied by all nodes.

If these conditions hold, all correct nodes derive the same primary order.

---

## 11. Effect on Fairness and Stake-Aware Selection

The traditional fallback order is purely hash-derived over the candidate list.  
VRFStake adds an explicit **stake-aware proposer selection path**.

This makes it possible to reflect policies such as:

- eligibility derived from active warrant state;
- epoch-dependent proposer rotation;
- stake-influenced proposer choice;
- deterministic weighting logic shared by all nodes.

The exact weight policy may evolve, but the specification requires that:

```text
The same parent state and the same protocol parameters
must always produce the same proposer.
```

---

## 12. Epoch Context

VRFStake may use an epoch length to stabilize or group selection behavior across ranges of block heights.

An epoch can be understood as a fixed block interval:

```text
epoch = floor(blockNumber / epochLength)
```

The epoch value may influence:

- selection seed derivation;
- eligible stake snapshot interpretation;
- proposer rotation behavior;
- consistency of stake treatment over a defined period.

The key requirement is that epoch calculation must be deterministic and identical across all nodes.

---

## 13. Fallback Policy

During the stabilization and validation phase, VRFStake failure may fall back to the legacy rearrangement rule.

Fallback exists to prevent temporary proposer-resolution failure from stopping the entire consensus process.

### Fallback may occur when:

- proposer resolution cannot be completed;
- parent state lookup fails;
- the expected proposer is not found in the candidate list;
- the VRFStake subsystem is temporarily unavailable.

### Current policy

```text
VRFStake success:
    use VRFStake proposer ordering

VRFStake failure:
    use legacy hash-based fallback ordering
```

### Target policy

For final validation and stable operation:

```text
fallback count should converge to 0
```

Fallback is therefore a **stabilization mechanism**, not the intended steady-state path.

---

## 14. Correctness Rule for Block Number and Parent Hash

This is the most important operational rule in the specification.

### Correct form

For block `N`:

```text
blockNumber = N
parentHash  = hash of block N-1
```

### Incorrect form

```text
blockNumber = N
parentHash  = hash of block N
```

The incorrect form attempts to use a block’s own hash as though it were its parent reference.  
This breaks parent lookup and causes proposer calculation failure.

Typical symptom:

```text
missing parent for VRFStake primary
```

---

## 15. Consensus Lifecycle Impact

VRFStake affects several points in the consensus lifecycle.

### 15.1 Candidate Preparation

The candidate population itself is still determined by VoW logic.  
VRFStake begins only **after** that candidate set exists.

### 15.2 Primary Arrangement

The expected proposer is moved to the front of the list.

### 15.3 Commit-Time Consistency

Committed VoW ordering must remain aligned with the VRFStake result.

### 15.4 Block-Based Re-evaluation

When block-based VoW ordering is refreshed, the same parent-hash rule applies.

### 15.5 Simulator Verification

Simulation must verify not merely that blocks are produced, but that they are produced with the **correct protocol-selected primary**.

---

## 16. Validation Requirements

A VRFStake implementation is considered correct only if the following conditions hold.

### 16.1 Proposer Calculation

- The proposer is calculated without error.
- The proposer is not an empty address.
- The proposer is derived from the correct parent block state.

### 16.2 Candidate Membership

- The calculated proposer appears in the VoW candidate list.
- The candidate list is reordered to place that proposer first.

### 16.3 Block Consistency

- The actual block primary equals the expected VRFStake proposer.

```text
block.Primary == ExpectedVRFStakeProposer(...)
```

### 16.4 Fallback Monitoring

- Fallback occurrence is counted.
- Stable verification target is:

```text
fallback count == 0
```

---

## 17. Example Walkthrough

Assume the chain is preparing block `3826`.

### Inputs

```text
Target block number: 3826
Parent block number: 3825
Parent hash: H(3825)
Active warrant state: W
Epoch policy: E
```

### Step 1 — Candidate list exists

```text
[A, B, C, D]
```

### Step 2 — VRFStake proposer is computed

```text
Expected proposer = B
```

### Step 3 — Candidate list is reordered

```text
[B, A, C, D]
```

### Step 4 — Primary expectation

```text
Primary = B
```

### Step 5 — Block validation

The produced block must satisfy:

```text
block.Primary = B
```

---

## 18. Operational Observability

A running node should make it possible to distinguish:

1. **VRFStake success**
2. **VRFStake proposer missing from candidates**
3. **Resolver failure**
4. **Fallback activation**
5. **Primary mismatch**

These observations are important because:

- they reveal whether VRFStake is actually being used;
- they expose parent-hash misuse;
- they show whether membership state and candidate state are aligned;
- they help identify divergence before it becomes a consensus failure.

---

## 19. Failure Cases

### 19.1 Missing Parent Reference

Cause:

```text
The target block number and the supplied parent hash do not match.
```

Example:

```text
blockNumber = N
parentHash  = hash of block N
```

Expected correction:

```text
parentHash = hash of block N-1
```

---

### 19.2 Proposer Not Found in Candidate List

Cause:

- the proposer was selected from valid stake state;
- but the current VoW candidate list does not include it.

Possible reasons:

- eligibility filters differ between proposer calculation and candidate construction;
- membership state or timing differs;
- candidate recovery rules changed the set unexpectedly.

This condition must be treated as important because it indicates a mismatch between:

```text
selection policy
and
candidate eligibility policy
```

---

### 19.3 Frequent Fallback

Cause may include:

- unresolved parent state;
- proposer calculation failure;
- candidate-set inconsistency;
- insufficient synchronization between consensus modules.

Frequent fallback means that the network is not yet operating in full VRFStake mode.

---

## 20. Security and Design Considerations

VRFStake improves primary selection by binding proposer choice to finalized chain context and active membership state.

Its design intention is to provide:

- deterministic proposer selection;
- reduced static predictability;
- stake-aware ordering;
- consistent proposer verification.

However, VRFStake remains dependent on:

- correct parent-state lookup;
- correct membership-state interpretation;
- strict consistency of candidate-list construction;
- deterministic implementation across all nodes.

Any mismatch in these inputs can create proposer disagreement.

---

## 21. Implementation Independence

This specification defines the **protocol behavior**, not a single fixed code layout.

Implementations may differ internally, but they must preserve:

```text
1. block N uses parent hash of block N-1;
2. proposer calculation is deterministic;
3. candidate reordering is consistent;
4. actual primary matches the expected proposer;
5. fallback is observable and minimized.
```

---

## 22. Summary

VRFStake is a deterministic, stake-aware proposer selection mechanism integrated into the existing PoN / VoW flow.

Its role is not to replace consensus, but to strengthen proposer selection by ensuring that:

- the proposer is derived from finalized parent-chain context;
- all nodes can independently compute the same expected proposer;
- the VoW candidate list is reordered around that proposer;
- block primary selection becomes verifiable at the protocol level.

The single most important rule is:

```text
For block N, VRFStake must use the parent hash of block N-1.
```

When this rule is maintained consistently, VRFStake can serve as a stable foundation for deterministic and stake-aware primary selection.

# SymVerse V3 Testing Guide

> **Status:** Draft v0.1  
> **Date:** 2026-05-14  
> **Scope:** Reproducible protocol, membership, transaction, and sync validation scenarios

---

# 1. Purpose

This guide defines the initial testing direction for SymVerse V3 documentation.

It focuses on reproducible scenarios for:

- Account creation
- PQC account generation
- Funding
- Raw transaction submission
- Membership flows
- Link / LinkedBy verification
- Restart persistence
- Sync consistency

---

# 2. Testing Principles

A V3 test should aim to be:

- Repeatable
- Scriptable
- Observable through logs and RPC
- Restart-safe where relevant
- Clear about expected success and failure outcomes

---

# 3. Recommended Test Categories

| Category | Purpose |
|---|---|
| Account creation | Validate account/citizen setup |
| PQC account creation | Validate algorithm-specific account metadata |
| Funding | Prepare accounts for transaction tests |
| Transfer matrix | Exercise sender/receiver combinations |
| Membership | Validate nickname, Referrer, Link |
| Query consistency | Validate public API correctness |
| Restart persistence | Ensure state remains available |
| Sync validation | Ensure reconstructed state matches canonical state |

---

# 4. Account Creation Tests

## 4.1 Legacy ECDSA Sender Accounts

Create multiple sender accounts for transaction matrix testing.

Example objective:

```text
2 ECDSA sender accounts
```

---

## 4.2 PQC Sender Accounts

Create multiple PQC sender accounts.

Example objective:

```text
3 PQC sender accounts
```

Algorithm variants may be tested as supported.

---

## 4.3 Receiver Accounts

Create receiver accounts separately from sender accounts.

Example objective:

```text
5 receiver accounts
```

This prevents ambiguity when testing transfer direction and balance changes.

---

# 5. Funding Tests

Funding SHOULD be performed in a clearly separated phase after account creation.

The test guide SHOULD distinguish:

1. Create accounts
2. Fund accounts
3. Verify balances
4. Submit transactions

---

# 6. Transaction Matrix Tests

A transaction matrix SHOULD cover combinations such as:

- ECDSA sender → ECDSA receiver
- ECDSA sender → PQC receiver
- PQC sender → ECDSA receiver
- PQC sender → PQC receiver

Each test SHOULD define:

- Sender
- Receiver
- Value
- Expected balance change
- Expected transaction success/failure

---

# 7. Nickname Tests

Nickname tests SHOULD verify:

1. Successful nickname creation
2. Query by nickname
3. Duplicate nickname rejection
4. Existing nickname conflict behavior
5. Post-restart persistence

---

# 8. RefCode Tests

RefCode tests SHOULD verify:

1. RefCode is generated after citizen creation
2. RefCode query returns expected value or format
3. RefCode remains stable after restart
4. RefCode remains stable after sync

---

# 9. Referrer Tests

Referrer tests SHOULD verify:

1. Successful Referrer registration
2. Query result reflects the relation
3. Invalid RefCode or invalid target rejection
4. Duplicate/refill behavior, once policy is fixed

---

# 10. Link Tests

## 10.1 Forward Link Registration

The baseline scenario SHOULD support sequential registration of multiple links.

Example:

```text
A registers Link targets:
B1, B2, B3, ... B10
```

---

## 10.2 Forward Query

Querying `A` SHOULD expose the Link list.

---

# 11. LinkedBy Tests

## 11.1 Reverse Query Requirement

If:

```text
A links B
```

then querying `B` SHOULD expose:

```text
LinkedBy includes A
```

---

## 11.2 Multi-Source Reverse Query

If multiple citizens link the same target, the reverse list behavior SHOULD be specified and tested.

---

# 12. Node Restart Tests

After completing membership state mutations:

1. Stop node
2. Restart node
3. Re-query:
   - Nickname
   - RefCode
   - Referrer
   - Link
   - LinkedBy

All results SHOULD remain consistent.

---

# 13. Sync Tests

Sync tests SHOULD verify that a node reconstructing state through peer synchronization reaches the same membership/query results as the originating node.

Recommended checks:

- Citizen state availability
- RefCode consistency
- Link list consistency
- LinkedBy consistency
- Transaction receipt visibility, if applicable

---

# 14. Performance and Load Follow-up

The current V3 documentation can later include:

- `wrk2`-based RPC pressure tests
- Transaction send-rate tests
- Block-per-second observation
- Per-block transaction capacity measurements
- Consensus responsiveness under transaction load

---

# 15. Logging Recommendations

Tests SHOULD document the minimum useful log categories or grep patterns required to validate:

- Transaction acceptance
- Citizen processing
- Membership state updates
- Block inclusion
- Consensus write path
- Sync completion

---

# 16. Output Format Recommendation

Each future test case SHOULD eventually be written in a predictable format:

```text
Test Name:
Purpose:
Preconditions:
Steps:
Expected Result:
Negative Case:
Restart Check:
Sync Check:
```

---

# 17. Open Items

1. Final command examples for each scenario
2. Standard test account naming convention
3. Final raw transaction scripts
4. Final restart automation flow
5. Final sync validation command set
6. CAD-related test vector format

---

# 18. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-14 | Initial V3 testing guide draft |

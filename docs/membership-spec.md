# SymVerse V3 Membership Specification

> **Status:** Draft v0.1  
> **Date:** 2026-05-14  
> **Scope:** Membership runtime policy for Nickname, RefCode, Referrer, Link, and LinkedBy

---

# 1. Purpose

This document defines the initial **membership runtime specification** for SymVerse V3.

The membership subsystem formalizes:

- Nickname ownership
- Deterministic RefCode generation
- Referrer assignment
- Link relationship registration
- Reverse LinkedBy query behavior
- Public membership query expectations

---

# 2. Membership Design Principle

The membership system distinguishes:

1. **Citizen-creation-time deterministic state**
2. **Explicit transaction-driven membership relationships**

Citizen creation may initialize only data that is deterministic and self-owned.

---

# 3. Membership State Categories

| Category | Created At | Notes |
|---|---|---|
| Nickname | Citizen creation or membership transaction, according to policy | Unique owner mapping |
| RefCode | Citizen creation | Deterministic |
| Referrer | Membership transaction | Not auto-created |
| Link | Membership transaction | Directional relationship |
| LinkedBy | Derived/reverse query view or indexed reverse relation | Must be queryable |

---

# 4. Nickname

## 4.1 Definition

A **Nickname** is a membership-facing identifier associated with a citizen.

It serves as:

- A human-readable lookup key
- A uniqueness-constrained membership label
- A query path for public citizen APIs

---

## 4.2 Ownership Rule

A nickname MUST resolve to at most one owner at a given state height.

Implementations SHOULD reject attempts to create a nickname that is already owned by another citizen.

---

## 4.3 Replacement / Reassignment

The V3 baseline distinguishes ownership lookup from mutation policy.

The detailed mutation policy SHOULD specify:

- Whether a citizen may replace an existing nickname
- Whether deletion is required before reassignment
- Whether historical lookup is preserved

Current operational behavior indicates that duplicate nickname creation for the same sender may be rejected when a nickname already exists.

---

# 5. RefCode

## 5.1 Definition

A **RefCode** is a deterministic membership code derived from citizen block placement.

---

## 5.2 Generation Rule

The baseline rule is:

```text
refCode = (citizenBlockNumber << 12) | citizenIndex
```

---

## 5.3 Capacity

Because 12 bits are reserved for `citizenIndex`, this supports:

```text
4096 citizens per CitizenBlock
```

---

## 5.4 Generation Time

RefCode MUST be generated during citizen creation when the citizen becomes part of a CitizenBlock.

RefCode SHOULD NOT depend on:

- Nickname strings
- Transaction arrival time alone
- External mutable metadata

---

## 5.5 Display Formatting

The storage-level value and user-facing string may differ.

A public formatter MAY normalize padded sections for display, but it MUST preserve deterministic reversibility or a clearly documented lookup rule.

Detailed display formatting SHOULD be frozen in a later revision when the public representation is finalized.

---

# 6. Referrer

## 6.1 Definition

A **Referrer** expresses who referred a citizen into the membership relationship model.

---

## 6.2 Initialization Rule

Citizen creation MUST NOT automatically set:

- Referrer
- ReferredBy
- Referral ancestry metadata, unless explicitly specified in a future revision

Referrer state SHOULD be written only through a dedicated membership transaction.

---

## 6.3 Validation Requirements

A Referrer registration SHOULD validate:

- Sender authorization
- Existence of referenced citizen or RefCode
- Duplicate registration rule
- Self-reference rule, if prohibited
- Idempotency or rejection policy

---

## 6.4 State Transition Direction

At minimum, a successful Referrer operation SHOULD produce a queryable relationship between:

- The subject citizen
- The designated referrer identity or code

The final storage layout is implementation-defined unless separately standardized.

---

# 7. Link

## 7.1 Definition

A **Link** is a directional membership relationship.

If citizen `A` links citizen `B`:

```text
A → B
```

then `A` is the link owner and `B` is the link target.

---

## 7.2 Registration Rule

Link state SHOULD be created only through an explicit membership transaction.

Citizen creation MUST NOT auto-populate Link.

---

## 7.3 Minimum Test Requirement

The V3 test baseline requires support for dynamic Link registration across multiple entries.

A practical minimum scenario is:

```text
At least 10 Link targets can be registered and queried.
```

This is a test target, not necessarily a final protocol hard cap.

---

# 8. LinkedBy

## 8.1 Definition

`LinkedBy` is the reverse-query view of Link.

If:

```text
A → B
```

then:

- Querying `A` should show `B` in `Link`
- Querying `B` should show `A` in `LinkedBy`

---

## 8.2 Query Requirement

The reverse view MUST be queryable through a public citizen or membership query API.

This behavior is part of the functional membership contract, not merely an internal optimization.

---

## 8.3 Storage Consideration

Implementations may realize `LinkedBy` as:

- A separately maintained reverse index
- A derived query over stored directional links
- Another deterministic strategy

The externally visible query result MUST remain consistent.

---

# 9. Citizen Creation Policy

Citizen creation MAY initialize:

- Nickname owner mapping, when included in the citizen creation path
- RefCode

Citizen creation MUST NOT initialize:

- Referrer
- Link
- LinkedBy

---

# 10. Public Query Expectations

A public API SHOULD support:

| Query | Expected Result |
|---|---|
| Citizen by SymID | Core citizen state plus membership-related fields where available |
| Citizen by Nickname | Citizen identity resolved from nickname |
| RefCode query | Deterministic code or formatted representation |
| Referrer query | Referrer metadata |
| Link query | Forward link list |
| LinkedBy query | Reverse link list |

---

# 11. Restart and Persistence Requirement

Membership state MUST remain consistent across node restart.

At minimum, the following should survive restart and remain queryable:

- Nickname ownership
- RefCode
- Referrer
- Link
- LinkedBy

---

# 12. Synchronization Requirement

A node syncing from peers MUST reconstruct membership state consistently with block/state execution.

After sync, query results for:

- Nickname
- RefCode
- Referrer
- Link
- LinkedBy

MUST match the canonical state.

---

# 13. Open Items

The following items remain to be finalized:

1. Exact nickname mutation policy
2. Referrer reassignment policy
3. Duplicate Link behavior
4. Link deletion or unlink policy
5. Public display format for RefCode
6. Canonical JSON-RPC method names
7. Historical-state query semantics

---

# 14. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-14 | Initial membership specification draft |

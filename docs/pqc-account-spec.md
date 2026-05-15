# SymVerse V3 PQC Account Specification

> **Status:** Draft v0.1  
> **Date:** 2026-05-14  
> **Scope:** PQC account metadata, algorithm identification, and validation direction

---

# 1. Purpose

This document defines the initial V3 specification direction for **PQC-capable accounts**.

It covers:

- Account authorization categories
- PQC metadata
- Algorithm identifiers
- Public-key storage/reference concepts
- Interaction with transaction verification

---

# 2. Account Authorization Categories

V3 SHOULD support clear authorization families.

| Category | Description |
|---|---|
| Legacy account | Existing ECDSA-style authorization |
| PQC account | Authorization based on a post-quantum signature scheme |
| Hybrid account | Potential future multi-mode or migration account |

Hybrid behavior is not finalized in this draft.

---

# 3. PQC Account Metadata

A representative account extension is:

```go
type QuantumInfo struct {
    QAlgo   *uint16
    QKeyPub []byte
}
```

Integrated into account metadata conceptually as:

```go
type AccountInfo struct {
    // Existing account fields omitted
    Pqc *QuantumInfo
}
```

---

# 4. Field Meaning

## 4.1 `QAlgo`

`QAlgo` identifies the PQC signature scheme or scheme family selected for the account.

It SHOULD:

- Be unambiguous
- Be stable once assigned unless migration policy exists
- Map to a verification implementation

---

## 4.2 `QKeyPub`

`QKeyPub` stores or references the public verification material required by the selected PQC scheme.

The specification SHOULD eventually define:

- Raw byte format
- Length constraints
- Encoding constraints
- Whether compression or hashing is used
- Whether external references are allowed

---

# 5. Account Creation

A PQC account creation flow SHOULD provide:

1. Authorization algorithm code
2. PQC public key material
3. Account identifier derivation or binding rule
4. Citizen/account registration path
5. Public query visibility

---

# 6. Query Expectations

Public account/citizen APIs SHOULD make PQC-relevant information queryable as policy permits.

Potentially queryable fields include:

- Algorithm code
- Presence of PQC metadata
- Public-key hash or representation
- Authorization mode

Whether full public-key bytes are exposed is a specification decision.

---

# 7. Transaction Verification Coupling

When a sender is a PQC account:

1. The transaction verifier MUST recognize the sender's PQC authorization mode.
2. The verifier MUST select the correct algorithm using `QAlgo`.
3. The verifier MUST use the correct public-key material.
4. The verifier MUST validate the transaction's PQC witness, such as `PQSig`.

---

# 8. Interoperability

A PQC sender may interact with:

- Legacy receiver
- PQC receiver
- Membership state transitions
- Raw transaction submission flows

The transaction matrix should be documented and tested.

---

# 9. Compatibility Considerations

Adding PQC metadata to existing account models raises compatibility questions around:

- RLP encoding
- Existing storage decoding
- Zero/default field handling
- Old-state readability by upgraded nodes
- New-state readability by old nodes, if applicable

These require implementation-specific compatibility notes.

---

# 10. Security Considerations

The account specification SHOULD eventually address:

- Invalid public-key rejection
- Algorithm confusion prevention
- Domain separation
- Replay resistance interaction
- Key replacement / migration policy

---

# 11. Open Items

1. Final `QAlgo` registry
2. Final `QKeyPub` encoding
3. Key hash vs raw key query behavior
4. Account migration policy
5. RLP backward compatibility rules
6. Hybrid account policy

---

# 12. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-14 | Initial PQC account specification draft |

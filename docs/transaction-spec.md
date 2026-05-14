# SymVerse V3 Transaction Specification

> **Status:** Draft v0.1  
> **Date:** 2026-05-14  
> **Scope:** Baseline transaction structure, authorization fields, and PQC-compatible signing model

---

# 1. Purpose

This document defines the initial SymVerse V3 transaction specification direction.

It focuses on:

- Transaction domain separation
- Legacy and PQC authorization coexistence
- Signature payload placement
- Membership transaction relevance
- Future alignment with CAD

---

# 2. Design Goals

A V3 transaction model SHOULD provide:

1. Support for existing transaction behavior where required
2. Extension points for PQC signature authorization
3. Deterministic validation across nodes
4. Clean separation between transaction content and authorization witness
5. Compatibility with CAD-based authorization commitments

---

# 3. Transaction Families

V3 documentation recognizes several transaction categories:

| Family | Purpose |
|---|---|
| Value transfer | Standard balance movement |
| Citizen/account creation | Account or citizen registration flow |
| Membership transaction | Nickname, Referrer, Link operations |
| Future PQC-specific flow | Scheme-specific authorization or migration actions |

---

# 4. Transaction Content

A transaction typically includes:

- Sender
- Recipient
- Nonce
- Value
- Gas-related parameters
- Input/data payload
- Transaction type
- Chain/domain constraints
- Signature or witness fields

The exact serialization order MUST be fixed by implementation and documented when finalized.

---

# 5. Authorization Separation

The specification SHOULD conceptually separate:

## 5.1 Core Transaction Body

The body contains the state-changing intent:

- From
- To
- Value
- Nonce
- Gas
- Type
- Data

## 5.2 Authorization Metadata

Authorization metadata selects or constrains validation:

- Signature scheme identifier
- Algorithm code, where applicable
- Account authorization mode

## 5.3 Witness Payload

Witness data contains the signature material or proof bytes required to authorize the transaction.

---

# 6. Legacy Signature Fields

A legacy transaction path may retain fields such as:

```go
V *big.Int
R *big.Int
S *big.Int
```

These preserve compatibility with existing ECDSA-style transaction authorization.

---

# 7. PQC Signature Extension

A PQC-capable transaction may include a dedicated PQC signature byte field:

```go
PQSig []byte
```

The transaction structure currently tracked by the V3 documentation follows this conceptual shape:

```go
type Transaction struct {
    data txdata
}

type txdata struct {
    // Existing transaction fields omitted

    // Legacy signature fields
    V *big.Int
    R *big.Int
    S *big.Int

    // PQC signature field
    PQSig []byte
}
```

---

# 8. Signing Input

The signing input MUST be deterministic.

A dedicated revision SHOULD specify:

- Which fields are included
- Which fields are excluded
- Whether signature fields are blanked during hash derivation
- How transaction type contributes to signing input
- Domain separation rules

---

# 9. Verification Model

Verification SHOULD proceed according to the sender account's authorization mode.

At a high level:

1. Decode transaction
2. Identify sender/account authorization policy
3. Select verification method
4. Reconstruct signing input
5. Verify legacy or PQC witness
6. Apply transaction if valid

---

# 10. Membership Transaction Consideration

Membership transactions are ordinary consensus-visible transactions with membership-specific semantics.

Potential membership actions include:

- Nickname-related operations
- Referrer assignment
- Link registration

The exact `TxTypeMembership` payload schema SHOULD be documented in a dedicated membership/API document as it stabilizes.

---

# 11. CAD Alignment

Transaction design SHOULD remain compatible with CAD.

CAD-related transaction questions include:

- What transaction content contributes to authorization digest?
- Is witness material excluded from transaction commitment?
- How is CAD tied to block-level roots?
- What equivalent witness scenarios preserve digest invariance?

These are specified conceptually in `cad-spec.md`.

---

# 12. Validation Requirements

A valid V3 transaction SHOULD satisfy:

- Correct transaction type encoding
- Correct nonce rule
- Sufficient balance / fee requirements, where applicable
- Correct sender authorization
- Valid signature or witness field
- Valid membership payload, if membership type
- Deterministic state transition behavior

---

# 13. Compatibility Notes

The specification SHOULD explicitly track:

- Legacy sender behavior
- Legacy receiver behavior
- ECDSA and PQC account interoperability
- RPC submission format expectations
- Raw transaction test matrices

---

# 14. Open Items

1. Final transaction RLP layout
2. Final typed transaction versioning, if any
3. Final hash/signing algorithm input definition
4. Per-algorithm signature field requirements
5. Mixed ECDSA/PQC sender-receiver matrices
6. Membership payload canonical schema

---

# 15. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-14 | Initial transaction specification draft |

# SymVerse V3 Basic Specification

> **Status:** Draft v0.1  
> **Date:** 2026-05-14  
> **Repository:** `symverse-lab/V3`  
> **Document Role:** Initial baseline specification for the SymVerse V3 documentation set

---

# 1. Scope

## 1.1 Purpose

This document establishes the first public baseline for **SymVerse V3**.

V3 documentation covers:

- Quantum-resistant blockchain transaction architecture
- PQC-capable account and signature models
- Consensus Authorization Digest (CAD)
- Membership-aware citizen state processing
- RefCode / Referrer / Link / LinkedBy runtime behavior
- Public API specification direction
- Compatibility and testability principles

This is a **baseline spec**, intended to be expanded into dedicated detailed documents.

---

## 1.2 Specification Goals

SymVerse V3 documentation SHOULD make the following clear:

1. What V3 changes or formalizes
2. Which protocol areas are in scope
3. Which concepts are already implementation-backed
4. Which areas remain architectural or draft-level
5. How detailed sub-specifications should be organized

---

# 2. Design Principles

## 2.1 Cryptographic Adaptability

V3 SHOULD support a blockchain authorization model that can evolve across cryptographic schemes, including post-quantum signature algorithms.

The specification SHOULD avoid assumptions that bind the protocol permanently to one signature representation.

---

## 2.2 Consensus Determinism

Consensus-relevant inputs MUST remain deterministic and reproducible across nodes.

Any V3 authorization model MUST define:

- Deterministic serialization
- Deterministic verification inputs
- Deterministic state transition rules
- Deterministic commitment behavior

---

## 2.3 Implementation Awareness

V3 documentation SHOULD stay aligned with actual SymVerse implementation concerns, including:

- Transaction encoding
- Citizen block processing
- StateDB updates
- Membership runtime policy
- Sync and block-processing behavior
- Public RPC behavior

---

## 2.4 Progressive Specification

V3 documents MAY begin with high-level architecture and later split into:

- Normative specification
- Implementation notes
- Test vectors
- Migration notes
- Research notes

---

# 3. Terminology

| Term | Meaning |
|---|---|
| **V3** | The next-generation SymVerse protocol and documentation family |
| **Citizen** | A SymVerse citizen/account state entity |
| **SymID** | SymVerse account identifier |
| **Nickname** | Membership nickname associated with a citizen |
| **RefCode** | Deterministic referral/membership code |
| **Referrer** | A citizen or code representing referral origin |
| **Link** | Directional membership relationship registered by a transaction |
| **LinkedBy** | Reverse lookup perspective of Link |
| **PQC** | Post-Quantum Cryptography |
| **CAD** | Consensus Authorization Digest |
| **Witness** | Signature or proof payload used for authorization |
| **Authorization Digest** | Consensus-facing digest of authorization validity context |

---

# 4. V3 Documentation Domains

V3 is organized into the following documentation domains:

| Domain | Description |
|---|---|
| **Core Baseline** | Scope, terms, versioning, common principles |
| **PQC Account Model** | Account structure and algorithm identification |
| **Transaction Model** | Transaction body, authorization fields, signature witness |
| **CAD** | Consensus Authorization Digest architecture |
| **Membership Runtime** | Nickname, RefCode, Referrer, Link, LinkedBy |
| **RPC/API** | Public query and submission interfaces |
| **Testing** | Reproducible scenarios and validation procedures |

---

# 5. PQC Account Model

## 5.1 Account Authorization Families

V3 SHOULD distinguish account authorization capabilities, for example:

- Legacy ECDSA-style authorization
- PQC-enabled authorization
- Future hybrid authorization, if later adopted

The protocol documentation MUST make clear:

- How an account identifies its authorization algorithm
- How public-key data is represented or referenced
- How validation rules are selected

---

## 5.2 PQC Metadata

A representative implementation-level concept is:

```go
type QuantumInfo struct {
    QAlgo   *uint16
    QKeyPub []byte
}
```

The specification does not finalize encoding in this document, but it establishes that V3 requires a formally documented PQC account representation.

---

# 6. Transaction Model

## 6.1 Transaction Separation Principle

A V3 transaction SHOULD separate:

1. Core transaction data
2. Authorization metadata
3. Signature or proof witness material

This separation is important for:

- PQC signature integration
- CAD architecture
- Future algorithm migration
- Stable transaction verification semantics

---

## 6.2 Signature Payload Direction

A representative transaction structure may preserve legacy signature fields while adding PQC-specific witness bytes.

Example implementation direction:

```go
type txdata struct {
    // Legacy signature fields
    V *big.Int
    R *big.Int
    S *big.Int

    // PQC signature payload
    PQSig []byte
}
```

Detailed transaction field ordering, encoding, hashing, and signing rules SHOULD be defined in `transaction-spec.md`.

---

# 7. Consensus Authorization Digest (CAD)

## 7.1 Purpose

PQC signature schemes may differ significantly in witness size and format.

CAD is introduced to define a **consensus-facing authorization digest model** that reduces direct protocol dependence on scheme-specific witness layout.

---

## 7.2 Baseline CAD Concept

At the conceptual level:

1. A transaction carries authorization witness data.
2. Witness verification follows the account's authorization scheme.
3. A deterministic authorization digest is derived.
4. Consensus commitments use the digest according to the CAD rules.

---

## 7.3 CAD Specification Requirements

A dedicated CAD specification SHOULD define:

- Digest inputs
- Domain separation
- Relationship with transaction encoding
- Relationship with block commitment structures
- TxRoot / CADRoot interaction, if adopted
- Witness substitution and non-contradiction cases
- Compatibility with multiple PQC algorithms

---

# 8. Membership Runtime

## 8.1 Citizen Creation Policy

Citizen creation MAY initialize deterministic self-owned membership data only.

Citizen creation MAY initialize:

- Nickname ownership
- RefCode

Citizen creation MUST NOT automatically initialize:

- Referrer
- Link
- LinkedBy

Referrer and Link-related relations SHOULD be written only through explicit membership transactions.

---

## 8.2 RefCode Generation

The current design direction uses:

```text
refCode = (citizenBlockNumber << 12) | citizenIndex
```

This supports:

```text
4096 citizens per CitizenBlock
```

The final display format, normalization, and query behavior SHOULD be defined in `membership-spec.md`.

---

## 8.3 Referrer

The Referrer model SHOULD specify:

- Assignment rules
- Duplicate assignment handling
- Self-reference prohibition, if required
- Invalid code rejection
- State persistence behavior

---

## 8.4 Link and LinkedBy

When citizen **A** registers citizen **B** as a Link:

```text
A → B
```

Two query perspectives MUST be supported:

- A's query view shows B in `Link`
- B's query view shows A in `LinkedBy`

This reverse-query expectation is part of the membership runtime behavior.

---

# 9. Public API Direction

V3 public documentation SHOULD define APIs in categories.

## 9.1 Citizen APIs

Examples:

- Query by SymID
- Query by Nickname
- Query citizen membership metadata

## 9.2 Membership APIs

Examples:

- Query RefCode
- Query Referrer
- Query Link list
- Query LinkedBy list

## 9.3 Transaction APIs

Examples:

- Submit legacy transaction
- Submit PQC transaction
- Submit membership transaction

The exact JSON-RPC methods and payload schemas SHOULD be maintained in `rpc-api-spec.md`.

---

# 10. Compatibility and Migration

V3 SHOULD explicitly document compatibility boundaries for:

- Existing SymVerse account behavior
- Legacy transaction submission
- Existing RPC clients
- Node upgrade processes
- Sync behavior after protocol feature introduction
- Storage compatibility and encoding changes

Migration documents MAY be separated later if the topic grows.

---

# 11. Testing Baseline

The documentation set SHOULD eventually include reproducible tests for:

1. ECDSA account creation
2. PQC account creation
3. Funding flows
4. Raw transaction submission
5. Nickname assignment
6. RefCode creation and lookup
7. Referral registration
8. Link registration
9. Reverse LinkedBy query
10. Node restart persistence
11. Sync correctness
12. CAD-related digest stability

---

# 12. Planned Document Split

This basic specification SHOULD later be expanded through dedicated documents:

| File | Main Scope |
|---|---|
| `transaction-spec.md` | Transaction structure, signing, encoding |
| `pqc-account-spec.md` | PQC account state and validation |
| `cad-spec.md` | CAD architecture and digest rules |
| `membership-spec.md` | Runtime membership policy |
| `rpc-api-spec.md` | Public JSON-RPC specification |
| `testing-guide.md` | End-to-end validation scenarios |

---

# 13. Status

This document is:

- A **docs-repository baseline**
- A **draft-level specification**
- A **starting point for detailed V3 technical documentation**

It is not yet a final frozen protocol standard.

---

# 14. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-14 | Initial V3 docs baseline specification |

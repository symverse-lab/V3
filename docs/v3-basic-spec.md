# SymVerse V3 Basic Specification

> **Status:** Draft v0.2  
> **Date:** 2026-05-15  
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
- **Citizen Protocol** and citizen-state processing
- CitizenBlock-confirmed Nickname / RefCode bootstrap behavior
- Referrer / Link / LinkedBy relationship runtime behavior
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
- CitizenBlock processing
- Citizen Protocol initial-state application
- StateDB updates
- Citizen relationship runtime policy
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
| **Citizen** | Protocol-level SymVerse identity object |
| **Citizen Protocol** | Rules for Citizen creation, confirmation, initial state, relationship state, and public query behavior |
| **CitizenBlock** | Block structure that confirms Citizen records |
| **SymID** | SymVerse account/identity identifier |
| **Nickname** | Citizen-owned public identifier |
| **RefCode** | Deterministic Citizen owner code |
| **Referrer** | Explicit Citizen relationship state |
| **Link** | Directional Citizen relationship registered by transaction |
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
| **Citizen Protocol** | Citizen lifecycle, CitizenBlock confirmation, Nickname, RefCode, Referrer, Link, LinkedBy |
| **RPC/API** | Public query and submission interfaces |
| **Testing** | Reproducible scenarios and validation procedures |

---

# 5. Citizen Protocol Baseline

The Citizen Protocol specification is maintained in:

```text
docs/citizen-protocol-spec.md
```

The Citizen Protocol formalizes:

- Citizen creation versus account creation
- CitizenBlock confirmation as the canonical Citizen activation boundary
- Initial Nickname ownership registration
- Deterministic RefCode generation
- Explicit Referrer and Link relationship transactions
- LinkedBy reverse-query expectations
- Citizen-centric public query behavior
- Restart and sync consistency

---

# 6. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-14 | Initial V3 docs baseline specification |
| v0.2 | 2026-05-15 | Replaced membership-centric framing with Citizen Protocol framing and linked the dedicated Citizen Protocol Specification |

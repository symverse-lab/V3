# SymVerse V3 Docs

<div align="center">

**Official documentation workspace for SymVerse V3**  
_Protocol concepts, baseline specifications, architecture notes, and implementation references._

</div>

---

## Purpose

This repository is the **documentation hub** for **SymVerse V3**.

It is intended to organize and publish:

- V3 baseline specifications
- Quantum-resistant blockchain architecture
- PQC account and transaction models
- Consensus Authorization Digest (CAD)
- Membership and citizen-state rules
- Public RPC/API references
- Test scenarios and implementation notes

`V3` is treated as a **docs-first repository**, not as a node source-code repository.

---

## Documentation Status

| Area | Status |
|---|---|
| Repository landing page | Initial draft |
| V3 basic specification | Draft v0.1 |
| Transaction specification | Planned |
| PQC account specification | Planned |
| CAD specification | Planned |
| Membership specification | Planned |
| RPC/API reference | Planned |
| Test guide | Planned |

---

## Start Here

### 1. Basic Specification

The first baseline document is:

- [`docs/v3-basic-spec.md`](./docs/v3-basic-spec.md)

This document defines the initial V3 scope, terminology, architecture domains, and the first public protocol-level baseline.

---

## Planned Documentation Map

```text
V3/
├── README.md
└── docs/
    ├── v3-basic-spec.md
    ├── transaction-spec.md
    ├── pqc-account-spec.md
    ├── cad-spec.md
    ├── membership-spec.md
    ├── rpc-api-spec.md
    ├── testing-guide.md
    └── changelog.md
```

---

## Documentation Domains

### A. V3 Core Specification

Defines:

- V3 protocol goals
- Terminology
- Compatibility principles
- Specification structure
- Versioning direction

### B. PQC Account and Transaction Model

Defines:

- PQC algorithm identifiers
- Public-key representation
- PQC signature payload handling
- Transaction authorization fields
- Legacy compatibility boundaries

### C. Consensus Authorization Digest (CAD)

Defines:

- CAD motivation
- Digest construction scope
- Relationship between transaction data, witness data, and consensus commitment
- Future-proofing for multiple PQC signature schemes

### D. Membership Runtime

Defines:

- Nickname ownership
- RefCode generation
- Referrer processing
- Link / LinkedBy relationship semantics
- Public query behavior

### E. Public APIs and Test References

Defines:

- Citizen query APIs
- Membership query APIs
- PQC transaction testing
- Referral and Link test scenarios
- Node restart and sync validation references

---

## Current V3 Design Themes

The documentation currently tracks the following major themes:

1. **Quantum-resistant transaction architecture**
2. **PQC-capable account representation**
3. **Consensus Authorization Digest (CAD)**
4. **Membership state formalization**
5. **Compatibility with existing SymVerse flows**
6. **Reproducible protocol and runtime test procedures**

---

## Writing Direction

The V3 docs should aim to be:

- **Specification-oriented** — clear rules, definitions, and boundaries
- **Implementation-aware** — grounded in real node behavior and structures
- **Versioned** — draft status and change history should be explicit
- **Expandable** — large topics should split into dedicated documents over time

---

## Recommended Next Documents

After the basic spec, the most natural next documents are:

1. `membership-spec.md`
2. `transaction-spec.md`
3. `cad-spec.md`

This order matches the current implementation and research flow:

- Membership rules are already becoming concrete in code and test scripts.
- Transaction and PQC structures are the next baseline.
- CAD should then be documented as the consensus-level architecture.

---

## License

Documentation licensing and reuse policy will be specified by SymVerse Lab.

---

<div align="center">

**SymVerse V3 Docs**  
_A documentation-first repository for the next SymVerse protocol generation._

</div>

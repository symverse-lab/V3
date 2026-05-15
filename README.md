# SymVerse V3 Docs

<div align="center">

**Official documentation workspace for SymVerse V3**  
_Protocol concepts, background primers, baseline specifications, architecture notes, and implementation references._

</div>

---

## Purpose

This repository is the **documentation hub** for **SymVerse V3**.

It organizes and publishes:

- Introductory background documents
- V3 baseline specifications
- Quantum-resistant blockchain architecture
- PQC account and transaction models
- Consensus Authorization Digest (CAD)
- Citizen Protocol and citizen-state rules
- Public RPC/API references
- Test scenarios and implementation notes

`V3` is a **docs-first repository**, not a node source-code repository.

---

## Documentation Index

| Document | Role |
|---|---|
| [`docs/pqc-and-blockchain-introduction.md`](./docs/pqc-and-blockchain-introduction.md) | Introductory guide to PQC and blockchain |
| [`docs/v3-basic-spec.md`](./docs/v3-basic-spec.md) | Overall V3 baseline specification |
| [`docs/cad-overview.md`](./docs/cad-overview.md) | Figure-first explanation of CAD |
| [`docs/cad-spec.md`](./docs/cad-spec.md) | Formal CAD specification draft |
| [`docs/citizen-protocol-spec.md`](./docs/citizen-protocol-spec.md) | Citizen lifecycle, CitizenBlock confirmation, Nickname, RefCode, Referrer, Link, LinkedBy |
| [`docs/transaction-spec.md`](./docs/transaction-spec.md) | Transaction model, signing boundaries, signature payloads |
| [`docs/pqc-account-spec.md`](./docs/pqc-account-spec.md) | PQC account representation and authorization metadata |
| [`docs/rpc-api-spec.md`](./docs/rpc-api-spec.md) | Public API documentation baseline |
| [`docs/testing-guide.md`](./docs/testing-guide.md) | End-to-end scenario testing reference |
| [`docs/changelog.md`](./docs/changelog.md) | Documentation revision log |

---

## Recommended Reading Order

1. [`pqc-and-blockchain-introduction.md`](./docs/pqc-and-blockchain-introduction.md)
2. [`v3-basic-spec.md`](./docs/v3-basic-spec.md)
3. [`cad-overview.md`](./docs/cad-overview.md)
4. [`cad-spec.md`](./docs/cad-spec.md)
5. [`pqc-account-spec.md`](./docs/pqc-account-spec.md)
6. [`transaction-spec.md`](./docs/transaction-spec.md)
7. [`citizen-protocol-spec.md`](./docs/citizen-protocol-spec.md)
8. [`rpc-api-spec.md`](./docs/rpc-api-spec.md)
9. [`testing-guide.md`](./docs/testing-guide.md)

---

## Documentation Status

| Area | Status |
|---|---|
| PQC and blockchain introduction | Draft |
| V3 basic spec | Draft v0.1 |
| CAD overview | Draft v0.1 |
| CAD spec | Draft v0.3 |
| Citizen Protocol spec | Draft v0.1 |
| Transaction spec | Draft v0.1 |
| PQC account spec | Draft v0.1 |
| RPC/API spec | Draft v0.1 |
| Testing guide | Draft v0.1 |
| Changelog | Updated |

---

## Documentation Structure

```text
V3/
├── README.md
└── docs/
    ├── pqc-and-blockchain-introduction.md
    ├── v3-basic-spec.md
    ├── cad-overview.md
    ├── cad-spec.md
    ├── citizen-protocol-spec.md
    ├── transaction-spec.md
    ├── pqc-account-spec.md
    ├── rpc-api-spec.md
    ├── testing-guide.md
    └── changelog.md
```

---

## Citizen Protocol Direction

The Citizen Protocol defines:

- Citizen creation and confirmation as first-class protocol concerns
- Nickname and RefCode as Citizen-owned initial state
- Referrer and Link/LinkedBy as explicit Citizen relationship states
- Citizen queries as an integrated public view of identity, PQC metadata, and relationship state

---

## Current V3 Themes

The documentation tracks these major themes:

1. **Post-quantum cryptography and blockchain motivation**
2. **Quantum-resistant transaction architecture**
3. **Consensus Authorization Digest (CAD)**
4. **PQC-capable account representation**
5. **Citizen Protocol and citizen-state formalization**
6. **Compatibility with existing SymVerse flows**
7. **Reproducible protocol and runtime tests**

---

## Writing Direction

V3 documents should remain:

- **Accessible where needed** — introductory and overview documents should help non-specialist readers
- **Specification-oriented** — rules, terms, and boundaries are explicit
- **Implementation-aware** — tied to real runtime behavior where applicable
- **Versioned** — draft state and revision history are visible
- **Expandable** — large topics can later split into deeper documents

---

## License

Documentation licensing and reuse policy will be specified by SymVerse Lab.

---

<div align="center">

**SymVerse V3 Docs**  
_A documentation-first repository for the next SymVerse protocol generation._

</div>

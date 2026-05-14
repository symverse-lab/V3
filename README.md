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
- Membership and citizen-state rules
- Public RPC/API references
- Test scenarios and implementation notes

`V3` is a **docs-first repository**, not a node source-code repository.

---

## Documentation Index

| Document | Role |
|---|---|
| [`docs/pqc-and-blockchain-introduction.md`](./docs/pqc-and-blockchain-introduction.md) | Introductory guide to PQC and blockchain |
| [`docs/v3-basic-spec.md`](./docs/v3-basic-spec.md) | Overall V3 baseline specification |
| [`docs/membership-spec.md`](./docs/membership-spec.md) | Nickname, RefCode, Referrer, Link, LinkedBy |
| [`docs/transaction-spec.md`](./docs/transaction-spec.md) | Transaction model, signing boundaries, signature payloads |
| [`docs/pqc-account-spec.md`](./docs/pqc-account-spec.md) | PQC account representation and authorization metadata |
| [`docs/cad-spec.md`](./docs/cad-spec.md) | Consensus Authorization Digest architecture |
| [`docs/rpc-api-spec.md`](./docs/rpc-api-spec.md) | Public API documentation baseline |
| [`docs/testing-guide.md`](./docs/testing-guide.md) | End-to-end scenario testing reference |
| [`docs/changelog.md`](./docs/changelog.md) | Documentation revision log |

---

## Recommended Reading Order

1. [`pqc-and-blockchain-introduction.md`](./docs/pqc-and-blockchain-introduction.md)
2. [`v3-basic-spec.md`](./docs/v3-basic-spec.md)
3. [`pqc-account-spec.md`](./docs/pqc-account-spec.md)
4. [`transaction-spec.md`](./docs/transaction-spec.md)
5. [`cad-spec.md`](./docs/cad-spec.md)
6. [`membership-spec.md`](./docs/membership-spec.md)
7. [`rpc-api-spec.md`](./docs/rpc-api-spec.md)
8. [`testing-guide.md`](./docs/testing-guide.md)

---

## Documentation Status

| Area | Status |
|---|---|
| PQC and blockchain introduction | Draft v0.1 |
| V3 basic spec | Draft v0.1 |
| Membership spec | Draft v0.1 |
| Transaction spec | Draft v0.1 |
| PQC account spec | Draft v0.1 |
| CAD spec | Draft v0.1 |
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
    ├── membership-spec.md
    ├── transaction-spec.md
    ├── pqc-account-spec.md
    ├── cad-spec.md
    ├── rpc-api-spec.md
    ├── testing-guide.md
    └── changelog.md
```

---

## Current V3 Themes

The documentation tracks these major themes:

1. **Post-quantum cryptography and blockchain motivation**
2. **Quantum-resistant transaction architecture**
3. **PQC-capable account representation**
4. **Consensus Authorization Digest (CAD)**
5. **Membership state formalization**
6. **Compatibility with existing SymVerse flows**
7. **Reproducible protocol and runtime tests**

---

## Writing Direction

V3 documents should remain:

- **Accessible where needed** — introductory documents should help non-specialist readers
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

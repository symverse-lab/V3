# SymVerse V3 Docs Changelog

> Documentation-level change log for `symverse-lab/V3`

---

## 2026-05-15

### Added

- `docs/cad-overview.md`
  - figure-first explanation of CAD
  - traditional vs CAD comparison
  - CAD processing flow
  - same TxRoot / different CADRoot concept
  - annual commitment impact table
  - dual-acceptance illustration

### Changed

- `docs/cad-spec.md`
  - refocused as a formal specification document
  - moved figure-heavy explanatory content to `cad-overview.md`
  - simplified around definitions, rules, and V3 integration decisions

- `README.md`
  - added CAD overview to documentation index and reading order

- `docs/changelog.md`
  - updated for CAD overview / spec split

---

## 2026-05-14

### Added

- `README.md`
  - V3 docs repository purpose
  - Documentation index
  - Recommended reading order
  - Status overview

- `docs/pqc-and-blockchain-introduction.md`
  - PQC background primer
  - NIST-standardized PQC families at a high level
  - Why blockchains care about quantum-resistant signatures
  - Connection to V3 account, transaction, and CAD documents

- `docs/v3-basic-spec.md`
  - V3 baseline scope
  - Design principles
  - Documentation domains
  - PQC / CAD / Membership overview

- `docs/membership-spec.md`
  - Nickname
  - RefCode
  - Referrer
  - Link / LinkedBy
  - Restart and sync expectations

- `docs/transaction-spec.md`
  - Transaction model direction
  - Signature payload separation
  - Legacy and PQC signature coexistence
  - CAD alignment notes

- `docs/pqc-account-spec.md`
  - PQC account metadata
  - `QAlgo`
  - `QKeyPub`
  - Verification coupling

- `docs/cad-spec.md`
  - CAD architecture motivation
  - digest role
  - CADRoot / TxRoot discussion areas
  - non-contradiction requirements

- `docs/rpc-api-spec.md`
  - Citizen APIs
  - Membership query APIs
  - Transaction submission reference structure

- `docs/testing-guide.md`
  - Account creation matrix
  - Membership tests
  - Link / LinkedBy tests
  - Restart and sync validation

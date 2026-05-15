# SymVerse V3 CAD Specification

> **Status:** Draft v0.3  
> **Date:** 2026-05-15  
> **Primary Reference:** *Consensus Authorization Digest (CAD) Enables Quantum-Resistant Blockchains for Any PQC Signature*  
> **Role:** Formal specification draft for the CAD model used by SymVerse V3

---

# 1. Scope

This document defines the **formal specification direction** for CAD in SymVerse V3.

If you want the intuitive and figure-first explanation, read:

- [`cad-overview.md`](./cad-overview.md)

This document focuses on:

- formal CAD objects,
- canonicalization requirements,
- block assembly rules,
- block validation rules,
- integration points for SymVerse V3.

---

# 2. Core Definitions

## 2.1 Authorization Projection

For an admitted transaction `tx`, define:

```text
Auth(tx)
```

`Auth(tx)` contains the authorization-relevant content selected by protocol rules and **must exclude raw signature bytes**.

---

## 2.2 Canonicalized Authorization Form

Define the canonical form:

```text
Auth*(tx) = Canonicalize(Auth(tx))
```

The canonicalization procedure MUST define:

- field order,
- field type,
- empty/nil encoding,
- serialization determinism.

---

## 2.3 Per-Transaction CAD

Define:

```text
CAD(tx) = H(Encode(Auth*(tx)))
```

The baseline hash choice follows the CAD paper direction:

```text
H = SHA3-256
```

Therefore:

```text
|CAD(tx)| = 32 bytes
```

---

## 2.4 Ordered CAD List

For a block `B` with transactions in block order:

```text
CADList(B) = [CAD(tx_0), CAD(tx_1), ..., CAD(tx_n-1)]
```

The transaction order MUST be preserved.

---

## 2.5 CADRoot

Define:

```text
CADRoot(B) = H(Encode([
  (0, CAD(tx_0)),
  (1, CAD(tx_1)),
  ...
  (n-1, CAD(tx_n-1))
]))
```

The current direction follows the paper’s **indexed ordered list + single SHA3-256 hash** approach.

Therefore:

```text
|CADRoot(B)| = 32 bytes
```

---

# 3. Signature Exclusion Rule

Raw signature or witness material MUST NOT be part of:

- `Auth(tx)`
- `CAD(tx)`
- `CADRoot(B)` input encoding

This is the defining property of the CAD commitment path.

---

# 4. Admission Rule

A transaction may enter block construction only if:

1. authorization verification succeeds, and
2. normal protocol rule checks succeed.

Conceptually:

```text
Admit(tx) = 1
iff
VerifySig(tx) = 1 ∧ RuleCheck(tx) = 1
```

Only admitted transactions contribute to CAD and CADRoot.

---

# 5. Block Assembly Rule

For each admitted transaction in block order:

1. compute `Auth(tx)`
2. compute `Auth*(tx)`
3. compute `CAD(tx)`
4. append `CAD(tx)` to `CADList(B)`

After processing all transactions:

5. compute `CADRoot(B)`
6. place `CADRoot(B)` in the block header

---

# 6. Validation Rule

A validator MUST:

1. recompute `CAD(tx)` for every transaction in block order
2. reject the block if any recomputed CAD differs from the included CAD
3. recompute `CADRoot(B)` from the ordered CAD list
4. reject the block if the recomputed CADRoot differs from the header value

---

# 7. Why CADRoot Is Separate

`CADRoot` is a distinct commitment and MUST NOT be assumed derivable from:

- `TxRoot`
- `StateRoot`
- or `TxRoot + StateRoot`

The paper’s guiding scenario is:

```text
Same TxRoot, Different CADRoot
```

Therefore, `CADRoot` must be represented explicitly if CAD is adopted.

---

# 8. Dual-Acceptance Direction

CAD is compatible with a migration stage in which:

- legacy ECDSA authorization,
- PQC authorization,

both coexist.

Scheme-specific verification remains in the admission layer.  
Consensus commitment remains CAD/CADRoot-based.

---

# 9. SymVerse V3 Open Decisions

The following chain-specific items still need to be fixed:

1. exact field set of `Auth(tx)`
2. exact deterministic encoding rule
3. where `CAD` is stored in the transaction structure
4. whether all transaction types carry `CAD`
5. exact `CADRoot` header field layout
6. signature-retention policy after admission
7. RPC exposure of CAD/CADRoot
8. sync and import validation semantics

---

# 10. Minimum Test Cases

A V3 implementation should test at least:

1. deterministic `CAD(tx)` recomputation
2. deterministic `CADRoot` recomputation
3. rejection when `tx.CAD` is tampered with
4. rejection when `CADRoot` is tampered with
5. same block order → same CADRoot
6. different ordered CAD list → different CADRoot
7. coexistence of ECDSA and PQC admission paths
8. restart and re-import stability

---

# 11. Relationship to Other Documents

| Document | Relationship |
|---|---|
| `cad-overview.md` | Visual explanation and summary tables |
| `transaction-spec.md` | Determines which fields may feed `Auth(tx)` |
| `pqc-account-spec.md` | Defines authorization modes used by admission verification |
| `rpc-api-spec.md` | May expose CAD/CADRoot fields |
| `testing-guide.md` | Defines CAD-related validation scenarios |

---

# 12. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-14 | Initial short draft |
| v0.2 | 2026-05-15 | Expanded from the CAD paper into a long-form explanation |
| v0.3 | 2026-05-15 | Refactored into a formal-spec role; overview and visual content moved to `cad-overview.md` |

# SymVerse V3 CAD Specification

> **Status:** Draft v0.1  
> **Date:** 2026-05-14  
> **Scope:** Consensus Authorization Digest architecture baseline

---

# 1. Purpose

This document defines the first public architecture draft for **Consensus Authorization Digest (CAD)** in SymVerse V3.

CAD is designed to support:

- Quantum-resistant authorization models
- Signature-scheme flexibility
- Consensus-visible authorization commitments
- Reduced coupling between block commitments and bulky witness formats

---

# 2. Motivation

Post-quantum signatures may:

- Be significantly larger than legacy signatures
- Differ widely in structure and encoding
- Evolve as standards and implementations change

A blockchain that ties consensus commitments too directly to scheme-specific witness layout may face:

- Reduced upgrade flexibility
- Larger consensus-path dependence
- More difficult migration across signature algorithms

CAD addresses this by introducing a **consensus authorization digest layer**.

---

# 3. Basic Concept

At a conceptual level:

```text
Transaction Body + Authorization Context + Verified Witness
                         │
                         ▼
              Authorization Digest
                         │
                         ▼
              Consensus Commitment
```

The digest is intended to represent validated authorization meaning in a deterministic form.

---

# 4. CAD Design Goals

CAD SHOULD provide:

1. Deterministic derivation
2. Stable consensus commitment behavior
3. Compatibility with multiple signature schemes
4. Clear transaction-to-authorization mapping
5. Freedom from direct dependence on raw witness layout where possible
6. Support for migration and future cryptographic agility

---

# 5. Key Terms

| Term | Meaning |
|---|---|
| **Witness** | Signature/proof bytes carried by or associated with a transaction |
| **Authorization Context** | Inputs required to interpret validity of the witness |
| **Authorization Digest** | Deterministic digest reflecting verified authorization meaning |
| **CADRoot** | Candidate block-level commitment over authorization digests |
| **TxRoot** | Existing transaction-root commitment |

---

# 6. CAD Flow

A candidate conceptual validation flow:

1. Decode transaction
2. Identify account authorization mode
3. Verify witness using the correct scheme
4. Build canonical authorization context
5. Compute authorization digest
6. Commit digest according to block-level CAD rules

---

# 7. Digest Input Requirements

The final CAD digest input SHOULD be:

- Deterministic
- Domain-separated
- Sufficient to bind authorization to transaction intent
- Unambiguous across signature schemes

Potential input categories include:

- Transaction intent hash
- Sender/account identity
- Authorization scheme identifier
- Chain or protocol domain tag
- Additional context required to prevent cross-domain reuse

The final exact input tuple is not frozen in this draft.

---

# 8. Relationship to Transaction Root

A central V3 research/specification topic is the relationship between:

- `TxRoot`
- `CADRoot`

The CAD documentation SHOULD make explicit:

1. Whether witness material remains in transaction commitment
2. Whether CAD commitments are supplemental or structural
3. How differing witnesses for identical transaction intent are treated
4. How consensus non-contradiction is maintained

---

# 9. Same TxRoot, Different CADRoot Scenario

A future detailed CAD spec SHOULD include the scenario:

```text
Same transaction intent / same TxRoot relation
but different authorization witness interpretation
leading to a different CAD commitment
```

This scenario is useful for documenting:

- Witness sensitivity
- Authorization digest necessity
- Consensus non-contradiction reasoning

---

# 10. Non-Contradiction Requirement

CAD MUST NOT introduce ambiguous validity.

For a block to be accepted, all validating nodes MUST derive the same authorization digest results from the same canonical inputs and valid witness interpretation.

A future theorem-style section SHOULD formalize the non-contradiction requirement.

---

# 11. Scheme Generality

CAD is intended to remain compatible with:

- ML-DSA family members
- Other PQC signature families
- Legacy authorization schemes where applicable
- Future hybrid/migration authorization models

The digest architecture SHOULD be signature-scheme-aware but not scheme-dependent in a brittle way.

---

# 12. Block-Level Considerations

A future detailed CAD specification SHOULD decide:

- Whether each transaction has one CAD digest
- Whether CAD digests are Merkle-committed
- Whether the header includes `CADRoot`
- How receipts, state execution, and block validation interact with CAD
- Whether CAD is activated at genesis or through upgrade boundary

---

# 13. Compatibility Considerations

CAD design SHOULD consider:

- Legacy chain structures
- Existing transaction validation flow
- P2P propagation behavior
- Synchronization and re-execution
- Light-client or proof implications, if applicable

---

# 14. Open Items

1. Final CAD digest formula
2. `CADRoot` commitment design
3. Relation to `TxRoot`
4. Witness substitution rules
5. Block header extension, if any
6. Activation / migration policy
7. Formal non-contradiction argument

---

# 15. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-14 | Initial CAD specification draft |

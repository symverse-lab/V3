# SymVerse V3 CAD Specification

> **Status:** Draft v0.2  
> **Date:** 2026-05-15  
> **Primary Reference:**  
> Hyug Jun Ko and Soo Hyuk Choi,  
> *Consensus Authorization Digest (CAD) Enables Quantum-Resistant Blockchains for Any PQC Signature*,  
> Research Square Preprint, Version 1, 2026.  
> DOI: `10.21203/rs.3.rs-8890873/v1`
>
> **Document Role:**  
> This document translates the core architecture, formal model, validation flow, security assumptions, and cost-accounting implications of the CAD paper into the SymVerse V3 documentation set.
>
> **Important Note:**  
> The referenced paper is a preprint under review. This specification is therefore an architectural and implementation-oriented draft, not a finalized protocol standard.

---

# 1. Purpose

The purpose of **Consensus Authorization Digest (CAD)** is to redesign the object that blockchain consensus permanently commits when transaction authorization transitions from compact classical signatures to larger post-quantum signatures.

A naïve post-quantum migration strategy replaces ECDSA or Schnorr signatures with PQC signatures while leaving the consensus-committed transaction representation unchanged. Under that model:

- larger PQC signatures remain inside transaction bodies,
- transaction roots and block data inherit that size growth,
- every validator must propagate and retain the larger committed bytes,
- network and storage burdens grow linearly with signature size and transaction volume.

The CAD model addresses this by separating:

1. **Admission-time authorization verification**, and
2. **Consensus-time commitment of authorization outcomes**.

In CAD-based designs:

- raw signature material is used during transaction admission,
- signature material is removed from the consensus commitment path,
- a fixed-size per-transaction digest `tx.CAD` is committed instead,
- a fixed-size per-block `CADRoot` is included in the block header.

This creates a **signature-size-independent consensus commitment interface**.

---

# 2. Problem Statement

## 2.1 Signature-Committing Consensus

Many blockchain transaction formats treat authorization evidence as part of the consensus-committed transaction object.

Examples of authorization evidence include:

- ECDSA signatures,
- Schnorr signatures,
- PQC signatures,
- permissioned-chain endorsements,
- other admission witnesses.

We call this model:

```text
Signature-Committing Consensus
```

Under this model, the object committed by consensus contains both:

1. execution-relevant transaction content, and
2. the raw evidence proving transaction authorization.

This model was less problematic when signatures were relatively small.  
It becomes structurally costly when post-quantum signatures are introduced.

---

## 2.2 Structural Cost Under PQC Migration

Let:

- `|Ev(tx)|` denote the size of admission evidence,
- `N` denote transaction volume,
- `CommitBytes` denote globally replicated consensus-committed bytes.

If evidence remains in the consensus object, then:

```text
CommitBytes ∝ N × |Ev(tx)|
```

When PQC signatures replace ECDSA-style signatures:

```text
|Ev(tx)| increases substantially
```

Therefore:

```text
Consensus-committed bytes,
network propagation burden,
and long-term replicated storage
all increase linearly.
```

The paper’s core claim is that this is a **structural consequence of what consensus commits**, not merely an artifact of slow cryptographic verification or poor implementation.

---

# 3. Design Thesis

The CAD paper reframes post-quantum blockchain migration as a **consensus commitment design problem**.

Instead of asking only:

```text
Which PQC signature scheme should replace ECDSA?
```

the protocol must also ask:

```text
What should consensus permanently commit after authorization has already been verified?
```

CAD answers:

```text
Consensus should commit deterministic authorization outcomes,
not raw signature bytes.
```

---

# 4. Core CAD Architecture

## 4.1 Validity–Consensus Separation

CAD is built on **validity–consensus separation**:

| Layer | Responsibility |
|---|---|
| Execution / admission layer | Verify signatures and decide whether a transaction is admissible |
| Consensus commitment layer | Commit a deterministic, fixed-size summary of the admitted authorization outcome |

In other words:

```text
Signature verification remains mandatory,
but raw signature bytes do not remain in the consensus-committed object.
```

---

## 4.2 CAD Components

CAD introduces two commitment objects.

### 4.2.1 Per-Transaction CAD

Each admitted transaction includes:

```text
tx.CAD
```

where:

```text
tx.CAD = fixed-size digest of canonical authorization-relevant data
```

The paper instantiates `CAD` as a 32-byte SHA3-256 digest.

---

### 4.2.2 Per-Block CADRoot

Each block header includes:

```text
CADRoot
```

where:

```text
CADRoot = fixed-size commitment over the ordered list of tx.CAD values
```

The paper defines `CADRoot` as:

- a **non-Merkle**,
- **single-hash**,
- **SHA3-256** commitment
- over a deterministic encoding of the indexed ordered CAD list.

---

# 5. Formal Requirements

The CAD commitment path must satisfy three foundational constraints.

## 5.1 Signature Exclusion

Raw signature material MUST NOT be part of the input to:

```text
CAD(tx)
CADRoot(B)
```

Formally, the paper expresses:

```text
Sig(tx) ∉ Input(CAD(tx))
Sig(tx) ∉ Input(CADRoot(B))
```

This requirement is central.  
Without it, the consensus object remains signature-size-dependent.

---

## 5.2 Determinism

For the same transaction sequence and the same protocol rules:

- all honest validators must derive the same `CAD(tx)`,
- all honest validators must derive the same `CADRoot(B)`.

In conceptual form:

```text
CADRoot_i(B) = CADRoot_j(B)
for all honest validators i, j
```

---

## 5.3 Fixed-Size Commitment

The commitment size MUST NOT grow with:

- signature scheme,
- signature byte length,
- witness encoding size.

The paper’s implementation choice is:

```text
|CAD(tx)|     = 32 bytes
|CADRoot(B)|  = 32 bytes
```

---

# 6. Why TxRoot Is Insufficient

## 6.1 The Dilemma

The paper argues that `TxRoot` cannot replace `CADRoot`.

There are two possibilities:

### Case A — TxRoot Includes Signatures

If `TxRoot` is computed over transaction representations that retain signatures:

- consensus remains signature-size-dependent,
- PQC signature growth remains permanently committed,
- the structural scalability problem is not solved.

### Case B — TxRoot Excludes Signatures

If `TxRoot` is computed over transaction representations that exclude signatures:

- `TxRoot` no longer binds the authorization-outcome semantics that determine transaction validity,
- two blocks may share the same transaction payload commitment while differing in their authorization summaries.

---

## 6.2 Same TxRoot, Different CADRoot

The paper highlights a critical scenario:

```text
Block A and Block B contain:
- identical transaction payloads,
- identical transaction order,
- same TxRoot,

but they may still have:
- different valid admission-evidence instances,
- different per-transaction authorization summaries,
- different CADRoot values.
```

Therefore:

```text
CADRoot cannot be derived from TxRoot.
```

The same reasoning rules out deriving CADRoot from:

- `StateRoot`,
- or any combination of `TxRoot` and `StateRoot`.

`CADRoot` must be committed independently.

---

# 7. Formal CAD Derivation

This section follows the deterministic construction from the paper.

## 7.1 Hash Function

The paper instantiates the hash function as:

```text
H = SHA3-256
```

with:

```text
|H(x)| = 32 bytes
```

Thus:

```text
|CAD(tx)| = 32 bytes
|CADRoot(B)| = 32 bytes
```

---

## 7.2 Authorization Projection

For each admitted transaction `tx`, define an authorization projection:

```text
Auth(tx)
```

`Auth(tx)` includes the minimal authorization-relevant information needed to evaluate admission rules, while excluding signature bytes.

Examples of authorization-relevant information may include:

- transaction intent or transaction identity inputs,
- sender/account authorization context,
- declared authorization scheme,
- protocol-specific admission fields.

The final SymVerse field list remains to be fixed in the implementation specification.

---

## 7.3 Canonicalization

To avoid implementation drift, the authorization projection is canonicalized:

```text
Auth*(tx) = Canonicalize(Auth(tx))
```

Canonicalization fixes:

- field order,
- field type,
- length rules,
- serialization stability.

The purpose is to ensure that all honest validators derive identical authorization bytes from the same admitted transaction.

---

## 7.4 Deterministic Authorization Encoding

For transaction index `i` in block order:

```text
Enc_i = Enc_tx(Auth*(tx_i))
```

The paper allows this to be instantiated as deterministic RLP encoding over the canonical authorization field sequence.

---

## 7.5 Per-Transaction CAD

The per-transaction digest is:

```text
CAD(tx_i) = H(Enc_i)
```

With SHA3-256:

```text
CAD(tx_i) is always 32 bytes.
```

---

## 7.6 Ordered CAD List

For a block `B` with transactions in block order:

```text
CADList(B) = [
  CAD(tx_0),
  CAD(tx_1),
  ...,
  CAD(tx_n-1)
]
```

The order of transactions is consensus-relevant and must be preserved.

---

## 7.7 CADRoot Encoding

The paper commits to an indexed ordered list:

```text
[
  (0, CAD(tx_0)),
  (1, CAD(tx_1)),
  ...,
  (n-1, CAD(tx_n-1))
]
```

The encoded block-level authorization commitment is:

```text
Enc(B) = RLP([
  (0, CAD(tx_0)),
  (1, CAD(tx_1)),
  ...,
  (n-1, CAD(tx_n-1))
])
```

---

## 7.8 CADRoot

The block-level commitment is:

```text
CADRoot(B) = H(Enc(B))
```

The paper explicitly treats `CADRoot` as:

```text
a non-Merkle single-hash commitment
```

over the deterministic indexed ordered CAD list.

---

# 8. Admission-Time Verification

## 8.1 Signature Verification Predicate

For each transaction:

```text
VerifySig(tx) ∈ {0, 1}
```

The verifier may represent:

- ECDSA validation,
- ML-DSA validation,
- SLH-DSA validation,
- another PQC signature validation rule,
- another protocol-approved authorization rule.

CAD does not prescribe one PQC signature scheme.  
It prescribes how consensus should avoid being structurally coupled to signature-byte size.

---

## 8.2 Admission Rule

A transaction is admitted only when:

```text
VerifySig(tx) = 1
and
RuleCheck(tx) = 1
```

Conceptually:

```text
Admit(tx) = 1
iff
VerifySig(tx) = 1 ∧ RuleCheck(tx) = 1
```

Only admitted transactions enter block construction and contribute to CAD/CADRoot.

---

## 8.3 Discardability of Signature Bytes

After admission:

```text
signature bytes are discardable from the consensus commitment path.
```

They are not inputs to:

```text
CAD(tx)
CADRoot(B)
BlockValidation commitment equality checks
```

This does **not** mean signatures are irrelevant.  
It means signatures are used before consensus commitment is finalized.

---

# 9. Insertion Rules

## 9.1 Transaction Body

For each admitted transaction:

```text
tx.CAD ← CAD(tx)
```

The transaction body retained in the block keeps:

- consensus-relevant transaction content,
- the fixed-size `tx.CAD` commitment,

but not the raw signature material, under the CAD-based body representation.

---

## 9.2 Block Header

The block header includes:

```text
CADRoot
```

Conceptually:

```go
type Header struct {
    // existing header fields
    CADRoot []byte // 32-byte authorization commitment
}
```

---

# 10. Block Assembly Pipeline

The paper’s Geth-compatible model can be summarized as follows.

```text
for each admitted transaction in block order:
    1. verify its signature during admission
    2. derive canonical authorization material
    3. compute tx.CAD
    4. append tx.CAD to CADList
    5. remove raw signature fields from the block-committed body
```

Then:

```text
header.CADRoot = ComputeCADRoot(CADList)
```

Representative pseudocode:

```text
CADList = []

for tx in transactions_in_block_order:
    auth_star = Canonicalize(ExtractAuth(tx))
    enc_i = EncodeAuthorization(auth_star)
    tx.CAD = SHA3_256(enc_i)

    CADList.append(tx.CAD)

    // Signature material is not consensus-committed
    tx.signature = nil
    tx.pqc_signature = nil

header.CADRoot = SHA3_256(EncodeIndexedCADList(CADList))
```

---

# 11. Block Validation Rules

A validator checks the proposed block using deterministic recomputation.

## 11.1 Per-Transaction CAD Consistency

For each transaction in block order:

```text
cad' = RecomputeCAD(tx)
```

Validation requires:

```text
cad' = tx.CAD
```

---

## 11.2 Block-Level CADRoot Consistency

The validator recomputes:

```text
expected = RecomputeCADRoot([
  tx_0.CAD,
  tx_1.CAD,
  ...,
  tx_n-1.CAD
])
```

Validation requires:

```text
expected = block.header.CADRoot
```

---

## 11.3 Block Validity

A block is valid if and only if:

1. every per-transaction CAD equality check passes, and
2. the recomputed CADRoot equals the header CADRoot.

Conceptually:

```text
Valid(B) = 1
iff
all tx.CAD checks pass
and
CADRoot check passes
```

---

# 12. Separation From Raw Signature Material During Block Validation

The paper’s model states that raw signature material is not used as an input to the CAD-based block-validation equality path:

```text
Sig(tx) ∉ Input(BlockValidationCommitmentChecks)
```

The signature validity decision is performed at admission.  
Block validation then checks deterministic commitments to those admission outcomes.

---

# 13. Compatibility With Fork Choice and Finality

The paper argues that CADRoot can be integrated as a header-level commitment without changing the structural role of existing fork-choice and finality mechanisms.

The consensus system still operates over:

```text
header-level equality-comparable commitments
```

What changes is the **meaning of one committed field**, not the abstract pattern of header comparison.

The paper’s framing is:

```text
CADRoot changes what is committed,
without changing the consensus protocol’s commitment interface.
```

---

# 14. Dual-Acceptance Transaction Model

## 14.1 Purpose

The paper proposes a **dual-acceptance model** during migration:

- existing ECDSA-style authorization,
- post-quantum authorization,

can coexist in the same execution client pipeline.

---

## 14.2 Execution-Layer Responsibility

The execution layer:

- interprets the declared authorization mode,
- dispatches to the appropriate verifier,
- computes CAD after successful admission.

Examples:

```text
VerifyECDSA(tx)
VerifyPQC(tx)
```

Both feed the same downstream consensus commitment form:

```text
tx.CAD
CADRoot
```

---

## 14.3 Consensus-Layer Independence From Signature Family

Consensus does not need to become signature-scheme-specific.

It checks:

- per-transaction CAD equality,
- block-level CADRoot equality.

Thus:

```text
heterogeneous authorization schemes are confined to admission-time verification.
```

---

# 15. Geth-Compatible Integration Model

The paper describes localized execution-client modifications in three areas.

| Area | CAD Modification |
|---|---|
| Admission path | Verify signature, derive CAD |
| Block assembly path | Retain CAD, remove signature material, compute CADRoot |
| Validation path | Recompute CAD and CADRoot, check equality |

The paper’s implementation reference is a Geth v1.13.x baseline with CAD/CADRoot modifications.

---

# 16. Structural Results From the Paper

## 16.1 Result 1 — Structural Limit of Signature-Committing Consensus

Any design that retains admission evidence in the consensus-committed object exhibits unavoidable linear growth:

```text
committed bytes scale with evidence size × transaction volume
```

This is true regardless of local verification speed.

---

## 16.2 Result 2 — CAD as a Signature-Free Consensus Object

CAD replaces evidence-retaining commitments with:

- fixed-size per-transaction digests,
- fixed-size per-block commitments.

The committed object becomes independent of raw signature size.

---

## 16.3 Result 3 — CADRoot Is Necessary

The paper argues that a sustainable post-quantum commitment cannot be replaced by `TxRoot` or `StateRoot`.

`CADRoot` is necessary because it commits specifically to:

```text
ordered authorization-outcome summaries
```

which other roots do not encode.

---

## 16.4 Result 4 — Correctness and Safety Preservation

Replacing a signature-bearing consensus object with deterministic CAD/CADRoot commitments does not, under the paper’s model, weaken:

- block validity checking,
- consensus safety,
- consensus liveness.

The key condition is that all honest validators:

- apply the same admission rules,
- canonicalize the same authorization material,
- recompute the same CADRoot.

---

## 16.5 Result 5 — Empirical Evidence for Structural Limits

The paper’s implementation-oriented experiments are not framed as throughput benchmarks.  
Instead, they validate that changing only the commitment definition is enough to reproduce the predicted cost divergence:

- evidence-retaining commitments scale with signature size,
- CAD/CADRoot commitments do not.

---

## 16.6 Result 6 — Cost Impact of Post-Quantum Adoption

Using a 2025 Ethereum L1 workload snapshot:

```text
Transactions/year = 580,264,117
Blocks/year       = 2,628,000
```

the paper compares:

- legacy ECDSA + PoS BLS,
- naïve PQC replacement that keeps signatures consensus-committed,
- CAD-based commitment.

### 16.6.1 Annual Commitment Impact

| Design / Scheme | AnnualCommit (GiB/year) | × Legacy |
|---|---:|---:|
| Legacy ECDSA + PoS BLS | 35.38 | 1.00× |
| Naïve ML-DSA-44 | 1308.26 | 36.99× |
| Naïve ML-DSA-65 | 1788.69 | 50.55× |
| Naïve ML-DSA-87 | 2500.96 | 70.68× |
| Naïve SL-DSA-512 | 360.38 | 10.18× |
| Naïve SL-DSA-1024 | 692.19 | 19.57× |
| Naïve SLH-DSA-128s | 4245.95 | 120.02× |
| Naïve SLH-DSA-192s | 8768.13 | 247.79× |
| CAD, any crypto algorithm | 17.60 | 0.50× |

The central point is not that every blockchain will have those exact numbers, but that:

```text
naïve signature-committing adoption remains signature-size-dependent,
whereas CAD commitment is independent of PQ signature size.
```

---

## 16.7 Fixed CADRoot Header Overhead

The paper computes:

```text
Blocks/year × 32 bytes
= 2,628,000 × 32
≈ 80.20 MiB/year
```

This is independent of the PQC signature scheme.

---

# 17. Cross-Chain Interpretation

The paper generalizes its argument beyond one execution model.

The same structural issue can appear in:

- account-based blockchains,
- UTXO-based blockchains,
- permissioned blockchains with endorsements.

If authorization evidence is directly committed to consensus, commitment size grows with:

- signature size,
- input multiplicity,
- endorsement multiplicity.

By contrast, CAD-style commitments aim to commit only:

```text
fixed-size authorization outcomes
```

rather than raw evidence.

---

# 18. Formal Properties of CAD

The paper contrasts legacy signature-committing consensus and CAD-based consensus as follows.

| Dimension | Signature-Committing Consensus | CAD-Based Consensus |
|---|---|---|
| Consensus object | Transaction/ledger representation retains evidence | Signature-free authorization commitments |
| Granularity | Evidence committed in the ledger object | Per-tx CAD + per-block CADRoot |
| Commitment size | Grows with workload and evidence size | Fixed-size per tx and per block |
| Signature-size dependence | Linear | None in the CAD commitment path |
| Cryptographic coupling | Scheme-specific commitment object | Scheme-agnostic commitment interface |
| PQ signature effect | Larger signatures grow committed bytes | PQ evidence size does not grow CAD/CADRoot |
| Network impact | Scales with evidence size | Scales with tx count and block count |
| Storage impact | Scales with workload and evidence size | Scales with CAD count and CADRoot count |
| Consensus semantics | Authorization evidence intertwined with committed object | Authorization abstracted via admitted-outcome commitments |

---

# 19. Threat Model and Security Assumptions

## 19.1 Adversary Capabilities

The paper considers an adversary with:

1. polynomial-time classical computation,
2. long-term access to large-scale quantum computation,
3. full observation and replay of public transactions and blocks,
4. ability to weaken or break classical public-key assumptions such as discrete-log-based signatures.

---

## 19.2 Primary Quantum Threat

The focus is:

```text
quantum attacks on digital signature schemes
```

The paper highlights that:

- ECDSA and Schnorr face Shor-type threats,
- long-term classical signature forgery resistance cannot be assumed indefinitely.

---

## 19.3 Cryptographic Assumptions

The CAD analysis assumes:

1. the hash function used for CAD/CADRoot remains collision-resistant,
2. admission-time signature verification is correctly implemented for the declared signature rule,
3. scheme-specific PQC security details are abstracted behind the correctness of `VerifySig(tx)`.

The CAD paper does not choose a single “best” PQC signature scheme.  
Its purpose is to define a consensus commitment structure that is not dominated by signature byte size.

---

# 20. What CAD Does Not Solve

The paper explicitly keeps several topics out of scope.

CAD does not by itself specify:

- key management,
- wallet migration,
- key rotation,
- governance over admission policies,
- layer-2 interaction,
- quantum networking,
- quantum-communication-based consensus,
- the final choice of PQC signature scheme.

CAD addresses:

```text
how consensus commitments should be structured
once authorization verification has been performed.
```

---

# 21. Implications for SymVerse V3

For SymVerse V3, CAD suggests the following design direction.

## 21.1 Transaction Model

A V3 transaction may include:

- legacy authorization fields during admission,
- PQC signature bytes during admission,
- a committed `CAD` field in the block body.

The stored consensus object should be carefully defined so that the signature commitment path is not permanently retained unless intentionally required by another protocol rule.

---

## 21.2 Block Header

The V3 block header may include:

```text
CADRoot
```

as a 32-byte authorization commitment field.

---

## 21.3 Execution Pipeline

V3 should distinguish:

1. transaction admission,
2. authorization verification,
3. CAD derivation,
4. signature material handling policy,
5. block commitment generation,
6. deterministic validation.

---

## 21.4 Compatibility With Multi-Scheme PQC

CAD aligns with the V3 objective of supporting:

- ML-DSA,
- SLH-DSA,
- future PQC schemes,
- potentially hybrid or migration-stage signature modes,

without forcing the consensus commitment size to track the largest signature representation.

---

# 22. Open Specification Decisions for V3

The paper gives the architecture.  
V3 implementation still needs to settle the chain-specific details below.

## 22.1 Auth(tx) Field Set

Which exact SymVerse fields belong in:

```text
Auth(tx)
```

must be fixed.

Candidate categories include:

- sender identity,
- nonce,
- destination,
- value,
- transaction type,
- chain/domain identifier,
- algorithm identifier,
- membership-operation fields where relevant.

---

## 22.2 Canonical Serialization

The implementation must define:

- exact field order,
- exact types,
- exact nil/empty encoding,
- RLP or alternative deterministic encoding,
- domain-separation tag, if used.

---

## 22.3 CAD Storage Position

The V3 transaction structure must define:

- where `CAD` lives,
- whether it is present in all transaction types,
- how it is exposed through RPC,
- how it interacts with raw-transaction encoding.

---

## 22.4 Signature Retention Policy

The paper’s CAD model removes signature material from the consensus-committed block body after admission.

V3 documentation should explicitly distinguish:

- admission-time temporary signature availability,
- mempool availability,
- block-body retained fields,
- archival/debugging policy, if any.

---

## 22.5 Block Header Integration

The header layout must define:

- `CADRoot` field position,
- hashing inclusion,
- genesis compatibility,
- hard-fork or protocol-version activation conditions.

---

## 22.6 Validation Path

The node implementation must specify:

- recomputation path,
- failure conditions,
- interaction with block import,
- error semantics,
- sync behavior.

---

# 23. Suggested V3 Reference Pseudocode

## 23.1 Admission

```text
function AdmitTransaction(tx):
    if !RuleCheck(tx):
        reject

    if !VerifySig(tx):
        reject

    auth = ExtractAuth(tx)
    auth_star = Canonicalize(auth)
    enc = EncodeAuth(auth_star)

    tx.CAD = SHA3_256(enc)

    accept tx
```

---

## 23.2 Block Assembly

```text
function BuildBlock(transactions):
    cad_list = []

    for tx in transactions in block order:
        cad_list.append(tx.CAD)

        RemoveSignatureMaterialFromCommittedBody(tx)

    header.CADRoot = ComputeCADRoot(cad_list)

    return Block(header, transactions)
```

---

## 23.3 CADRoot

```text
function ComputeCADRoot(cad_list):
    indexed = []

    for i, cad in enumerate(cad_list):
        indexed.append((i, cad))

    enc_block = RLP(indexed)
    return SHA3_256(enc_block)
```

---

## 23.4 Block Validation

```text
function ValidateBlock(block):
    cad_list = []

    for tx in block.transactions in block order:
        auth = ExtractAuth(tx)
        auth_star = Canonicalize(auth)
        enc = EncodeAuth(auth_star)
        expected_cad = SHA3_256(enc)

        if expected_cad != tx.CAD:
            reject block

        cad_list.append(tx.CAD)

    expected_root = ComputeCADRoot(cad_list)

    if expected_root != block.header.CADRoot:
        reject block

    accept block
```

---

# 24. Relationship to Other V3 Documents

| Document | Relationship to CAD |
|---|---|
| `pqc-and-blockchain-introduction.md` | Explains why post-quantum blockchain architecture matters |
| `pqc-account-spec.md` | Defines which authorization modes may feed CAD |
| `transaction-spec.md` | Defines fields that may contribute to `Auth(tx)` and where `CAD` is carried |
| `membership-spec.md` | Membership transactions may require CAD-compatible authorization handling |
| `rpc-api-spec.md` | Should expose CAD/CADRoot query and debugging fields if adopted |
| `testing-guide.md` | Should include CAD derivation, CADRoot equality, and malformed-CAD rejection scenarios |

---

# 25. Minimum CAD Test Requirements for V3

A V3 implementation should eventually provide tests for:

1. deterministic `CAD(tx)` recomputation,
2. deterministic `CADRoot(B)` recomputation,
3. block rejection when `tx.CAD` is altered,
4. block rejection when `CADRoot` is altered,
5. same transaction order yielding same CADRoot,
6. changed CAD list yielding changed CADRoot,
7. same TxRoot with different authorization summaries producing different CADRoot in the documented scenario,
8. ECDSA and PQC transactions coexisting under dual acceptance,
9. signature bytes absent from CAD/CADRoot inputs,
10. stable behavior across restart and block re-import.

---

# 26. Summary

CAD is a protocol architecture for post-quantum blockchain sustainability.

Its key principles are:

```text
1. Verify authorization before consensus commitment.
2. Exclude raw signature bytes from the consensus commitment path.
3. Commit fixed-size per-transaction CAD digests.
4. Commit a fixed-size per-block CADRoot in the header.
5. Preserve deterministic validation through equality checks.
6. Keep the commitment interface independent of signature byte size.
```

In the CAD paper’s framing, this enables quantum-resistant blockchain architecture for **any PQC signature scheme** at the commitment-layer level, because consensus no longer scales with raw signature representation size.

---

# 27. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-14 | Initial short CAD architecture draft |
| v0.2 | 2026-05-15 | Expanded substantially from the CAD Research Square preprint: architecture, formal model, derivation rules, validation pipeline, results, cost-accounting summary, threat model, and V3 implementation decisions |

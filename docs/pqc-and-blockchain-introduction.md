# Post-Quantum Cryptography and Blockchain

> **Status:** Draft v0.5  
> **Date:** 2026-05-15  
> **Document Role:** Background introduction for the SymVerse V3 documentation set  
> **Audience:** Developers, blockchain operators, researchers, and readers new to post-quantum blockchain design

---

# 1. Why This Document Exists

SymVerse V3 is being documented in the context of a broader cryptographic transition.

Modern blockchains rely heavily on **public-key cryptography**, especially **digital signatures**, to prove account ownership and authorize transactions.  
At the same time, the cryptographic community is preparing for a future in which sufficiently capable quantum computers could break many of today’s widely used public-key systems.

This document introduces:

1. What **Post-Quantum Cryptography (PQC)** means
2. Why PQC matters for blockchains
3. Which NIST-standardized PQC algorithms are relevant
4. The **actual byte sizes** of PQC keys and signatures
5. Why those sizes matter for blockchain transaction and consensus design
6. Where **Falcon / FN-DSA** fits in the standardization landscape
7. What **NIST Security Levels** mean
8. How classical **ECDSA/secp256k1** compares with PQC signatures
9. Why SymVerse plans to recommend **ML-DSA-87** after CADFork
10. How these concerns connect to the SymVerse V3 documentation set

This is a **background primer**, not a finalized protocol specification.

---

# 2. What Is Post-Quantum Cryptography?

## 2.1 Definition

**Post-Quantum Cryptography (PQC)** refers to cryptographic algorithms designed to remain secure even against adversaries equipped with large-scale quantum computers.

PQC does **not** mean that the algorithms run on quantum computers.  
Instead, they are classical cryptographic algorithms intended to resist attacks that quantum computers may make practical in the future.

---

## 2.2 Why a Transition Is Needed

Many widely deployed public-key cryptographic systems depend on mathematical problems that are believed to be vulnerable to large-scale quantum attacks.

The transition is being driven by the need to protect:

- Long-lived confidential data
- Authentication systems
- Digital signatures
- Public-key infrastructure
- Software update trust chains
- Blockchain account authorization models

NIST finalized its first three post-quantum cryptography standards in August 2024:

| Standard | Algorithm | Primary Role | Status |
|---|---|---|---|
| **FIPS 203** | **ML-KEM** | Key encapsulation / shared-secret establishment | Final |
| **FIPS 204** | **ML-DSA** | Digital signatures | Final |
| **FIPS 205** | **SLH-DSA** | Stateless hash-based digital signatures | Final |
| **FIPS 206** | **FN-DSA** — based on **Falcon** | Compact lattice-based digital signatures | In development / not yet finalized |

---

# 3. NIST PQC Algorithms Relevant to Blockchain

For blockchain protocol design, the most immediate concern is **digital signatures**, because signatures authorize transactions and prove control of accounts.

Therefore:

- **ML-DSA** and **SLH-DSA** are directly relevant to transaction authorization.
- **Falcon / FN-DSA** is also highly relevant to blockchain because it offers notably compact signatures and public keys, but its NIST standardization as **FIPS 206** is still in development rather than final.
- **ML-KEM** is important in the broader PQC ecosystem, especially for secure communication and key establishment, but it is **not** a transaction-signature algorithm.

---

# 4. ECDSA Baseline for Blockchain Comparison

## 4.1 What Is ECDSA?

**ECDSA** — Elliptic Curve Digital Signature Algorithm — is a classical public-key signature algorithm standardized in the NIST Digital Signature Standard.

Many blockchain systems adopted ECDSA before the post-quantum transition became a practical protocol-design concern.  
In Ethereum-compatible transaction models, ECDSA over the `secp256k1` curve is a familiar baseline for comparing key and signature sizes.

ECDSA is **not post-quantum secure**.  
It is included here because it provides the size baseline that makes PQC signature growth easier to understand.

---

## 4.2 secp256k1 Blockchain Baseline

The `secp256k1` curve is a 256-bit elliptic curve.  
In Ethereum’s signing model:

- the private key is represented as **32 bytes**,
- the public key is represented as **64 bytes** in raw `x || y` form,
- the recoverable transaction signature is represented by:
  - `v`: 1 byte recovery identifier,
  - `r`: 32 bytes,
  - `s`: 32 bytes.

Thus:

```text
Ethereum-style recoverable ECDSA signature = 65 bytes
```

---

## 4.3 ECDSA/secp256k1 Size Table

| Item | Size | Notes |
|---|---:|---|
| **Private key** | 32 bytes | 256-bit scalar |
| **Public key, raw form** | 64 bytes | `x || y`, as used in the Ethereum Yellow Paper representation |
| **Public key, SEC1 compressed encoding** | 33 bytes | 1-byte prefix + 32-byte x-coordinate |
| **Public key, SEC1 uncompressed encoding** | 65 bytes | 1-byte prefix + 32-byte x-coordinate + 32-byte y-coordinate |
| **Signature, raw `(r,s)` pair** | 64 bytes | Two 32-byte scalar values |
| **Ethereum-style recoverable signature `(v,r,s)`** | 65 bytes | 1-byte recovery identifier + 64-byte `(r,s)` |

### Practical interpretation

ECDSA signatures used in legacy blockchain transaction flows are compact:

```text
~65 bytes
```

This is the baseline against which PQC signatures should be compared.

When a PQC signature is:

- 666 bytes,
- 2,420 bytes,
- 4,627 bytes,
- 7,856 bytes,
- or even 49,856 bytes,

the protocol-level impact is no longer a small serialization detail.  
It can materially affect:

- transaction propagation,
- mempool pressure,
- block payload size,
- long-term ledger storage,
- and consensus commitment design.

---

# 5. ML-DSA Specification Snapshot

## 5.1 What Is ML-DSA?

**ML-DSA** is NIST’s module-lattice-based post-quantum digital signature standard.

It provides three approved parameter sets:

- `ML-DSA-44`
- `ML-DSA-65`
- `ML-DSA-87`

These parameter sets trade off:

- security category,
- public-key size,
- private-key size,
- signature size.

---

## 5.2 ML-DSA Key and Signature Sizes

| Parameter Set | NIST Security Level | Private Key | Public Key | Signature |
|---|---:|---:|---:|---:|
| **ML-DSA-44** | Level 2 | 2,560 bytes | 1,312 bytes | 2,420 bytes |
| **ML-DSA-65** | Level 3 | 4,032 bytes | 1,952 bytes | 3,309 bytes |
| **ML-DSA-87** | Level 5 | 4,896 bytes | 2,592 bytes | 4,627 bytes |

### Practical interpretation

Even the smallest standardized ML-DSA signature is measured in **kilobytes**, not tens of bytes.  
This matters for blockchains because signatures may appear:

- in raw transaction payloads,
- in mempool traffic,
- in transaction propagation,
- in block payloads,
- in consensus-committed storage, depending on protocol design.

---

# 6. SLH-DSA Specification Snapshot

## 6.1 What Is SLH-DSA?

**SLH-DSA** is NIST’s stateless hash-based post-quantum digital signature standard.

It includes 12 approved parameter sets:

- SHA2-based and SHAKE-based variants
- “s” variants optimized for **smaller signatures**
- “f” variants optimized for **faster signing**

The table below groups SHA2 and SHAKE variants together where the standardized public-key and signature sizes are identical.

---

## 6.2 SLH-DSA Key and Signature Sizes

> **Note on private-key size:**  
> FIPS 205 Table 2 lists public-key size and signature size.  
> The private-key byte size shown below is derived from the standardized private-key tuple  
> `(SK.seed, SK.prf, PK.seed, PK.root)`, where each component is `n` bytes.  
> Thus, the private-key size is `4n`.

| Parameter Set Family | NIST Security Level | Private Key | Public Key | Signature |
|---|---:|---:|---:|---:|
| **SLH-DSA-128s** (`SHA2-128s`, `SHAKE-128s`) | Level 1 | 64 bytes | 32 bytes | 7,856 bytes |
| **SLH-DSA-128f** (`SHA2-128f`, `SHAKE-128f`) | Level 1 | 64 bytes | 32 bytes | 17,088 bytes |
| **SLH-DSA-192s** (`SHA2-192s`, `SHAKE-192s`) | Level 3 | 96 bytes | 48 bytes | 16,224 bytes |
| **SLH-DSA-192f** (`SHA2-192f`, `SHAKE-192f`) | Level 3 | 96 bytes | 48 bytes | 35,664 bytes |
| **SLH-DSA-256s** (`SHA2-256s`, `SHAKE-256s`) | Level 5 | 128 bytes | 64 bytes | 29,792 bytes |
| **SLH-DSA-256f** (`SHA2-256f`, `SHAKE-256f`) | Level 5 | 128 bytes | 64 bytes | 49,856 bytes |

### Practical interpretation

SLH-DSA public keys are very compact, but signatures are substantially larger than ML-DSA signatures.

That makes SLH-DSA especially useful for explaining why post-quantum blockchain design cannot be reduced to:

```text
“Replace ECDSA with a PQC signature and keep everything else unchanged.”
```

The transaction, block, and commitment structure may need to evolve as well.

---

# 7. Understanding NIST Security Levels

## 7.1 Why NIST Uses Categories Instead of a Single “Bit Security” Number

NIST does not classify post-quantum algorithms only by a single exact “bits of security” estimate.  
Because future quantum-computer capabilities and future cryptanalytic advances are uncertain, this document uses the reader-friendly term **NIST Security Level**.

> **Terminology note:**  
> NIST’s official documents use the term **security category**.  
> In this V3 documentation, we display it as **NIST Security Level** so that non-specialist readers can understand it more easily.

The categories are defined by comparison with well-understood symmetric-cryptography attack targets.

---

## 7.2 NIST Security Level Reference Table

| NIST Security Level | Reference Attack Difficulty |
|---|---|
| **Level 1** | At least as hard as key search on **AES-128** |
| **Level 2** | At least as hard as collision search on **SHA-256 / SHA3-256** |
| **Level 3** | At least as hard as key search on **AES-192** |
| **Level 4** | At least as hard as collision search on **SHA-384 / SHA3-384** |
| **Level 5** | At least as hard as key search on **AES-256** |

This does **not** mean that a Level 5 PQC scheme is literally “AES-256.”  
It means that NIST’s official **security category** for that scheme corresponds to an attack-difficulty target comparable to or greater than the listed reference target.  
This document labels those categories as **Security Levels** for readability.

---

## 7.3 How to Read Levels 1, 3, and 5

For practical interpretation:

| Category | Practical Reading |
|---|---|
| **Level 1** | Baseline high-security PQC level; roughly aligned with 128-bit classical security expectations |
| **Level 3** | Stronger long-term margin; roughly aligned with 192-bit classical security expectations |
| **Level 5** | Highest standard category in this scale; roughly aligned with 256-bit classical security expectations |

NIST’s FAQ explains that Categories **1, 3, and 5** are intended to be consistent with schemes that provide classical security strengths of approximately **128, 192, and 256 bits**, respectively, assuming they are not subject to stronger specialized quantum attacks beyond generic quantum speedups.

---

## 7.4 ML-DSA Parameter Sets and NIST Security Levels

| ML-DSA Parameter Set | NIST Security Level | Practical Interpretation |
|---|---:|---|
| **ML-DSA-44** | Level 2 | Moderate-to-strong security margin with the smallest ML-DSA signature size |
| **ML-DSA-65** | Level 3 | Higher security margin with larger keys and signatures |
| **ML-DSA-87** | Level 5 | Highest ML-DSA security category and the strongest long-term margin among the standardized ML-DSA parameter sets |

NIST FIPS 204 identifies:

- `ML-DSA-44` as Level 2,
- `ML-DSA-65` as Level 3,
- `ML-DSA-87` as Level 5.

---

## 7.5 SymVerse Direction After CADFork

SymVerse plans to recommend:

```text
ML-DSA-87
```

as the preferred PQC signature parameter set **after CADFork**.

The reasoning is architectural:

1. **ML-DSA-87 provides Level 5 security**, the highest ML-DSA category in FIPS 204.
2. Its larger signature size would normally raise blockchain storage and propagation concerns.
3. **CAD is designed precisely to decouple consensus commitment size from raw signature size.**
4. Therefore, after CADFork, SymVerse can prioritize a stronger long-term security margin without making consensus-committed storage scale directly with the larger raw signature bytes.

This is a **SymVerse protocol recommendation**, not a NIST requirement.  
NIST standardizes ML-DSA parameter sets and their security categories; SymVerse determines which parameter set it recommends for its blockchain architecture.

---

## 7.6 Practical Comparison Within ML-DSA

| Parameter Set | Category | Signature Size | SymVerse CADFork Position |
|---|---:|---:|---|
| **ML-DSA-44** | 2 | 2,420 bytes | Smaller, but not the recommended post-CADFork target |
| **ML-DSA-65** | 3 | 3,309 bytes | Intermediate option |
| **ML-DSA-87** | 5 | 4,627 bytes | **Recommended by SymVerse after CADFork** |

This comparison helps explain the role of CAD:

```text
Without CAD:
  larger signatures create stronger pressure on block and ledger size.

With CAD:
  the chain can recommend a stronger signature parameter set
  while keeping the consensus commitment path fixed-size.
```

---

# 8. Falcon / FN-DSA Specification Snapshot

## 8.1 What Is Falcon?

**Falcon** is a lattice-based post-quantum digital signature scheme selected by NIST for standardization.  
NIST’s future standard based on Falcon is named:

```text
FN-DSA — FFT over NTRU-Lattice-Based Digital Signature Algorithm
```

As of the current NIST standardization materials, **FIPS 206 / FN-DSA is still under development** and has not yet reached the same finalized status as FIPS 203, 204, and 205.

Falcon is especially relevant to blockchain design because it offers:

- compact public keys,
- compact signatures compared with many other PQC signature families,
- high verification speed,
- but a more complicated implementation profile than ML-DSA.

---

## 8.2 Falcon Key and Signature Sizes

| Parameter Set | Approx. NIST Security Level | Private Key | Public Key | Signature |
|---|---:|---:|---:|---:|
| **Falcon-512** | Level 1 | 1,281 bytes | 897 bytes | 666 bytes |
| **Falcon-1024** | Level 5 | 2,305 bytes | 1,793 bytes | 1,280 bytes |

### Practical interpretation

Falcon’s signatures are dramatically smaller than those of:

- ML-DSA,
- SLH-DSA.

For example:

| Scheme | Signature Size |
|---|---:|
| **Falcon-512** | 666 bytes |
| **ML-DSA-44** | 2,420 bytes |
| **SLH-DSA-128s** | 7,856 bytes |

This makes Falcon particularly interesting for bandwidth-sensitive environments, including blockchains.

At the same time, the implementation trade-off is important:

- Falcon is attractive for compactness,
- but NIST materials emphasize that FN-DSA/Falcon is more difficult to implement than ML-DSA.

---

## 8.3 Standardization Status

Falcon should be presented differently from ML-DSA and SLH-DSA:

| Scheme Family | NIST Standardization Status |
|---|---|
| **ML-DSA** | Finalized as FIPS 204 |
| **SLH-DSA** | Finalized as FIPS 205 |
| **Falcon / FN-DSA** | Selected by NIST; planned as FIPS 206; still in development |

This distinction matters for blockchain adoption:

- Falcon is a **NIST-selected** PQC signature family,
- but **FN-DSA / FIPS 206 is not yet a finalized NIST standard**,
- so a blockchain that adopts Falcon today is adopting a compact and promising PQC signature family **before final standardization is complete**.

---

## 8.4 Falcon Adoption in Blockchain and the CAD Perspective

Falcon has attracted strong interest in blockchain-oriented post-quantum work because its signatures are much smaller than those of ML-DSA and SLH-DSA.

Recent public examples include:

- **Algorand**, which has demonstrated Falcon-based post-quantum transaction work on mainnet,
- **Solana ecosystem research**, where Anza and Firedancer independently explored Falcon as a compact post-quantum signature path for high-throughput blockchain use.

These examples show why Falcon is appealing:

```text
When the blockchain keeps raw PQC signatures in transaction and ledger structures,
smaller signatures look immediately attractive.
```

However, from the SymVerse V3 and CAD perspective, this also reveals an architectural limitation.

If a blockchain selects Falcon primarily because:

- raw signature bytes remain embedded in transaction data,
- block propagation and storage remain directly sensitive to signature size,
- and the commitment model itself is not redesigned,

then the choice is still being driven by the **old signature-committing blockchain architecture**.

SymVerse V3 takes a different position:

```text
The protocol should not be forced to choose its long-term security recommendation
mainly by whichever PQC signature happens to be smallest.
```

This is where **CAD** becomes important.

CAD changes the problem from:

```text
Which PQC signature is small enough to fit the existing commitment model?
```

to:

```text
How should consensus commit authorization outcomes
without hard-coding itself to raw signature size?
```

For SymVerse V3 documentation, Falcon is therefore useful for four reasons:

1. it is a major NIST-selected PQC signature family,
2. it demonstrates that PQC signature sizes vary significantly by algorithm,
3. it illustrates why some blockchain designs are naturally attracted to compact signatures when they retain traditional signature-committing structures,
4. it strengthens the CAD argument that a blockchain architecture should not hard-code its consensus commitment model around one signature family or select its long-term security posture mainly from raw signature size.

In this sense, Falcon-centered blockchain adoption is not “wrong,” but it often reflects a design path that **optimizes within the pre-CAD commitment model**.  
SymVerse V3 aims to move beyond that constraint by adopting CAD and, after CADFork, recommending **ML-DSA-87** for its stronger long-term security level.

---

# 9. ML-KEM Specification Snapshot

## 9.1 What Is ML-KEM?

**ML-KEM** is NIST’s module-lattice-based key-encapsulation mechanism.  
It is used to establish shared secrets over a public channel.

ML-KEM is not used to sign blockchain transactions, but it is part of the broader post-quantum cryptography landscape and may matter for:

- node-to-node secure communication,
- client-server encrypted channels,
- protocol tooling,
- future hybrid cryptographic stacks.

---

## 9.2 ML-KEM Key and Ciphertext Sizes

| Parameter Set | Security Category | Encapsulation Key | Decapsulation Key | Ciphertext | Shared Secret |
|---|---:|---:|---:|---:|---:|
| **ML-KEM-512** | Level 1 | 800 bytes | 1,632 bytes | 768 bytes | 32 bytes |
| **ML-KEM-768** | Level 3 | 1,184 bytes | 2,400 bytes | 1,088 bytes | 32 bytes |
| **ML-KEM-1024** | Level 5 | 1,568 bytes | 3,168 bytes | 1,568 bytes | 32 bytes |

---

# 10. ECDSA vs PQC Signature Size Comparison

## 10.1 Representative Comparison Table

The following table compares a familiar blockchain ECDSA baseline with representative PQC signature families.

> **Reading note:**  
> This table is intended as a high-level comparison.  
> Full parameter-set tables appear in the preceding sections.

| Scheme / Parameter Set | Standardization Status | NIST Security Level | Private Key | Public Key | Signature |
|---|---|---:|---:|---:|---:|
| **ECDSA / secp256k1** | Classical legacy baseline | Not PQC-rated | 32 B | 64 B raw public key | 65 B recoverable transaction signature |
| **Falcon-512** | NIST-selected; FN-DSA/FIPS 206 not yet final | Approx. Level 1 | 1,281 B | 897 B | 666 B |
| **ML-DSA-44** | FIPS 204 final | Level 2 | 2,560 B | 1,312 B | 2,420 B |
| **ML-DSA-65** | FIPS 204 final | Level 3 | 4,032 B | 1,952 B | 3,309 B |
| **ML-DSA-87** | FIPS 204 final | Level 5 | 4,896 B | 2,592 B | 4,627 B |
| **SLH-DSA-128s** | FIPS 205 final | Level 1 | 64 B | 32 B | 7,856 B |
| **SLH-DSA-256f** | FIPS 205 final | Level 5 | 128 B | 64 B | 49,856 B |

---

## 10.2 Signature Size Multiples Relative to ECDSA

Using the **65-byte Ethereum-style recoverable ECDSA signature** as a familiar baseline:

| Scheme | Signature Size | Approx. Multiple vs ECDSA |
|---|---:|---:|
| **ECDSA / secp256k1** | 65 B | 1.0× |
| **Falcon-512** | 666 B | 10.2× |
| **ML-DSA-44** | 2,420 B | 37.2× |
| **ML-DSA-65** | 3,309 B | 50.9× |
| **ML-DSA-87** | 4,627 B | 71.2× |
| **SLH-DSA-128s** | 7,856 B | 120.9× |
| **SLH-DSA-256f** | 49,856 B | 767.0× |

### Practical interpretation

This comparison makes the protocol-design challenge concrete:

```text
ECDSA-sized blockchain commitment assumptions
do not transfer cleanly to PQC-sized signatures.
```

Even Falcon-512, which is compact among major PQC signature candidates, is already roughly:

```text
10× the size of a familiar ECDSA transaction signature baseline.
```

`ML-DSA-87`, which SymVerse plans to recommend after CADFork, is roughly:

```text
71× that ECDSA signature baseline.
```

That is precisely why **CAD** matters:

- without CAD, a stronger PQC parameter set can directly amplify committed ledger bytes,
- with CAD, SymVerse can recommend a stronger long-term signature level while keeping the consensus commitment path fixed-size.

---

# 11. Why These Numbers Matter for Blockchain

## 11.1 Digital Signatures Are Central to Transaction Authorization

A blockchain account is typically controlled through a private key.  
A transaction is accepted only when the network can verify that the proper key holder authorized it.

In practice, this means that transaction security depends on:

- private-key confidentiality,
- signature unforgeability,
- correct verification rules,
- stable transaction serialization and hashing.

If the signature scheme protecting accounts becomes breakable, an attacker may be able to forge authorizations.

---

## 11.2 PQC Signatures Are Much Larger

The signature-size landscape above shows that:

- `Falcon-512` signature: **666 bytes**
- `Falcon-1024` signature: **1,280 bytes**
- `ML-DSA-44` signature: **2,420 bytes**
- `ML-DSA-87` signature: **4,627 bytes**
- `SLH-DSA-128s` signature: **7,856 bytes**
- `SLH-DSA-256f` signature: **49,856 bytes**

These are not minor formatting changes.  
They can materially affect:

- transaction size,
- block size,
- network propagation,
- storage,
- validation pipelines,
- long-term consensus-committed data volume.

---

## 11.3 Blockchain Design Must Consider Commitment Structure

If a blockchain permanently commits raw post-quantum signature bytes inside its consensus object, larger signatures may lead to sharply increased long-term replication and storage burden.

This is why SymVerse V3 documents:

- PQC account structures,
- PQC transaction fields,
- signature-aware validation,
- and the **Consensus Authorization Digest (CAD)** architecture.

CAD is intended to address the question:

```text
After the signature has already been verified,
what should consensus permanently commit?
```

---

# 12. Public-Key Exposure and Migration

In many blockchain designs, a public key or verification material becomes visible once a transaction is broadcast.

This creates a migration concern:

- unspent or unused accounts may have different exposure properties,
- active accounts may already reveal verification material,
- wallets, nodes, and transaction formats may need coordinated updates.

The exact exposure model differs by blockchain design, but the general post-quantum migration challenge remains important.

---

# 13. How This Connects to SymVerse V3

The SymVerse V3 documentation set addresses PQC transition across several layers.

| Document | Role |
|---|---|
| `pqc-and-blockchain-introduction.md` | Background, ECDSA baseline, PQC standards, Falcon/FN-DSA status, and practical size comparison |
| `v3-basic-spec.md` | Overall V3 scope and architecture |
| `pqc-account-spec.md` | PQC account representation |
| `transaction-spec.md` | PQC signature payloads and transaction structure |
| `cad-overview.md` | Visual explanation of why CAD matters |
| `cad-spec.md` | Formal CAD specification |
| `membership-spec.md` | Membership runtime that coexists with V3 account/transaction flows |
| `rpc-api-spec.md` | Public query and submission surfaces |
| `testing-guide.md` | Reproducible validation scenarios |

---

# 14. Practical Takeaway

Post-quantum blockchain design is not only about replacing one signature algorithm with another.

The standardized parameter tables — when compared against a familiar ECDSA/secp256k1 blockchain baseline — show why:

- keys may become larger,
- signatures often become much larger,
- transaction and block layouts may need to change,
- consensus commitment design becomes a first-class issue.

SymVerse V3 treats PQC as a **protocol architecture topic**, not merely as a cryptographic plug-in.

In that architecture, **CADFork** is important because it allows SymVerse to recommend the stronger `ML-DSA-87` parameter set while avoiding a consensus-commitment model that grows directly with raw PQC signature size.

---

# 15. Recommended Reading Path

For readers new to the V3 documents:

1. `pqc-and-blockchain-introduction.md`
2. `v3-basic-spec.md`
3. `cad-overview.md`
4. `cad-spec.md`
5. `pqc-account-spec.md`
6. `transaction-spec.md`
7. `membership-spec.md`
8. `rpc-api-spec.md`
9. `testing-guide.md`

---

# 16. Source Notes

The algorithm names, parameter sets, security categories, and byte sizes in this document are based on:

- NIST FIPS 186-5 — Digital Signature Standard / ECDSA
- SEC 2 — `secp256k1` 256-bit elliptic curve domain parameters
- SEC 1 — elliptic-curve public-key point encoding rules
- Ethereum Yellow Paper — `secp256k1` transaction-signing representation and `(v,r,s)` signature fields
- NIST FIPS 203 — ML-KEM
- NIST FIPS 204 — ML-DSA
- NIST FIPS 205 — SLH-DSA
- NIST PQC standardization materials for Falcon / future FN-DSA (FIPS 206 in development)
- The Falcon submitter specification and reference materials for Falcon-512 and Falcon-1024 size data
- NIST PQC security evaluation criteria and FAQ materials for the meaning of official security categories, presented in this document as Security Levels 1–5
- Public blockchain materials from Algorand and Solana discussing Falcon-oriented post-quantum blockchain work

For SLH-DSA, the private-key byte sizes are derived from the standardized private-key structure defined in FIPS 205.

For Falcon, the section is intentionally labeled as **Falcon / FN-DSA** because the compact size figures come from Falcon reference materials, while the final NIST FN-DSA standardization work remains in development.

---

# 17. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-14 | Initial background introduction for PQC and blockchain |
| v0.2 | 2026-05-15 | Added NIST PQC parameter tables for ML-DSA, SLH-DSA, and ML-KEM; expanded blockchain relevance discussion around concrete byte sizes |
| v0.3 | 2026-05-15 | Added Falcon / FN-DSA section, including compact key/signature sizes and explicit note that FIPS 206 remains under development |
| v0.4 | 2026-05-15 | Added NIST Security Level explanation, ML-DSA category interpretation, and SymVerse CADFork recommendation for ML-DSA-87 |
| v0.5 | 2026-05-15 | Reworded reader-facing NIST security-category language into the more accessible term “NIST Security Level” while retaining the official terminology note in the text |
| v0.6 | 2026-05-15 | Expanded Falcon discussion to explain its pre-final-standardization status, blockchain adoption motivation, and why CAD avoids selecting a long-term signature strategy mainly from raw signature size |
| v0.7 | 2026-05-15 | Added ECDSA/secp256k1 blockchain baseline specification and direct ECDSA-vs-PQC comparison tables for key and signature sizes |

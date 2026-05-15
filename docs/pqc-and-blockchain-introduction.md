# Post-Quantum Cryptography and Blockchain

> **Status:** Draft v0.3  
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
7. How these concerns connect to the SymVerse V3 documentation set

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

# 4. ML-DSA Specification Snapshot

## 4.1 What Is ML-DSA?

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

## 4.2 ML-DSA Key and Signature Sizes

| Parameter Set | NIST Security Category | Private Key | Public Key | Signature |
|---|---:|---:|---:|---:|
| **ML-DSA-44** | Category 2 | 2,560 bytes | 1,312 bytes | 2,420 bytes |
| **ML-DSA-65** | Category 3 | 4,032 bytes | 1,952 bytes | 3,309 bytes |
| **ML-DSA-87** | Category 5 | 4,896 bytes | 2,592 bytes | 4,627 bytes |

### Practical interpretation

Even the smallest standardized ML-DSA signature is measured in **kilobytes**, not tens of bytes.  
This matters for blockchains because signatures may appear:

- in raw transaction payloads,
- in mempool traffic,
- in transaction propagation,
- in block payloads,
- in consensus-committed storage, depending on protocol design.

---

# 5. SLH-DSA Specification Snapshot

## 5.1 What Is SLH-DSA?

**SLH-DSA** is NIST’s stateless hash-based post-quantum digital signature standard.

It includes 12 approved parameter sets:

- SHA2-based and SHAKE-based variants
- “s” variants optimized for **smaller signatures**
- “f” variants optimized for **faster signing**

The table below groups SHA2 and SHAKE variants together where the standardized public-key and signature sizes are identical.

---

## 5.2 SLH-DSA Key and Signature Sizes

> **Note on private-key size:**  
> FIPS 205 Table 2 lists public-key size and signature size.  
> The private-key byte size shown below is derived from the standardized private-key tuple  
> `(SK.seed, SK.prf, PK.seed, PK.root)`, where each component is `n` bytes.  
> Thus, the private-key size is `4n`.

| Parameter Set Family | NIST Security Category | Private Key | Public Key | Signature |
|---|---:|---:|---:|---:|
| **SLH-DSA-128s** (`SHA2-128s`, `SHAKE-128s`) | Category 1 | 64 bytes | 32 bytes | 7,856 bytes |
| **SLH-DSA-128f** (`SHA2-128f`, `SHAKE-128f`) | Category 1 | 64 bytes | 32 bytes | 17,088 bytes |
| **SLH-DSA-192s** (`SHA2-192s`, `SHAKE-192s`) | Category 3 | 96 bytes | 48 bytes | 16,224 bytes |
| **SLH-DSA-192f** (`SHA2-192f`, `SHAKE-192f`) | Category 3 | 96 bytes | 48 bytes | 35,664 bytes |
| **SLH-DSA-256s** (`SHA2-256s`, `SHAKE-256s`) | Category 5 | 128 bytes | 64 bytes | 29,792 bytes |
| **SLH-DSA-256f** (`SHA2-256f`, `SHAKE-256f`) | Category 5 | 128 bytes | 64 bytes | 49,856 bytes |

### Practical interpretation

SLH-DSA public keys are very compact, but signatures are substantially larger than ML-DSA signatures.

That makes SLH-DSA especially useful for explaining why post-quantum blockchain design cannot be reduced to:

```text
“Replace ECDSA with a PQC signature and keep everything else unchanged.”
```

The transaction, block, and commitment structure may need to evolve as well.

---

# 6. Falcon / FN-DSA Specification Snapshot

## 6.1 What Is Falcon?

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

## 6.2 Falcon Key and Signature Sizes

| Parameter Set | Approx. NIST Security Category | Private Key | Public Key | Signature |
|---|---:|---:|---:|---:|
| **Falcon-512** | Category 1 | 1,281 bytes | 897 bytes | 666 bytes |
| **Falcon-1024** | Category 5 | 2,305 bytes | 1,793 bytes | 1,280 bytes |

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

## 6.3 Standardization Status

Falcon should be presented differently from ML-DSA and SLH-DSA:

| Scheme Family | NIST Standardization Status |
|---|---|
| **ML-DSA** | Finalized as FIPS 204 |
| **SLH-DSA** | Finalized as FIPS 205 |
| **Falcon / FN-DSA** | Selected by NIST; planned as FIPS 206; still in development |

For SymVerse V3 documentation, Falcon is useful to include because:

1. it is a major NIST-selected PQC signature family,
2. it demonstrates that PQC signature sizes vary significantly by algorithm,
3. it strengthens the argument that a blockchain architecture should not hard-code its commitment model around one signature family.

---

# 7. ML-KEM Specification Snapshot

## 7.1 What Is ML-KEM?

**ML-KEM** is NIST’s module-lattice-based key-encapsulation mechanism.  
It is used to establish shared secrets over a public channel.

ML-KEM is not used to sign blockchain transactions, but it is part of the broader post-quantum cryptography landscape and may matter for:

- node-to-node secure communication,
- client-server encrypted channels,
- protocol tooling,
- future hybrid cryptographic stacks.

---

## 7.2 ML-KEM Key and Ciphertext Sizes

| Parameter Set | Security Category | Encapsulation Key | Decapsulation Key | Ciphertext | Shared Secret |
|---|---:|---:|---:|---:|---:|
| **ML-KEM-512** | Category 1 | 800 bytes | 1,632 bytes | 768 bytes | 32 bytes |
| **ML-KEM-768** | Category 3 | 1,184 bytes | 2,400 bytes | 1,088 bytes | 32 bytes |
| **ML-KEM-1024** | Category 5 | 1,568 bytes | 3,168 bytes | 1,568 bytes | 32 bytes |

---

# 8. Why These Numbers Matter for Blockchain

## 8.1 Digital Signatures Are Central to Transaction Authorization

A blockchain account is typically controlled through a private key.  
A transaction is accepted only when the network can verify that the proper key holder authorized it.

In practice, this means that transaction security depends on:

- private-key confidentiality,
- signature unforgeability,
- correct verification rules,
- stable transaction serialization and hashing.

If the signature scheme protecting accounts becomes breakable, an attacker may be able to forge authorizations.

---

## 8.2 PQC Signatures Are Much Larger

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

## 8.3 Blockchain Design Must Consider Commitment Structure

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

# 9. Public-Key Exposure and Migration

In many blockchain designs, a public key or verification material becomes visible once a transaction is broadcast.

This creates a migration concern:

- unspent or unused accounts may have different exposure properties,
- active accounts may already reveal verification material,
- wallets, nodes, and transaction formats may need coordinated updates.

The exact exposure model differs by blockchain design, but the general post-quantum migration challenge remains important.

---

# 10. How This Connects to SymVerse V3

The SymVerse V3 documentation set addresses PQC transition across several layers.

| Document | Role |
|---|---|
| `pqc-and-blockchain-introduction.md` | Background, standards, Falcon/FN-DSA status, and practical size comparison |
| `v3-basic-spec.md` | Overall V3 scope and architecture |
| `pqc-account-spec.md` | PQC account representation |
| `transaction-spec.md` | PQC signature payloads and transaction structure |
| `cad-overview.md` | Visual explanation of why CAD matters |
| `cad-spec.md` | Formal CAD specification |
| `membership-spec.md` | Membership runtime that coexists with V3 account/transaction flows |
| `rpc-api-spec.md` | Public query and submission surfaces |
| `testing-guide.md` | Reproducible validation scenarios |

---

# 11. Practical Takeaway

Post-quantum blockchain design is not only about replacing one signature algorithm with another.

The standardized parameter tables show why:

- keys may become larger,
- signatures often become much larger,
- transaction and block layouts may need to change,
- consensus commitment design becomes a first-class issue.

SymVerse V3 treats PQC as a **protocol architecture topic**, not merely as a cryptographic plug-in.

---

# 12. Recommended Reading Path

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

# 13. Source Notes

The algorithm names, parameter sets, security categories, and byte sizes in this document are based on:

- NIST FIPS 203 — ML-KEM
- NIST FIPS 204 — ML-DSA
- NIST FIPS 205 — SLH-DSA
- NIST PQC standardization materials for Falcon / future FN-DSA (FIPS 206 in development)
- The Falcon submitter specification and reference materials for Falcon-512 and Falcon-1024 size data

For SLH-DSA, the private-key byte sizes are derived from the standardized private-key structure defined in FIPS 205.

For Falcon, the section is intentionally labeled as **Falcon / FN-DSA** because the compact size figures come from Falcon reference materials, while the final NIST FN-DSA standardization work remains in development.

---

# 14. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-14 | Initial background introduction for PQC and blockchain |
| v0.2 | 2026-05-15 | Added NIST PQC parameter tables for ML-DSA, SLH-DSA, and ML-KEM; expanded blockchain relevance discussion around concrete byte sizes |
| v0.3 | 2026-05-15 | Added Falcon / FN-DSA section, including compact key/signature sizes and explicit note that FIPS 206 remains under development |

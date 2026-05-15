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

Many widely used public-key signature systems are based on mathematical problems that are believed to be hard for classical computers but vulnerable to sufficiently capable quantum computers.

For blockchain systems, the most important example is:

```text
ECDSA over elliptic curves
```

which is widely used to authorize transactions in Ethereum-compatible account models.

---

### 2.2.1 The Classical Security Assumption Behind ECDSA

ECDSA depends on the hardness of the **Elliptic Curve Discrete Logarithm Problem (ECDLP)**.

In simplified form:

```text
Private key: d
Base point:  G
Public key:  Q = dG
```

Computing the public key `Q` from the private key `d` is easy.

```text
d  →  Q = dG
```

But recovering the private key `d` from the public key `Q` is believed to be computationally infeasible for classical computers.

```text
Q  →  d
```

This one-way difficulty is the foundation of ECDSA security.

---

### 2.2.2 Why Shor’s Algorithm Changes the Threat Model

A sufficiently large fault-tolerant quantum computer can use **Shor’s algorithm** to solve discrete logarithm problems efficiently, including the elliptic-curve discrete logarithm problem that underlies ECDSA.

At a high level:

1. the attacker observes a public key `Q`,
2. the problem is formulated as recovering the hidden scalar `d` such that `Q = dG`,
3. Shor’s algorithm exploits quantum superposition and periodic structure,
4. the **Quantum Fourier Transform (QFT)** is used as a core subroutine to extract the periodic information needed to recover the hidden relation,
5. the private key `d` can then be derived.

The result is conceptually:

```text
Public key Q
   ↓  Shor’s algorithm + Quantum Fourier Transform
Private key d
```

This is not merely a faster brute-force search.  
It is a fundamentally different algorithmic attack against the mathematical structure that protects ECDSA.

The Proos–Zalka analysis of Shor’s discrete-logarithm algorithm for elliptic curves shows how Shor’s method can be specialized to elliptic-curve groups.  
See: [Shor’s discrete logarithm quantum algorithm for elliptic curves](https://arxiv.org/pdf/quant-ph/0301141).

---

### 2.2.3 Why Ethereum-Compatible Signatures Are Especially Exposed

Ethereum-style ECDSA signatures are **recoverable signatures**.

Given:

- the signed message hash,
- the signature values `(v, r, s)`,

the signer’s public key can be recovered. Ethereum formalizes this through its `ECREC` / `ecrecover` public-key recovery function.

```text
message hash + (v, r, s)
        ↓
recovered public key
```

This is not itself a vulnerability.  
It is a normal and intentional feature of Ethereum-style transaction verification and sender recovery.

However, it matters in the quantum threat model:

```text
Recoverable signature
        ↓
Public key becomes available
        ↓
Quantum attacker can target the exposed public key
        ↓
Shor’s algorithm may derive the private key
        ↓
Forged signatures and asset theft become possible
```

Ethereum’s own roadmap states this directly:

> when an account sends a transaction, its public key is exposed onchain, and a quantum computer could derive the private key from that exposed public key.

See: [Future-proofing Ethereum and crypto quantum security](https://ethereum.org/roadmap/future-proofing/).

The Ethereum Yellow Paper also defines `ECREC` as an ECDSA public-key recovery function.  
See: [Ethereum Yellow Paper](https://ethereum.github.io/yellowpaper/paper.pdf).

---

### 2.2.4 Why This Matters for Ethereum-Style Account Models

In account-based systems such as Ethereum, addresses are commonly reused.  
Once an account has sent a transaction, the corresponding public key can be reconstructed from transaction-signature data and becomes part of the observable attack surface.

Deloitte’s quantitative Ethereum analysis defined **quantum-exposed funds** as funds held in addresses whose public keys have already been revealed through prior transactions.  
In that analysis, Deloitte reported that **over 65% of all Ether** was in quantum-exposed addresses at the time of the study.

See: [Quantum risk to the Ethereum blockchain](https://www.deloitte.com/nl/en/services/consulting-risk/perspectives/quantum-risk-to-the-ethereum-blockchain.html).

This explains why Ethereum-style systems are often described as having a broader quantum-exposure surface than UTXO systems where address reuse can be more easily avoided.

---

### 2.2.5 Recent Risk Assessments and Industry Reports

The urgency of the transition is increasingly being discussed outside purely academic cryptography literature.

- **Tiger Research** summarizes the blockchain “Q-Day” concern and notes that Bitcoin and Ethereum remain in the early discussion phase relative to broader industry PQC migration.  
  See: [Will Quantum Computers Break Bitcoin and Ethereum?](https://reports.tiger-research.com/p/quantum-pqc-eng)

- **Project Eleven’s 2026 report** argues that blockchain systems are particularly exposed because addresses may hold meaningful value under the same public key for years, signature schemes are deeply embedded in protocol rules, and a compromised key has no simple recovery mechanism.  
  See: [The Quantum Threat to Blockchains – 2026 Report](https://www.projecteleven.com/blog/the-quantum-threat-to-blockchains---2026-report)

---

#### 2.2.5.1 Google Quantum AI: The Estimated Attack Cost Is Falling

A major 2026 warning came from the whitepaper:

```text
Securing Elliptic Curve Cryptocurrencies against Quantum Vulnerabilities:
Resource Estimates and Mitigations
```

Google Research highlighted the work on **March 31, 2026**.  
The paper studies the quantum resources needed to attack:

```text
secp256k1
```

the elliptic curve used by Bitcoin and widely used in Ethereum-compatible signing systems.

The most important message for general readers is simple:

```text
The paper does not claim that current quantum computers can break Bitcoin or Ethereum.
It shows that the estimated machine needed to do so may be much smaller
than earlier estimates suggested.
```

---

#### 2.2.5.2 Key Result in Plain Language

| Question | 2026 Google Quantum AI Estimate |
|---|---|
| What is being targeted? | `ECDLP-256` over `secp256k1` |
| What changed? | Estimated attack resources were reduced substantially |
| Headline estimate | Fewer than **500,000 physical qubits** under stated fault-tolerant assumptions |
| Runtime estimate | Potentially **minutes** once such a machine exists |
| Comparison with prior estimate | Roughly **20× fewer physical qubits** than a 2023-era estimate |

Security industry reporting summarized the result in similarly direct terms:  
Google’s work suggests that the hardware required to threaten Bitcoin- and Ethereum-style elliptic-curve cryptography may be **far lower than previously estimated**.

See:

- [Google Research — Safeguarding cryptocurrency by disclosing quantum vulnerabilities responsibly](https://research.google/blog/safeguarding-cryptocurrency-by-disclosing-quantum-vulnerabilities-responsibly/)
- [SecurityWeek — Google Slashes Quantum Resource Requirements for Breaking Cryptocurrency Encryption](https://www.securityweek.com/google-slashes-quantum-resource-requirements-for-breaking-cryptocurrency-encryption/)

---

#### 2.2.5.3 Why This Matters for Blockchain Users

For blockchains, the concern is straightforward:

1. a transaction can reveal or allow recovery of public-key material,
2. a sufficiently powerful future quantum computer could derive the private key,
3. an attacker could try to create a competing fraudulent transaction.

This is why **public-key exposure**, **mempool timing**, and **account migration** matter in quantum-risk discussions.

The Google paper also discusses responsible disclosure and uses a zero-knowledge proof approach to support verification of sensitive circuit claims without publishing every implementation detail.  
That point is important academically, but the core takeaway for blockchain readers is simpler:

```text
The attack is still future-oriented,
but the estimated distance to that future has become smaller.
```

---

#### 2.2.5.4 What This Means for PQC Migration

These studies should be read as **risk assessments and resource-estimation work**, not as proof that a cryptographically relevant quantum computer exists today.  
No public quantum computer is currently known to break Ethereum’s ECDSA security in practice.

However, the strategic conclusion is clear:

```text
Blockchain migration should begin before the attack machine exists.
```

The reason is that:

- cryptographic migration takes years,
- accounts and wallets need transition paths,
- protocol rules may need upgrades,
- and public-key exposure can exist long before an actual attack becomes practical.

This strengthens the case for early PQC migration and for architectures such as **CAD**, which reduce the pressure to select a long-term signature strategy mainly by raw signature byte size.

---

### 2.2.6 Why Post-Quantum Transition Must Start Early

The transition is being driven by the need to protect:

- long-lived confidential data,
- authentication systems,
- digital signatures,
- public-key infrastructure,
- software update trust chains,
- blockchain account authorization models.

For blockchains, the important lesson is:

```text
The danger begins before quantum attacks become practical,
because migration of accounts, wallets, protocols, and consensus rules takes time.
```

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
| **Public key, raw form** | 64 bytes | 32-byte x-coordinate + 32-byte y-coordinate |
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

## 8.4 Falcon, ML-DSA, and Why CADFork Matters

Falcon is important to discuss in a blockchain document because it is one of the major NIST-selected post-quantum signature families and because its signatures are smaller than those of ML-DSA and SLH-DSA.

However, the key point for SymVerse V3 is not:

```text
Falcon is small, therefore Falcon solves the blockchain scaling problem.
```

The more accurate point is:

```text
Falcon is smaller than many PQC alternatives,
but it is still far larger than legacy ECDSA signatures.
Without CADFork, Falcon and ML-DSA both increase
the size burden of signature-committing blockchain designs.
```

---

### 8.4.1 Falcon-512: Smaller PQC Signature, but Still Much Larger Than ECDSA

`Falcon-512` is commonly discussed as a compact PQC signature option.

| Item | Falcon-512 |
|---|---:|
| NIST Security Level | Level 1 |
| Public key | 897 bytes |
| Private key | 1,281 bytes |
| Signature | 666 bytes |
| Relative to 65-byte ECDSA signature | ~10.2× larger |
| NIST standardization status | Falcon selected; FN-DSA / FIPS 206 still in development |

This matters because even the compact Falcon-512 signature is still:

```text
more than 10 times larger than
a familiar 65-byte ECDSA transaction-signature baseline.
```

Thus, Falcon reduces the degree of size growth compared with larger PQC signatures, but it does **not** remove the underlying blockchain scaling issue when raw signatures remain inside transaction and consensus-committed ledger structures.

---

### 8.4.2 Current Blockchain Experiments Do Not Remove the CAD Problem

Some blockchain ecosystems have publicly demonstrated or explored Falcon-based post-quantum signing paths.

- **Algorand** has publicly demonstrated a Falcon-signed post-quantum transaction on mainnet.
- **Solana ecosystem research** from Anza and Firedancer has explored Falcon as a compact post-quantum signature path for a high-throughput blockchain environment.

These efforts are valuable as early post-quantum engineering work.  
But from the CAD perspective, they do not eliminate the core structural issue:

```text
If raw PQC signatures remain part of the transaction
or consensus-committed ledger object,
the blockchain still scales with signature size.
```

This is true for:

- Falcon,
- ML-DSA,
- SLH-DSA,
- and any other large post-quantum signature family.

Falcon can make the burden smaller than some alternatives, but **it does not solve the signature-committing consensus problem identified by CAD**.

---

### 8.4.3 The CAD Interpretation

The CAD paper argues that post-quantum migration should not stop at:

```text
Which PQC signature is smallest?
```

The deeper protocol question is:

```text
Why should consensus permanently commit raw signature bytes at all,
after authorization has already been verified?
```

CAD addresses that question by separating:

1. **admission-time signature verification**, and
2. **consensus-time commitment of a fixed-size authorization digest**.

Therefore:

```text
Without CADFork:
  Falcon and ML-DSA both increase the commitment burden
  relative to ECDSA.

With CADFork:
  the consensus commitment path is no longer dictated
  by raw PQC signature size.
```

---

### 8.4.4 Why SymVerse Recommends ML-DSA-87 After CADFork

SymVerse V3 plans to recommend:

```text
ML-DSA-87
```

after CADFork.

The reason is not that ML-DSA-87 is small.  
It is not. Its signature size is:

```text
4,627 bytes
```

which is roughly:

```text
71× larger than a 65-byte ECDSA signature baseline.
```

SymVerse recommends ML-DSA-87 because:

1. **ML-DSA-87 is finalized in FIPS 204.**
2. **It provides NIST Security Level 5**, the strongest ML-DSA security level in the standard.
3. Recent quantum resource-estimation work indicates that the expected cost of attacking elliptic-curve cryptography may fall faster than earlier estimates suggested.
4. CADFork removes the need to choose a weaker or smaller long-term signature recommendation merely to reduce consensus-committed signature bytes.

In other words:

```text
SymVerse prioritizes stronger long-term post-quantum security
after CADFork,
because CAD changes the scaling equation.
```

---

### 8.4.5 Future Falcon Support Remains Compatible With SymVerse V3

Falcon should not be excluded from the SymVerse V3 roadmap.

Once:

```text
FN-DSA / FIPS 206 is finalized
```

SymVerse may also support Falcon-family signatures.

That future path is compatible with the CAD design because:

```text
CAD is intended to support any PQC signature scheme.
```

This gives SymVerse two advantages at once:

- a clear post-CADFork default recommendation:
  - **ML-DSA-87**
- and long-term algorithm agility:
  - **Falcon / FN-DSA** can be added after standardization is complete,
  - without redesigning the consensus commitment model.

---

### 8.4.6 Summary

| Question | SymVerse V3 Position |
|---|---|
| Is Falcon smaller than ML-DSA and SLH-DSA? | Yes |
| Is Falcon-512 still much larger than ECDSA? | Yes, about 10× |
| Is Falcon / FN-DSA already a finalized NIST standard? | No |
| Does Falcon alone solve blockchain signature-size scalability? | No |
| Do Falcon and ML-DSA both create size pressure without CADFork? | Yes |
| Why recommend ML-DSA-87 after CADFork? | Highest standardized ML-DSA security level and stronger proactive quantum-resistance posture |
| Can SymVerse later add Falcon? | Yes, after standardization, because CAD supports any PQC signature scheme |

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
- Proos and Zalka — Shor’s discrete logarithm quantum algorithm for elliptic curves
- Ethereum Yellow Paper — `ECREC` / `ecrecover` public-key recovery
- Ethereum.org future-proofing roadmap — account public-key exposure and quantum private-key derivation risk
- Deloitte — quantitative analysis of quantum-exposed Ether
- Tiger Research — blockchain Q-Day risk framing
- Project Eleven — 2026 blockchain quantum-threat report
- Babbush et al. — *Securing Elliptic Curve Cryptocurrencies against Quantum Vulnerabilities: Resource Estimates and Mitigations*
- Google Research — March 31, 2026 announcement on responsible disclosure of cryptocurrency quantum vulnerabilities
- SecurityWeek — March 31, 2026 reporting on Google’s reduced quantum-resource estimates for cryptocurrency encryption
- Zenodo supporting artifact for the paper’s zero-knowledge proof verification data
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
| v0.8 | 2026-05-15 | Expanded the transition rationale with ECDLP, Shor’s algorithm, Quantum Fourier Transform, Ethereum recoverable signatures, public-key exposure, and linked risk analyses from Ethereum.org, Deloitte, Tiger Research, and Project Eleven |
| v0.9 | 2026-05-15 | Added Google Quantum AI / Ethereum Foundation / Stanford / UC Berkeley whitepaper discussion, updated secp256k1 resource estimates, 20-fold physical-qubit reduction framing, zero-knowledge-proof disclosure note, and fast-clock on-spend attack implications |
| v0.10 | 2026-05-15 | Condensed the Google Quantum AI discussion into a general-reader summary, kept only the most relevant resource estimates, and reframed the blockchain implication around public-key exposure and early migration |
| v0.11 | 2026-05-15 | Replaced the technical `x || y` raw-public-key notation with a reader-friendly explanation: 32-byte x-coordinate + 32-byte y-coordinate |
| v0.12 | 2026-05-15 | Reframed the Falcon blockchain discussion around Falcon-512 size/security, unfinished FN-DSA standardization, CAD-driven algorithm agility, and SymVerse’s plan to recommend ML-DSA-87 after CADFork while remaining open to Falcon after final standardization |
| v0.13 | 2026-05-15 | Rewrote the Falcon section to emphasize that Falcon and ML-DSA both remain much larger than ECDSA without CADFork, that compact Falcon signatures do not solve signature-committing scalability, and that SymVerse recommends ML-DSA-87 proactively because quantum-risk estimates are tightening while CAD preserves future Falcon support after standardization |

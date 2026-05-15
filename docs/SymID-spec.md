# SymVerse V3 SymID Specification

> **Status:** Draft v0.6  
> **Date:** 2026-05-15  
> **Document Role:** Public specification for the V3 SymID account identifier in the quantum-resistant SymVerse architecture

---

# 1. Overview

**SymID** is the account identifier used by the SymVerse blockchain.

In SymVerse V3, SymID is defined as a **chain-bound cryptographic account identifier**.

A SymID is associated with:

- one private key,
- one public key,
- one chain context,
- and one blockchain account identity.

The V3 identity relationship is:

```text
1 private key : 1 public key : 1 SymID
```

SymID is therefore not a multi-account identity container.  
It is the **direct account identifier derived from a cryptographic key pair within a specific chain context**.

Within the blockchain, SymID is used as a **unique account key**:

```text
SymID = chain-bound unique account identifier
```

It serves as the protocol-level identifier for:

- account lookup,
- transaction sender and receiver identification,
- Citizen/account registration references,
- public query and validation flows.

---

# 2. V3 Design Principles

## 2.1 Key-Bound Identity

A SymID is bound to a single cryptographic key pair.

| Object | Relationship |
|---|---|
| Private key | Controls the account |
| Public key | Provides verifiable authorization material |
| SymID | Identifies the account on-chain |

The core rule is:

```text
One key pair produces one SymID.
```

This applies to:

- ECDSA accounts,
- post-quantum accounts,
- future signature schemes supported by the protocol.

---

## 2.2 Chain-Bound Identity

SymID is not derived from public-key material alone.  
It is bound to the target chain through:

```text
ChainID
```

The public SymID composition is:

```text
Version (2 bits)
ChainID (14 bits)
PublicKeyHash (SHA3-256, last 8 bytes)
→ SymID
```

| Component | Size | Role |
|---|---:|---|
| `Version` | 2 bits | SymID format/version discriminator |
| `ChainID` | 14 bits | Chain-binding component |
| `PublicKeyHash` | 8 bytes used | Last 8 bytes of `SHA3-256(public key)` |

This field layout makes the SymID derivation rule publicly reproducible.  
Any party that knows:

- the public key,
- the Version,
- the ChainID,

can derive the same SymID.

`CaID` is not part of the V3 SymID derivation policy.  
The chain-binding component is:

```text
ChainID
```

---

## 2.3 Algorithm-Aware Account, Algorithm-Neutral SymID Derivation

SymVerse V3 is designed for quantum-resistant blockchain operation.

A SymID may belong to an account authorized by:

- legacy ECDSA,
- ML-DSA,
- future standardized PQC signature schemes.

However, the **SymID derivation rule itself does not take an algorithm code as a separate derivation input**.

The derivation rule uses:

```text
Version
ChainID
PublicKeyHash (last 8 bytes)
```

The signature algorithm is recorded and interpreted through the broader account authorization metadata, such as:

| Layer | Role |
|---|---|
| SymID | Chain-bound account identifier |
| Public key bytes | Source input for SymID derivation |
| Account authorization metadata | Signature algorithm and full public-key interpretation |
| Transaction authorization | Signature validation |
| CAD | Consensus authorization commitment after CADFork |

This distinction matters:

```text
The public key participates in SymID derivation.
The algorithm policy belongs to account authorization metadata.
```

---

## 2.4 Citizen Protocol Compatibility

A SymID identifies an account.  
The Citizen Protocol defines higher-level public Citizen state such as:

- initial `NickName`,
- ongoing `Nick`,
- `RefCode`,
- `Referrer`,
- `Link`,
- `LinkedBy`.

These are related, but not identical concepts.

| Concept | Role |
|---|---|
| SymID | Cryptographic account identifier |
| Citizen | Protocol-recognized account/citizen registration state |
| Nick | Human-readable public Citizen key |
| RefCode / Referrer / Link | Citizen runtime relation state |

---

# 3. SymID Identity Model

## 3.1 V3 Identity Relationship

The V3 model is:

```text
PrivateKey → PublicKey → PublicKeyHash → SymID
```

with the final SymID composed as:

```text
Version (2 bits)
ChainID (14 bits)
PublicKeyHash (SHA3-256, last 8 bytes)
→ SymID
```

This means:

1. a new key pair creates a new public key,
2. the public key is hashed,
3. the last 8 bytes of that public-key hash become the key-derived SymID component,
4. Version and ChainID are included in the SymID,
5. SymID is not a sequence under a shared Citizen ID,
6. SymID is not a CA-issued serial-account number.

---

## 3.2 One SymID Per Key Pair and Chain Context

A SymID is uniquely tied to:

- public key material,
- Version,
- ChainID.

| Case | Result |
|---|---|
| Same public key, same Version, same ChainID | Same SymID derivation result |
| Different public key | Different SymID derivation result |
| Same public key, different ChainID | Different chain-bound SymID |
| Public key missing | Invalid derivation |
| Version = `0` | Invalid derivation |
| ChainID missing | Invalid derivation |

The protocol therefore treats SymID as a deterministic account identifier under a specific chain and SymID version.

---

## 3.3 SymID as a Unique Account Key

A SymID functions as the unique account key used by the chain to identify a specific account.

The public meaning is:

```text
One derived SymID
  → one chain-specific account identity
```

Because SymID is composed from:

```text
Version (2 bits)
ChainID (14 bits)
PublicKeyHash (SHA3-256, last 8 bytes)
```

it is:

- tied to a specific chain,
- tied to public-key-derived material,
- stable for the corresponding account derivation context,
- suitable for use as the canonical account identifier in transaction and query flows.

In practical protocol terms:

| Context | Role of SymID |
|---|---|
| Account lookup | Unique account key |
| Transaction `from` | Sender account identifier |
| Transaction `to` | Recipient account identifier, unless an API resolves a Nick to SymID/address |
| Citizen registration | Account identity anchor |
| Verification | Identifier checked against the public-key-derived account context |

---

## 3.4 SymID and Account Creation

The typical V3 account creation flow is:

```text
1. Select the account authorization scheme.
2. Generate private key and public key.
3. Set SymID Version.
4. Set target ChainID.
5. Compute SHA3-256(public key).
6. Take the last 8 bytes of that 32-byte hash.
7. Compose SymID from:
   - Version (2 bits)
   - ChainID (14 bits)
   - PublicKeyHash (SHA3-256, last 8 bytes)
8. Register the account/Citizen state as required by the protocol.
```

For PQC-capable accounts, the public key may be significantly larger than an ECDSA public key, but the SymID composition remains the same:

```text
PublicKey
  → SHA3-256(public key)
  → last 8 bytes
  → SymID key-derived component
```

This means that SymID derivation is reproducible for both classical and post-quantum account types.

---

# 4. SymID Composition

## 4.1 Public Composition Rule

The V3 SymID composition is:

```text
Version (2 bits)
ChainID (14 bits)
PublicKeyHash (SHA3-256, last 8 bytes)
→ SymID
```

This is the primary public description of SymID.

The composition uses:

```text
Version      : 2 bits
ChainID      : 14 bits
PublicKeyHash: final 8 bytes of SHA3-256(public key)
```

Together, these components form a compact and deterministic SymID.

---

## 4.2 Component Meaning

| Component | Size | Meaning |
|---|---:|---|
| `Version` | 2 bits | SymID version |
| `ChainID` | 14 bits | Chain identifier |
| `PublicKeyHash (last 8 bytes)` | 8 bytes | Final 8 bytes of `SHA3-256(public key)` |

---

## 4.3 PublicKeyHash Rule

The `PublicKeyHash` component MUST be derived as follows:

```text
PublicKeyHashFull = SHA3-256(public key)
PublicKeyHash     = last 8 bytes of PublicKeyHashFull
```

In other words:

```text
PublicKey
  → SHA3-256(public key)
  → 32-byte hash
  → take bytes 24 through 31
  → PublicKeyHash (8 bytes)
```

This rule is part of the public SymID specification so that any implementation can reproduce the same SymID from the same inputs.

---

## 4.4 Derivation Flow

The derivation flow is:

```text
PublicKey
  → SHA3-256(public key)
  → last 8 bytes

Version (2 bits)
ChainID (14 bits)
PublicKeyHash (last 8 bytes)
  → SymID
```

A party can derive a SymID by following the above flow with the same:

- public key,
- Version,
- ChainID.

---

## 4.5 Input Validation

SymID derivation MUST reject invalid input.

| Condition | Result |
|---|---|
| Public key is empty | Reject |
| `Version = 0` | Reject |
| `ChainID` is empty | Reject |

---

## 4.6 ChainID Replaces CA-Oriented Prefixing

The V3 SymID derivation policy uses:

```text
ChainID
```

not `CaID`.

Legacy CA-style prefixing is outside the V3 SymID composition model.

---

## 4.7 Not Address-plus-Nonce Derivation

The V3 SymID derivation rule MUST NOT be confused with:

```text
address + nonce
```

style address derivation.

The V3 rule is:

```text
PublicKey
  → SHA3-256(public key)
  → last 8 bytes

Version (2 bits)
ChainID (14 bits)
PublicKeyHash (SHA3-256, last 8 bytes)
  → SymID
```

No external package or creation path should infer an alternative SymID derivation rule when the public key is available.

---

## 4.8 Same Composition for ECDSA and PQC

The same SymID composition policy applies to:

- ECDSA public keys,
- ML-DSA public keys,
- future PQC public keys supported by the chain.

The public-key material differs by algorithm.  
The SymID composition does not.

---

# 5. Authorization Metadata Associated with SymID

## 5.1 Account Authorization Model

A SymID-linked account carries authorization metadata used for transaction validation.

In V3, relevant authorization concepts include:

| Field / Concept | Meaning |
|---|---|
| Public-key hash or account public-key reference | Legacy account verification context |
| `QAlgo` | PQC algorithm identifier |
| `QKeyPub` | PQC public key |
| Account status / role fields | Protocol-defined account metadata |

---

## 5.2 ECDSA Account

An ECDSA-backed SymID uses classical signature authorization.

Conceptually:

```text
ECDSA private key
  → ECDSA public key
  → SHA3-256(public key)
  → last 8 bytes
  → SymID
```

ECDSA remains part of compatibility and transition handling where the protocol permits it.

---

## 5.3 PQC Account

A PQC-backed SymID uses post-quantum public-key material.

Conceptually:

```text
PQC private key
  → PQC public key
  → SHA3-256(public key)
  → last 8 bytes
  → ChainID-bound SymID
```

For ML-DSA accounts, the account metadata includes:

- `QAlgo`
- `QKeyPub`

The SymID account identifier and the PQC authorization metadata together form the V3 post-quantum account model.

---

## 5.4 Algorithm Examples

| Account Family | Example Algorithm Code | Authorization Material |
|---|---:|---|
| ECDSA | `0x0000` | ECDSA public-key context |
| ML-DSA-44 | `0x0044` | ML-DSA-44 public key |
| ML-DSA-65 | `0x0065` | ML-DSA-65 public key |
| ML-DSA-87 | `0x0087` | ML-DSA-87 public key |

SymVerse plans to recommend **ML-DSA-87** after CADFork because it provides the highest NIST security level among the standardized ML-DSA parameter sets.

---

# 6. SymID Derivation and Verification

## 6.1 Derivation Consistency

All SymID generation paths MUST derive the same SymID from the same:

- public key,
- Version,
- ChainID.

The public composition rule remains:

```text
Version (2 bits)
ChainID (14 bits)
PublicKeyHash (SHA3-256, last 8 bytes)
→ SymID
```

The protocol must not silently substitute chain context.  
A missing or mismatched ChainID must be treated as an error in derivation-sensitive paths.

---

## 6.2 Public-Key Binding

The SymID must remain consistent with the public-key material used for authorization.

Conceptually:

```text
VerifySymID(
  PublicKey,
  Version,
  ChainID,
  SymID
) = true
```

Verification replays the same public composition rule:

```text
PublicKey
  → SHA3-256(public key)
  → last 8 bytes

Version (2 bits)
ChainID (14 bits)
PublicKeyHash (SHA3-256, last 8 bytes)
  → SymID
```

---

## 6.3 Unified Creation Rule

Any account or Citizen creation path that already knows the public key SHOULD use the unified SymID composition rule.

This applies to:

- ECDSA account creation,
- PQC account creation,
- Citizen registration paths that carry public-key material.

The V3 policy is:

```text
Do not create a separate SymID derivation rule.
Do not derive SymID from address + nonce when the public key is available.
```

---

## 6.4 Sender Verification

For a transaction sender, the protocol validates that:

1. the transaction sender field contains a SymID,
2. the authorization public key corresponds to that SymID composition rule,
3. the signature satisfies the account’s algorithm rules.

For PQC accounts, this includes validation against:

- `QAlgo`,
- `QKeyPub`,
- the transaction’s post-quantum signature evidence where applicable.

---

# 7. SymID and Citizen Registration

## 7.1 SymID Before Citizen Registration

A key pair may be generated before Citizen registration is completed.

The generated SymID identifies the account candidate, but protocol-visible Citizen state becomes active only after the required Citizen registration flow is confirmed.

---

## 7.2 Citizen Registration Binding

Citizen registration binds protocol state to the SymID.

This may include:

- SymID/account activation,
- authorization metadata,
- optional initial `NickName`,
- future account/Citizen queryability.

---

## 7.3 Initial NickName Is Separate from SymID

A SymID is cryptographic and chain-bound.  
An initial `NickName` is human-readable Citizen metadata.

| Item | Nature |
|---|---|
| SymID | Key-derived chain-bound account identifier |
| NickName | Optional initial Citizen public alias |
| Nick | Post-registration public Citizen alias |

A Nick can be used for:

- public lookup,
- Citizen relation operations,
- direct transfer APIs.

But the Nick does not replace the SymID as the underlying account identifier.

---

# 8. SymID and CADFork

## 8.1 Why SymID Matters for Quantum-Resistant Accounts

SymID provides the account identity layer.  
PQC signatures provide the authorization layer.  
CAD provides the consensus commitment layer.

The V3 stack is therefore:

```text
SymID
  = account identity

PQC authorization
  = quantum-resistant signing model

CAD
  = signature-size-independent consensus authorization commitment
```

---

## 8.2 CADFork Direction

After CADFork:

- SymVerse can support PQC accounts without binding consensus commitment size directly to raw PQC signature size,
- SymID remains the account identity anchor,
- stronger PQC algorithms such as ML-DSA-87 can be recommended as the default direction.

---

# 9. SymID Compared with Nick

## 9.1 SymID

SymID is:

- cryptographic,
- deterministic under ChainID and key context,
- used in account and transaction validation,
- not user-selected.

---

## 9.2 Nick

Nick is:

- human-readable,
- globally unique within the Citizen protocol domain,
- user-facing,
- usable for lookup and direct transfer APIs.

---

## 9.3 Comparison Table

| Property | SymID | Nick |
|---|---|---|
| Derived from key material | Yes | No |
| Chain-bound | Yes | Indirectly, through protocol state |
| User-chosen | No | Yes, subject to validation |
| Used for transaction sender verification | Yes | No |
| Used as direct transfer destination | Underlying resolved account | User-facing destination identifier |
| May be changed | No | Yes, through Citizen operation rules |

---

# 10. Public Query Expectations

A public account or Citizen query surface may expose:

| Query Concern | Example Output |
|---|---|
| SymID | Account identifier |
| Account algorithm | ECDSA / ML-DSA variant |
| PQC algorithm code | `QAlgo` |
| PQC public key | `QKeyPub`, where permitted |
| Citizen Nick | Current Nick, if present |
| Citizen RefCode | Public Citizen referral code, if exposed by Citizen Protocol |

The exact RPC field names are defined in the related API specifications.

---

# 11. Relationship to Other V3 Documents

| Document | Relationship |
|---|---|
| `pqc-and-blockchain-introduction.md` | Why PQC migration is needed |
| `pqc-account-spec.md` | PQC account fields and authorization metadata |
| `transaction-spec.md` | Transaction fields and authorization submission |
| `citizen-protocol-spec.md` | Nick, RefCode, Referrer, Link, Citizen runtime rules |
| `cad-overview.md` | Visual explanation of CAD |
| `cad-spec.md` | Formal CAD commitment model |

---

# 12. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-15 | Initial draft based too closely on the legacy SymID identity hierarchy |
| v0.2 | 2026-05-15 | Rewritten for the quantum-resistant V3 architecture: one key pair maps to one SymID, ChainID replaces CA ID as the public chain-binding concept, issuer-hierarchy assumptions are removed, and PQC account/CAD/Citizen Protocol relationships are defined |
| v0.3 | 2026-05-15 | Rewritten around the concrete V3 SymID derivation policy: public key hashing, last-8-byte public-key-hash component, explicit invalid-input rejection, and unified ECDSA/PQC derivation rules |
| v0.4 | 2026-05-15 | Simplified the public SymID description to `Version + ChainID + PublicKeyHash (last 8 bytes) → SymID` and removed internal construction-function references from the public specification |
| v0.5 | 2026-05-15 | Added publicly reproducible SymID field sizes and hash rule: `Version = 2 bits`, `ChainID = 14 bits`, and `PublicKeyHash = last 8 bytes of SHA3-256(public key)` |
| v0.6 | 2026-05-15 | Clarified that SymID is used as the chain-bound unique account key across account lookup, transaction identification, Citizen registration, and verification flows |

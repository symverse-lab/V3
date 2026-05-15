# SymVerse V3 SymID Specification

> **Status:** Draft v0.2  
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
It is also bound to the target chain through:

```text
ChainID
```

The purpose is to prevent ambiguity across chains and to ensure that a SymID is interpreted in the proper SymVerse network context.

Conceptually:

```text
SymID = Derive(
  ChainID,
  PublicKey,
  Algorithm Context
)
```

The public specification uses the term:

```text
ChainID
```

for this chain-binding component.

---

## 2.3 Algorithm-Aware Identity

SymVerse V3 is designed for quantum-resistant blockchain operation.

A SymID may be associated with:

- legacy ECDSA authorization,
- ML-DSA authorization,
- future standardized PQC authorization schemes.

The signature algorithm itself is not expressed only by the visible SymID string.  
Instead, the broader V3 account model combines:

| Layer | Role |
|---|---|
| SymID | Account identifier |
| Account authorization metadata | Signature algorithm and public-key material |
| Transaction authorization | Signature validation |
| CAD | Consensus authorization commitment after CADFork |

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
PrivateKey → PublicKey → SymID
```

with chain binding:

```text
(ChainID, PublicKey) → SymID
```

This means:

1. a new key pair creates a new SymID,
2. changing the key pair creates a different SymID,
3. SymID is not a sequence under a shared Citizen ID,
4. SymID is not a CA-issued serial-account number.

---

## 3.2 One SymID Per Key Pair

A SymID is uniquely tied to its cryptographic account key pair.

| Case | Result |
|---|---|
| Same key pair, same ChainID | Same SymID derivation result |
| Different key pair | Different SymID |
| Same public key under a different ChainID | Different chain-bound identity context |

The protocol therefore treats SymID as a deterministic account identifier under a chain.

---

## 3.3 SymID and Account Creation

The typical V3 account creation flow is:

```text
1. Select account algorithm.
2. Generate private key and public key.
3. Select the target ChainID.
4. Derive SymID from the public-key context and ChainID.
5. Register the account/Citizen state as required by the protocol.
```

For PQC-capable accounts, the account creation flow includes post-quantum public-key material and algorithm metadata.

---

# 4. SymID Semantic Format

## 4.1 Public Semantic Composition

A V3 SymID is described semantically by the following components:

| Component | Meaning |
|---|---|
| Version | SymID format/version discriminator |
| ChainID | Chain-binding component |
| Key-derived account component | Identifier derived from public-key context |
| Algorithm context | Account authorization family used when deriving or interpreting the SymID |

Conceptually:

```text
SymID =
  Version
  + ChainID
  + Key-derived account component
```

The public specification focuses on these semantics.  
The exact binary layout and internal derivation encoding are implementation-defined by the SymVerse V3 protocol code and should remain consistent across all account creation and verification paths.

---

## 4.2 ChainID Replaces Issuer-Oriented Prefixing

V3 SymID is not organized around issuer-class ranges.

The V3 account identifier should be interpreted through:

```text
ChainID
```

rather than a CA/issuer hierarchy.

This design aligns SymID with the quantum-resistant blockchain architecture:

- accounts are derived from cryptographic keys,
- chain identity is explicit,
- Citizen registration is handled by the Citizen Protocol,
- authorization capability is handled by account metadata and transaction validation.

---

## 4.3 Visible SymID

A SymID is represented as a blockchain address string.

Representative V3-style examples may appear as:

```text
0x40058C54...
0x400A8C54...
0xC1223344...
```

The exact value depends on:

- chain context,
- key material,
- account derivation rules.

These examples illustrate that SymID is a chain/account identifier, not a user-chosen alias.

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

All SymID generation paths must derive the same SymID from the same:

- ChainID,
- public key,
- algorithm context.

The protocol must not silently substitute chain context.  
A missing or mismatched ChainID must be treated as an error in derivation-sensitive paths.

---

## 6.2 Public-Key Binding

The SymID must remain consistent with the public-key material used for authorization.

Conceptually:

```text
Verify(
  ChainID,
  PublicKey,
  SymID
) = true
```

This prevents a transaction or account record from claiming an unrelated SymID.

---

## 6.3 Sender Verification

For a transaction sender, the protocol validates that:

1. the transaction sender field contains a SymID,
2. the authorization public key corresponds to that SymID,
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

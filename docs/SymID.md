# SymVerse V3 SymID Specification

> **Status:** Draft v0.3  
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
It is bound to the target chain through:

```text
ChainID
```

The V3 derivation input is:

```text
SymIDParams {
  Version,
  ChainID
}
```

The public SymID derivation policy is:

```text
SymID = DeriveSymIDFromPub(
  PublicKeyBytes,
  Version,
  ChainID
)
```

This makes the same public key produce a SymID only within the intended chain context.

| Input | Role |
|---|---|
| `PublicKeyBytes` | Cryptographic identity source |
| `Version` | SymID format discriminator |
| `ChainID` | Chain-binding parameter |

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
PublicKeyBytes + Version + ChainID
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
PrivateKey → PublicKeyBytes → SymID
```

with chain binding:

```text
(PublicKeyBytes, Version, ChainID) → SymID
```

This means:

1. a new key pair creates a new public-key byte sequence,
2. the public-key byte sequence is hashed to derive the account component of SymID,
3. Version and ChainID are embedded through the SymID construction path,
4. SymID is not a sequence under a shared Citizen ID,
5. SymID is not a CA-issued serial-account number.

---

## 3.2 One SymID Per Key Pair and Chain Context

A SymID is uniquely tied to:

- public-key bytes,
- Version,
- ChainID.

| Case | Result |
|---|---|
| Same public key bytes, same Version, same ChainID | Same SymID derivation result |
| Different public key bytes | Different SymID derivation result |
| Same public key bytes, different ChainID | Different chain-bound SymID |
| Missing public key bytes | Invalid derivation |
| Version = `0` | Invalid derivation |
| ChainID missing | Invalid derivation |

The protocol therefore treats SymID as a deterministic account identifier under a specific chain and SymID version.

---

## 3.3 SymID and Account Creation

The typical V3 account creation flow is:

```text
1. Select the account authorization scheme.
2. Generate private key and public key bytes.
3. Set SymID Version.
4. Set target ChainID.
5. Compute SHA3-256(public key bytes).
6. Use the lower 8 bytes of the hash as the key-derived SymID component.
7. Construct SymID from Version, ChainID, and the 8-byte key-derived component.
8. Register the account/Citizen state as required by the protocol.
```

For PQC-capable accounts, the public-key bytes may be significantly larger than ECDSA public keys, but the SymID derivation policy remains the same:

```text
SHA3-256(public key bytes)
  → lower 8 bytes
  → SymID account component
```

---

# 4. SymID Derivation Format

## 4.1 Normative V3 Derivation Rule

The V3 SymID generation policy is defined by the following derivation procedure:

```text
Input:
  pub      = public key bytes
  Version  = non-zero SymID version
  ChainID  = non-empty chain identifier

Step 1:
  h = SHA3-256(pub)

Step 2:
  suffix = last 8 bytes of h

Step 3:
  SymID = SetValuesQ(
    Version,
    ChainID,
    suffix
  )
```

In implementation-oriented shorthand:

```text
SymID = SetValuesQ(
  Version,
  ChainID.Uint64(),
  SHA3-256(pub)[24:32]
)
```

---

## 4.2 Input Validation

SymID derivation MUST reject invalid derivation input.

| Condition | Result |
|---|---|
| `pub` is empty | Reject |
| `Version = 0` | Reject |
| `ChainID` is empty / nil | Reject |

These checks prevent malformed or under-specified account identifiers.

---

## 4.3 SymID Construction Components

The V3 SymID construction path combines:

| Component | Source |
|---|---|
| Version | `SymIDParams.Version` |
| ChainID | `SymIDParams.ChainID` |
| Key-derived suffix | Lower 8 bytes of `SHA3-256(public key bytes)` |

Conceptually:

```text
SymID =
  Version
  + ChainID
  + SHA3-256(pub) lower 8 bytes
```

The exact final address encoding is determined by the protocol’s `SetValuesQ(...)` construction rule.

---

## 4.4 ChainID Replaces CA-Oriented Prefixing

The V3 SymID derivation policy uses:

```text
ChainID
```

not `CaID`.

Legacy CA-style prefixing is outside the V3 derivation rule.  
All V3 account and Citizen creation paths that know the public key SHOULD use the unified public-key derivation rule described in this section.

---

## 4.5 Not Address-plus-Nonce Derivation

The V3 SymID derivation rule MUST NOT be confused with:

```text
address + nonce
```

style address derivation.

The rule is public-key-based:

```text
public key bytes
  → SHA3-256
  → lower 8 bytes
  → SymID construction with Version and ChainID
```

No external package or creation path should infer an alternative SymID derivation rule when the public key is available.

---

## 4.6 ECDSA and PQC Use the Same Derivation Policy

The same SymID derivation policy applies to:

- ECDSA public-key bytes,
- ML-DSA public-key bytes,
- future PQC public-key bytes supported by the chain.

The public-key byte content differs by algorithm.  
The SymID derivation policy does not.

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
  → ECDSA public key bytes
  → SHA3-256(pub)
  → lower 8-byte account component
  → SymID
```

ECDSA remains part of compatibility and transition handling where the protocol permits it.

---

## 5.3 PQC Account

A PQC-backed SymID uses post-quantum public-key material.

Conceptually:

```text
PQC private key
  → PQC public key bytes
  → SHA3-256(pub)
  → lower 8-byte account component
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

- public-key bytes,
- Version,
- ChainID.

The protocol must not silently substitute chain context.  
A missing or mismatched ChainID must be treated as an error in derivation-sensitive paths.

---

## 6.2 Public-Key Binding

The SymID must remain consistent with the public-key material used for authorization.

Conceptually:

```text
VerifySymID(
  pub,
  Version,
  ChainID,
  SymID
) = true
```

where verification replays the same derivation path:

```text
SHA3-256(pub)
  → lower 8 bytes
  → SetValuesQ(Version, ChainID, suffix)
```

---

## 6.3 Unified Creation Rule

Any account or Citizen creation path that already knows the public key bytes SHOULD use the unified SymID derivation rule.

This applies to:

- ECDSA account creation,
- PQC account creation,
- Citizen registration paths that carry public-key material.

The V3 policy is:

```text
Do not create a separate SymID derivation rule in another package.
Do not derive SymID from address + nonce when the public key is available.
```

---

## 6.4 Sender Verification

For a transaction sender, the protocol validates that:

1. the transaction sender field contains a SymID,
2. the authorization public key corresponds to that SymID derivation rule,
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
| v0.3 | 2026-05-15 | Rewritten around the concrete V3 SymID derivation policy: `DeriveSymIDFromPub`, `SymIDParams { Version, ChainID }`, SHA3-256 of public-key bytes, lower 8-byte hash suffix, `SetValuesQ(Version, ChainID, suffix)`, explicit invalid-input rejection, and unified ECDSA/PQC derivation rules |

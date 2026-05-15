# SymVerse V3 SymID Specification

> **Status:** Draft v0.10  
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

Version policy:

```text
Version 0 = Legacy SymID
Version 1 = PQCFork-era SymID
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

SymID is bound to the target chain through:

```text
ChainID
```

This chain-binding rule ensures that account identity is interpreted in the correct SymVerse network context.

The complete public SymID composition rule is defined in:

```text
Section 4. SymID Composition
```

---

## 2.3 Algorithm-Aware Account, Algorithm-Neutral SymID Derivation

SymVerse V3 is designed for quantum-resistant blockchain operation.

A SymID may belong to an account authorized by:

- legacy ECDSA,
- ML-DSA,
- future standardized PQC signature schemes.

However, the **SymID derivation rule itself does not take an algorithm code as a separate derivation input**.

The SymID composition rule is defined once in Section 4 and is shared across supported account types.

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

SymID is the cryptographic account identifier used by the blockchain.

The Citizen Protocol provides a user-facing convenience layer on top of SymID.  
It allows accounts to be addressed or discovered through easier public identifiers and relationship data such as:

- initial `NickName`,
- current `Nick`,
- `RefCode`,
- `Referrer`,
- `Link`,
- `LinkedBy`.

These Citizen Protocol fields improve usability, but they do not replace SymID as the blockchain’s underlying account identifier.

```text
User-facing Citizen Protocol identifiers
  → resolved to SymID-backed account identity
  → processed by the blockchain as SymID-based state
```

| Concept | Role |
|---|---|
| `SymID` | Cryptographic account identifier used by the blockchain |
| Citizen registration state | Protocol-recognized account/Citizen state attached to a SymID |
| `NickName` / `Nick` | Human-readable Citizen alias for user convenience |
| `RefCode` / `Referrer` / `Link` / `LinkedBy` | Citizen runtime relationship data built around the SymID-backed account |

In practical terms:

- a user may send coins to a `Nick`,
- a user may create a `Link` using another Citizen’s `Nick`,
- a user may register a `Referrer` using a `RefCode`,

but after resolution, the blockchain state transition is performed against the corresponding **SymID-backed account**.

---

# 3. SymID Identity Model

## 3.1 V3 Identity Relationship

The V3 identity model is:

```text
PrivateKey → PublicKey → PublicKeyHash → SymID
```

This means:

1. a new key pair creates a new public key,
2. the public key contributes to the SymID account component,
3. the final SymID is chain-bound,
4. SymID is not a sequence under a shared Citizen ID,
5. SymID is not a CA-issued serial-account number.

The exact composition rule is defined in Section 4.

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

Because SymID is chain-bound and derived from public-key material, it is suitable for use as the canonical account identifier in transaction and query flows.

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
5. Derive SymID using the composition rule in Section 4.
6. Register the account/Citizen state as required by the protocol.
```

The same account-identity flow applies to both ECDSA and PQC-capable accounts.

---

# 4. SymID Composition

## 4.1 Public Composition Rule

The V3 SymID composition is:

```text
Version
ChainID
PublicKeyHash (last 8 bytes)
→ SymID
```

---

## 4.2 Component Definition

| Component | Size | Meaning |
|---|---:|---|
| `Version` | 2 bits | SymID version; `0` for Legacy, `1` for PQCFork-era SymID |
| `ChainID` | 14 bits | Chain identifier |
| `PublicKeyHash (last 8 bytes)` | 8 bytes | Last 8 bytes of `SHA3-256(public key)` |

This table is the canonical public definition of the V3 SymID layout.

---

## 4.3 Version Policy

The `Version` field distinguishes Legacy SymID from the PQCFork-era V3 SymID model.

| Version | Meaning |
|---:|---|
| `0` | Legacy SymID |
| `1` | PQCFork-era SymID |

After PQCFork, newly derived V3 SymIDs use:

```text
Version = 1
```

Legacy SymIDs remain identifiable through:

```text
Version = 0
```

This version separation allows the protocol and documentation to distinguish:

- pre-PQCFork Legacy SymID space,
- post-PQCFork public-key-hash-based V3 SymID space.

---

## 4.4 PublicKeyHash Rule

The `PublicKeyHash` component is derived as follows:

```text
PublicKeyHashFull = SHA3-256(public key)
PublicKeyHash     = last 8 bytes of PublicKeyHashFull
```

This rule allows any implementation to derive the same SymID from the same:

- public key,
- Version,
- ChainID.

---

## 4.5 Input Validation

SymID derivation MUST reject invalid input.

| Condition | Result |
|---|---|
| Public key is empty | Reject |
| Version is outside the supported version policy | Reject |
| `ChainID` is empty | Reject |

---

## 4.6 Not Address-plus-Nonce Derivation

The V3 SymID derivation rule MUST NOT be confused with:

```text
address + nonce
```

style address derivation.

SymID is derived from the public-key hash component together with Version and ChainID, as defined in this section.

---

## 4.7 Same Composition for ECDSA and PQC

The same SymID composition policy applies to:

- ECDSA public keys,
- ML-DSA public keys,
- future PQC public keys supported by the chain.

The public-key material differs by algorithm.  
The SymID composition does not.

---

# 5. Legacy SymID Examples

## 5.1 Why Legacy Examples Are Included

Version `0` represents the Legacy SymID space.

The V3 specification uses a new PQCFork-era SymID interpretation for Version `1`, but Legacy SymIDs remain important for:

- historical chain compatibility,
- existing reserved/system address recognition,
- migration documentation,
- transaction and explorer interpretation.

---

## 5.2 Representative Legacy SymID Addresses

The earlier SymID documentation listed representative Legacy SymID addresses such as the following. citeturn131785view0

| Legacy Holder | Legacy SymID Example |
|---|---|
| Master CA first account | `0x0001 000000000001 0002` |
| Master CA second account | `0x0001 000000000001 0003` |
| Oracle | `0x0001 000000000002 0002` |
| Reward | `0x0001 000000000003 0002` |
| First CA first account | `0x0002 000000000001 0002` |
| First CA second account | `0x0002 000000000001 0003` |

These examples belong to the Legacy SymID interpretation and should not be confused with the Version `1` PQCFork-era SymID composition rule.

---

# 6. Authorization Metadata Associated with SymID

## 6.1 Account Authorization Model

A SymID-linked account carries authorization metadata used for transaction validation.

In V3, relevant authorization concepts include:

| Field / Concept | Meaning |
|---|---|
| Public-key hash or account public-key reference | Legacy account verification context |
| `QAlgo` | PQC algorithm identifier |
| `QKeyPub` | PQC public key |
| Account status / role fields | Protocol-defined account metadata |

---

## 6.2 ECDSA Account

An ECDSA-backed SymID uses classical signature authorization.

Its account identity follows the same SymID composition rule defined in Section 4.

ECDSA remains part of compatibility and transition handling where the protocol permits it.

---

## 6.3 PQC Account

A PQC-backed SymID uses post-quantum public-key material.

Its account identity follows the same SymID composition rule defined in Section 4.

For ML-DSA accounts, the account metadata includes:

- `QAlgo`
- `QKeyPub`

The SymID account identifier and the PQC authorization metadata together form the V3 post-quantum account model.

---

## 6.4 ECDSA and ML-DSA Follow the Same SymID Rule

SymID composition does not depend on whether the account uses:

- ECDSA,
- ML-DSA,
- or another supported signature scheme.

What matters for SymID creation is that the account has a public key.

Both ECDSA and ML-DSA accounts follow the same SymID composition path:

```text
PublicKey
  → SHA3-256(public key)
  → last 8 bytes
  → PublicKeyHash component of SymID
```

Therefore:

```text
ECDSA public key
  → SymID composition rule applies

ML-DSA public key
  → SymID composition rule applies
```

The public-key format and size differ by algorithm, but the SymID construction principle remains identical:

```text
Version
ChainID
PublicKeyHash (last 8 bytes)
→ SymID
```

This ensures that both legacy-compatible and post-quantum accounts can use the same SymID identity framework.

---

# 7. SymID Derivation and Verification

## 7.1 Derivation Consistency

All SymID generation paths MUST derive the same SymID from the same:

- public key,
- Version,
- ChainID.

For PQCFork-era V3 SymID generation:

```text
Version = 1
```

The canonical composition rule is defined in Section 4.

A missing or mismatched ChainID must be treated as an error in derivation-sensitive paths.

---

## 7.2 Public-Key Binding

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

Verification applies the composition rule defined in Section 4.

---

## 7.3 Unified Creation Rule

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

## 7.4 Sender Verification

For a transaction sender, the protocol validates that:

1. the transaction sender field contains a SymID,
2. the authorization public key corresponds to that SymID composition rule,
3. the signature satisfies the account’s algorithm rules.

For PQC accounts, this includes validation against:

- `QAlgo`,
- `QKeyPub`,
- the transaction’s post-quantum signature evidence where applicable.

---

# 8. SymID and Citizen Registration

## 8.1 SymID Before Citizen Registration

A key pair may be generated before Citizen registration is completed.

The generated SymID identifies the account candidate, but protocol-visible Citizen state becomes active only after the required Citizen registration flow is confirmed.

---

## 8.2 Citizen Registration Binding

Citizen registration binds protocol state to the SymID.

This may include:

- SymID/account activation,
- authorization metadata,
- optional initial `NickName`,
- future account/Citizen queryability.

---

## 8.3 Citizen Convenience Identifiers Resolve to SymID

Citizen Protocol identifiers are designed for user convenience.

| Identifier | User-Facing Purpose | Blockchain-Level Result |
|---|---|---|
| Initial `NickName` | Optional alias assigned during Citizen registration | Associated with the Citizen’s SymID-backed account |
| Current `Nick` | Human-readable public alias | Resolves to the SymID-backed account |
| `RefCode` | Easy referral input | Resolves to a Citizen relationship involving SymID-backed accounts |
| `Link` / `LinkedBy` | Human-friendly relation surface | Stored and interpreted as Citizen runtime state attached to SymID-backed accounts |

This means:

```text
Nick-based convenience
  does not replace
SymID-based blockchain identity.
```

For example:

```text
SendTransactionToNick(to = "target01")
```

is user-facing Nick convenience.  
The transaction ultimately targets the account resolved from that Nick and is processed in the blockchain’s SymID account model.

---

# 9. SymID and CADFork

## 9.1 Why SymID Matters for Quantum-Resistant Accounts

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

## 9.2 CADFork Direction

After CADFork:

- SymVerse can support PQC accounts without binding consensus commitment size directly to raw PQC signature size,
- SymID remains the account identity anchor,
- stronger PQC algorithms such as ML-DSA-87 can be recommended as the default direction.

---

# 10. SymID Compared with Nick

## 10.1 SymID

SymID is:

- cryptographic,
- deterministic under Version, ChainID, and public-key-hash composition,
- used in blockchain account and transaction processing,
- not user-selected,
- the underlying unique account key.

---

## 10.2 Nick

Nick is:

- human-readable,
- globally unique within the Citizen Protocol domain,
- user-facing,
- usable for lookup, direct transfer convenience, and Citizen relation operations.

Nick is not the blockchain’s underlying account key.  
It is a convenience identifier that resolves to a SymID-backed account.

---

## 10.3 Comparison Table

| Property | SymID | Nick |
|---|---|---|
| Derived from key material | Yes | No |
| Chain-bound | Yes | Indirectly, through Citizen Protocol state |
| User-chosen | No | Yes, subject to validation |
| Used for blockchain account processing | Yes | No, it resolves to SymID-backed state |
| Used for transfer input | Yes | Yes, as a convenience input that resolves to the target account |
| May be changed | No | Yes, through Citizen Protocol rules |

---

# 11. Public Query Expectations

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

# 12. Relationship to Other V3 Documents

| Document | Relationship |
|---|---|
| `pqc-and-blockchain-introduction.md` | Why PQC migration is needed |
| `pqc-account-spec.md` | PQC account fields and authorization metadata |
| `transaction-spec.md` | Transaction fields and authorization submission |
| `citizen-protocol-spec.md` | Nick, RefCode, Referrer, Link, Citizen runtime rules |
| `cad-overview.md` | Visual explanation of CAD |
| `cad-spec.md` | Formal CAD commitment model |

---

# 13. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-15 | Initial draft based too closely on the legacy SymID identity hierarchy |
| v0.2 | 2026-05-15 | Rewritten for the quantum-resistant V3 architecture: one key pair maps to one SymID, ChainID replaces CA ID as the public chain-binding concept, issuer-hierarchy assumptions are removed, and PQC account/CAD/Citizen Protocol relationships are defined |
| v0.3 | 2026-05-15 | Rewritten around the concrete V3 SymID derivation policy: public key hashing, last-8-byte public-key-hash component, explicit invalid-input rejection, and unified ECDSA/PQC derivation rules |
| v0.4 | 2026-05-15 | Simplified the public SymID description to `Version + ChainID + PublicKeyHash (last 8 bytes) → SymID` and removed internal construction-function references from the public specification |
| v0.5 | 2026-05-15 | Added publicly reproducible SymID field sizes and hash rule: `Version = 2 bits`, `ChainID = 14 bits`, and `PublicKeyHash = last 8 bytes of SHA3-256(public key)` |
| v0.6 | 2026-05-15 | Clarified that SymID is used as the chain-bound unique account key across account lookup, transaction identification, Citizen registration, and verification flows |
| v0.7 | 2026-05-15 | Reduced repeated SymID layout descriptions by keeping the canonical component table and hash rule in Section 4, with later sections referring back to that single definition for a more concise specification flow |
| v0.8 | 2026-05-15 | Added the SymID version policy (`0 = Legacy`, `1 = PQCFork-era SymID`), clarified post-PQCFork Version `1` usage, and added representative Legacy SymID/system-address examples from the earlier SymID documentation |
| v0.9 | 2026-05-15 | Replaced the algorithm-code example table with a clearer rule: ECDSA and ML-DSA both provide public keys, and both follow the same SymID composition path using the last 8 bytes of `SHA3-256(public key)` |
| v0.10 | 2026-05-15 | Removed non-V3 legacy prefix commentary and clarified that Citizen Protocol identifiers such as Nick, RefCode, Referrer, and Link are user-facing convenience layers that resolve to and operate over underlying SymID-backed blockchain accounts |

# SymVerse V3 SymID Specification

> **Status:** Draft v0.1  
> **Date:** 2026-05-15  
> **Document Role:** Public specification for SymID identity structure, Citizen identity binding, account information, issuer model, and V3 post-quantum extension direction  
> **Source Basis:** Existing SymVerse `SymID` documentation, revised for the SymVerse V3 documentation set

---

# 1. Overview

**SymID** is the identity foundation of the SymVerse blockchain.

A SymID provides:

- a blockchain-native identity identifier,
- an account identifier usable in transactions,
- a link between Citizen identity and account issuance,
- issuer-driven identity assurance,
- a structured basis for public, trusted, and anonymous participation,
- a protocol foundation that can evolve toward post-quantum authorization in SymVerse V3.

SymID is not merely a wallet address.  
It is a compact identity-oriented blockchain account scheme that combines:

1. Citizen identity context,
2. issuer context,
3. account sequencing,
4. account verification metadata,
5. transaction usability.

---

# 2. Core Concept

## 2.1 User, Citizen ID, and SymID

SymVerse distinguishes:

| Concept | Meaning |
|---|---|
| **User** | The real or logical participant |
| **Citizen ID** | Identity root assigned to the participant |
| **SymID** | Account-level identifier issued under a Citizen ID |

The basic relationship is:

```text
User : Citizen ID : SymID = 1 : 1 : N
```

This means:

- one user owns one Citizen ID,
- one Citizen ID may issue multiple SymIDs,
- each SymID can serve as a distinct account identity in the blockchain.

---

## 2.2 SymID as an Account Identifier

A SymID contains account-level identity information and can be used as:

- a blockchain account identifier,
- a transaction sender or receiver identifier,
- a service verification identifier,
- an identity reference in issuer and Citizen workflows.

For ordinary users, SymID functions as the practical identity/account number used in blockchain activity.

---

# 3. SymID Characteristics

SymID is designed around the following principles.

| Characteristic | Meaning |
|---|---|
| **Existence** | Proves that a protocol-recognized identity exists |
| **Decentralization** | Reduces reliance on a single centralized identity authority |
| **Self-Sovereignty** | Supports user control over identity-linked account usage |
| **Privacy** | Allows different identity-verification strengths without indiscriminate data disclosure |
| **Security** | Relies on cryptographic proof and issuer verification |
| **Interoperability** | Enables ID-based services to integrate with the SymVerse blockchain |
| **Portability** | Allows SymID to be used across systems that support the SymVerse identity model |
| **Extensibility** | Supports future service and network expansion |
| **Protection** | Helps define rights and responsibility among participants |
| **Persistence** | Provides a durable identity/account framework |
| **Trusted Network** | Supports issuer- and PKI-oriented trust relationships |
| **KYC / AML Flexibility** | Supports trusted or regulated participation models when needed |
| **Anonymity** | Supports anonymity-preserving issuance paths where appropriate |
| **Credential Support** | Provides a basis for certificate and eligibility models |
| **Stamp / Eligibility Model** | Allows verification of qualification without exposing unnecessary personal data |

---

# 4. SymID Format

## 4.1 High-Level Format

A SymID consists of:

1. **Version**
2. **Citizen ID**
3. **Account sequence number**

The total SymID structure preserves the compact identity model described in the original SymVerse documentation.

---

## 4.2 SymID Field Layout

| Field | Sub-field | Size | Description |
|---|---|---:|---|
| Version | — | 2 bits | SymID version number |
| Citizen ID | CA ID | 14 bits | Issuer identification code |
| Citizen ID | Random | 6 bytes | User identification component assigned by issuer |
| SeqNum | — | 2 bytes | Account sequence number under the Citizen ID |

Conceptually:

```text
SymID =
  Version
  + CA ID
  + Random
  + SeqNum
```

---

## 4.3 Citizen ID

The Citizen ID represents the identity root associated with the user.

| Component | Size | Meaning |
|---|---:|---|
| CA ID | 14 bits | Issuer classification / issuer identity |
| Random | 6 bytes | User identification component assigned by the issuer |

The Citizen ID is:

```text
CA ID + Random
```

---

## 4.4 CA ID Range

The CA ID indicates the issuer category.

| CA ID Range | Meaning |
|---|---|
| `0x0001` | Master CA |
| `0x0002 ~ 0x0400` | Trusted CA |
| `0x0401 ~ 0x0800` | Public CA |
| `0x0801 ~ 0x1400` | Anonymous CA |
| `0x1401 ~ 0xFFFF` | Reserved |

---

## 4.5 Random Component

The Random component is assigned by the issuer and identifies the Citizen under that issuer context.

| Random Value | Meaning |
|---|---|
| `0x00...01` | Issuer / CA identity |
| `0x00...02` and above | General user identity |

---

## 4.6 SeqNum

`SeqNum` is the account sequence number under the Citizen ID.

| SeqNum | Meaning |
|---:|---|
| `1` | Reserved |
| `2 ~ 9999` | Account sequence range |
| `10000` and above | Reserved |

A single Citizen ID may therefore manage multiple SymID accounts through sequence numbering.

---

# 5. SymID Examples

## 5.1 Representative SymID Forms

| Owner / Holder | SymID Example |
|---|---|
| Master CA first account | `0x0001 000000000001 0002` |
| Master CA second account | `0x0001 000000000001 0003` |
| Oracle account | `0x0001 000000000002 0002` |
| Reward account | `0x0001 000000000003 0002` |
| First CA first account | `0x0002 000000000001 0002` |
| First CA second account | `0x0002 000000000001 0003` |
| First CA first user first account | `0x0002 XXXXXXXXXXXX 0002` |
| First CA first user second account | `0x0002 XXXXXXXXXXXX 0003` |
| First CA second user first account | `0x0002 YYYYYYYYYYYY 0002` |

---

# 6. SymID Account Information

## 6.1 Account Information Model

A SymID is issued together with account information that describes authorization and status metadata.

The legacy SymID documentation describes the following core account information model:

| Field | Description |
|---|---|
| Public-key hash | Hash-derived public-key identifier |
| Role | Account role and purpose |
| Verification flag | Identity-verification strength |
| State | Current account state |
| Credit | External blockchain credit index |
| Country | Country code |
| Reference code | Issuer-defined additional information |
| Issued time | Account issuance timestamp |

---

## 6.2 Public-Key Hash

The public-key hash is used for account authorization workflows and transaction verification.

Legacy SymID documentation describes this as:

```text
Lower 20 bytes of the user’s public-key hash value
```

In SymVerse V3, the account authorization model is extended to support post-quantum account metadata while preserving the account-identity role of SymID.

---

## 6.3 Role

The Role field indicates the role and purpose of the SymID.

Representative role examples include:

| Role Value | Meaning |
|---|---|
| `0x0001` | General account |
| `0xF0F0` | Master CA |
| `0xF0F1` | CA |
| Others | Reserved or protocol-defined roles |

---

## 6.4 Verification Flag

The verification flag records the strength or method of identity verification.

| Bit | Meaning |
|---|---|
| `lsb + 0` | E-mail verified |
| `lsb + 1` | Phone number verified |
| `lsb + 2` | National ID card verified |
| `lsb + 3` | Face-to-face verification |
| `lsb + 4` | Deposit |
| `lsb + 5 ~ msb` | Reserved |

---

## 6.5 State

The State field provides the current status of the SymID.

| State Value | Meaning |
|---:|---|
| `0x01` | Active |
| `0x02` | Revoked |
| `0x03` | Locked |
| `0x04` | Holding |
| `0x05` | Marked |

---

## 6.6 Credit

Credit refers to an external or protocol-associated trust/credit index attached to SymID use cases.

In the V3 documentation set, this field should be kept conceptually distinct from the **Citizen Protocol Credit** accumulated through Citizen runtime operations.

| Credit Concept | Location in V3 Docs |
|---|---|
| SymID account credit | SymID account metadata |
| Citizen runtime credit | Citizen Protocol activity score |

---

## 6.7 Reference Code

The reference code provides issuer-defined additional information at SymID issuance time.

This legacy reference code is separate from the **Citizen Protocol RefCode** described in the Citizen Protocol Specification.

| Term | Meaning |
|---|---|
| SymID reference code | Issuer-defined account metadata |
| Citizen Protocol RefCode | Citizen referral code used for Referrer operations |

---

## 6.8 Issued Time

The issued-time field records the creation time of the SymID account.

The legacy description uses a date/time representation in the order:

```text
YYYY MM DD HH MM SS
```

---

# 7. SymVerse V3 Extension — PQC-Aware SymID Account

## 7.1 Motivation

SymVerse V3 extends the account model to support post-quantum authorization.

A V3 SymID-linked account may include additional quantum-safe authorization metadata such as:

| Field | Meaning |
|---|---|
| `QAlgo` | Post-quantum signature algorithm identifier |
| `QKeyPub` | Post-quantum public key material |

These fields allow SymID-linked accounts to participate in the V3 post-quantum transaction model.

---

## 7.2 Relationship to PQC Account Specification

The detailed structure and policy of PQC account metadata are defined in:

```text
pqc-account-spec.md
```

The SymID specification only establishes the architectural relationship:

```text
SymID account identity
  + PQC authorization metadata
  = V3-compatible post-quantum account model
```

---

## 7.3 SymID and CADFork

After CADFork, SymVerse V3 can support stronger post-quantum authorization while preserving the identity role of SymID.

The V3 documentation direction is:

- SymID remains the account/identity framework,
- PQC metadata defines cryptographic authorization capability,
- CAD defines how authorization results are committed without binding consensus cost directly to raw PQC signature size.

---

# 8. Relationship to Citizen Protocol

## 8.1 SymID and Citizen

SymID and Citizen are closely related but serve different protocol roles.

| Concept | Role |
|---|---|
| **Citizen** | Protocol-level identity participant |
| **Citizen ID** | Identity root linked to issuer context |
| **SymID** | Account identifier issued under the Citizen identity root |
| **Nick** | Human-readable Citizen alias defined by Citizen Protocol |

---

## 8.2 SymID vs Nick

SymID is a compact account identifier.  
Nick is a human-readable Citizen alias.

| Identifier | Primary Use |
|---|---|
| SymID | Account identity and transaction participation |
| Nick | Public Citizen lookup, Link target, and Nick-based transfer destination |

A Citizen may therefore be exposed through:

- SymID-based account representation,
- Nick-based human-readable representation.

---

## 8.3 Citizen Protocol Separation

The following are defined outside the SymID structure and belong to the Citizen Protocol Specification:

- Initial `NickName`
- Current `Nick`
- Citizen `RefCode`
- Referrer relation
- Link / LinkedBy relation
- Citizen runtime Credit
- Nick-based direct transfer APIs

This separation keeps:

- SymID identity/account structure clear,
- Citizen social/runtime identity behavior separate,
- transaction and API documentation modular.

---

# 9. Issuer Model

## 9.1 Issuer Roles

The SymID model supports several issuer classes.

| Issuer Type | Description |
|---|---|
| Master CA | Root issuer / issuer governance authority |
| Trusted CA | Stronger identity-verification issuer |
| Public CA | Lower-identification or limited-disclosure issuer |
| Anonymous CA | Issuer supporting anonymity-preserving participation |

---

## 9.2 Trusted, Public, and Anonymous Participation

| Issuer Type | Intended Service Context |
|---|---|
| Trusted CA | Services requiring stronger assurance, e.g. age, tax, country-specific access |
| Public CA | Services requiring limited identification |
| Anonymous CA | Services prioritizing anonymous participation |

SymID allows different service environments to choose appropriate identity assurance levels.

---

# 10. System Configuration

## 10.1 Major Participants

The SymID ecosystem includes:

| Participant | Role |
|---|---|
| User | Requests and uses SymID |
| Issuer | Issues SymID under its authorization scope |
| SymID ledger | Records and verifies SymID-related blockchain state |
| Privacy repository | Stores personal data outside the blockchain where needed |

---

## 10.2 SymID Ledger and Main-Block Distinction

The original SymID model distinguishes:

| Data Area | Purpose |
|---|---|
| SymID ledger / Citizen-Block | Records and verifies SymID-related identity information |
| Main-Block | Stores transaction details |

This distinction remains important in understanding how identity data and transaction data coexist in the SymVerse architecture.

---

## 10.3 Privacy Repository

Personal information used for issuance may be stored outside the blockchain in a separate privacy repository.

The blockchain records the protocol identity state, while sensitive off-chain identity material can remain outside the distributed ledger.

---

# 11. Services Using SymID

## 11.1 Trusted Service

A trusted service can require SymID issued under stronger verification by a trusted issuer.

This may be used for services involving:

- country-specific access,
- age restrictions,
- tax-sensitive operations,
- stronger accountability requirements.

---

## 11.2 Public Service

A public service can operate with lower-identification or minimal-information SymID issuance.

This supports use cases where:

- full identity disclosure is unnecessary,
- limited service verification is sufficient.

---

## 11.3 Anonymous Service

An anonymous service can use SymID issued by an anonymous issuer.

This supports:

- privacy-preserving participation,
- economic activity without unnecessary personal-data exposure,
- anonymous service access where appropriate.

---

# 12. SymID Issuance Flow

## 12.1 Issuance Procedure

A high-level issuance flow is:

```text
1. User generates cryptographic key material.
2. User authenticates with the issuer.
3. User provides required issuance data and public authorization material.
4. Issuer creates and records the SymID account.
5. User verifies that the SymID was recorded correctly.
```

---

## 12.2 V3 Issuance Extension

For a V3 PQC-aware account, the issuance flow may also include:

- post-quantum algorithm selection,
- post-quantum public-key material,
- account metadata needed for PQC transaction authorization.

The exact V3 PQC account fields are defined in the PQC Account Specification.

---

# 13. SymID Verification Flow

## 13.1 Verification Procedure

A high-level verification flow is:

```text
1. User and service provider perform mutual identity-aware interaction.
2. Service provider reviews the SymID verification context.
3. Service provider decides whether service access is permitted.
```

---

## 13.2 Public Information and Selective Verification

The SymID model is intended to support identity-aware service decisions while avoiding unnecessary disclosure of personal information.

Service providers may evaluate:

- issuer class,
- verification flag,
- account state,
- service-specific requirements.

---

# 14. Relationship to Other V3 Documents

| Document | Relationship |
|---|---|
| `pqc-and-blockchain-introduction.md` | Background on PQC and blockchain risk |
| `pqc-account-spec.md` | Detailed PQC account metadata |
| `transaction-spec.md` | Transaction fields and submission models |
| `citizen-protocol-spec.md` | Nick, RefCode, Referrer, Link, Citizen runtime rules |
| `cad-overview.md` | Visual explanation of CAD |
| `cad-spec.md` | Formal CAD commitment model |

---

# 15. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-15 | Initial SymVerse V3 SymID specification drafted from the existing SymID documentation and extended with V3 PQC-account, Citizen Protocol, and CADFork positioning |

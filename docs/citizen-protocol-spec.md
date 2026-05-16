# SymVerse V3 Citizen Protocol Specification

> **Status:** Draft v0.3  
> **Date:** 2026-05-16  
> **Document Role:** Public specification for Citizen identity aliases, referral relations, link relations, and Citizen runtime transactions in SymVerse V3

---

# 1. Overview

The **SymVerse V3 Citizen Protocol** defines the user-facing Citizen identity and relationship layer built on top of SymID-based blockchain accounts.

The blockchain itself operates on **SymID-backed accounts**.  
The Citizen Protocol adds a convenient public layer for:

- human-readable Citizen aliases,
- referral relationships,
- link relationships,
- Nick-based lookup and transfer flows.

The Citizen Protocol is designed to improve usability without replacing SymID as the underlying blockchain account identifier.

```text
Citizen-facing identifier or relation
  → resolved to a SymID-backed account
  → processed by the blockchain
```

---

# 2. Core Concepts

| Concept | Meaning |
|---|---|
| `SymID` | Underlying blockchain account identifier |
| `Citizen` | Protocol-recognized user/account state associated with a SymID |
| `NickName` | Initial alias declared at first Citizen registration |
| `Nick` | Current active public alias used after registration |
| `RefCode` | Referral code owned by a Citizen |
| `Referrer` | Citizen relationship created from another Citizen’s RefCode |
| `Link` | Outgoing Citizen relation created toward another Citizen |
| `LinkedBy` | Reverse relation showing Citizens that linked to the current Citizen |

---

# 3. NickName and Nick

## 3.1 Terminology Rule

The specification distinguishes:

| Term | Use |
|---|---|
| `NickName` | Initial alias provided during first Citizen registration |
| `Nick` | Current live alias used after registration |

After the initial Citizen registration flow, the protocol uses:

```text
Nick
```

as the standard runtime term.

For example:

```text
DeleteNick
CreateNick
```

are runtime actions.

The term `NickName` should be reserved for the initial Citizen registration context.

---

## 3.2 Nick as a Unique Citizen Key

A Nick is a human-readable public Citizen key.

It is:

- globally unique within the Citizen Protocol domain,
- resolvable to one Citizen/SymID-backed account,
- usable for lookup and direct transfer convenience,
- changeable only through explicit Citizen runtime operations.

```text
Nick → Citizen account → SymID-backed blockchain account
```

---

# 4. Nick Format Policy

## 4.1 Length

| Rule | Value |
|---|---:|
| Minimum length | 6 characters |
| Maximum length | 32 characters |

---

## 4.2 Allowed Characters

A Nick uses a DNS-style label rule.

Allowed:

- lowercase ASCII letters: `a-z`
- digits: `0-9`
- hyphen: `-`

Normalization:

- leading and trailing spaces are removed,
- letters are normalized to lowercase.

---

## 4.3 Rejected Forms

The following forms are invalid:

| Invalid Form | Example |
|---|---|
| Fewer than 6 characters | `abc12` |
| More than 32 characters | 33+ characters |
| Starts with hyphen | `-target01` |
| Ends with hyphen | `target01-` |
| Contains underscore | `target_01` |
| Contains dot | `target.01` |
| Contains internal space | `target 01` |
| Contains Korean or non-ASCII characters | `테스트01` |
| Contains other special characters | `target@01` |

---

## 4.4 Practical Regex

A practical validation expression is:

```regex
^[a-z0-9](?:[a-z0-9-]{4,30})[a-z0-9]$
```

This expression enforces:

- 6 to 32 characters,
- lowercase letters, digits, and hyphens only,
- no leading hyphen,
- no trailing hyphen.

---

# 5. Nick Lifecycle

## 5.1 Initial Registration

During first Citizen registration:

```text
NickName
```

may be supplied as the initial public alias.

Once the Citizen exists, the active public alias is referred to as:

```text
Nick
```

---

## 5.2 Runtime Change Process

A Nick is not overwritten in a single operation.

The change process is:

```text
DeleteNick
  → CreateNick
```

This ensures that alias ownership transitions remain explicit and deterministic.

---

## 5.3 Gas Fee

Citizen runtime operations consume blockchain gas.

This includes:

- `CreateNick`
- `DeleteNick`
- Referrer operations
- Link operations

---

# 6. Nick-Based Coin Transfer

The Citizen Protocol allows direct transfer using a Nick instead of a raw address-oriented user input.

## 6.1 Standard Transaction to Nick

```text
SendTransactionToNick(
  from = addrA,
  to = "target01",
  value = 100 SYM
)
```

The Nick is resolved to the corresponding SymID-backed account before blockchain execution.

---

## 6.2 Raw Transaction to Nick

```text
signedRawTx contains:
  to = target Nick destination

SendRawTransactionToNick(
  rawTransaction = signedRawTx
)
```

The public API accepts the raw transaction and resolves the Nick target according to the Citizen Protocol rule.

---

# 7. Referrer Policy

## 7.1 Meaning

A Referrer relationship is created by using another Citizen’s `RefCode`.

```text
input RefCode
  → RefCode owner Citizen
  → Referrer relationship
```

The stored relationship points to the resolved Citizen account rather than treating the numeric RefCode itself as the final relation value.

---

## 7.2 Lifecycle

| Runtime Action | Meaning |
|---|---|
| `CreateReferrer` | Registers a Referrer relationship from an input RefCode |
| `DeleteReferrer` | Deletes the current Referrer relationship |

A Citizen may create a Referrer only when no current Referrer is registered, and may delete an existing Referrer explicitly.

---

# 8. Link Policy

## 8.1 Meaning

A Link is a Citizen-to-Citizen relation created through the Citizen Protocol.

The target Citizen is identified through a Nick-based input and resolved to the corresponding Citizen account.

```text
input Nick
  → target Citizen
  → Link relation
```

The protocol exposes the relation from both sides:

| Relation | Meaning |
|---|---|
| `Link` | Outgoing relation created by the Citizen |
| `LinkedBy` | Reverse relation visible from the target Citizen |

Example:

```text
Citizen A creates Link to Citizen B
```

Then:

```text
A.Link      includes B
B.LinkedBy  includes A
```

---

## 8.2 Lifecycle

| Runtime Action | Meaning |
|---|---|
| `CreateLink` | Creates a Link relation to the target Citizen |
| `DeleteLink` | Deletes an existing Link relation |

---

# 9. Citizen Runtime Transactions

## 9.1 Transaction Type

Citizen runtime operations are submitted as ordinary blockchain transactions with:

```text
TxTypeCitizen = 11
```

These transactions are used for:

- Nick creation and deletion,
- Referrer creation and deletion,
- Link creation and deletion.

---

## 9.2 Sender and Target Rule

For a Citizen runtime transaction:

```text
from == to
```

The transaction submitter and the affected Citizen account must match.

---

## 9.3 Gas Requirement

Citizen runtime transactions consume gas fees like other blockchain transactions.

---

# 10. Citizen Operation Codes

The current Citizen operation model is:

| Citizen Operation | Code | Meaning |
|---|---:|---|
| `CitizenOpCreateNickName` | `1` | Create the current Nick when none exists |
| `CitizenOpDeleteNickName` | `2` | Delete the current Nick |
| `CitizenOpCreateReferrer` | `11` | Create a Referrer relationship |
| `CitizenOpDeleteReferrer` | `12` | Delete the current Referrer relationship |
| `CitizenOpCreateLink` | `21` | Create a Link relation |
| `CitizenOpDeleteLink` | `22` | Delete a Link relation |

The operation family is explicitly part of the **Citizen Protocol** rather than a separate membership operation namespace.

---

# 11. Operation Semantics Summary

| Subject | Create Operation | Delete Operation |
|---|---|---|
| Nick | `CitizenOpCreateNickName` | `CitizenOpDeleteNickName` |
| Referrer | `CitizenOpCreateReferrer` | `CitizenOpDeleteReferrer` |
| Link | `CitizenOpCreateLink` | `CitizenOpDeleteLink` |

---

# 12. Public Interpretation

The Citizen Protocol exposes a usability layer over SymID-backed blockchain accounts.

| User-Facing Concept | Blockchain Interpretation |
|---|---|
| Nick lookup | Resolve Nick to Citizen/SymID-backed account |
| Nick-based transfer | Resolve Nick target, then execute account transfer |
| Referrer registration | Resolve RefCode owner and record Citizen relation |
| Link registration | Resolve target Nick and record the Citizen Link relation |
| Citizen runtime transaction | Execute `TxTypeCitizen = 11` with the relevant Citizen operation |

---

# 13. Key Rules

1. `NickName` is the initial registration term.
2. `Nick` is the runtime alias term.
3. Nick must follow the 6–32 character DNS-style label policy.
4. Nick ownership is globally unique within the Citizen Protocol domain.
5. Nick changes follow `DeleteNick → CreateNick`.
6. Nick-based coin transfer is directly supported through dedicated APIs.
7. Referrer relationships are created from RefCode input.
8. Link relations are managed through Citizen runtime operations.
9. Citizen runtime transactions use `TxTypeCitizen = 11`.
10. Citizen runtime transactions consume gas.
11. Citizen runtime transactions require `from == to`.

---

# 14. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-15 | Initial Citizen Protocol public specification |
| v0.2 | 2026-05-16 | Reframed the specification around the Citizen Protocol namespace, replaced Membership-oriented terminology with Citizen-oriented terminology, introduced `TxTypeCitizen = 11`, and aligned operation naming with `CitizenOp*` constants including Link relation operations |
| v0.3 | 2026-05-16 | Aligned relation terminology to Link, updated operation codes to `CitizenOpCreateLink = 21` and `CitizenOpDeleteLink = 22`, and documented the `Link` / `LinkedBy` relation model |

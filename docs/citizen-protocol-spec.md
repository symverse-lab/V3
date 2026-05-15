# SymVerse V3 Citizen Protocol Specification

> **Status:** Draft v0.3  
> **Date:** 2026-05-15  
> **Document Role:** Public protocol specification for Citizen identity, Citizen referral state, Citizen link relations, and externally visible Citizen operations in SymVerse V3

---

# 1. Overview

The **SymVerse V3 Citizen Protocol** defines how a Citizen identity is created, how its public Citizen properties are assigned, and how Citizen-to-Citizen relationships are managed.

The protocol covers:

- Citizen public alias management through `NickName`
- Deterministic Citizen referral codes through `RefCode`
- Referrer registration through an existing Citizen’s RefCode
- Link and LinkedBy relations between Citizens
- Credit accumulation from Citizen relationship operations
- Publicly visible query results and transaction-level operation rules

The Citizen Protocol distinguishes between:

1. **Citizen initialization**, which occurs when a Citizen is confirmed, and  
2. **Citizen relationship operations**, which occur only through explicit Citizen membership transactions.

---

# 2. Final Policy Summary

## 2.1 Automatically assigned at Citizen confirmation

When a Citizen is confirmed, the protocol may automatically assign:

| Item | Policy |
|---|---|
| `NickName` | Registered if supplied during Citizen creation |
| `RefCode` | Automatically generated and assigned |

```text
Citizen confirmation may initialize:
- NickName
- RefCode
```

---

## 2.2 Managed only by explicit Citizen membership transactions

The following are **not** created automatically when a Citizen is confirmed:

| Item | Automatic at Citizen confirmation? |
|---|---:|
| Referrer | No |
| Link | No |
| LinkedBy | No |

They are created or removed only through explicit Citizen membership operations.

```text
Citizen confirmation does not automatically initialize:
- Referrer
- Link
- LinkedBy
```

---

## 2.3 Immutable and mutable properties

| Item | Mutability |
|---|---|
| `NickName` | Can be deleted and recreated |
| `RefCode` | Immutable |
| `Referrer` | Can be deleted and recreated |
| `Link` | Multiple links may be added and removed individually |
| `Credit` | Increases when membership operations succeed |

---

# 3. Terminology

| Term | Meaning |
|---|---|
| **Citizen** | Protocol-level SymVerse identity |
| **NickName** | Public alias owned by a Citizen |
| **RefCode** | Citizen-owned deterministic referral code |
| **inputRefCode** | RefCode supplied when registering a Referrer |
| **Referrer** | Citizen address resolved from `inputRefCode` |
| **Link** | Relationship created from one Citizen to another by target NickName |
| **LinkedBy** | Reverse view showing which Citizens linked the target |
| **Credit** | Citizen relationship activity score |
| **Citizen membership transaction** | Transaction that creates or deletes NickName, Referrer, or Link state |

---

## 3.1 Important distinctions

### RefCode and Referrer are different objects

```text
RefCode  = numeric referral code
Referrer = address of the Citizen who owns that RefCode
```

A Citizen enters a RefCode to create a Referrer relation.  
The protocol resolves that RefCode to its owner Citizen.

---

### Link and Referrer are independent relations

A `Referrer` relation and a `Link` relation are separate.

| Relation | Input | Result |
|---|---|---|
| Referrer | RefCode | Referrer address is registered |
| Link | NickName | Link target address is registered |

---

# 4. Citizen Confirmation Policy

## 4.1 Initial Citizen properties

At Citizen confirmation, the protocol may initialize only deterministic or directly supplied Citizen-owned properties:

1. `NickName`, if supplied
2. `RefCode`, always generated

No relational state is created automatically.

---

## 4.2 Initial NickName

If a Citizen is created with a NickName, that NickName is registered as the Citizen’s initial public alias.

| Input | Result |
|---|---|
| NickName provided | Citizen begins with that NickName |
| NickName omitted or blank | Citizen begins without a NickName |

Example:

```text
Citizen A is confirmed with NickName "addra01"
→ Citizen A becomes discoverable by NickName "addra01"
```

---

## 4.3 Initial RefCode

Every confirmed Citizen receives a deterministic RefCode.

A RefCode:

- is assigned automatically,
- uniquely belongs to the Citizen,
- is not chosen by the user,
- cannot be changed or deleted by membership transaction.

---

# 5. RefCode Policy

## 5.1 Meaning

`RefCode` is the Citizen’s permanent referral code.

It is used by another Citizen when creating a Referrer relation.

---

## 5.2 Generation formula

```text
refCode = (citizenBlockNumber << 12) | citizenIndex
```

| Field | Value |
|---|---:|
| Citizen index bit width | 12 bits |
| Maximum Citizens per CitizenBlock | 4096 |
| `citizenIndex` range | `0 ~ 4095` |
| Block-number region | 52 bits |

---

## 5.3 Example RefCodes

| CitizenBlock | Index | RefCode | Full display example |
|---:|---:|---:|---|
| 1 | 0 | 4096 | `0000-0000-4096` |
| 2 | 0 | 8192 | `0000-0000-8192` |
| 2 | 1 | 8193 | `0000-0000-8193` |
| 3 | 1 | 12289 | `0000-0001-2289` |

---

## 5.4 Reverse calculation

A RefCode may be decomposed as:

```text
citizenBlockNumber = refCode >> 12
citizenIndex       = refCode & 0xfff
```

---

## 5.5 RefCode lifecycle

| Action | Allowed? |
|---|---:|
| Automatic creation at Citizen confirmation | Yes |
| Transaction create | No |
| Transaction update | No |
| Transaction delete | No |

---

## 5.6 Public display format

A RefCode may be displayed in a compact user-facing `ownCode` form.

| RefCode | Full format | Display `ownCode` |
|---:|---|---|
| 4096 | `0000-0000-4096` | `4096` |
| 8192 | `0000-0000-8192` | `8192` |
| 8193 | `0000-0000-8193` | `8193` |
| 12289 | `0000-0001-2289` | `0001-2289` |
| 100002289 | `0001-0000-2289` | `0001-0000-2289` |

---

# 6. NickName Policy

## 6.1 Meaning

`NickName` is a public alias that represents a Citizen in user-facing queries and Link operations.

---

## 6.2 Citizen creation with NickName

| Case | Result |
|---|---|
| Citizen is created with a valid NickName | NickName is initially registered |
| Citizen is created without a NickName | Citizen has no initial NickName |

---

## 6.3 CreateNickName

`CreateNickName` is allowed only if the Citizen currently has no NickName.

| Current State | Operation Result |
|---|---|
| `NO_NICK` | Allowed |
| `HAS_NICK` | Rejected |

---

## 6.4 DeleteNickName

`DeleteNickName` is allowed only if the Citizen currently has a NickName.

| Current State | Operation Result |
|---|---|
| `HAS_NICK` | Allowed |
| `NO_NICK` | Rejected |

---

## 6.5 NickName change policy

NickName does not support direct update.

A NickName change is performed as:

```text
DeleteNickName → CreateNickName
```

State transitions:

```text
NO_NICK  --CreateNickName--> HAS_NICK
HAS_NICK --DeleteNickName--> NO_NICK
HAS_NICK --CreateNickName--> reject
NO_NICK  --DeleteNickName--> reject
```

---

## 6.6 NickName validation

Input NickName values follow the same normalization and validation policy:

| Rule | Policy |
|---|---|
| Trim | Leading and trailing spaces removed |
| Normalize | Lowercase |
| Minimum length | 6 characters |
| Empty string | Rejected |

Validation applies to:

- `CreateNickName`
- `CreateLink`
- `DeleteLink`

`DeleteNickName` refers to the Citizen’s current NickName and therefore does not require a NickName input.

---

# 7. Referrer Policy

## 7.1 Meaning

A `Referrer` is created by entering another Citizen’s RefCode.

Resolution model:

```text
inputRefCode
  → RefCode owner
  → Referrer address
```

Example:

```text
Citizen T owns RefCode 12290

Citizen A submits CreateReferrer with RefCode 12290

Result:
Citizen A.referrer = Citizen T
```

---

## 7.2 Citizen confirmation does not create Referrer

Referrer is **not** automatically created during Citizen confirmation.

It is created only through:

```text
CreateReferrer
```

---

## 7.3 CreateReferrer

`CreateReferrer` is allowed only if the Citizen currently has no Referrer.

| Current State | Operation Result |
|---|---|
| `NO_PARENT` | Allowed |
| `HAS_PARENT` | Rejected |

Validation:

| Condition | Result |
|---|---|
| `inputRefCode = 0` | Reject |
| input RefCode has no owner | Reject |
| RefCode owner equals sender | Reject recommended |
| sender already has a Referrer | Reject |

---

## 7.4 DeleteReferrer

`DeleteReferrer` is allowed only if a Referrer currently exists.

| Current State | Operation Result |
|---|---|
| `HAS_PARENT` | Allowed |
| `NO_PARENT` | Rejected |

---

## 7.5 Referrer change policy

Referrer does not support direct update.

A Referrer change is performed as:

```text
DeleteReferrer → CreateReferrer
```

State transitions:

```text
NO_PARENT  --CreateReferrer--> HAS_PARENT
HAS_PARENT --DeleteReferrer--> NO_PARENT
HAS_PARENT --CreateReferrer--> reject
NO_PARENT  --DeleteReferrer--> reject
```

---

# 8. Link and LinkedBy Policy

## 8.1 Meaning

A `Link` is a directional Citizen relationship created by target NickName.

Example:

```text
Citizen A links target NickName "target01"
```

If `target01` belongs to Citizen T:

```text
A → T
```

Then:

- Citizen A shows T in `links`
- Citizen T shows A in `linkedBy`

---

## 8.2 Multiple Link policy

A Citizen may maintain multiple Link relations.

| Rule | Policy |
|---|---|
| Multiple targets | Allowed |
| Self-link | Rejected |
| Duplicate Link to same target | Rejected |

Example:

```text
A links B
A links C
A links D
```

---

## 8.3 CreateLink

Input:

```text
target NickName
```

Validation:

| Condition | Result |
|---|---|
| target NickName empty | Reject |
| target NickName owner missing | Reject |
| target is sender | Reject |
| same target already linked | Reject |
| sender already has another Link | Allowed |

---

## 8.4 DeleteLink

Input:

```text
target NickName
```

Validation:

| Condition | Result |
|---|---|
| target NickName empty | Reject |
| target NickName owner missing | Reject |
| Link relation missing | Reject |
| Link relation exists | Allowed |

---

## 8.5 LinkedBy reverse view

If:

```text
Citizen A links Citizen T
```

then public query results must be able to express:

```text
Citizen A.links includes Citizen T
Citizen T.linkedBy includes Citizen A
```

This reverse relation is part of the externally visible Citizen Protocol behavior.

---

# 9. Credit Policy

## 9.1 Meaning

`Credit` is the Citizen relationship activity score.

---

## 9.2 Credit increments

A successful Citizen membership operation increases the sender’s Credit by `+1`.

| Operation | Credit Change |
|---|---:|
| `CreateNickName` | `+1` |
| `DeleteNickName` | `+1` |
| `CreateReferrer` | `+1` |
| `DeleteReferrer` | `+1` |
| `CreateLink` | `+1` |
| `DeleteLink` | `+1` |

RefCode creation does not increase Credit because it is assigned automatically at Citizen confirmation.

---

# 10. Membership Operation Summary

| Target | Create | Delete | Change Policy |
|---|---|---|---|
| NickName | Initial Citizen creation, or `CreateNickName` when no NickName exists | `DeleteNickName` | `Delete → Create` |
| RefCode | Automatic at Citizen confirmation | Not allowed | Immutable |
| Referrer | `CreateReferrer` | `DeleteReferrer` | `Delete → Create` |
| Link | `CreateLink` by target NickName | `DeleteLink` by target NickName | Multiple Links added/removed individually |

---

# 11. External Transaction Interface

Citizen relationship updates are expressed through Citizen membership transactions.

The externally meaningful fields are:

| Field | Meaning |
|---|---|
| `Op` | Operation type |
| `Domain` | Citizen protocol domain |
| `Nick` | NickName or Link target NickName, depending on operation |
| `RefCode` | Input RefCode used to create a Referrer relation |
| `Extra` | Reserved extension field |

---

## 11.1 Field usage by operation

| Operation | `Nick` | `RefCode` | Description |
|---|---|---|---|
| `CreateNickName` | NickName to register | unused | Create Citizen NickName |
| `DeleteNickName` | unused | unused | Delete current Citizen NickName |
| `CreateReferrer` | unused | input RefCode | Register Referrer |
| `DeleteReferrer` | unused | unused | Delete current Referrer |
| `CreateLink` | target NickName | unused | Link target Citizen |
| `DeleteLink` | target NickName | unused | Remove target Link |

---

## 11.2 Supported operation set

| Operation | Status |
|---|---|
| `CreateNickName` | Supported |
| `DeleteNickName` | Supported |
| `CreateReferrer` | Supported |
| `DeleteReferrer` | Supported |
| `CreateLink` | Supported |
| `DeleteLink` | Supported |

The following are not Citizen Protocol transaction operations:

| Operation | Status |
|---|---|
| `UpdateNickName` | Not supported |
| `CreateRefCode` | Not supported |
| `UpdateRefCode` | Not supported |
| `DeleteRefCode` | Not supported |

---

# 12. Public Query Surface

The Citizen Protocol should expose Citizen state through public Citizen query APIs.

The query surface should allow clients to inspect:

| Field Group | Example Observable Values |
|---|---|
| Citizen identity | SymID / Citizen address |
| NickName | Current NickName |
| RefCode | RefCode / formatted ownCode |
| Referrer | Current Referrer address |
| Links | Citizens linked by the current Citizen |
| LinkedBy | Citizens that linked the current Citizen |
| Credit | Current Citizen Credit |

---

## 12.1 Query by Citizen identity

A client should be able to retrieve the current Citizen state by Citizen identity.

Representative result shape:

```text
Citizen
  address
  nickname
  refCode
  ownCode
  referrer
  linkCount
  links
  linkedByCount
  linkedBy
  credit
```

---

## 12.2 Query by NickName

A client should be able to resolve a valid NickName to its owning Citizen and retrieve the same public Citizen state.

Example:

```text
NickName "target01"
  → Citizen T
  → Citizen T public state
```

---

# 13. State Transition Summary

| Target | Empty State | Create | Existing State | Delete |
|---|---|---|---|---|
| NickName | `NO_NICK` | `CreateNickName` | `HAS_NICK` | `DeleteNickName` |
| Referrer | `NO_PARENT` | `CreateReferrer` | `HAS_PARENT` | `DeleteReferrer` |
| Link | `NO_LINK_RELATION` | `CreateLink` | `HAS_LINK_RELATION` | `DeleteLink` |

RefCode is not a state-transition target.

```text
RefCode = immutable deterministic code assigned at Citizen confirmation
```

---

# 14. Final Protocol Summary

## 14.1 NickName

- May be registered during Citizen confirmation.
- May be created by transaction only if no NickName exists.
- Change requires:

```text
DeleteNickName → CreateNickName
```

---

## 14.2 RefCode

- Automatically generated at Citizen confirmation.
- Formula:

```text
(CitizenBlockNumber << 12) | CitizenIndex
```

- Immutable.
- Cannot be created, updated, or deleted by membership transaction.

---

## 14.3 Referrer

- Referrer is the owner Citizen address of an input RefCode.
- Not created automatically at Citizen confirmation.
- Managed only by:

```text
CreateReferrer
DeleteReferrer
```

---

## 14.4 Link

- Input is target NickName.
- Public results expose both `links` and `linkedBy`.
- Not created automatically at Citizen confirmation.
- Managed only by:

```text
CreateLink
DeleteLink
```

---

## 14.5 Credit

- Incremented by successful Citizen membership operations.
- RefCode assignment does not grant Credit.

---

# 15. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-15 | Initial Citizen Protocol specification draft |
| v0.2 | 2026-05-15 | Expanded Citizen runtime and operation policy from internal implementation notes |
| v0.3 | 2026-05-15 | Rewritten as a public-facing protocol specification by removing internal storage layout, internal function names, block-processing insertion details, and simulator validation procedures while strengthening external operation rules and public query behavior |

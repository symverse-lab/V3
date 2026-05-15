# SymVerse V3 Citizen Protocol Specification

> **Status:** Draft v0.2  
> **Date:** 2026-05-15  
> **Document Role:** Protocol specification for Citizen registration, Citizen runtime registry, and Citizen relationship operations in SymVerse V3

---

# 1. Overview

The **SymVerse V3 Citizen Protocol** defines how Citizen identity data and Citizen runtime relationship state are managed across the protocol.

The protocol separates:

1. **Citizen registration data**, which confirms the existence and base attributes of a Citizen, and
2. **Citizen runtime registry state**, which maintains public aliases, referral codes, referrer relationships, link relationships, and membership activity credit.

This separation is intentional.

| Category | Purpose | Storage |
|---|---|---|
| Citizen base information | Citizen creation and confirmation data | `CitizenStateDB` |
| Citizen runtime registry | NickName, RefCode, Referrer, Link, LinkedBy, Credit | Main `StateDB` |

The runtime registry is stored in a protocol-reserved storage account:

```text
StateDB[CitizenRegistryAddress].storage[key] = value
```

`CitizenRegistryAddress` is not an operational user account.  
It is a protocol-reserved storage namespace used to maintain deterministic Citizen runtime state.

---

# 2. Final Policy Summary

The finalized Citizen Protocol policy is:

## 2.1 Automatically applied when a Citizen is confirmed

When a Citizen is created and attached through CitizenBlock processing, only the following deterministic Citizen-owned state is automatically initialized:

1. `CitizenV2.NickName`
   - registered through `SetMembershipNickOwner`
2. `RefCode`
   - generated deterministically and registered through `SetMembershipInitialRefCode`

```text
Citizen confirmation may initialize:
- NickName owner registry
- RefCode owner registry
```

---

## 2.2 Managed only through `TxTypeMembership`

The following runtime relationships are **not** automatically created during Citizen generation.

They are managed only through explicit `TxTypeMembership` operations:

- NickName creation and deletion after initial registration
- Referrer creation and deletion
- Link creation and deletion

```text
Citizen creation does not automatically initialize:
- Referrer
- Link
```

---

## 2.3 Deprecated / unused flow

The following initial-runtime mechanism is not used:

```text
InitialMembershipRuntimeEntry
```

The Citizen Protocol intentionally limits automatic processing during Citizen creation to deterministic self-owned index generation only.

Allowed automatic processing:

- NickName owner registration
- RefCode owner registration

Rejected automatic processing:

- automatic Referrer creation
- automatic Link creation

---

# 3. Terminology

| Term | Meaning | Type |
|---|---|---|
| **NickName** | Public Citizen alias | `string` |
| **RefCode** | Citizen-owned unique referral code | `uint64` |
| **inputRefCode** | RefCode supplied when creating a Referrer relation | `uint64` |
| **Referrer** | Address of the owner of `inputRefCode` | `common.Address` |
| **Link** | Relationship created by resolving a target NickName to an address | `common.Address` |
| **LinkedBy** | Reverse relation showing who linked the target Citizen | `common.Address` |
| **Credit** | Activity score updated by Citizen relationship operations | `uint64` |

---

## 3.1 Important Distinctions

The protocol uses the following distinctions:

```text
RefCode is a numeric code.
Referrer is an address.
```

More precisely:

- `RefCode` is a deterministic `uint64` generated for the Citizen itself.
- `Referrer` is **not** a RefCode value.
- `Referrer` is the **address** of the owner resolved from an input RefCode.

Also:

```text
Link is separate from Referrer.
```

A Link relation:

- uses **NickName** as the input,
- resolves that NickName to an address,
- stores a directional relationship from one Citizen to another.

---

# 4. Storage Policy

## 4.1 Storage Placement

| Item | Storage Location | Description |
|---|---|---|
| Citizen base information | `CitizenStateDB` | Citizen creation and confirmation data |
| NickName | Main `StateDB` | NickName runtime registry |
| RefCode | Main `StateDB` | Citizen-owned referral code |
| Referrer | Main `StateDB` | Owner address resolved from an input RefCode |
| Link | Main `StateDB` | Forward link relation |
| LinkedBy | Main `StateDB` | Reverse link index |
| Credit | Main `StateDB` | Citizen relationship activity score |

---

## 4.2 Registry Storage Namespace

Citizen runtime registry state is stored under a protocol-reserved account:

```text
StateDB[CitizenRegistryAddress].storage[key] = value
```

The reserved account is used to avoid mixing:

- Citizen confirmation data in `CitizenStateDB`
- mutable runtime index and relationship data in main `StateDB`

This separation preserves:

- deterministic state transitions,
- clean block processing responsibilities,
- sync and replay consistency.

---

# 5. Initial Citizen Runtime Policy

## 5.1 Automatic Runtime Initialization

When a `CitizenBlock` is attached to a main block, the main block processing path applies initial Citizen runtime state.

Conceptual processing flow:

```text
CitizenBlock creation / attach
  → main block header CBHash / CBNum set
  → main block generation or validation/import path
  → ApplyInitialMembershipRuntimeFromCitizenRef(...)
  → ApplyInitialMembershipRuntimeFromCitizenBlock(...)
  → NickName and RefCode are applied to main StateDB
```

---

## 5.2 Automatically Applied Items

### 5.2.1 NickName

If `CitizenV2.NickName` exists, the protocol attempts to register the initial NickName owner.

Storage layout:

```text
nick.owner(domain, nick) -> owner address
addr.nick(domain, addr)  -> nickHash
nick.text(nickHash)      -> nick
```

Reference function:

```go
SetMembershipNickOwner(statedb, domain, nickName, symID)
```

---

### 5.2.2 RefCode

A RefCode is generated deterministically using:

```text
refCode = (citizenBlockNumber << 12) | citizenIndex
```

Storage layout:

```text
refcode.owner(domain, refCode) -> owner address
addr.refcode(domain, addr)     -> own refCode
```

Reference function:

```go
SetMembershipInitialRefCode(statedb, domain, refCode, symID)
```

---

## 5.3 Items Not Automatically Applied

Citizen creation does **not** automatically create:

- Referrer relations
- Link relations

Both are intentionally excluded from initial Citizen runtime processing.

They must be created and deleted only through `TxTypeMembership`.

---

# 6. RefCode Policy

## 6.1 Meaning

`RefCode` is a deterministic referral code owned by the Citizen.

It is:

- generated automatically during Citizen confirmation,
- immutable,
- non-random,
- not user-selected,
- not transaction-created,
- not transaction-updatable,
- not transaction-deletable.

---

## 6.2 Generation Formula

```text
refCode = (citizenBlockNumber << 12) | citizenIndex
```

| Field | Value |
|---|---:|
| Citizen index bit width | 12 bits |
| Maximum Citizens per CitizenBlock | 4096 |
| `citizenIndex` range | `0 ~ 4095` |
| Block number region | 52 bits |

---

## 6.3 Example RefCodes

| CitizenBlock | Index | RefCode | Full Format Example |
|---:|---:|---:|---|
| 1 | 0 | 4096 | `0000-0000-4096` |
| 2 | 0 | 8192 | `0000-0000-8192` |
| 2 | 1 | 8193 | `0000-0000-8193` |
| 3 | 1 | 12289 | `0000-0001-2289` |

---

## 6.4 Reverse Calculation

A RefCode can be decomposed as:

```text
citizenBlockNumber = refCode >> 12
citizenIndex       = refCode & 0xfff
```

---

## 6.5 Generation Policy

| Policy Item | Rule |
|---|---|
| Creation timing | When the CitizenBlock is attached and main block processing runs |
| Generation basis | CitizenBlock number + index inside CitizenBlock |
| Transaction create | Not used |
| Transaction update | Not allowed |
| Transaction delete | Not allowed |
| Mutability | Immutable |

---

## 6.6 Display Format

The raw RefCode may be displayed in a normalized user-facing format.

| RefCode | Full Format | Display `ownCode` |
|---:|---|---|
| 4096 | `0000-0000-4096` | `4096` |
| 8192 | `0000-0000-8192` | `8192` |
| 8193 | `0000-0000-8193` | `8193` |
| 12289 | `0000-0001-2289` | `0001-2289` |
| 100002289 | `0001-0000-2289` | `0001-0000-2289` |

The display formatter may remove leading `0000` groups while preserving the underlying deterministic RefCode.

---

# 7. NickName Policy

## 7.1 Meaning

`NickName` is a public alias owned by a Citizen.

Storage layout:

```text
nick.owner(domain, nick) -> owner address
addr.nick(domain, addr)  -> nickHash
nick.text(nickHash)      -> nick
```

---

## 7.2 NickName During Citizen Creation

If `CitizenV2` contains a NickName, the protocol attempts to register the initial NickName owner.

| Input | Processing |
|---|---|
| NickName present | NickName registry is created |
| NickName blank | Citizen is created without a NickName |

Example:

```text
nick.owner(SYMVERSE, addra01) -> addrA
addr.nick(SYMVERSE, addrA)    -> hash(addra01)
nick.text(hash(addra01))      -> addra01
```

---

## 7.3 `TxTypeMembership CreateNickName`

`CreateNickName` is allowed only when the sender currently has no NickName.

| Current State | `CreateNickName` |
|---|---|
| `NO_NICK` | Allowed |
| `HAS_NICK` | Rejected |

Policy:

```text
A Citizen can create a NickName through transaction only if no NickName is currently registered.
```

---

## 7.4 `TxTypeMembership DeleteNickName`

`DeleteNickName` is allowed only when the sender currently has a NickName.

| Current State | `DeleteNickName` |
|---|---|
| `HAS_NICK` | Allowed |
| `NO_NICK` | Rejected |

Deletion removes:

```text
nick.owner(domain, currentNick)
addr.nick(domain, addr)
nick.text(hash(currentNick))
```

---

## 7.5 NickName Change Policy

NickName does not support direct update.

A NickName change is represented as:

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

## 7.6 NickName Validation

NickName is used as a registry key.  
Therefore all transaction-input NickName values are normalized before use.

| Rule | Policy |
|---|---|
| Trim | `strings.TrimSpace` |
| Normalize | `strings.ToLower` |
| Minimum length | 6 characters |
| Empty string | Not allowed |

Validation applies to:

- `CreateNickName`
- `CreateLink`
- `DeleteLink`

`DeleteNickName` deletes the current NickName from state and therefore does not require payload NickName validation.

---

## 7.7 Simulator Fixture Values

Representative fixture values:

```text
nickA  = addra01
nickA2 = bddra01
nickT  = target01
nickS  = second01
```

---

# 8. Referrer Policy

## 8.1 Meaning

`Referrer` is the owner address of an input RefCode.

Resolution flow:

```text
inputRefCode uint64
  → refcode.owner lookup
  → Referrer owner address
```

Therefore:

```text
Referrer is an address, not a numeric RefCode.
```

Example:

```text
T.RefCode = 12290
refcode.owner(SYMVERSE, 12290) -> T

A submits CreateReferrer with RefCode = 12290
addr.referrer(SYMVERSE, A) -> T
```

---

## 8.2 Citizen Creation Does Not Create Referrer

Citizen creation does not automatically initialize Referrer.

The previous idea of applying `inputRefCode` during Citizen creation is not used in the final policy.

Referrer is created only by:

```text
TxTypeMembership CreateReferrer
```

---

## 8.3 Storage Structure

```text
addr.referrer(domain, addr) -> parent owner address
```

---

## 8.4 `TxTypeMembership CreateReferrer`

`CreateReferrer` is allowed only when no Referrer currently exists.

| Current State | `CreateReferrer` |
|---|---|
| `NO_PARENT` | Allowed |
| `HAS_PARENT` | Rejected |

Processing:

```text
payload.RefCode = inputRefCode
parent = refcode.owner(domain, inputRefCode)
addr.referrer(domain, sender) = parent
```

Validation:

| Condition | Policy |
|---|---|
| `inputRefCode = 0` | Reject |
| input RefCode owner missing | Reject |
| `sender == parent` | Recommended reject |
| sender already has parent | Reject |

---

## 8.5 `TxTypeMembership DeleteReferrer`

`DeleteReferrer` is allowed only when a Referrer exists.

| Current State | `DeleteReferrer` |
|---|---|
| `HAS_PARENT` | Allowed |
| `NO_PARENT` | Rejected |

Deletion:

```text
addr.referrer(domain, sender) = empty
```

---

## 8.6 Referrer Change Policy

Referrer does not support direct update.

A Referrer change is represented as:

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

# 9. Link Policy

## 9.1 Meaning

`Link` is a NickName-based Citizen relationship created by `TxTypeMembership`.

Link terminology replaces older sponsor-oriented terminology in policy, API, and logs.

Terminology mapping:

| Legacy Internal Term | Citizen Protocol Term |
|---|---|
| Sponsor | Link |
| `sponsorCount` | `linkCount` |
| `sponsors` | `links` |
| `sponsorChildCount` | `linkedByCount` |
| `sponsorChildren` | `linkedBy` |

---

## 9.2 Input and Storage Model

The input to Link creation is:

```text
target NickName
```

Processing resolves:

```text
target NickName → target owner address
```

Then stores the relationship by address.

Example:

```text
T.NickName = target01
nick.owner(SYMVERSE, target01) -> T

A submits CreateLink("target01")
A -> T link relation is stored
```

---

## 9.3 Multiple Link Policy

A Citizen may link multiple targets.

Example:

```text
A links B
A links C
A links D
```

Rules:

- A Citizen may maintain multiple Link relations.
- Self-link is not allowed.
- Duplicate Link registration is not allowed.

---

## 9.4 Storage Structure

A Link relation is stored in both forward and reverse directions.

### Forward index

```text
addr.link.index(domain, user, target) -> 1
```

### Reverse `LinkedBy` index

```text
link.children.count(domain, target) -> count
link.children.at(domain, target, i) -> user address
link.children.index(domain, target, user) -> i + 1
```

Example:

```text
A links T

addr.link.index(SYMVERSE, A, T) -> 1

link.children.count(SYMVERSE, T) -> 1
link.children.at(SYMVERSE, T, 0) -> A
link.children.index(SYMVERSE, T, A) -> 1
```

Some internal storage key names may still retain sponsor-based implementation naming.  
However, public policy, API, documentation, and logs should use the term **Link**.

---

## 9.5 `TxTypeMembership CreateLink`

Input:

```text
payload.Nick = target NickName
```

Validation:

| Condition | Policy |
|---|---|
| target NickName empty | Reject |
| target NickName owner missing | Reject |
| target == sender | Reject |
| same target already linked | Reject |
| another Link already exists | Allowed |

Processing:

```text
1. target = nick.owner(domain, payload.Nick)
2. addr.link.index(domain, sender, target) = 1
3. sender is added to target LinkedBy reverse index
```

---

## 9.6 `TxTypeMembership DeleteLink`

Deletion also uses the target NickName.

Input:

```text
payload.Nick = target NickName
```

Validation:

| Condition | Policy |
|---|---|
| target NickName empty | Reject |
| target NickName owner missing | Reject |
| Link relation missing | Reject |
| Link relation exists | Allowed |

Processing:

```text
1. target = nick.owner(domain, payload.Nick)
2. addr.link.index(domain, sender, target) = empty
3. sender is removed from target LinkedBy reverse index
```

`LinkedBy` deletion uses a swap-last strategy to maintain compact indexed storage.

---

# 10. Credit Policy

## 10.1 Meaning

`Credit` is the Citizen relationship activity score.

Storage structure:

```text
credit(addr) -> uint64
```

---

## 10.2 Credit Increment Rules

| Action | Credit Change |
|---|---:|
| `CreateNickName` | sender `+1` |
| `DeleteNickName` | sender `+1` |
| `CreateReferrer` | sender `+1` |
| `DeleteReferrer` | sender `+1` |
| `CreateLink` | sender `+1` |
| `DeleteLink` | sender `+1` |

RefCode generation does not increase Credit because RefCode is automatically initialized during Citizen confirmation rather than created by a transaction.

---

# 11. Operation Policy Summary

| Target | Create | Delete | Change Policy |
|---|---|---|---|
| NickName | During Citizen creation, or `CreateNickName` when `NO_NICK` | `DeleteNickName` when `HAS_NICK` | `Delete → Create` |
| RefCode | Automatically generated during Citizen confirmation | Not allowed | Immutable |
| Referrer | `CreateReferrer` when `NO_PARENT` | `DeleteReferrer` when `HAS_PARENT` | `Delete → Create` |
| Link | `CreateLink` by target NickName | `DeleteLink` by target NickName | Multiple Links individually added/removed |

---

# 12. `MembershipPayload` Field Meaning

Current payload structure:

```go
type MembershipPayload struct {
    Op      MembershipOp
    Domain  string
    Nick    string
    RefCode uint64
    Sponsor common.Address
    Extra   []byte
}
```

---

## 12.1 Field Meaning by Operation

| Operation | `Nick` | `RefCode` | `Sponsor` |
|---|---|---|---|
| `CreateNickName` | NickName to set | unused | unused |
| `DeleteNickName` | unused | unused | unused |
| `CreateReferrer` | unused | input RefCode | unused |
| `DeleteReferrer` | unused | unused | unused |
| `CreateLink` | target NickName | unused | unused |
| `DeleteLink` | target NickName | unused | unused |

`Sponsor common.Address` is not used under the current Citizen Protocol policy.

---

# 13. Recommended Enum Organization

Recommended enum:

```go
type MembershipOp uint8

const (
    MembershipOpCreateNickName MembershipOp = 1
    MembershipOpDeleteNickName MembershipOp = 2

    MembershipOpCreateReferrer MembershipOp = 11
    MembershipOpDeleteReferrer MembershipOp = 12

    // Existing enum names may remain for compatibility,
    // but policy/API/log terms should use Link.
    MembershipOpCreateLink MembershipOp = 21
    MembershipOpDeleteLink MembershipOp = 22
)
```

Compatibility names may remain in code:

```go
MembershipOpCreateSponsor
MembershipOpDeleteSponsor
```

But policy terminology should use:

```text
Link
```

---

## 13.1 Unsupported / Rejected Operations

The following operations should be removed or rejected:

- `UpdateNickName`
- `CreateRefCode`
- `UpdateRefCode`
- `DeleteRefCode`

RefCode is created automatically during Citizen confirmation and is not a transaction operation.

---

# 14. State Transition Summary

| Target | Empty State | Create | Existing State | Delete |
|---|---|---|---|---|
| NickName | `NO_NICK` | `CreateNickName` | `HAS_NICK` | `DeleteNickName` |
| Referrer | `NO_PARENT` | `CreateReferrer` | `HAS_PARENT` | `DeleteReferrer` |
| Link | `NO_LINK_RELATION` | `CreateLink` | `HAS_LINK_RELATION` | `DeleteLink` |

RefCode is not a state-transition target.

```text
RefCode = immutable deterministic code generated during Citizen confirmation
```

---

# 15. Main Block Processing Integration

Initial Citizen runtime processing is not a simulator-only adjustment.  
It must be part of the canonical main-chain state transition.

---

## 15.1 Required Application Points

### 1. `GenerateChainWithBlockChain()`

Apply before:

```text
finalize.Finalize()
```

using:

```go
ApplyInitialMembershipRuntimeFromCitizenRef(...)
```

---

### 2. `StateProcessor.Process()`

Apply:

```text
after transaction loop
before finalize.Finalize()
```

using:

```go
ApplyInitialMembershipRuntimeFromCitizenRef(...)
```

---

### 3. `dminer/worker` production block generation

Apply before:

```text
finalize.Finalize()
```

using:

```go
ApplyInitialMembershipRuntimeFromCitizenRef(...)
```

---

## 15.2 CitizenStateDB Responsibility Boundary

`ApplyCitizenV2()` is responsible only for:

```text
CitizenStateDB storage
```

It must **not** directly mutate the main `StateDB` Citizen runtime registry.

The main runtime registry should be updated only through the dedicated initial-runtime application path.

---

## 15.3 Shared Functions to Keep

```go
ApplyInitialMembershipRuntimeFromCitizenRef(
    config,
    statedb,
    cichain,
    cbHash,
    cbNum,
)

ApplyInitialMembershipRuntimeFromCitizenBlock(
    config,
    statedb,
    cb,
)
```

---

## 15.4 Deprecated / Unused Functions

The following should remain removed or unused:

```text
InitialMembershipRuntimeEntry
ApplyCitizenV2InitialMembership
ApplyCitizenV2Membership
ExtractInitialMembershipRuntimeEntries
ApplyInitialMembershipRuntimeEntries
```

---

# 16. Simulator Validation Criteria

## 16.1 Initial Citizen Runtime

Expected initial state:

```text
addrN nickname(doltwo)
addrN refCode(4096 / 0000-0000-4096)
```

---

## 16.2 NickName Lifecycle

```text
addrA CreateNickName(addra01)
addrA DeleteNickName(addra01)
addrA CreateNickName(bddra01)
```

Final expected state:

```text
addrA nickname(bddra01)
```

---

## 16.3 Referrer Lifecycle

```text
addrA CreateReferrer(addrT_refcode)
addrA referrer == addrT
```

Optional delete path:

```text
DeleteReferrer
```

---

## 16.4 Link Lifecycle

```text
addrA CreateLink(target01)
addrA links contains addrT
addrT linkedBy contains addrA
```

Optional delete path:

```text
DeleteLink
```

---

## 16.5 Final Expected State for Persistent Validation Scenario

```text
addrA
  nickname(bddra01)
  referrer(addrT)
  linkCount(1)
  links([addrT])

addrT
  nickname(target01)
  linkedByCount(1)
  linkedBy([addrA])
```

---

# 17. Final Protocol Summary

## 17.1 NickName

- May be initially registered during Citizen confirmation.
- Transactional creation is allowed only when no NickName exists.
- Change requires:

```text
DeleteNickName → CreateNickName
```

---

## 17.2 RefCode

- Automatically generated during Citizen confirmation.
- Formula:

```text
(CitizenBlockNumber << 12) | CitizenIndex
```

- Immutable.
- Cannot be created, updated, or deleted by transaction.

---

## 17.3 Referrer

- Referrer is the owner address of an input RefCode.
- Not automatically created during Citizen generation.
- Managed only by:

```text
CreateReferrer
DeleteReferrer
```

---

## 17.4 Link

- Input is target NickName.
- Storage is target address-based.
- Supports forward and reverse indexes.
- Not automatically created during Citizen generation.
- Managed only by:

```text
CreateLink
DeleteLink
```

---

## 17.5 Credit

- Activity score incremented by `TxTypeMembership` actions.
- RefCode generation does not grant Credit.

---

# 18. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-15 | Initial Citizen Protocol specification draft |
| v0.2 | 2026-05-15 | Expanded into a full protocol specification covering Citizen runtime storage policy, RefCode, NickName, Referrer, Link, Credit, MembershipPayload semantics, enum guidance, block-processing integration, and simulator validation criteria |

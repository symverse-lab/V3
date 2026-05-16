# SymVerse V3 Citizen Protocol Specification

> **Status:** Draft v0.12  
> **Date:** 2026-05-15  
> **Document Role:** Public protocol specification for Citizen identity, Citizen referral state, Citizen link relations, and externally visible Citizen operations in SymVerse V3

---

# 1. Overview

The **SymVerse V3 Citizen Protocol** defines how a Citizen identity is created, how its public Citizen properties are assigned, and how Citizen-to-Citizen relationships are managed.

The protocol covers:

- Initial Citizen public identity registration through `NickName` at Citizen confirmation
- Ongoing Citizen public alias operations through `Nick`
- Nick as a globally unique Citizen lookup key
- **Nick-aware coin transfer** through `fromNick` and `toNick`, without requiring users to manually enter raw blockchain addresses
- Citizen referral codes through `RefCode`
- Referrer registration through an existing Citizen’s RefCode
- Link and LinkedBy relations created through Nick resolution
- Credit accumulation from Citizen relationship operations
- Publicly visible query results and transaction-level operation rules

The Citizen Protocol distinguishes between:

1. **Citizen initialization**, which occurs when a Citizen is confirmed, and  
2. **Citizen relationship operations**, which occur only through explicit Citizen transactions.

---

# 2. Final Policy Summary

## 2.1 Automatically assigned at Citizen confirmation

When a Citizen is confirmed, the protocol may automatically assign:

| Item | Policy |
|---|---|
| `NickName` | Registered once if supplied during initial Citizen creation |
| `RefCode` | Automatically generated and assigned |

```text
Citizen confirmation may initialize:
- NickName
- RefCode
```

---

## 2.2 Managed only by explicit Citizen transactions

The following are **not** created automatically when a Citizen is confirmed:

| Item | Automatic at Citizen confirmation? |
|---|---:|
| Referrer | No |
| Link | No |
| LinkedBy | No |

They are created or removed only through explicit Citizen Citizen operations.

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
| `Nick` | Can be deleted and recreated after initial Citizen registration |
| `RefCode` | Immutable |
| `Referrer` | Can be deleted and recreated |
| `Link` | Multiple links may be added and removed individually |
| `Credit` | Increases when Citizen operations succeed |

---

## 2.4 Gas Fee for Citizen Runtime Transactions

Citizen runtime state changes are executed as blockchain transactions and therefore consume **Gas Fee**.

This applies to operations such as:

- `CreateNick`
- `DeleteNick`
- `CreateReferrer`
- `DeleteReferrer`
- `CreateLink`
- `DeleteLink`

The exact gas cost is determined by the chain’s transaction and execution rules, but the protocol-level principle is:

```text
Any post-registration Citizen state change requires an on-chain transaction
and consumes Gas Fee.
```

Initial `NickName` assignment during Citizen registration is part of the Citizen creation flow.  
Post-registration Nick operations such as `CreateNick` and `DeleteNick` are separate transactions and consume Gas Fee.

---

# 3. Terminology

| Term | Meaning |
|---|---|
| **Citizen** | Protocol-level SymVerse identity |
| **NickName** | Initial Citizen alias field used only during first Citizen registration |
| **Nick** | Public Citizen alias used after registration in transactions, queries, links, and transfers |
| **RefCode** | Citizen-owned deterministic referral code |
| **inputRefCode** | RefCode supplied when registering a Referrer |
| **Referrer** | Citizen address resolved from `inputRefCode` |
| **Link** | Relationship created from one Citizen to another by target Nick |
| **LinkedBy** | Reverse view showing which Citizens linked the target |
| **Credit** | Citizen relationship activity score |
| **Citizen transaction** | Transaction that creates or deletes NickName, Referrer, or Link state |

---

## 3.1 Important distinctions

### NickName and Nick are different terms

```text
NickName = initial Citizen registration field
Nick     = ongoing public Citizen alias after registration
```

`NickName` is used only when a Citizen is first registered through the Citizen creation path.  
After that first registration boundary, the protocol terminology should use:

- `Nick`
- `CreateNick`
- `DeleteNick`
- `fromNick`
- `toNick`

This distinction keeps CitizenBlock initialization terminology separate from post-registration runtime operations.

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

1. `NickName`, if supplied during initial Citizen registration
2. `RefCode`, always assigned

No relational state is created automatically.

---

## 4.2 Initial NickName

If a Citizen is created with a NickName, that NickName is registered as the Citizen’s initial public alias. After this initial registration step, the protocol refers to the public alias as `Nick`.

| Input | Result |
|---|---|
| NickName provided | Citizen begins with that NickName |
| NickName omitted or blank | Citizen begins without a NickName |

Example:

```text
Citizen A is confirmed with NickName "addra01"
→ Citizen A becomes discoverable by Nick "addra01"
```

---

## 4.3 Initial RefCode

Every confirmed Citizen receives a deterministic RefCode.

A RefCode:

- is assigned automatically,
- uniquely belongs to the Citizen,
- is not chosen by the user,
- cannot be changed or deleted by Citizen transaction.

---

# 5. Nick Policy

## 5.1 Why NickName Is Central to the Citizen Protocol

`Nick` is the primary public identifier of a Citizen after the initial Citizen registration step.

The Citizen Protocol is designed around Nick because it provides:

- a human-readable Citizen identifier,
- a globally unique public lookup key within the blockchain,
- a stable input for Citizen discovery,
- the basis for NickName-driven Link creation,
- a direct destination for coin transfer,
- a user-facing identity layer distinct from raw blockchain addresses.

In practical terms:

```text
Address identifies a blockchain account.
NickName identifies a Citizen in a human-readable way.
```

A Nick is not merely a display label.  
It is a **protocol-recognized unique Citizen key**.

This design enables a distinctive Citizen Protocol feature:

```text
A sender may transfer coins directly to a Nick,
instead of manually entering the recipient's raw blockchain address.
```

That capability is exposed through dedicated transfer APIs such as:

- `fromNick`
- `toNick`

---

## 5.2 Nick as a Unique Blockchain Key

A valid Nick must be unique within the Citizen protocol domain.

```text
One Nick → One Citizen
```

At any given state:

- the same Nick cannot be owned by multiple Citizens,
- a Citizen query by Nick must resolve to a single owner,
- Link operations using Nick must resolve to exactly one target Citizen,
- Nick-based coin transfer must resolve to exactly one recipient Citizen.

This uniqueness is fundamental to the Citizen Protocol.

Example:

```text
Nick "target01"
  → resolves to exactly one Citizen
```

If another Citizen attempts to register the same Nick, the operation must be rejected.

---

## 5.3 Domain-Label Style Nick Rule

Nick follows a **domain-label style rule**.

This means that a Nick behaves like a compact, single-label public identifier rather than an arbitrary free-form display name.

The rule is designed to make NickNames:

- simple to type,
- easy to share,
- predictable to normalize,
- safe to use as protocol keys,
- usable in direct transfer and Link operations.

### 5.3.1 Allowed characters

A NickName may contain only:

| Character Type | Allowed? | Example |
|---|---:|---|
| Lowercase letters `a-z` | Yes | `citizen` |
| Digits `0-9` | Yes | `target01` |
| Hyphen `-` | Yes, with position restrictions | `alpha-01` |
| Underscore `_` | No | `alpha_01` rejected |
| Space | No | `alpha 01` rejected |
| Dot `.` | No | `alpha.01` rejected |
| Uppercase letters | Normalized to lowercase before validation | `Target01` → `target01` |
| Korean / non-ASCII letters | Not allowed in the baseline rule | rejected |
| Other symbols such as `@`, `#`, `!` | No | rejected |

---

### 5.3.2 Length rule

| Rule | Policy |
|---|---|
| Minimum length | 6 characters |
| Maximum length | 32 characters |

The minimum length of **6 characters** prevents overly short and ambiguous labels.  
The maximum length of **32 characters** keeps Nicks concise and practical for public Citizen identity, transfer, and link operations.

---

### 5.3.3 Hyphen rule

Hyphen `-` is allowed only inside the NickName.

| Nick | Valid? | Reason |
|---|---:|---|
| `alpha-01` | Yes | Hyphen is internal |
| `-alpha01` | No | Cannot start with hyphen |
| `alpha01-` | No | Cannot end with hyphen |

The baseline policy allows an internal hyphen, including multiple internal hyphens, as long as the NickName still matches the overall validation rule.

---

### 5.3.4 Normalization rule

Before uniqueness checks or protocol use, NickName input is normalized.

Normalization policy:

```text
1. Trim leading and trailing spaces
2. Convert to lowercase
3. Validate with the NickName rule
```

Examples:

| User Input | Normalized Value | Result |
|---|---|---|
| `" Target01 "` | `target01` | Valid |
| `"ADDRA01"` | `addra01` | Valid |
| `" Alpha-01 "` | `alpha-01` | Valid |
| `" abc "` | `abc` | Rejected: fewer than 6 characters |

Normalization ensures that logically equivalent values do not create different ownership keys.

For example:

```text
"Target01"
" target01 "
"TARGET01"
```

all normalize to:

```text
target01
```

and therefore refer to the same NickName key.

---

## 5.4 Recommended Validation Regex

A reader-friendly validation rule may be expressed as:

```regex
^[a-z0-9](?:[a-z0-9-]{4,30}[a-z0-9])$
```

This regex enforces:

- total length of 6 to 32 characters,
- first character must be `a-z` or `0-9`,
- last character must be `a-z` or `0-9`,
- middle characters may be `a-z`, `0-9`, or `-`,
- `_`, `.`, spaces, and other symbols are rejected.

### 5.4.1 Regex interpretation

| Regex Part | Meaning |
|---|---|
| `^` | Start of string |
| `[a-z0-9]` | First character must be lowercase letter or digit |
| `(?:[a-z0-9-]{4,30}[a-z0-9])` | Middle and final portion; ensures total length and final alphanumeric character |
| `$` | End of string |

---

## 5.5 NickName Examples

### 5.5.1 Valid examples

| Nick | Why valid |
|---|---|
| `addra01` | 7 characters, lowercase letters and digits |
| `target01` | 8 characters, lowercase letters and digits |
| `second01` | 8 characters, lowercase letters and digits |
| `alpha-01` | Internal hyphen allowed |
| `citizen-2026` | Letters, digits, internal hyphen |
| `node001` | Minimum-length-compatible alphanumeric label |

---

### 5.5.2 Invalid examples

| Nick | Why rejected |
|---|---|
| `abc` | Fewer than 6 characters |
| `_alpha01` | Underscore not allowed |
| `alpha_01` | Underscore not allowed |
| `alpha.01` | Dot not allowed |
| `alpha 01` | Space not allowed |
| `-alpha01` | Cannot start with hyphen |
| `alpha01-` | Cannot end with hyphen |
| `알파001` | Non-ASCII characters not allowed under baseline rule |
| `alpha@01` | Symbol `@` not allowed |

---

## 5.6 Initial Citizen Registration with NickName

A Citizen may receive an initial `NickName` at confirmation. This is the only stage where the protocol uses the term `NickName` for the alias field.

| Case | Result |
|---|---|
| Citizen is created with a valid `NickName` | Initial Nick is registered |
| Citizen is created without a `NickName` | Citizen has no initial Nick |

Example:

```text
Citizen A is confirmed with `NickName = "addra01"`
→ after registration, `addra01` becomes Citizen A’s public `Nick`
```

---

## 5.7 CreateNick

`CreateNick` is allowed only if the Citizen currently has no Nick. Because it is an on-chain Citizen runtime transaction, it consumes Gas Fee.

| Current State | Operation Result |
|---|---|
| `NO_NICK` | Allowed |
| `HAS_NICK` | Rejected |

The requested Nick must:

- satisfy the NickName validation rule,
- not already belong to another Citizen,
- become the Citizen’s unique public Nick when accepted.

---

## 5.8 DeleteNick

`DeleteNick` is allowed only if the Citizen currently has a Nick. Because it is an on-chain Citizen runtime transaction, it consumes Gas Fee.

| Current State | Operation Result |
|---|---|
| `HAS_NICK` | Allowed |
| `NO_NICK` | Rejected |

Deletion releases the Citizen’s current Nick from ownership.

After deletion:

- the Citizen has no Nick,
- the deleted Nick no longer resolves to that Citizen,
- a new Nick may later be created through `CreateNick`.

---

## 5.9 Nick Change Process

Nick does **not** support direct update.

A Nick change is performed as a two-step protocol process:

```text
DeleteNick → CreateNick
```

Example:

```text
Citizen A currently owns Nick "addra01"

1. DeleteNick
   → Citizen A no longer owns "addra01"

2. CreateNick("bddra01")
   → Citizen A now owns "bddra01"
```

State transitions:

```text
NO_NICK  --CreateNick--> HAS_NICK
HAS_NICK --DeleteNick--> NO_NICK
HAS_NICK --CreateNick--> reject
NO_NICK  --DeleteNick--> reject
```

This explicit two-step process keeps Nick ownership changes clear and auditable.

---

## 5.10 Nick-Aware Coin Transfer

A Nick can act as a human-readable transaction input while the underlying blockchain transaction remains address-based after resolution.

Instead of requiring a user to manually enter only raw addresses, the transaction argument may use:

| Field | Purpose |
|---|---|
| `fromNick` | Resolves the sender Nick to its owner address |
| `toNick` | Resolves the recipient Nick to its owner address |

The resolution principle is:

```text
Nick
  → resolved Citizen owner address
  → normal transaction processing
```

The Nick-aware fields are integrated into existing transaction APIs.  
They do not require separate `*ToNick` RPC methods.

The detailed transaction/API rules are defined in:

```text
Section 11.1 Direct Coin Transfer APIs
```

---

## 5.11 Nick Use in Link Operations

Nick is also the target identifier for Link creation and deletion.

| Operation | Nick Role |
|---|---|
| `CreateLink` | Target Citizen is selected by Nick |
| `DeleteLink` | Existing Link target is selected by Nick |

Because Link operations depend on NickName resolution:

- target Nick must be valid,
- target Nick must resolve to an existing Citizen,
- Link creation cannot rely on ambiguous or duplicate identifiers.

This is another reason Nick uniqueness is foundational to the Citizen Protocol.

---

## 5.12 Nick Validation Scope

The Nick validation rule applies to:

- initial Citizen registration with `NickName`,
- `CreateNick`,
- `CreateLink`,
- `DeleteLink`,
- transaction arguments using `fromNick`,
- transaction arguments using `toNick`.

`DeleteNick` acts on the Citizen’s current Nick and therefore does not require a NickName input.

---

# 6. RefCode Policy

## 6.1 Meaning

`RefCode` is the Citizen’s permanent referral code.

It is assigned automatically when a Citizen is confirmed and may be used by another Citizen to create a Referrer relation.

---

## 6.2 RefCode lifecycle

| Action | Allowed? |
|---|---:|
| Automatic assignment at Citizen confirmation | Yes |
| User-selected value | No |
| Transaction create | No |
| Transaction update | No |
| Transaction delete | No |

RefCode is a protocol-assigned, immutable Citizen property.

---

## 6.3 Public display format

A RefCode may be displayed in a compact user-facing `ownCode` form.

| RefCode | Full format | Display `ownCode` |
|---:|---|---|
| 4096 | `0000-0000-4096` | `4096` |
| 8192 | `0000-0000-8192` | `8192` |
| 8193 | `0000-0000-8193` | `8193` |
| 12289 | `0000-0001-2289` | `0001-2289` |
| 100002289 | `0001-0000-2289` | `0001-0000-2289` |

The display format is intended to make Citizen referral codes easier to read and share.

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

`CreateReferrer` is allowed only if the Citizen currently has no Referrer. Because it is an on-chain Citizen runtime transaction, it consumes Gas Fee.

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

`DeleteReferrer` is allowed only if a Referrer currently exists. Because it is an on-chain Citizen runtime transaction, it consumes Gas Fee.

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

A `Link` is a directional Citizen relationship created by target Nick.

Example:

```text
Citizen A links target Nick "target01"
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

`CreateLink` is an on-chain Citizen runtime transaction and consumes Gas Fee.

Input:

```text
target Nick
```

Validation:

| Condition | Result |
|---|---|
| target Nick empty | Reject |
| target Nick owner missing | Reject |
| target is sender | Reject |
| same target already linked | Reject |
| sender already has another Link | Allowed |

---

## 8.4 DeleteLink

`DeleteLink` is an on-chain Citizen runtime transaction and consumes Gas Fee.

Input:

```text
target Nick
```

Validation:

| Condition | Result |
|---|---|
| target Nick empty | Reject |
| target Nick owner missing | Reject |
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

A successful Citizen Citizen operation increases the sender’s Credit by `+1`.

| Operation | Credit Change |
|---|---:|
| `CreateNick` | `+1` |
| `DeleteNick` | `+1` |
| `CreateReferrer` | `+1` |
| `DeleteReferrer` | `+1` |
| `CreateLink` | `+1` |
| `DeleteLink` | `+1` |

RefCode creation does not increase Credit because it is assigned automatically at Citizen confirmation.

---

# 10. Citizen Operation Summary

| Target | Create | Delete | Change Policy |
|---|---|---|---|
| NickName | Initial Citizen creation, or `CreateNick` when no NickName exists | `DeleteNick` | `Delete → Create` |
| RefCode | Automatic at Citizen confirmation | Not allowed | Immutable |
| Referrer | `CreateReferrer` | `DeleteReferrer` | `Delete → Create` |
| Link | `CreateLink` by target Nick | `DeleteLink` by target Nick | Multiple Links added/removed individually |

---

# 11. External Transaction Interface

The Citizen Protocol exposes two externally important transaction surfaces:

1. **direct coin transfer by Nick**, and
2. **Citizen runtime operation transactions** for Nick, Referrer, and Link state.

---

## 11.1 Direct Coin Transfer APIs

Nick-based coin transfer is integrated into the existing transaction APIs rather than exposed through separate `*ToNick` RPC methods.

The following transaction argument fields are supported:

| Field | Purpose |
|---|---|
| `fromNick` | Resolves the sender Nick to its owner address |
| `toNick` | Resolves the recipient Nick to its owner address |

These fields are mutually exclusive with their address equivalents:

```txt
from     and fromNick cannot be used together
to       and toNick   cannot be used together
```

Both Nick fields follow the same Citizen Protocol principle:

```txt
Nick
  → resolved Citizen owner address
  → normal transaction processing
```

### Supported APIs

Nick-based address resolution is available through the existing transaction APIs:

| API | Nick support |
|---|---|
| `sym_sendTransaction` | Supports `fromNick` and `toNick` |
| `personal_sendTransaction` | Supports `fromNick` and `toNick` |
| `personal_signTransaction` | Supports `fromNick` and `toNick` before raw transaction signing |

No separate `SendTransactionToNick` or `SendRawTransactionToNick` API is required.

### Example A — standard transaction sent from an address to a Nick

```js
sym.sendTransaction({
  from: addrA,
  toNick: "target01",
  value: web3.toHug(100, "sym")
})
```

Resolution flow:

```txt
toNick = "target01"
  → Citizen Nick owner address
  → normal coin transfer recipient
```

### Example B — standard transaction sent from a Nick to a Nick

```js
sym.sendTransaction({
  fromNick: "sender01",
  toNick: "target01",
  value: web3.toHug(100, "sym")
})
```

Resolution flow:

```txt
fromNick = "sender01"
  → sender owner address

toNick = "target01"
  → recipient owner address

resolved addresses
  → normal transaction signing and submission
```

### Example C — password-based personal transaction using Nicks

```js
personal.sendTransaction({
  fromNick: "sender01",
  toNick: "target01",
  value: web3.toHug(100, "sym")
}, "account-password")
```

The sender Nick is resolved before nonce handling and account signing, so the transaction is processed exactly as an ordinary address-based personal transaction.

### Example D — raw transaction workflow using Nick-based signing

Raw transaction submission itself remains unchanged:

```txt
sym_sendRawTransaction(rawTransaction)
```

A raw transaction cannot resolve or rewrite a Nick after signing, because its destination address is already part of the signed transaction payload.

Instead, Nick resolution is performed during `personal_signTransaction`:

```js
nonce = sym.getTransactionCountByNick("sender01", "pending")

result = personal.signTransaction({
  fromNick: "sender01",
  toNick: "target01",
  value: web3.toHug(100, "sym"),
  gas: 49000,
  gasPrice: sym.gasPrice,
  nonce: nonce
}, "account-password")

sym.sendRawTransaction(result.raw)
```

Resolution flow:

```txt
fromNick / toNick
  → concrete sender and recipient addresses
  → transaction signed as raw bytes
  → raw bytes submitted unchanged
```

### Related helper APIs

Nick-aware transaction workflows are supported by the following lookup APIs:

| API | Purpose |
|---|---|
| `sym_getBalanceByNick` | Returns the balance of the address that owns the Nick |
| `sym_getTransactionCountByNick` | Returns the account nonce for the Nick owner address |
| `citizen_getNickOwner` / console alias `citizen.getAddressByNick` | Resolves Nick to owner address |
| `citizen_getNickByAddress` | Resolves address to Nick |

These APIs allow applications, wallets, and command-line users to work with Citizen Nicks as practical account aliases while preserving the underlying address-based transaction model.

---

## 11.2 Citizen Runtime Operation Transaction

Nick, Referrer, and Link changes are **Citizen runtime operations**.  
They are submitted as ordinary blockchain transactions with a Citizen-specific transaction type.

### 11.2.1 Transaction Type

Every Citizen runtime operation transaction MUST use:

```text
type = 11
```

where:

```text
TxTypeCitizen = 11
```

This transaction type identifies the transaction as a Citizen Protocol state-change transaction.

---

### 11.2.2 Sender and Target Address Rule

For Citizen runtime operations:

```text
from == to
```

MUST hold.

| Field | Meaning |
|---|---|
| `from` | Address submitting the Citizen operation |
| `to` | Address whose Citizen runtime state is being changed |

The protocol rule is:

```text
The submitter and the changed Citizen address must be identical.
```

Examples:

| Case | Result |
|---|---|
| `from = addrA`, `to = addrA` | Valid form |
| `from = addrA`, `to = addrB` | Invalid form |

This rule ensures that a Citizen runtime operation changes only the sender’s own Citizen runtime state.

---

### 11.2.3 Gas Fee

Citizen runtime operation transactions consume **Gas Fee**.

This applies to:

- `CreateNick`
- `DeleteNick`
- `CreateReferrer`
- `DeleteReferrer`
- `CreateLink`
- `DeleteLink`

Protocol principle:

```text
Every post-registration Citizen runtime state change
is an on-chain transaction and consumes Gas Fee.
```

---

### 11.2.4 Standard Transaction Layout

A direct Citizen runtime operation transaction follows the ordinary transaction shape, with:

- `type = 11`
- `from == to`
- `input` containing the encoded Citizen operation payload

| Transaction Field | Meaning |
|---|---|
| `from` | Citizen operation submitter |
| `to` | Same address as `from` |
| `nonce` | Sender account nonce |
| `gasPrice` | Gas price |
| `gas` | Gas limit |
| `value` | Usually `0` for pure Citizen runtime operations |
| `input` | Encoded Citizen operation payload |
| `type` | `11` (`TxTypeCitizen`) |

Conceptual example:

```text
Citizen runtime operation transaction:
  from  = addrA
  to    = addrA
  value = 0
  type  = 11
  input = EncodeCitizenOperationPayload(...)
```

---

### 11.2.5 Wrapper Functions

Convenience APIs or wrapper functions such as:

- `CreateNick`
- `DeleteNick`
- `CreateReferrer`
- `DeleteReferrer`
- `CreateLink`
- `DeleteLink`

may be implemented by constructing and submitting the standard Citizen runtime operation transaction described above.

In other words:

```text
CreateNick(...)
```

is a convenience wrapper over a transaction equivalent to:

```text
type  = TxTypeCitizen(11)
from  = sender
to    = sender
input = Citizen operation payload with Op = CreateNick
```

The wrapper may simplify client usage, but the underlying protocol rule remains the same:

```text
Citizen runtime changes are performed through TxTypeCitizen(11) transactions.
```

---

## 11.3 Citizen Payload Fields

A Citizen runtime operation transaction carries a Citizen operation payload with the following externally relevant fields:

| Field | Meaning |
|---|---|
| `Op` | Citizen operation code |
| `Domain` | Citizen protocol domain |
| `Nick` | Nick to create, or Link target Nick, depending on operation |
| `RefCode` | Input RefCode used to create a Referrer relation |
| `Link` | Reserved by current policy; Link registration uses `Nick`, not direct address |
| `Extra` | Reserved extension field |

---

## 11.4 Operation Code Mapping

The public Citizen Protocol terms are:

- `CreateNick`
- `DeleteNick`
- `CreateReferrer`
- `DeleteReferrer`
- `CreateLink`
- `DeleteLink`

The currently referenced operation values are:

| Public Protocol Term | Current Operation Constant Reference | `Op` Value |
|---|---|---:|
| `CreateNick` | `CitizenOpCreateNickName` | `1` |
| `DeleteNick` | `CitizenOpDeleteNickName` | `2` |
| `CreateReferrer` | `CitizenOpCreateReferrer` | `11` |
| `DeleteReferrer` | `CitizenOpDeleteReferrer` | `12` |
| `CreateLink` | `CitizenOpCreateLink` | `21` |
| `DeleteLink` | `CitizenOpDeleteLink` | `22` |

The Citizen Protocol operation namespace is:

```text
CitizenOp*
```

Link relation operations use:

```text
CitizenOpCreateLink = 21
CitizenOpDeleteLink = 22
```

This mapping is provided to make direct Citizen transaction construction unambiguous.

---

## 11.5 Field Usage by Operation

| Public Operation | `Nick` | `RefCode` | `Link` | Description |
|---|---|---|---|---|
| `CreateNick` | Nick to register | unused | unused | Create current Citizen Nick |
| `DeleteNick` | unused | unused | unused | Delete current Citizen Nick |
| `CreateReferrer` | unused | input RefCode | unused | Register Referrer |
| `DeleteReferrer` | unused | unused | unused | Delete current Referrer |
| `CreateLink` | target Nick | unused | unused | Link target Citizen |
| `DeleteLink` | target Nick | unused | unused | Remove target Link |

---

## 11.6 Supported and Unsupported Operation Set

### Supported

| Operation | Status |
|---|---|
| `CreateNick` | Supported |
| `DeleteNick` | Supported |
| `CreateReferrer` | Supported |
| `DeleteReferrer` | Supported |
| `CreateLink` | Supported |
| `DeleteLink` | Supported |

### Not supported

| Operation | Status |
|---|---|
| `UpdateNick` | Not supported |
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
| Nick | Current Nick and initial NickName |
| RefCode | RefCode / formatted ownCode |
| Referrer | Current Referrer address |
| Link relations | Links and LinkedBy views where applicable |
| Credit | Current Citizen Credit |
| PQC identity metadata | `qAlgo`, `qAlgoName`, `qKeyPub` |

---

## 12.1 Citizen Query Example by Nick

A client may query the current public Citizen state by Nick.

Example:

```js
citizen.getCitizenByNick(nick1, "latest")
```

Representative result:

```js
{
  aKeyPubH: "0xcf9220b7a519a9550852a52bfee8fbb39170ed62",
  blockNumber: 0,
  ca: "0x00050000000000010002",
  citizenIndex: 0,
  country: "0x0",
  credit: "0x0",
  domain: "SYMVERSE",
  nick: "mldsa444014701",
  nickName: "mldsa444014701",
  nonce: 0,
  ownCode: "0001-2288",
  qAlgo: "0x44",
  qAlgoName: "ML-DSA-44",
  qKeyPub: "0x3594b10a1bcd087b5d1b4d5a84beed85...74b3944b",
  refCode: "0x3000",
  referrer: "",
  role: "0x1",
  status: "0x1",
  vFlag: "0x0",
  writeTime: 1778905514
}
```

In this example:

| Field | Meaning |
|---|---|
| `nick` | Current runtime Nick |
| `nickName` | Initial NickName assigned at Citizen confirmation |
| `ownCode` | User-facing formatted RefCode |
| `refCode` | Protocol RefCode value |
| `qAlgo` | PQC algorithm code |
| `qAlgoName` | Human-readable PQC algorithm name |
| `qKeyPub` | Registered PQC public key; shortened above for readability |
| `referrer` | Current Referrer relation, empty when none is set |
| `credit` | Current Citizen Credit |

This query result shows that a Nick-based public lookup can return both:

- Citizen Protocol runtime state, and
- the Citizen’s registered cryptographic identity metadata.

---

# 13. State Transition Summary

| Target | Empty State | Create | Existing State | Delete |
|---|---|---|---|---|
| Nick | `NO_NICK` | `CreateNick` | `HAS_NICK` | `DeleteNick` |
| Referrer | `NO_PARENT` | `CreateReferrer` | `HAS_PARENT` | `DeleteReferrer` |
| Link | `NO_LINK_RELATION` | `CreateLink` | `HAS_LINK_RELATION` | `DeleteLink` |

RefCode is not a state-transition target.

```text
RefCode = immutable deterministic code assigned at Citizen confirmation
```

---

# 14. Final Protocol Summary

## 14.1 Nick

- Core public Citizen identifier after initial `NickName` registration.
- Globally unique Citizen lookup key within the protocol domain.
- Domain-label-style rule:
  - 6 to 32 characters
  - lowercase letters, digits, and internal hyphen only
  - `_`, `.`, spaces, and other symbols rejected
- Recommended validation regex:

```regex
^[a-z0-9](?:[a-z0-9-]{4,30}[a-z0-9])$
```

- Initial alias may be registered once through `NickName` during Citizen confirmation.
- May be created by `CreateNick` transaction only if no Nick exists.
- Can be used in Nick-aware transaction fields:
  - `fromNick`
  - `toNick`
- Existing transaction APIs resolve those fields before ordinary address-based processing.
- Change requires:

```text
DeleteNick → CreateNick
```

---

## 14.2 RefCode

- Automatically assigned at Citizen confirmation.
- Immutable.
- Cannot be created, updated, or deleted by Citizen transaction.

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

- Input is target Nick.
- Public results expose both `links` and `linkedBy`.
- Not created automatically at Citizen confirmation.
- Managed only by:

```text
CreateLink
DeleteLink
```

---

## 14.5 Credit

- Incremented by successful Citizen Citizen operations.
- RefCode assignment does not grant Credit.

---

# 15. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-15 | Initial Citizen Protocol specification draft |
| v0.2 | 2026-05-15 | Expanded Citizen runtime and operation policy from internal implementation notes |
| v0.3 | 2026-05-15 | Rewritten as a public-facing protocol specification by removing internal storage layout, internal function names, block-processing insertion details, and simulator validation procedures while strengthening external operation rules and public query behavior |
| v0.4 | 2026-05-15 | Simplified RefCode presentation by removing implementation-oriented derivation details and substantially expanded NickName as the core Citizen identifier, including uniqueness, domain-label-style validation, normalization, lifecycle, change process, and Link resolution role |
| v0.5 | 2026-05-15 | Added NickName-based direct coin transfer as a core Citizen Protocol capability, including `SendTransactionToNick`, `SendRawTransactionToNick`, conceptual usage examples, validation scope, and public API significance |
| v0.6 | 2026-05-15 | Moved NickName policy ahead of RefCode policy and expanded NickName into a user-facing domain-label specification with allowed/prohibited characters, hyphen rules, 6–63 character range, normalization examples, validation regex, and valid/invalid nickname examples |
| v0.7 | 2026-05-15 | Reduced Nick maximum length to 32 characters, clarified that `NickName` is only the initial Citizen registration term while post-registration operations use `Nick`, renamed lifecycle operations to `CreateNick` and `DeleteNick`, and documented that all post-registration Citizen runtime state changes consume Gas Fee |
| v0.8 | 2026-05-15 | Renamed the public direct Citizen transaction type to `TxTypeCitizen = 11`, reframed post-registration operations as Citizen transactions, and clarified that `SendTransactionToNick` and `SendRawTransactionToNick` are `to`-destination variants where address input is replaced by Nick input |
| v0.9 | 2026-05-15 | Rewrote Citizen runtime operation submission as a normative protocol rule: `TxTypeCitizen = 11`, `from == to`, Gas Fee consumption, standard transaction layout, and wrapper-function interpretation for `CreateNick`, `DeleteNick`, `CreateReferrer`, and Link operations |
| v0.10 | 2026-05-16 | Updated the Citizen runtime operation namespace to the finalized `CitizenOp*` names, aligned remaining relation-oriented payload/table terminology to Link, and aligned Link relation operations to `CitizenOpCreateLink = 21` and `CitizenOpDeleteLink = 22` |
| v0.11 | 2026-05-16 | Replaced separate `*ToNick` transfer APIs with Nick-aware transaction fields `fromNick` and `toNick`, integrated Nick resolution into existing transaction and signing APIs, clarified address/Nick mutual exclusivity, documented the raw signing flow through `personal_signTransaction`, and added related Nick lookup helper APIs |
| v0.12 | 2026-05-16 | Simplified the public query section by replacing the abstract query-shape discussion with a concrete `citizen.getCitizenByNick(..., "latest")` example, documenting the returned Citizen and PQC metadata fields, and removing the redundant standalone Query by Nick subsection |

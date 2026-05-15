# SymVerse V3 Transaction Specification

> **Status:** Draft v0.6  
> **Date:** 2026-05-15  
> **Document Role:** Baseline transaction specification for SymVerse transaction fields, signing flow, transaction types, and deposit policy  
> **Source Basis:** Existing SymVerse Transaction documentation

---

# 1. Overview

The SymVerse transaction model defines how value transfer, smart contract execution, SymVerse Contract Template execution, and deposit operations are represented and submitted to the blockchain.

This document summarizes the existing SymVerse transaction baseline:

- General transaction
- Smart contract creation and call
- SCT transaction
- Deposit transaction
- Citizen Protocol transaction
- Common transaction fields
- Gas calculation
- Signing process
- Deposit policy

The transaction type determines the processing path.

| Transaction Type | Meaning |
|---:|---|
| `0` | General transaction |
| `1` | SCT transaction |
| `2` | Deposit transaction |
| `11` | Citizen Protocol transaction |

If `type` is omitted, the transaction is treated as a general transaction by default.

---

# 2. Types of Transaction

## 2.1 General Transaction

General transactions include:

1. **Remit transaction**
   - transfers value from one account to another

2. **Smart contract transaction**
   - creates a smart contract when `to` is empty
   - calls a smart contract when `to` is a contract address

---

## 2.2 SCT Transaction

SCT transactions are used for:

```text
SymVerse Contract Template operations
```

They provide a structured transaction path for SCT creation and SCT method execution.

---

## 2.3 Deposit Transaction

Deposit transactions are used for:

1. deposit creation
2. deposit restoration

The deposit transaction type is:

```text
type = 2
```

---

## 2.4 Citizen Protocol Transaction

Citizen Protocol transactions are used for runtime Citizen state changes such as:

- Nick creation and deletion
- Referrer creation and deletion
- Link creation and deletion

The Citizen Protocol transaction type is:

```text
type = 11
```

where:

```text
TxTypeCitizen = 11
```

Citizen Protocol transactions are ordinary blockchain transactions with a Citizen-specific payload and execution rule.

---

# 3. Transaction Data

## 3.1 Common Parameters

| Field | Type | Description |
|---|---|---|
| `from` | address | Sender address |
| `nonce` | integer | Number of transactions published by the account |
| `gasPrice` | integer | Gas price per gas unit |
| `gas` | integer | Gas amount for transaction execution |
| `to` | address or nil | Receiver address, contract address, or nil |
| `value` | integer | Amount transferred or deposit amount |
| `input` | bytes | RLP-encoded data for contract or SCT execution |
| `type` | integer | Transaction type |
| `workNodes` | address array | Work node list that relays the transaction |
| `extraData` | bytes | Additional transaction data |

---

## 3.2 Address Semantics

The transaction address is fundamentally a SymVerse account identifier.

```text
address = SymID-based account address
```

---

## 3.3 Type Field

The `type` field selects the transaction usage.

| Type | Meaning |
|---:|---|
| `0` | General transaction |
| `1` | SCT transaction |
| `2` | Deposit transaction |
| `11` | Citizen Protocol transaction |

If `type` is not specified, it defaults to:

```text
type = 0
```

---

## 3.4 Defaulting Behavior

The following defaulting behavior applies when transaction fields are not explicitly supplied:

| Field | Behavior |
|---|---|
| `nonce` | Automatically incremented from the latest account state |
| `type` | Defaults to general transaction |
| `gas` | Default value may be used |
| `gasPrice` | Default value may be used |
| `value` | Default value may be used |
| `workNodes` | May be set from node configuration when omitted |

---

## 3.5 WorkNodes

`workNodes` should be included before signing.

The existing transaction baseline expects:

```text
workNodes count == 1
```

If omitted, the node may populate it from its default SymBase configuration.

---

# 4. Gas

## 4.1 Gas Price

The fixed `gasPrice` value described in the baseline documentation is:

```text
100 GHug
```

hexadecimal representation:

```text
0x174876e800
```

---

## 4.2 Gas Formula

```text
gas =
    base_gas
    + (number of non-zero bytes) × 680
    + (number of zero bytes) × 40
    + contract_operation_gas
```

---

## 4.3 Base Gas

| Transaction Situation | Base Gas |
|---|---:|
| Smart contract or SCT creation where `to` is nil | `8,000,000` |
| Other cases | `49,000` |

Hexadecimal forms:

| Value | Hex |
|---:|---|
| `8,000,000` | `0x7a1200` |
| `49,000` | `0xbf68` |

---

## 4.4 Contract Operation Gas

`contract_operation_gas` is charged for:

- smart contract creation,
- smart contract execution,
- SCT creation,
- SCT execution.

For non-contract transactions:

```text
contract_operation_gas = 0
```

---

# 5. Signing Process

## 5.1 Signing Flow

The transaction signing process follows this sequence.

```text
Step 1. Tx = tx_data + {chain_id, "", ""}
Step 2. encoded_Tx = RLP_encode(Tx)
Step 3. Tx_hash = SHA3(encoded_Tx)
Step 4. V, R, S = SIGN(Tx_hash)
Step 5. signed_Tx = (tx_data, V, R, S)
Step 6. RLP_encode(signed_Tx)
Step 7. Send transaction message
```

Step 6 is required for raw transaction submission.

---

## 5.2 Signing Input Order

The pre-signing transaction input is ordered as:

```text
[
  from,
  nonce,
  gasPrice,
  gas,
  to,
  value,
  input,
  type,
  workNodes,
  extraData,
  chain_id,
  "",
  ""
]
```

The ordering MUST be preserved for signature compatibility.

---

## 5.3 Hash and Signature Algorithm

The baseline transaction signing process uses:

| Stage | Algorithm |
|---|---|
| Transaction hash | `SHA3-256` |
| Signature | `ECDSA` |

---

## 5.4 Signature Values

The ECDSA signature fields are:

| Field | Meaning |
|---|---|
| `V` | Recovery identifier |
| `R` | Lower 32-byte signature component |
| `S` | Upper 32-byte signature component |

`V` is expected to be:

```text
0 or 1
```

---

## 5.5 `sendTransaction` and `sendRawTransaction`

| Method | Field Requirements |
|---|---|
| `sendTransaction` | Missing data fields may be filled with defaults |
| `sendRawTransaction` | All fields must be explicitly present, including default-value fields |

For raw transactions:

- empty byte arrays such as `input` and `extraData` should be represented as empty arrays where required,
- all signing and transaction fields must already be resolved before submission.

---

## 5.6 Chain ID

| Network | Chain ID |
|---|---:|
| Mainnet | `1` |
| Testnet | `2` |

---

# 6. Type `0` — General Transaction

## 6.1 Meaning

When:

```text
type = 0
```

the transaction is a general transaction.

If `type` is omitted, the transaction is processed as:

```text
type = 0
```

---

## 6.2 General Transaction Modes

| Condition | Result |
|---|---|
| `to` is nil | Smart contract creation |
| `to` is a normal account address | Remit transaction |
| `to` is a contract address | Contract call |

---

## 6.3 Remit Transaction Parameters

| Field | Type | Description |
|---|---|---|
| `from` | address | Sender address |
| `nonce` | integer | Sender transaction count |
| `gasPrice` | integer | Gas price per unit |
| `gas` | integer | Gas limit |
| `to` | address | Receiver address |
| `value` | integer | Amount transferred |
| `input` | bytes | RLP-encoded data |
| `type` | integer | `0` for general transaction |
| `workNodes` | address array | Relay work node list |
| `extraData` | bytes | Additional data |

---

## 6.4 Contract Transaction Parameters

| Field | Type | Description |
|---|---|---|
| `from` | address | Sender address |
| `nonce` | integer | Sender transaction count |
| `gasPrice` | integer | Gas price per unit |
| `gas` | integer | Gas limit |
| `to` | address or nil | Contract address or nil |
| `value` | integer | Amount transferred |
| `input` | bytes | Contract bytecode or call data |
| `type` | integer | `0` for general transaction |
| `workNodes` | address array | Relay work node list |
| `extraData` | bytes | Additional data |

---

## 6.5 Input Requirement

`input` is required for:

- smart contract creation,
- smart contract call.

For contract calls:

```text
to = contract address
```

---

## 6.6 Examples

### 6.6.1 Remit Transaction

```javascript
sym.sendTransaction({
  from: "0x00021000000000010002",
  to: "0x00021000000000070002",
  workNodes: ["0x00021000000000010002"],
  value: web3.toHug(100, "sym")
})
```

---

### 6.6.2 Contract Creation

```javascript
sym.sendTransaction({
  from: "0x00021000000000010002",
  workNodes: ["0x00021000000000010002"],
  input: "0xf8418080f83d9a30783533373936643736363537323733363534333666363936658830783533353934648b39313834653732613030308c307830303030303030303031",
  gas: 9000000
})
```

---

### 6.6.3 Contract Call

```javascript
sym.sendTransaction({
  from: "0x00021000000000010002",
  to: "0x0CED1024EEd02B234df2",
  workNodes: ["0x00021000000000010002"],
  input: "0xf8418080f83d9a30783533373936643736363537323733363534333666363936658830783533353934648b39313834653732613030308c307830303030303030303031",
  gas: 150000
})
```

---

### 6.6.4 Raw Transaction

```javascript
sym.sendRawTransaction(
  "0xf8738a0000000000000000000901850430e2340083015f908a00000000000000000009808002cb8a0000000000000000000901a068c19c97383288faa6373c8b058ed386753c767a3e4976937b2afca1515df875a0142de5cf2687167da8d13f51a4767536ea1473b6d46f76edfa644b04aa428901"
)
```

---

### 6.6.5 Balance Check

```javascript
sym.getBalance("0x00021000000000010002")
```

---

# 7. Type `1` — SCT Transaction

## 7.1 Meaning

When:

```text
type = 1
```

the transaction is an SCT transaction.

---

## 7.2 SCT Creation and SCT Call

| Condition | Result |
|---|---|
| `to` is nil | SCT creation |
| `to` is a contract address | SCT call |

---

## 7.3 SCT Parameters

| Field | Type | Description |
|---|---|---|
| `from` | address | Sender address |
| `nonce` | integer | Sender transaction count |
| `gasPrice` | integer | Gas price per unit |
| `gas` | integer | Gas limit |
| `to` | address or nil | SCT contract address or nil |
| `value` | integer | Not used |
| `input` | bytes | RLP-encoded SCT data |
| `type` | integer | `1` for SCT transaction |
| `workNodes` | address array | Relay work node list |
| `extraData` | bytes | Additional data |

---

## 7.4 SCT Input Format

The SCT input format is:

```text
[
  Type,
  Method,
  Parameter
]
```

where:

| Element | Meaning |
|---|---|
| `Type` | SCT category such as SCT20, SCT21, SCT30, SCT40 |
| `Method` | Method within the SCT type |
| `Parameter` | Method parameters |

The SCT data is RLP-encoded before being placed in `input`.

```text
input = RLP_encode([Type, Method, Parameter])
```

---

# 8. Type `2` — Deposit Transaction

## 8.1 Meaning

When:

```text
type = 2
```

the transaction is a Deposit transaction.

---

## 8.2 Deposit Set and Deposit Restoration

| Condition | Result |
|---|---|
| `to` is nil | Deposit set |
| `to` is present | Deposit restoration |

---

## 8.3 Deposit Transaction Parameters

| Field | Type | Description |
|---|---|---|
| `from` | address | Sender address |
| `nonce` | integer | Sender transaction count |
| `gasPrice` | integer | Gas price per unit |
| `gas` | integer | Gas limit |
| `to` | address or nil | Deposit owner address or nil |
| `value` | integer | Deposit amount |
| `input` | bytes | Not used |
| `type` | integer | `2` for Deposit transaction |
| `workNodes` | address array | Relay work node list |
| `extraData` | bytes | Additional data |

---

## 8.4 Deposit Set

In a deposit set transaction:

```text
to = nil
```

The transaction value becomes the deposited amount.

---

## 8.5 Deposit Restoration

In a deposit restoration transaction:

```text
from == to
```

The deposit balance is restored to the account balance, subject to protocol restrictions.

---

## 8.6 Examples

### 8.6.1 Deposit Set

```javascript
sym.sendTransaction({
  from: "0x0002A000000000010002",
  type: "0x2",
  value: web3.toHug(20, "sym")
})
```

Optional convenience method:

```javascript
sym.setDeposit(
  "0x0002A000000000010002",
  web3.toHug(20, "sym")
)
```

---

### 8.6.2 Deposit Restoration

```javascript
sym.sendTransaction({
  from: "0x0002A000000000010002",
  to: "0x0002A000000000010002",
  type: "0x2"
})
```

Optional convenience method:

```javascript
sym.restoreDeposit(
  "0x0002A000000000010002"
)
```

---

### 8.6.3 Raw Deposit Transaction

```javascript
sym.sendRawTransaction(
  "0xf8758a0000000000000000000901850430e2340083015f90808901158e460913d00000838207d002cb8a0000000000000000000980a0dceb7d07d0ba181f8d918a2b61cf9e55f8cac1b19dc94729f56dd7210e0a4b9aa0511c581b649ce6748586f176be60042b6b020fbc22615c5248c14ef54c97fc05"
)
```

---

### 8.6.4 Deposit Check

```javascript
sym.getDeposit("0x0002A000000000010002")
```

---

# 9. Type `11` — Citizen Protocol Transaction

## 9.1 Meaning

When:

```text
type = 11
```

the transaction is a Citizen Protocol transaction.

The transaction type constant is:

```text
TxTypeCitizen = 11
```

Citizen Protocol transactions are used to change post-registration Citizen runtime state.

Supported operation families include:

- Nick
- Referrer
- Link

---

## 9.2 Core Transaction Rule

A Citizen Protocol transaction MUST satisfy:

```text
from == to
```

| Field | Meaning |
|---|---|
| `from` | Citizen operation submitter |
| `to` | Citizen whose runtime state is changed |

The sender may change only their own Citizen runtime state.

| Example | Result |
|---|---|
| `from = addrA`, `to = addrA` | Valid form |
| `from = addrA`, `to = addrB` | Invalid form |

---

## 9.3 Gas Fee

Citizen Protocol transactions consume **Gas Fee**.

This applies to operations such as:

- `CreateNick`
- `DeleteNick`
- `CreateReferrer`
- `DeleteReferrer`
- `CreateLink`
- `DeleteLink`

Protocol principle:

```text
Any post-registration Citizen runtime state change
is an on-chain transaction and consumes Gas Fee.
```

---

## 9.4 Citizen Protocol Transaction Parameters

| Field | Type | Description |
|---|---|---|
| `from` | address | Citizen operation submitter |
| `nonce` | integer | Sender transaction count |
| `gasPrice` | integer | Gas price per unit |
| `gas` | integer | Gas limit |
| `to` | address | MUST be the same address as `from` |
| `value` | integer | Usually `0` for pure Citizen runtime operations |
| `input` | bytes | Encoded Citizen operation payload |
| `type` | integer | `11` for Citizen Protocol transaction |
| `workNodes` | address array | Relay work node list |
| `extraData` | bytes | Additional data |

---

## 9.5 Citizen Operation Payload

The `input` field of a `TxTypeCitizen(11)` transaction contains an encoded Citizen operation payload.

The payload carries the following protocol-level fields:

| Field | Meaning |
|---|---|
| `Op` | Citizen operation code |
| `Domain` | Citizen protocol domain |
| `Nick` | Nick to create, or Link target Nick, depending on operation |
| `RefCode` | Input RefCode used to create a Referrer relation |
| `Link` | Reserved by current policy; Link registration uses `Nick`, not a direct address |
| `Extra` | Reserved extension field |

The key field is:

```text
Op
```

`Op` determines which Citizen runtime operation the transaction requests.

---

## 9.6 Operation Codes

The Citizen operation code is carried in the payload field:

```text
Op
```

The current operation values are:

| Payload `Op` Value | Operation Constant Reference | Public Citizen Action |
|---:|---|---|
| `1` | `CitizenOpCreateNickName` | `CreateNick` |
| `2` | `CitizenOpDeleteNickName` | `DeleteNick` |
| `11` | `CitizenOpCreateReferrer` | `CreateReferrer` |
| `12` | `CitizenOpDeleteReferrer` | `DeleteReferrer` |
| `21` | `CitizenOpCreateSponsor` | `CreateLink` |
| `22` | `CitizenOpDeleteSponsor` | `DeleteLink` |

These values define the state-change request carried by the Citizen transaction payload.

---

## 9.7 Operation Semantic Mapping

The public Citizen Protocol terminology and the encoded operation constants map as follows.

| Public Citizen Action | Encoded `Op` Constant | `Op` Value | Meaning |
|---|---|---:|---|
| `CreateNick` | `CitizenOpCreateNickName` | `1` | Creates the sender’s current Nick |
| `DeleteNick` | `CitizenOpDeleteNickName` | `2` | Deletes the sender’s current Nick |
| `CreateReferrer` | `CitizenOpCreateReferrer` | `11` | Registers the sender’s Referrer using the input `RefCode` |
| `DeleteReferrer` | `CitizenOpDeleteReferrer` | `12` | Deletes the sender’s current Referrer |
| `CreateLink` | `CitizenOpCreateSponsor` | `21` | Creates a Link to the target Citizen resolved from `Nick` |
| `DeleteLink` | `CitizenOpDeleteSponsor` | `22` | Deletes a Link to the target Citizen resolved from `Nick` |

The public specification uses:

```text
Nick
Link
```

while some current encoded constant names retain:

```text
NickName
Sponsor
```

The operation values remain authoritative for transaction payload construction.

---

## 9.8 Payload Field Usage by Operation

| Public Citizen Action | `Op` | `Nick` | `RefCode` | `Link` | Description |
|---|---|---|---|---|---|
| `CreateNick` | `CitizenOpCreateNickName` | Nick to register | unused | unused | Create the sender’s current Nick |
| `DeleteNick` | `CitizenOpDeleteNickName` | unused | unused | unused | Delete the sender’s current Nick |
| `CreateReferrer` | `CitizenOpCreateReferrer` | unused | input RefCode | unused | Register the sender’s Referrer |
| `DeleteReferrer` | `CitizenOpDeleteReferrer` | unused | unused | unused | Delete the sender’s current Referrer |
| `CreateLink` | `CitizenOpCreateSponsor` | target Nick | unused | unused | Create a Link to the target Citizen |
| `DeleteLink` | `CitizenOpDeleteSponsor` | target Nick | unused | unused | Delete a Link to the target Citizen |

`Link` is reserved by the current policy.  
Link creation and deletion use the payload field:

```text
Nick
```

rather than a direct Link address.

---

## 9.9 Supported and Unsupported Operations

### Supported `Op` values

| `Op` Constant | Status |
|---|---|
| `CitizenOpCreateNickName` | Supported |
| `CitizenOpDeleteNickName` | Supported |
| `CitizenOpCreateReferrer` | Supported |
| `CitizenOpDeleteReferrer` | Supported |
| `CitizenOpCreateSponsor` | Supported |
| `CitizenOpDeleteSponsor` | Supported |

### Not supported as Citizen payload operations

| Operation | Status |
|---|---|
| `UpdateNick` | Not supported |
| `CreateRefCode` | Not supported |
| `UpdateRefCode` | Not supported |
| `DeleteRefCode` | Not supported |

---

## 9.10 Conceptual Example — `CitizenOpCreateNickName` Transaction

```text
Citizen Protocol transaction:
  from  = addrA
  to    = addrA
  value = 0
  type  = 11
  input = Citizen operation payload with Op = CitizenOpCreateNickName
```

The transaction input payload carries:

```text
Op   = CitizenOpCreateNickName
Nick = "addra01"
```

---

## 9.11 Relationship to Citizen Protocol Specification

Detailed rules for:

- Nick validation,
- Referrer state transitions,
- Link / LinkedBy semantics,
- direct transfer to Nick,
- Credit behavior,

are defined in:

```text
citizen-protocol-spec.md
```

---

# 10. Deposit Policy

## 10.1 Deposit Set Policy

A user may set a deposit for their own account at any time.

When a deposit set transaction succeeds:

- the balance is deducted,
- the amount is converted into deposit state.

A deposit set transaction may be submitted repeatedly if the balance is sufficient.

---

## 10.2 Deposit Restoration Policy

A user may restore a deposit for their own account at any time, subject to restoration restrictions.

When a deposit restoration transaction succeeds:

- the deposit is cleared,
- the restored value returns to the account balance.

---

## 10.3 Restrictions on Deposit Restoration

Deposit restoration fails when the account is actively serving in restricted protocol roles.

Restrictions include:

- the account acts as an active Warrant node,
- the account role is CA,
- the account role is Oraclizer.

---

# 11. V3 Extension Note

This specification records the existing baseline SymVerse transaction model and includes the `type = 11` Citizen Protocol transaction extension.

Additional V3-related extensions such as:

- CAD transaction structure,
- PQC signature-bearing transaction variants,
- CADFork-era authorization flow,

should be specified in later dedicated revisions or companion documents so that the original baseline model remains clear.

---

# 12. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-15 | Initial baseline transaction specification drafted from the existing SymVerse Transaction documentation |
| v0.2 | 2026-05-15 | Added `type = 11` Citizen Protocol transaction, including `TxTypeCitizen`, `from == to` rule, Gas Fee statement, Citizen payload fields, supported operation families, and cross-reference to the Citizen Protocol Specification |
| v0.3 | 2026-05-15 | Clarified that Citizen runtime actions are encoded through the payload `Op` field, documented concrete operation values, mapped public Citizen actions to encoded operation constants, and detailed per-operation payload field usage |
| v0.4 | 2026-05-15 | Refined the Type 11 transaction section so that it remains focused on transaction structure, operation code semantics, and direct payload construction rules |
| v0.5 | 2026-05-15 | Removed internal Go struct and enum declarations from the Type 11 transaction section, retaining only public payload field definitions, operation-code tables, and protocol-level transaction semantics |
| v0.6 | 2026-05-15 | Corrected the reserved Citizen payload address field from `Sponsor` to `Link`; Link creation and deletion remain Nick-resolved operations rather than direct-address payload operations |
 

# SymVerse V3 JSON-RPC SCT API

> **Status:** Baseline import  
> **Date:** 2026-05-16  
> **Document Role:** JSON-RPC SCT API documentation carried into the SymVerse V3 documentation set without PQCFork-specific changes

---

## Reference

[SCT Introduction](https://github.com/symverse-lab/Document/wiki/SCT) 

[SCT Transaction](https://github.com/symverse-lab/Document/wiki/Transaction#type-1---sct-transaction) 

[JSON-RPC 2.0](https://www.jsonrpc.org/specification)

## Enabling the SCT(Symverse Contract Template) APIs

SCT uses write and get methods differently. In the case of recording, a generic transaction is generated to process the SCT and the stored contract can be confirmed via the next (# JSON-RPC SCT). 

To offer these APIs over the Gsym RPC endpoints, please specify them with the `--${interface}api`
command line argument (where `${interface}` can be `rpc` for the HTTP endpoint, `ws` for the WebSocket
endpoint and `ipc` for the unix socket (Unix) or named pipe (Windows) endpoint).

For example: `gsym --ipcapi sct --rpcapi sct --wsapi sct --ws --rpc`

- Enables the official sct API over the IPC interface
- Enables the official sct API over the HTTP interface
- Enables the official sct API over the WebSoket interface

The HTTP RPC interface must be explicitly enabled using the `--rpc` flag.

## SCT APIs Format

[SCT Transaction](https://github.com/symverse-lab/Document/wiki/Transaction#type-1---sct-transaction) 

## SCT APIs Format [for debug]

[debug_getsctrlp](https://github.com/symverse-lab/Document/wiki/Management-API#debug_getsctrlp)

## List of SCT(Symverse Contract Template) APIs

Contracts APIs based on SCT Type and Method.

- `SCT20`: Contract template that implements the interface of ERC20.
- `SCT21`: Contract added lock function to sct20.
- `SCT22`: Contract template that implements the authorized transfer.
- `SCT30`: Contract template that implements the interface of ERC721.
- `SCT40`: SCT30 based coupon contract template.
- `SCT51`: Contract template that implements the management of Vote service.
- `SCT52`: Contract template that implements the Voting service.

| [SCT20](#sct20)                                       | [SCT30](#sct30)                               | [SCT40](#sct40)                               |
| :--------------------------------                     | :-------------------------------------------  | --------------------------                    |
| [SCT20_CREATE](#sct20_create)                         | [SCT30_CREATE](#sct30_create)                 | [SCT40_CREATE](#sct40_create)                 |
| [SCT20_TRANSFER](#sct20_transfer)                     | [SCT30_CREATE_ITEM](#sct30_create_item)       | [SCT40_CREATE_COUPON](#sct40_create_coupon)   |
| [SCT20_TRANSFER_FROM](#sct20_transfer_from)           | [SCT30_TRANSFER](#sct30_transfer)             | [SCT40_TRANSFER](#sct40_transfer)             |
| [SCT20_APPROVE](#sct20_approve)                       | [SCT30_TRANSFER_FROM](#sct30_transfer_from)   | [SCT40_TRANSFER_FROM](#sct40_transfer_from)   |
| [SCT20_DECREASE_APPROVE](#sct20_decrease_approve) |                                               |
| [SCT20_MINT](#sct20_mint)                             | [SCT30_APPROVE](#sct30_approve)               | [SCT40_APPROVE](#sct40_approve)               |
| [SCT20_BURN](#sct20_burn)                             | [SCT30_ITEM_PAUSE](#sct30_item_pause)         | [SCT40_COUPON_USE](#sct40_coupon_use)         |
| [SCT20_PAUSE](#sct20_pause)                           | [SCT30_ITEM_UNPAUSE](#sct30_item_unpause)     | [SCT40_COUPON_PAUSE](#sct40_coupon_pause)     |
| [SCT20_UNPAUSE](#sct20_unpause)                       |                                               | [SCT40_COUPON_UNPAUSE](#sct40_coupon_unpause) |             
| [SCT20_TRANSFER_OWNER](#sct20_transfer_owner)         | [SCT30_TRANSFER_OWNER](#sct30_transfer_owner) | [SCT40_TRANSFER_OWNER](#sct40_transfer_owner) |                                                                    

| [SCT21](#sct21)                                           |  [SCT22](#sct22)                                           |
| :-----------------------------------------------------    |  :-----------------------------------------------------    |
| [SCT21_CREATE](#sct21_create)                             |  [SCT22_CREATE](#sct22_create)                             |
| [SCT21_TRANSFER](#sct21_transfer)                         |  [SCT22_TRANSFER](#sct22_transfer)                         |
| [SCT21_TRANSFER_FROM](#sct21_transfer_from)               |                                                            |
| [SCT21_APPROVE](#sct21_approve)                           |                                                            |
| [SCT21_DECREASE_APPROVE](#sct21_decrease_approve)         |                                                            |
| [SCT21_MINT](#sct21_mint)                                 |  [SCT22_MINT](#sct22_mint)                                 |
| [SCT21_BURN](#sct21_burn)                                 |  [SCT22_BURN](#sct22_burn)                                 |
| [SCT21_PAUSE](#sct21_pause)                               |  [SCT22_PAUSE](#sct22_pause)                               |
| [SCT21_UNPAUSE](#sct21_unpause)                           |  [SCT22_UNPAUSE](#sct22_unpause)                           |
| [SCT21_TRANSFER_OWNER](#sct21_transfer_owner)             |  [SCT22_TRANSFER_OWNER](#sct22_transfer_owner)             |
| [SCT21_LOCK_TRANSFER](#sct21_lock_transfer)               |  [SCT22_SET_AUTHORITY](#sct22_set_authority)               |
| [SCT21_UNLOCK_AMOUNT](#sct21_unlock_amount)               |                                                            |
| [SCT21_RESTORE_LOCK_AMOUNT](#sct21_restore_lock_amount)   |                                                            |
| [SCT21_ADD_LOCK_AMOUNT](#sct21_add_lock_amount)           |                                                            |
| [SCT21_SUB_LOCK_AMOUNT](#sct21_sub_lock_amount)           |                                                            |
| [SCT21_ACCOUNT_LOCK](#sct21_account_lock)                 |                                                            |
| [SCT21_ACCOUNT_UNLOCK](#sct21_account_unlock)             |                                                            |

  

| [SCT50](#sct50)                                           | [SCT51](#sct51)                                           |
| :-------------------------------------------------------- | :-------------------------------------------------------- |
| [SCT50_CREATE](#sct50_create)                             | [SCT51_CREATE_POLL](#sct51_create_poll)                   |
| [SCT50_ADD_POLL_CREATORS](#sct50_add_poll_creators)       | [SCT51_VOTE_IN_POLL](#sct51_vote_in_poll)                 |
| [SCT50_ROMOVE_POLL_CREATORS](#sct50_remove_poll_cretors)  | [SCT51_UNSTAKE_TOKENS](#sct51_unstake_tokens)             |
|                                                           | [SCT51_EMERGENCY_STOP_POLL](#sct51_emergency_stop_poll)   |
|                                                           | [SCT51_FINISH_POLL](#sct51_finish_poll)                   |
|                                                           | [SCT51_WRITE_POLL_RESULTS](#sct51_write_poll_results)     |




## SCT20

##### SCT20 is a template that implements ERC20 Interface. You can use the same features as Total Supply, Transfer, Balance Of, Transfer From, and Approve.

### SCT20_CREATE
Create an SCT20 contract.

-  Type : 20
-  Method : 0
-  Parameters :
    1. `Name`: `STRING` - Contract(Token) Name
    2. `Symbol`: `STRING` - Contract(Token) Symbol. The length should be from 3 to 10.
    3. `Amount`: `QUANTITY` - Total Supply
    4. `Owner`: `DATA`, 10 Bytes - address of the contract owner.

#### Example

```javascript
// Create SCT20 Token whose name is "SymToken" and 
// symbol is "STK" and 
// total supply is 100 * 10^18 STK (="0x56bc75e2d63100000") and 
// contract owner is "0x00020000000000070002"
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x14", "method": "0x0", "params": {"name": "SymToken", "symbol": "STK", "amount": "0x56bc75e2d63100000", "owner": "0x00020000000000070002"}})
// Result: RLP encoded SCT data
"0xe51480e28853796d546f6b656e8353544b89056bc75e2d631000008a00020000000000070002"

// Send SCT Transaction
sym.sendTransaction({"from": "0x00020000000000070002", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x0", "type": "0x1", "input": "0xe51480e28853796d546f6b656e8353544b89056bc75e2d631000008a00020000000000070002"})
// Result: Transaction Hash
"0xa90be0a11140b1fb2e5e11f4c3702791dc4467f6e2a3616b261ca45107faadb9"

// <Validate: Create>
// Get contract address from transaction hash
sym.getTransactionReceipt("0xa90be0a11140b1fb2e5e11f4c3702791dc4467f6e2a3616b261ca45107faadb9").contractAddress
// Result: contractAddress.
"0xeda14153a4cb151daeae"

// Get Contract information from contract address.
sct.getContract("0xeda14153a4cb151daeae")
// Result: Contract information created with SCT20 create method.
{ 
  creator: "0x00020000000000070002",
  name: "SymToken",
  owner: "0x00020000000000070002",
  state: "active",
  symbol: "STK",
  total: "0x56bc75e2d63100000",
  type: "sct20"
}
```

### SCT20_TRANSFER
Ownership token transfer capability `(Authorization: all)`

-  Type : 20
-  Method : 1
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address to receive sct20 token.
    2. `Amount`: `QUANTITY` - value to transfer sct20 token.

#### Example

```javascript
// Transfer 50 * 10^18 STK (="0x2b5e3af16b1880000") token created from above example to "0x00020000000000060002"
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x14", "method": "0x1", "params": {"to": "0x00020000000000060002", "amount":"0x2b5e3af16b1880000"}})
// Result
"0xd81401d58a000200000000000600028902b5e3af16b1880000"

// Send SCT Transaction
sym.sendTransaction({"from": "0x00020000000000070002", "to": "0xeda14153a4cb151daeae", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x1", "type": "0x1", "input": "0xd81401d58a000200000000000600028902b5e3af16b1880000"})
// Result
"0x39bd90c419da2114fca9b61bdd6287b29545a39f2e53ac3c1e4fae1dc9ac1c32"

// <Validate: Transfer>
// Get token holder's balance
> sct.getContractAccount("0xeda14153a4cb151daeae","0x00020000000000070002")
{
  balance: "0x2b5e3af16b1880000‬"
}
> sct.getContractAccount("0xeda14153a4cb151daeae","0x00020000000000060002")
{ 
  balance: "0x2b5e3af16b1880000"
}
```

### SCT20_TRANSFER_FROM
Send a delegated token via APPROVE `(Authorization: all)`

-  Type : 20
-  Method : 2
-  Parameters :
    1. `From`: `DATA`, 10 Bytes - address to transfer.
    2. `To`: `DATA`, 10 Bytes - address to receive sct20 token.
    3. `Amount`: `QUANTITY` - value to transfer sct20 token.

#### Example

```javascript
// <Pre-setting: Approve>
// Approve 1 * 10^18 STK(="0xde0b6b3a7640000") to "0x00020000000000060002".
// Get SCT rlp encoded string
> debug.getSCTRlp({ "type": "0x14", "method": "0x3", "params": {"to": "0x00020000000000060002", "amount":"0xde0b6b3a7640000"}})
// Result
"0xd71403d48a00020000000000060002880de0b6b3a7640000"
// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000070002", "to": "0xeda14153a4cb151daeae", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x3", "type": "0x1", "input": "0xd71403d48a00020000000000060002880de0b6b3a7640000"})
// Result
"0xafa11d55043ec1c85b100fd52cfe8b4127129224d8171d5a3fe995190480349d"

// <Tranfer From>
// Transfer 1 * 10^18 STK(="0xde0b6b3a7640000") from "0x00020000000000070002" to "0x00020000000000060002".
// Get SCT rlp encoded string
> debug.getSCTRlp({ "type": "0x14", "method": "0x2", "params": {"from": "0x00020000000000070002", "to": "0x00020000000000060002", "amount":"0xde0b6b3a7640000"}})
// Result
"0xe21402df8a000200000000000700028a00020000000000060002880de0b6b3a7640000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0xeda14153a4cb151daeae", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x0", "type": "0x1", "input": "0xe21402df8a000200000000000700028a00020000000000060002880de0b6b3a7640000"})
// Result
"0x6ddb04417211b33776c15e5921a280bf27d1c1c96ffe967988e621d68125c5ee"


// <Validate: Transfer From>
// Before Transfer From
// Get Approved receiver account's allowance
> sct.getAllowance("0xeda14153a4cb151daeae","0x00020000000000070002","0x00020000000000060002")
{ 
  balance: "0xde0b6b3a7640000"
}
// Get Approved receiver account's balance
> sct.getContractAccount("0xeda14153a4cb151daeae","0x00020000000000060002")
{ 
  balance: "0x0"
}
// Get token holder's balance
> sct.getContractAccount("0xeda14153a4cb151daeae","0x00020000000000070002")
{ 
  balance: "0x2b5e3af16b1880000"‬
}
// After Transfer From
// Get Approved receiver account's allowance
> sct.getAllowance("0xeda14153a4cb151daeae","0x00020000000000070002","0x00020000000000060002")
{ 
  balance: "0x0"
}
// Get Approved receiver account's balance
> sct.getContractAccount("0xeda14153a4cb151daeae","0x00020000000000060002")
{ 
  balance: "0xde0b6b3a7640000"
}
// Get Token holder's balance
> sct.getContractAccount("0xeda14153a4cb151daeae","0x00020000000000070002")
{ 
  balance: "0x2a802f8630a240000"
}
```

### SCT20_APPROVE
Delegate some tokens to other users `(Authorization: all)`

-  Type : 20
-  Method : 3
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address of the spender.
    2. `Amount`: `QUANTITY` - value to transfer sct20 token.

#### Example

```javascript
// Approve 1 * 10^18 STK(="0xde0b6b3a7640000") to "0x00020000000000060002".
// Get SCT rlp encoded string
> debug.getSCTRlp({ "type": "0x14", "method": "0x3", "params": {"to": "0x00020000000000060002", "amount":"0xde0b6b3a7640000"}})
// Result
"0xd71403d48a00020000000000060002880de0b6b3a7640000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000070002", "to": "0xeda14153a4cb151daeae", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x3", "type": "0x1", "input": "0xd71403d48a00020000000000060002880de0b6b3a7640000"})
// Result
"0xafa11d55043ec1c85b100fd52cfe8b4127129224d8171d5a3fe995190480349d"

// Validate Approve
// Get Approved receiver account's allowance
> sct.getAllowance("0xeda14153a4cb151daeae","0x00020000000000070002","0x00020000000000060002")
{ 
  balance: "0xde0b6b3a7640000"
}
```

### SCT20_DECREASE_APPROVE
Decrease the amount of tokens that an owner allowed to a spender.  `(Authorization: all)`

-  Type : 20
-  Method : 4
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address of the spender.
    2. `Amount`: `QUANTITY` - value to transfer sct20 token.

#### Example

```javascript
// Decrease approve 1 * 10^18 STK(="0xde0b6b3a7640000") to "0x00020000000000060002".
// Get SCT rlp encoded string
> debug.getSCTRlp({ "type": "0x14", "method": "0x4", "params": {"to": "0x00020000000000060002", "amount":"0xde0b6b3a7640000"}})
// Result
"0xd71404d48a00020000000000060002880de0b6b3a7640000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000070002", "to": "0xeda14153a4cb151daeae", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x4", "type": "0x1", "input": "0xd71404d48a00020000000000060002880de0b6b3a7640000"})
// Result
"0x8bf97d0a2582fc5bbc7e4fd2ea69d1585cac0fe61d88350c847627f5b06c754a"

// <Validate: Decrease Approve>
// Get De-Approved receiver account's allowance
> sct.getAllowance("0xeda14153a4cb151daeae","0x00020000000000070002","0x00020000000000060002")
{ 
  balance: "0x0"
}
```

### SCT20_MINT
Token amount mint `(Authorization: owner, creator)`

-  Type : 20
-  Method : 5
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address to receive sct20 token.
    2. `Amount`: `QUANTITY` - amount to mint sct20 token.

#### Example

```javascript
// Issue 1 * 10^18 STK (="0xde0b6b3a7640000") to "0x00020000000000070002".
// Get SCT rlp encoded string
> debug.getSCTRlp({ "type": "0x14", "method": "0x5", "params": {"to": "0x00020000000000070002", "amount":"0xde0b6b3a7640000"}})
// Result
"0xd71405d48a00020000000000070002880de0b6b3a7640000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000070002", "to": "0xeda14153a4cb151daeae", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x5", "type": "0x1", "input": "0xd71405d48a00020000000000070002880de0b6b3a7640000"})
// Result
"0x042bf7bdd76a2a2832429b5904e3f152b35ec55b5368a880a93ebae9ad63e8e6"

// <Validate: Mint>
// Before Mint
> sct.getContractAccount("0xeda14153a4cb151daeae","0x00020000000000070002")
{
  balance: "0x2a802f8630a240000"
}
// After Mint
> sct.getContractAccount("0xeda14153a4cb151daeae","0x00020000000000070002")
{
  balance: "0x2b5e3af16b1880000"
}
```

### SCT20_BURN
Token amount burn `(Authorization: owner, creator)`

- Type : 20
- Method : 6
- Parameters :
    1. `From`: `DATA`, 10 Bytes - address to remove sct20 token.
    2. `Amount`: `QUANTITY` - amount to remove sct20 token.

#### Example

```javascript
// Remove 1 * 10^18 STK (="0xde0b6b3a7640000") from "0x00020000000000070002"'s balance.
// Get SCT rlp encoded string
> debug.getSCTRlp({ "type": "0x14", "method": "0x6", "params": {"from": "0x00020000000000070002", "amount":"0xde0b6b3a7640000"}})
// Result
"0xd71406d48a00020000000000070002880de0b6b3a7640000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000070002", "to": "0xeda14153a4cb151daeae", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x6", "type": "0x1", "input": "0xd71406d48a00020000000000070002880de0b6b3a7640000"})
// Result
"0x29e18e05ffd2a75aba4966542c6088e3128d26b10ea68821b86fb4cca537809b"

// <Validate: Burn>
// Before Burn
> sct.getContractAccount("0xeda14153a4cb151daeae","0x00020000000000070002")
{
  balance: "0x2b5e3af16b1880000‬"
}
// After Burn
> sct.getContractAccount("0xeda14153a4cb151daeae","0x00020000000000070002")
{
  balance: "0x2a802f8630a240000"
}
```

### SCT20_PAUSE
Pause the sct20 transaction `(Authorization: owner, creator)`

- Type : 20
- Method : 7
- Parameters : Null

#### Example

```javascript
// Get SCT rlp encoded string
> debug.getSCTRlp({ "type": "0x14", "method": "0x7"})
// Result
"0xc31407c0"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000070002", "to": "0xeda14153a4cb151daeae", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x7", "type": "0x1", "input": "0xc31407c0"})
// Result
"0x02eef9e76f076f557f05dbd40f09d59d293feb47a3b84a162b581c3f4040ad0d"
```

### SCT20_UNPAUSE
Unpause the sct20 transaction `(Authorization: owner, creator)`

- Type : 20
- Method : 8
- Parameters : Null

#### Example

```javascript
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x14", "method": "0x8"})
// Result
"0xc31408c0"

// Send SCT Transaction
sym.sendTransaction({"from": "0x00020000000000070002", "to": "0xeda14153a4cb151daeae", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x8", "type": "0x1", "input": "0xc31408c0"})
// Result
"0xa7c27b6375ed8379d022453ab5305dd27a5b7ad9c8f88e4592b983357654eeb1"
```

### SCT20_TRANSFER_OWNER
change owner permissions `(Authorization: owner, creator)`

- Type : 20
- Method : 9
- Parameters : 
    1. `To`: `DATA`, 10 Bytes - address to be contract owner
    
#### Example

```javascript
// Transfer contract ownership to "0x00020000000000060002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x14", "method": "0x9", "params": {"to": "0x00020000000000060002"}})
// Result
"0xce1409cb8a00020000000000060002"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000070002", "to": "0xeda14153a4cb151daeae", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x9", "type": "0x1", "input": "0xce1409cb8a00020000000000060002"})
// Result
"0x2edae8387e79e05d16bb5eb08deeff905a2171a74d5b7ab9cd0da664d82102ff"

// <Validate: Transfer Owner>
// Before Transfer Owner
> sct.getContract("0xeda14153a4cb151daeae").owner
"0x00020000000000070002"
// After Transfer Owner
> sct.getContract("0xeda14153a4cb151daeae").owner
"0x00020000000000060002"
```

## SCT21
##### SCT21 is the addition of a lock function to SCT20. You can manage tokens in greater detail by locking accounts or locking amounts.

### SCT21_CREATE
Create an SCT21 contract.

-  Type : 21
-  Method : 0
-  Parameters :
    1. `Name`: `STRING` - Contract(Token) Name
    2. `Symbol`: `STRING` - Contract(Token) Symbol. The length should be from 3 to 10.
    3. `Amount`: `QUANTITY` - Total supply
    4. `LockAmount`: `QUANTITY` - Locked amount
    5. `Owner`: `DATA`, 10 Bytes - address of the contract owner

#### Example

```javascript
// Create SCT21 Token whose name is "SymToken" and 
// symbol is "STK" and 
// total supply is 100 * 10^18 STK (="0x56bc75e2d63100000") and 
// locked amount is 100 * 10^18 STK (="0x56bc75e2d63100000") and 
// contract owner is "0x00020000000000060002"
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0x0", "params": {"name": "SymToken", "symbol": "STK", "amount": "0x56bc75e2d63100000", "lockAmount": "0x56bc75e2d63100000", "owner": "0x00020000000000060002"}})
// Result: RLP encoded SCT data
"0xef1580ec8853796d546f6b656e8353544b89056bc75e2d6310000089056bc75e2d631000008a00020000000000060002"

// Send SCT Transaction
sym.sendTransaction({"from": "0x00020000000000060002", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x0", "type": "0x1", "input": "0xef1580ec8853796d546f6b656e8353544b89056bc75e2d6310000089056bc75e2d631000008a00020000000000060002"})
// Result: Transaction Hash
"0xc821c83439f0000f4dd83f98b2d097c21a108ddf6b2031b918cd7da8c4ddd432"

// Validate Create
// Get Transaction Recipt from transaction hash
sym.getTransactionReceipt("0xc821c83439f0000f4dd83f98b2d097c21a108ddf6b2031b918cd7da8c4ddd432").contractAddress
// Result: Transaction information with contractAddress.
"0x32731dbab5dc4134790c"

// Get Contract information from contract address.
sct.getContract("0x32731dbab5dc4134790c")
// Result: Contract information created with SCT20 create method.
{ 
  creator: "0x00020000000000060002",
  lock: "0x56bc75e2d63100000",
  name: "SymToken",
  owner: "0x00020000000000060002",
  state: "active",
  symbol: "STK",
  total: "0x56bc75e2d63100000‬",
  type: "sct21"
}
```

### SCT21_TRANSFER
Ownership token transfer capability `(Authorization: all)`

-  Type : 21
-  Method : 1
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address to receive sct21 token.
    2. `Amount`: `QUANTITY` - value transferred in sct21 token.

#### Example

```javascript
// Transfer 50 * 10^18 STK (="0x2b5e3af16b1880000") token created from above example to "0x00020000000000050002"
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0x1", "params": {"to": "0x00020000000000050002", "amount": "0x2b5e3af16b1880000"}})
// Result
"0xd81501d58a000200000000000500028902b5e3af16b1880000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x1", "type": "0x1", "input": "0xd81501d58a000200000000000500028902b5e3af16b1880000"})
// Result
"0xf4d15813040b80c260dafdd8c1037ad7156dc3cb7349c193bec46ad67c212a57"
```

### SCT21_TRANSFER_FROM
Send a delegated token via APPROVE `(Authorization: all)`

-  Type : 21
-  Method : 2
-  Parameters :
    1. `From`: `DATA`, 10 Bytes - address to transfer.
    2. `To`: `DATA`, 10 Bytes - address to receive sct21 token.
    3. `Amount`: `QUANTITY` - value to transfer sct21 token.

#### Example

```javascript
// Transfer 1 * 10^18 STK(="0xde0b6b3a7640000") from "0x00020000000000060002" to "0x00020000000000050002". (from address should be preceded approve. Refer to SCT20 Transfer From example.)
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0x2", "params": {"from": "0x00020000000000060002", "to": "0x00020000000000050002", "amount": "0xde0b6b3a7640000"}})
// Result
"0xe21502df8a000200000000000600028a00020000000000050002880de0b6b3a7640000"

// Send SCT Transaction
sym.sendTransaction({"from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x2", "type": "0x1", "input": "0xe21502df8a000200000000000600028a00020000000000050002880de0b6b3a7640000"})
// Result
"0x5c65b7e678423611fa19f13bb333d1c8cd2152ede2fcb94f83da72d3c4a88d1c"
```

### SCT21_APPROVE
Delegate some tokens to other users `(Authorization: all)`

-  Type : 21
-  Method : 3
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address of the spender.
    2. `Amount`: `QUANTITY` - value to transfer sct21 token.

#### Example

```javascript
// Approve 1 * 10^18 STK(="0xde0b6b3a7640000") to "0x00020000000000050002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0x3", "params": {"to": "0x00020000000000050002", "amount": "0xde0b6b3a7640000"}})
// Result
"0xd71503d48a00020000000000050002880de0b6b3a7640000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x3", "type": "0x1", "input": "0xd71503d48a00020000000000050002880de0b6b3a7640000"})
// Result
"0x635fadbec85b1ca7368cb32d48832d0a58935f121900a4d052e032e8e2634b1b"
```

### SCT21_DECREASE_APPROVE
Decrease the amount of tokens that an owner allowed to a spender. `(Authorization: all)`

-  Type : 21
-  Method : 4
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address of the spender.
    2. `Amount`: `QUANTITY` - value to transfer sct21 token.

#### Example

```javascript
// Decrease approve 1 * 10^18 STK(="0xde0b6b3a7640000") to "0x00020000000000050002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0x4", "params": {"to": "0x00020000000000050002", "amount": "0xde0b6b3a7640000"}})
// Result
"0xd71504d48a00020000000000050002880de0b6b3a7640000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x4", "type": "0x1", "input": "0xd71504d48a00020000000000050002880de0b6b3a7640000"})
// Result
"0x24e0ddc8e56060161448e1cceef9a9407ff77ddc25e1dbf3bf7c0c48d70f6b0e"
```

### SCT21_MINT
Token amount mint  `(Authorization: all)`

-  Type : 21
-  Method : 5
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address to receive sct21 token.
    2. `Amount`: `QUANTITY` - amount to issue in sct21 token.

#### Example

```javascript
// Issue 1 * 10^18 STK (="0xde0b6b3a7640000") to "0x00020000000000060002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0x5", "params": {"from": "0x00020000000000060002", "amount": "0xde0b6b3a7640000"}})
// Result
"0xd71505d48a00020000000000060002880de0b6b3a7640000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x5", "type": "0x1", "input": "0xd71505d48a00020000000000060002880de0b6b3a7640000"})
// Result
"0xd750609cdc4723d244efd34dea8d129debf10be574ea94d7d084386c092b53ae"
```

### SCT21_BURN
Token amount burn `(Authorization: owner, creator)`

- Type : 21
- Method : 6
- Parameters :
    1. `To`: `DATA`, 10 Bytes - address to remove sct21 token.
    2. `Amount`: `QUANTITY` - amount to remove sct21 token.

#### Example

```javascript
// Remove 1 * 10^18 STK (="0xde0b6b3a7640000") from "0x00020000000000060002"'s balance.
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0x6", "params": {"to": "0x00020000000000060002", "amount": "0xde0b6b3a7640000"}})
// Result
"0xd71506d48a00020000000000060002880de0b6b3a7640000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x6", "type": "0x1", "input": "0xd71506d48a00020000000000060002880de0b6b3a7640000"})
// Result
"0xfde9237efaa328e2ce0a289defd31d1a265e2eb2d4f79859b48611db7cfa25dd"
```

### SCT21_PAUSE
Pause the sct21 contract `(Authorization: owner, creator)`

- Type : 21
- Method : 7
- Parameters : Null

#### Example

```javascript
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0x7"})
// Result
"0xc31507c0"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x7", "type": "0x1", "input": "0xc31507c0"})
// Result
"0x417915b3b4e6b22c079b0cdceca7372cf7ba3df682507a73d91d685149153ba1"
```

### SCT21_UNPAUSE
Unpause the sct21 contract `(Authorization: owner, creator)`

- Type : 21
- Method : 8
- Parameters : Null

#### Example

```javascript
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0x8"})
// Result
"0xc31508c0"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x8", "type": "0x1", "input": "0xc31508c0"})
// Result
"0x5b6ffe84611df757659a52f6313b252d021ed75ab88e428009de253117402fdc"
```

### SCT21_TRANSFER_OWNER
change owner permissions `(Authorization: owner, creator)`

- Type : 21
- Method : 9
- Parameters : 
    1. `To`: `DATA`, 10 Bytes - address to be the contract owner

#### Example

```javascript
// Transfer contract ownership to "0x00020000000000040002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0x9", "params": {"to": "0x00020000000000040002"}})
// Result
"0xce1509cb8a00020000000000040002"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x9", "type": "0x1", "input": "0xce1509cb8a00020000000000040002"})
// Result
"0x785118298e2a32bb08c51cf3d68fdcf1e52b8111cabea94ad042ddba7a5db168"
```

### SCT21_LOCK_TRANSFER
Send amount of locked token `(Authorization: owner, creator)`

-  Type : 21
-  Method : 10
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address to receive sct21 token.
    2. `Amount`: `QUANTITY` - value transferred in sct21 token.
    3. `LockAmount`: `QUANTITY` - lock value transferred in sct21 token.

#### Example

```javascript
// Transfer 1 * 10^18 (="0xde0b6b3a7640000") locked STK and 1 * 10^18 (="0xde0b6b3a7640000") STK to "0x00020000000000040002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0xa", "params": {"to": "0x00020000000000040002", "amount": "0xde0b6b3a7640000", "lockAmount": "0xde0b6b3a7640000"}})
// Result
"0xe0150add8a00020000000000040002880de0b6b3a7640000880de0b6b3a7640000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0xa", "type": "0x1", "input": "0xe0150add8a00020000000000040002880de0b6b3a7640000880de0b6b3a7640000"})
// Result
"0x4f9e170913eae3d05f3fa3354d4b5850a1729d8237e7440884aaf55843083ba5"
```

### SCT21_UNLOCK_AMOUNT
Unlock transmitted token `(Authorization: owner, creator)`

-  Type : 21
-  Method : 11
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address of the spender.
    2. `Amount`: `QUANTITY` - unlock amount

#### Example

```javascript
// Unlock 1 * 10^18 (="0xde0b6b3a7640000") STK to "0x00020000000000040002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0xb", "params": {"to": "0x00020000000000040002", "amount": "0xde0b6b3a7640000"}})
// Result
"0xd7150bd48a00020000000000040002880de0b6b3a7640000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0xb", "type": "0x1", "input": "0xd7150bd48a00020000000000040002880de0b6b3a7640000"})
// Result
"0xaf8f8dcfad0d03c0f33ff2084cb356db9d7b47dc228299f0b45298705f2c8949"
```

### SCT21_RESTORE_LOCK_AMOUNT
Reclaims transmitted lock token `(Authorization: owner, creator)`

-  Type : 21
-  Method : 12
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address of the spender.
    2. `Amount`: `QUANTITY` - amount to reclaims

#### Example

```javascript
// Reclaim 50 * 10^18 (="0x2b5e3af16b1880000") locked STK to "0x00020000000000040002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0xc", "params": {"to": "0x00020000000000040002", "amount": "0x2b5e3af16b1880000"}})
// Result
"0xd8150cd58a000200000000000400028902b5e3af16b1880000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0xc", "type": "0x1", "input": "0xd8150cd58a000200000000000400028902b5e3af16b1880000"})
// Result
"0x9fdac71548c4ba1c3b6e23f70ec6c75cbeee3ef435e672826877fa729ce6f39b"
```

### SCT21_ADD_LOCK_AMOUNT
Add amount to TotalLockSupply `(Authorization: creator)`

-  Type : 21
-  Method : 13
-  Parameters :
    1. `Amount`: `QUANTITY` - lock amount

#### Example

```javascript
// Add 50 * 10^18 (="0x2b5e3af16b1880000") locked STK to creator.
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0xd", "params": {"amount": "0x2b5e3af16b1880000"}})
// Result
"0xcd150dca8902b5e3af16b1880000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0xd", "type": "0x1", "input": "0xcd150dca8902b5e3af16b1880000"})
// Result
"0x16097c89a39a2f2f62793c111999f194f351dcb2b9cea87b4807d1fbc5f129f5"
```

### SCT21_SUB_LOCK_AMOUNT
Remove amount to Total Supply `(Authorization: creator)`

-  Type : 21
-  Method : 14
-  Parameters :
    1. `Amount`: `QUANTITY` - lock amount

#### Example

```javascript
// Remove 50 * 10^18 (="0x2b5e3af16b1880000") locked STK from creator.
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0xe", "params": {"amount": "0x2b5e3af16b1880000"}})
// Result
"0xcd150eca8902b5e3af16b1880000"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0xe", "type": "0x1", "input": "0xcd150eca8902b5e3af16b1880000"})
// Result
"0x08e0a6d730c606c54d0ca7735628fc259b9ea73ad9b6e6651c13f5303ab76ae3"
```

### SCT21_ACCOUNT_LOCK
Lock account `(Authorization: owner, creator)`

-  Type : 21
-  Method : 15
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - account to lock

#### Example

```javascript
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0xf", "params": {"to": "0x00020000000000060002"}})
// Result
"0xce150fcb8a00020000000000060002"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0xf", "type": "0x1", "input": "0xce150fcb8a00020000000000060002"})
// Result
"0xb7647db0f47e11ec32738d8cc4a77906cd175c3256879e5ca23b2a8c96d17848"
```

### SCT21_ACCOUNT_UNLOCK
UnLock account `(Authorization: owner, creator)`

-  Type : 21
-  Method : 16
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - account to unlock

#### Example

```javascript
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x15", "method": "0x10", "params": {"to": "0x00020000000000060002"}})
// Result
"0xce1510cb8a00020000000000060002"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x32731dbab5dc4134790c", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x10", "type": "0x1", "input": "0xce1510cb8a00020000000000060002"})
// Result
"0x41c5b560eca7c5d2666457f5745df1e1bcfbc746cf991042b51c32b99ff339c0"
```


## SCT22
##### SCT22 is a template that implements authorized transfer based on ERC20.

### SCT22_CREATE
Create an SCT22 contract.

-  Type : 22
-  Method : 0
-  Parameters :
    1. `Name`: `STRING` - Contract(Token) Name
    2. `Symbol`: `STRING` - Contract(Token) Symbol
    3. `TotalSupply`: `QUANTITY` - TotalSupply
    4. `OwnerSymId`: `DATA`, 10 Bytes - address of the contract owner.

#### Example

```javascript
> rlp.encode([22, 0, [
    "SymToken", "STK", 10000000000, 0x00000000000000000001
]])
0xe18080de8853796d546f6b656e8353544b8502540be4008a00000000000000000001
sym.sendTransaction({
    to: null, from: sym.accounts[0], type:"0x1", input: "0xe18080de8853796d546f6b656e8353544b8502540be4008a00000000000000000001"
})
```

### SCT22_TRANSFER
Ownership token transfer capability `(Authorization: all)`

-  Type : 22
-  Method : 1
-  Parameters :
    1. `Recipient`: `DATA`, 10 Bytes - address of the receiver.
    2. `Amount`: `QUANTITY` - value transferred in sct20 token.

#### Example

```javascript
> rlp.encode([22, 1, [
    0x00000000000000000002, 500
]])
0xd18001ce8a000000000000000000028201f4
sym.sendTransaction({
   to: "0x0CED1024EEd02B234df2", from: sym.accounts[0], type:"0x1", input: "0xd18001ce8a000000000000000000028201f4"
})
```

### SCT22_TRANSFER_FROM
Send a delegated token via APPROVE `(Authorization: all)`

-  Type : 22
-  Method : 2
-  Parameters :
    1. `From`: `DATA`, 10 Bytes - address of the from.
    2. `To`: `DATA`, 10 Bytes - address of the receiver.
    3. `Amount`: `QUANTITY` - value transferred in sct20 token.

#### Example

```javascript
> rlp.encode([22, 2, [
    0x00000000000000000001, 0x00000000000000000003, 500
]])
0xdc8002d98a000000000000000000018a000000000000000000038201f4
sym.sendTransaction({
   to: "0x0CED1024EEd02B234df2", from: sym.accounts[0], type:"0x1", input: "0xdc8002d98a000000000000000000018a000000000000000000038201f4"
})
```

### SCT22_MINT
Token amount mint `(Authorization: owner, creator)`

-  Type : 22
-  Method : 3
-  Parameters :
    1. `Recipient`: `DATA`, 10 Bytes - address of the receiver.
    2. `Amount`: `QUANTITY` - amount to issue in sct20 token.

#### Example

```javascript
> rlp.encode([22, 3, [
    0x00000000000000000002, 500
]])
0xd18005ce8a000000000000000000028201f4
sym.sendTransaction({
   to: "0x0CED1024EEd02B234df2", from: sym.accounts[0], type:"0x1", input: "0xd18005ce8a000000000000000000028201f4"
})
```

### SCT22_BURN
Token amount burn `(Authorization: owner, creator)`

- Type : 22
- Method : 4
- Parameters :
    1. `Recipient`: `DATA`, 10 Bytes - address of the receiver.
    2. `Amount`: `QUANTITY` - amount to issue in sct20 token.

#### Example

```javascript
> rlp.encode([22, 4, [
    0x00000000000000000002, 500
]])
0xd18006ce8a000000000000000000028201f4
sym.sendTransaction({
   to: "0x0CED1024EEd02B234df2", from: sym.accounts[0], type:"0x1", input: "0xd18006ce8a000000000000000000028201f4"
})
```

### SCT22_PAUSE
Pause the sct22 transaction `(Authorization: owner, creator)`

- Type : 22
- Method : 5
- Parameters : Null

#### Example

```javascript
> rlp.encode([22, 5, []])
0xc38007c0
sym.sendTransaction({
   to: "0x0CED1024EEd02B234df2", from: sym.accounts[0], type:"0x1", input: "0xc38007c0"
})
```

### SCT22_UNPAUSE
Unpause the sct22 transaction `(Authorization: owner, creator)`

- Type : 22
- Method : 6
- Parameters : Null

#### Example

```javascript
> rlp.encode([22, 6, []])
0xc38008c0
sym.sendTransaction({
   to: "0x0CED1024EEd02B234df2", from: sym.accounts[0], type:"0x1", input: "0xc38008c0"
})
```

### SCT22_TRANSFER_OWNER
change owner permissions `(Authorization: owner, creator)`

- Type : 22
- Method : 7
- Parameters : 
    1. `OwnerSymId`: `DATA`, 10 Bytes - address of the contract owner
    
#### Example

```javascript
> rlp.encode([22, 7, []])
0xc38009c0
sym.sendTransaction({
   to: "0x0CED1024EEd02B234df2", from: sym.accounts[0], type:"0x1", input: "0xc38009c0"
})
```

### SCT22_SET_AUTHORITY
Set Authority for sct22 token `(Authorization: owner, creator)`

- Type : 22
- Method : 8
- Parameters : 
    1. `OwnerSymId`: `DATA`, 10 Bytes - address of the contract owner
    
#### Example

```javascript
> rlp.encode([22, 8, []])
0xc38009c0
sym.sendTransaction({
   to: "0x0CED1024EEd02B234df2", from: sym.accounts[0], type:"0x1", input: "0xc38009c0"
})
```

## SCT30
SCT30 is a template that implements the ERC721 Interface. (NFT) A non-fungible token that is used when using transactions for unique items.

### SCT30_CREATE
Create an SCT30 contract.

-  Type : 30
-  Method : 0
-  Parameters :
    1. `Name`: `STRING` - Contract(Token) Name
    2. `Symbol`: `STRING` - Contract(Token) Symbol. The length should be from 3 to 10.
    3. `Owner`: `DATA`, 10 Bytes - address of the contract owner.

#### Example

```javascript
// Create SCT30 contract whose name is "SymContract" and
// symbol is "SCN" and
// contract owner is "0x00020000000000060002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x1e", "method": "0x0", "params": {"name": "SymContract", "symbol": "SCN", "owner": "0x00020000000000060002"}})
// Result: RLP encoded SCT data
"0xde1e80db8b53796d436f6e74726163748353434e8a00020000000000060002"

// Send SCT Transaction
sym.sendTransaction({"from": "0x00020000000000060002", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x12", "type": "0x1", "input": "0xde1e80db8b53796d436f6e74726163748353434e8a00020000000000060002"})
// Result: Transaction Hash
"0xecec96d8849dc2e289fdf4cd701118995aaa236661ec8e3e2e678d1cf98a820e"

// Get Transaction Recipt from transaction hash
sym.getTransactionReceipt("0xecec96d8849dc2e289fdf4cd701118995aaa236661ec8e3e2e678d1cf98a820e").contractAddress
// Result: Transaction information with contractAddress.
"0x701b171444a16e8b86ca"

// Get Contract information from contract address.
sct.getContract("0x701b171444a16e8b86ca")
// Result: Contract information created with SCT20 create method.
{ 
  creator: "0x00020000000000060002",
  name: "SymContract",
  owner: "0x00020000000000060002",
  state: "active",
  symbol: "SCN",
  total: "0x0‬",
  type: "sct30"
}
```

### SCT30_CREATE_ITEM
Generates N items of SCT30. It is a bulk-capable function that can generate up to tx-size 32kb calls in a single call.  `(Authorization: owner, creator)`

-  Type : 30
-  Method : 1
-  Parameters :
    1. `Items`:` ARRAY`, SCT30 items
        - `Type`:` DATA`, type of item
        - `Name`:` STRING`, name of item
        - `Value`:` QUANTITY`, the unique value of item
        - `Category`:`DATA`, category of item (search index value)
        - `Property`:` DATA`, unique attribute of item
        - `Description`: Additional explanation of `DATA`, item

#### Example

```javascript
// Create two SCT30 items. Each item has following informations:
// [first item]: type is "A" and name is "dog" and value is "500(0x1f4)" 
// and category is "animal" and property is "myFistPet"
// [second item]: type is "B" and name is "cat" and value is "500(0x1f4)" 
// and category is "animal" and property is "myFirstCat"
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x1e", "method": "0x1", "params": {"items": [{ "type": "A", "name": "dog", "value": "0x1f4", "category": "animal", "property": "myFistPet"}, { "type": "B", "name": "cat", "value": "0x1f4", "category": "animal", "property": "myFirstCat"}]}})
// Result
"0xf31e01f0d73183646f678201f486616e696d616c866d797065743180d732836361748201f486616e696d616c866d797065743280"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x13", "type": "0x1", "input": "0xf31e01f0d73183646f678201f486616e696d616c866d797065743180d732836361748201f486616e696d616c866d797065743280"})
// Result
"0x44588543af819fbf78ef97a05ba219568e7b81ecafe7488e9bda339c69d1fd0e"
```

### SCT30_TRANSFER
SCT30 Item transfer `(Authorization: all)`

-  Type : 30
-  Method : 2
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address to receive sct30 token.
    2. `Index`: `QUANTITY` - index transferred in sct30 item.

#### Example

```javascript
// Transfer SCT30 item via index (0x1) to "0x00020000000000040002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x1e", "method": "0x2", "params": {"to": "0x00020000000000040002", "index": "0x1"}})
// Result
"0xcf1e02cc8a0002000000000004000201"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x14", "type": "0x1", "input": "0xcf1e02cc8a0002000000000004000201"})
// Result
"0x765c060cdc172707fb3143e01bfbab0713fc1562b8643bfedb47047e063ba12c"
```

### SCT30_TRANSFER_FROM
Send a delegated item via APPROVE `(Authorization: all)`

-  Type : 30
-  Method : 3
-  Parameters :
    1. `From`: `DATA`, 10 Bytes - address of the from.
    2. `To`: `DATA`, 10 Bytes - address to receive sct30 token.
    3. `Index`: `QUANTITY` - index transferred in sct30 item.

#### Example

```javascript
// Transfer SCT30 item via index (0x1) to "0x00020000000000040002".
// Before execute SCT30 Transfer From method, 
// it is needed to be approved an index to transfer.
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x1e", "method": "0x3", "params": {"from": "0x00020000000000060002", "to": "0x00020000000000040002", "index": "0x1"}})
// Result
"0xda1e03d78a000200000000000600028a0002000000000004000201"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x15", "type": "0x1", "input": "0xda1e03d78a000200000000000400028a0002000000000003000201"})
// Result
"0x35cdd7009e4ef8a5357180bfbb5776c7a499654a7423cdc578f75bcc90f74874"
```

### SCT30_APPROVE
Delegate some item to other users `(Authorization: all)`

-  Type : 30
-  Method : 4
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address of the spender.
    2. `Index`: `QUANTITY` - index transferred in sct30 item.

#### Example

```javascript
// Approve item whose index is "0x1" to "0x00020000000000040002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x1e", "method": "0x4", "params": {"to": "0x00020000000000040002", "index": "0x1"}})
// Result
"0xcf1e04cc8a0002000000000004000201"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x16", "type": "0x1", "input": "0xcf1e04cc8a0002000000000004000201"})
// Result
"0x20ac6e573b515b3e0c6ed2ac79434a681ab08b58e7843066ec116f01551777d1"
```

### SCT30_ITEM_PAUSE
SCT30 item pause `(Authorization: owner, creator)`

- Type : 30
- Method : 5
- Parameters :
    1. `Index`: `QUANTITY` - index pause in sct30 item.

#### Example

```javascript
// Pause item whose index is "0x1".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x1e", "method": "0x5", "params": {"index": "0x1"}})
// Result
"0xc41e05c101"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x17", "type": "0x1", "input": "0xc41e05c101"})
// Result
"0x134d4751bc161e88af3f90e698a37ffc00cb2cdd11cdb497d5342d34058dee16"
```

### SCT30_ITEM_UNPAUSE
SCT30 item unpause `(Authorization: owner, creator)`

- Type : 30
- Method : 6
- Parameters :
    1. `Index`: `QUANTITY` - index unpause in sct30 item.

#### Example

```javascript
// Unpause item whose index is "0x1".
// Get SCT rlp encoded string.
> debug.getSCTRlp({"type": "0x1e", "method": "0x6", "params": {"index": "0x1"}})
// Result
"0xc41e06c101"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x18", "type": "0x1", "input": "0xc41e06c101"})
// Result
"0xf374e407c13e01e1bd542c0eeac3f44d02e0eb55946a155e003e1fb0bcccee33"
```

### SCT30_TRANSFER_OWNER
change owner permissions `(Authorization: owner, creator)`

- Type : 30
- Method : 7
- Parameters : 
    1. `To`: `DATA`, 10 Bytes - address to be the contract owner

#### Example

```javascript
// Transfer contract ownership to "0x00020000000000040002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x1e", "method": "0x7", "params": {"to": "0x00020000000000040002"}})
// Result
"0xce1e07cb8a00020000000000040002"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000060002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x19", "type": "0x1", "input": "0xce1e07cb8a00020000000000040002"})
// Result
"0xcdc432cb7975bb8930ac5740948007b686973b5fdc9ae2c7d4e30e898480cb40"
```

## SCT40
SCT40 is an agreement used for coupons with an improved contract through override of SCT30.

### SCT40_CREATE
Create an SCT40 contract.

-  Type : 40
-  Method : 0
-  Parameters :
    1. `Name`: `STRING` - Contract(Token) Name
    2. `Symbol`: `STRING` - Contract(Token) Symbol. The length should be from 3 to 10.
    3. `CPoint` : `QUANTITY` - Coupon Point
    4. `Owner`: `DATA`, 10 Bytes - address of the contract owner.

#### Example

```javascript
// Create SCT40 contract whose name is "SymContract" and
// symbol is "SCN" and
// contract owner is "0x00020000000000050002".
// Get SCT rlp encoded string.
> debug.getSCTRlp({"type": "0x1e", "method": "0x0", "params": {"name": "SymContract", "symbol": "SCN", "cpoint": "0xa", "owner": "0x00020000000000050002"}})
// Result: RLP encoded SCT data
"0xde1e80db8b53796d436f6e74726163748353434e8a00020000000000050002"

// Send SCT Transaction
sym.sendTransaction({"from": "0x00020000000000050002", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x0", "type": "0x1", "input": "0xde1e80db8b53796d436f6e74726163748353434e8a00020000000000050002"})
// Result: Transaction Hash
"0x6e5ec47d74d64a7f11b9921e610577fe3d958d96e7f50420415947c506355f69"

// Get Transaction Recipt from transaction hash
sym.getTransactionReceipt("0x6e5ec47d74d64a7f11b9921e610577fe3d958d96e7f50420415947c506355f69").contractAddress
// Result: Transaction information with contractAddress.
"0x152461a4284a938104ec"

// Get Contract information from contract address.
sct.getContract("0x152461a4284a938104ec")
// Result: Contract information created with SCT20 create method.
{ 
  cpoint: 10,
  creator: "0x00020000000000050002",
  name: "SymContract",
  owner: "0x00020000000000050002",
  state: "active",
  symbol: "SCN",
  total: "0x0‬",
  type: "sct40"
}
```

### SCT40_CREATE_COUPON 
Generates N coupon items of SCT40. It is a bulk-capable function that can generate up to tx-size 32kb calls in a single call. `(Authorization: owner, creator)`

-  Type : 40
-  Method : 1
-  Parameters :
    1. `Coupons`: `ARRAY`, SCT40 Coupon items
       - `Type` : `DATA` type of coupon item
       - `Name`: `STRING` name of coupon item
       - `Point` :`QUANTITY` point to be deducted when creating a Coupon item 
       - `GroupNo` : `DATA` and group of couple items (search index value)
       - `CouponNo` : `DATA`, unique attribute of item
       - `Etc` : `DATA` and 'coupon item' additional explanation
#### Example

```javascript
// Create two SCT40 coupons. Each coupon has following informations:
// [first coupon]: type is "A" and name is "hotdog" and point is "1(0x1)" 
// and groupNo is "fastfood1" and couponNo is "discount1" and etc is "happybirthday"
// [second coupon]: type is "B" and name is "spaghetti" and value is "500(0x1f4)" 
// and groupNo is "restaurant1" and couponNo is "discount1" and etc is "happybirthday"
// Get SCT rlp encoded string
// [itmes]: Items
> debug.getSCTRlp({"type": "0x28", "method": "0x1", "params": {"coupons": [{"type": "A", "name": "hotdog", "point": "0x1", "groupNo": "fastfood1", "couponNo": "discount1", "description": "happybirthday"}, {"type": "B", "name": "coupon2", "point": "0x1f4", "groupNo": "restaurant1", "couponNo": "discount1", "etc": "happybirthday"}]}})
// Result
"0xf8542801f850de4186686f74646f67018966617374666f6f643189646973636f756e743180f04287636f75706f6e328201f48b72657374617572616e743189646973636f756e74318d68617070796269727468646179"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000050002", "to": "0x152461a4284a938104ec", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x1", "type": "0x1", "input": "0xf8542801f850de4186686f74646f67018966617374666f6f643189646973636f756e743180f04287636f75706f6e328201f48b72657374617572616e743189646973636f756e74318d68617070796269727468646179"})
// Result
"0xd5a9cbda4ea82c39dbe2c40c620ada6178130e9bc4ba2929645d7f63f8b23001"
```

### SCT40_TRANSFER
SCT40 coupon Item transfer. `(Authorization: all)`

-  Type : 40
-  Method : 2
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address to receive sct40 item.
    2. `Index`: `QUANTITY` - index transferred in sct40 item.

#### Example

```javascript
// Transfer SCT40 coupon via index (0x1) to "0x00020000000000040002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x28", "method": "0x2", "params": {"to": "0x00020000000000040002", "index": "0x1"}})
// Result
"0xcf2802cc8a0002000000000004000201"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000050002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x2", "type": "0x1", "input": "0xcf2802cc8a0002000000000004000201"})
// Result
"0xe2fe1d2898431fa45d90db6b6c80dab8f192e3dd302e99466921312bc94cb191"
```

### SCT40_TRANSFER_FROM
Send a delegated coupon item via APPROVE `(Authorization: all)`

-  Type : 40
-  Method : 3
-  Parameters :
    1. `From`: `DATA`, 10 Bytes - address of the from.
    2. `To`: `DATA`, 10 Bytes - address to receive sct40 token.
    3. `Index`: `QUANTITY` - index transferred in sct40 item.

#### Example

```javascript
// Transfer SCT40 coupon via index (0x1) to "0x00020000000000040002".
// Before execute SCT40 Transfer From method, 
// it is needed to be approved an index to transfer.
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x28", "method": "0x3", "params": {"from": "0x00020000000000050002", "to": "0x00020000000000040002", "index": "0x1"}})
// Result
"0xda2803d78a000200000000000500028a0002000000000004000201"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000050002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x3", "type": "0x1", "input": "0xda2803d78a000200000000000400028a0002000000000005000201"})
// Result
"0x957e00b8c7807c7482a62cedd9b24fd950c23a4002d97155499b099461f0c0ce"
```

### SCT40_APPROVE
Delegate some item to other symid `(Authorization: all)`

-  Type : 40
-  Method : 4
-  Parameters :
    1. `To`: `DATA`, 10 Bytes - address of the spender.
    2. `Index`: `QUANTITY` - index transferred in sct40 item.

#### Example

```javascript
// Approve coupon whose index is "0x1" to "0x00020000000000040002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x28", "method": "0x4", "params": {"to": "0x00020000000000040002", "index": "0x1"}})
// Result
"0xcf2804cc8a0002000000000004000201"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000050002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x4", "type": "0x1", "input": "0xcf2804cc8a0002000000000004000201"})
// Result
"0x64ea37a2061dd97ae26141c618aee47d6d3b86d574ce06ead8b05c4ffa939c24"
```

### SCT40_COUPON_USE
SCT40 coupon item use `(Authorization: owner, creator)`

- Type : 40
- Method : 5
- Parameters :
    1. `Index`: `QUANTITY` - index pause in sct40 item.

#### Example

```javascript
// Use coupon whose index is "0x1".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x28", "method": "0x5", "params": {"index": "0x1"}})
// Result
"0xc42805c101"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000050002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x5", "type": "0x1", "input": "0xc42805c101"})
// Result
"0xedfe1064f63209e87b5a4f96aff1c0b39ab5b28eaa058295a04bd23c6ecd2d5e"
```

### SCT40_COUPON_PAUSE
SCT40 coupon item pause `(Authorization: owner, creator)`

- Type : 40
- Method : 6
- Parameters :
    1. `Index`: `QUANTITY` - index unpause in sct40 item.

#### Example

```javascript
// Pause coupon whose index is "0x1".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x28", "method": "0x6", "params": {"index": "0x1"}})
// Result
"0xc42806c101"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000050002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x6", "type": "0x1", "input": "0xc42806c101"})
// Result
"0xa67abde6d8892a8769d1177197851fa436650cff9b21b914ec42892723e8dc40"
```

### SCT40_COUPON_UNPAUSE
SCT40 coupon item unpause `(Authorization: owner, creator)`

- Type : 40
- Method : 7
- Parameters :
    1. `Index`: `QUANTITY` - index unpause in sct40 item.

#### Example

```javascript
// Unpause coupon whose index is "0x1".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x28", "method": "0x7", "params": {"index": "0x1"}})
// Result
"0xc42807c101"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000050002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x7", "type": "0x1", "input": "0xc42807c101"})
// Result
"0x58734cb968e4becaec3d64ee78b2c659dafd40baeefda0fc1dbedc8f7ae556ac"
```

### SCT40_TRANSFER_OWNER
change owner permissions `(Authorization: owner, creator)`

- Type : 40
- Method : 8
- Parameters : 
    1. `To`: `DATA`, 10 Bytes - address to be the contract owner

#### Example

```javascript
// Transfer contract ownership to "0x00020000000000060002".
// Get SCT rlp encoded string
> debug.getSCTRlp({"type": "0x28", "method": "0x8", "params": {"to": "0x00020000000000060002"}})
// Result
"0xce2808cb8a00020000000000060002"

// Send SCT Transaction
sym.sendTransaction({ "from": "0x00020000000000050002", "to": "0x701b171444a16e8b86ca", "gas": "0x76cff0", "gasPrice": "0x5d21dba00", "nonce": "0x8", "type": "0x1", "input": "0xce2808cb8a00020000000000060002"})
// Result
"0x9c82fbb3f9072241e20782122b6b9113020e4d3c8cea37127371085f4126fca4"
```


## SCT50 - VoteContract

**SCT50 is a template that handles Vote contract. It has following functions:**



### SCT50_CREATE

`sct50Create` creates SCT50 Vote Contract. It is needed to use SCT20 token to make. Referring SCT20 token will be standard currency when users stake tokens to poll contract to vote.

- Type : 50
- Method : 0
- Parameters:
      1. `Party Name`: `STRING` - Digital Party name
      2. `SCT20 Token Address`: `DATA` - SCT20 Token Address to use.
      3. `Owner`: `DATA` - Owner SymId 

- output:
  - Transaction Hash

- validation:
  - `Receipt`.`contractAddress` - sct50 Vote Contract Address:`Address` 
  - `sct_getVoteContract` - checkout sct50 Vote Contract Information

#### Example

```javascript
[rlp encode input data]
inputdata = rlp.encode(["0x32", "0x0", ["SymParty",   // PartyName
                    "0x7ceb501abaeaef5ff714",       // SCT20TokenAddress
                    "0x00020000000000020002"]])     // OwnerSymId
```



### SCT50_ADD_POLL_CREATORS

`sct50AddPollCreators` registers given users as a poll creator. Poll creators will be given authority to create SCT51 Poll Contract.

- Type : 50
- Method : 1
- Parameters:
      1. `Poll Creators`: `ARRAY` -  SymId lists to add as poll creators
        - `SymID`: `DATA` - SymID to add as poll creator
- output:
  - Transaction Hash
- validation:
  - `sct_getVoteContract` - checkout `pollCreators` index

#### Example

```javascript
[rlp encode input data]
inputdata = rlp.encode(["0x32", "0x1", ["0x00020000000000030002", 	// PollCreator
                    "0x00020000000000040002"]])                 	// PollCreator
```



#### SCT50_REMOVE_POLL_CREATORS

`sct50RemovePollCreators` unregisters given users from poll creator. They lost authority to create SCT51 Poll Contract.

- Type : 50
- Method : 2
- Parameters:
      1. `Poll Creators`: `ARRAY` -  SymId lists to remove from poll creators
        - `SymID`: `DATA` - SymID to remove from poll creator
- output:
  - Transaction Hash
- validation:
  - `sct_getVoteContract` - checkout `pollCreators` index

#### Example

```javascript
[rlp encode input data]
inputdata = rlp.encode(["0x32", "0x2", ["0x00020000000000030002", 	// PollCreator
                    "0x00020000000000040002"]])                 	// PollCreator
```



## SCT51 - PollContract

### SCT51_CREATE_POLL

`sct51CreatePoll` creates SCT51 Poll Contract. Only Poll Creators registered from SCT50 Vote Contract Creator(or Owner) can create. It is needed to use SCT50 Vote Contract to make. Created Poll Contract will handle about voting activity.

- Type : 51
- Method : 0
- Parameters:
      1. `PollNumber`: `QUANTITY` -  Poll number
      2. `Category`: `QUANTITY` -  Agenda category (it defines `voting rate`, `participation rate`)
      3. `Agenda`: `STRING` -  Agenda name
      4. `Description`: `STRING` -  Description about Agenda
      5. `Owner`: `DATA` -  Owner of Poll to create
      6. `Start`: `QUANTITY` -  Poll start time (`UNIX time stamp`)
      7. `End`: `QUANTITY` -  Poll start time (`UNIX time stamp`)
      8. `VoteContract`: `DATA` -  SCT50 Vote Contract Address
      9. `SecretOption`: `QUANTITY` -  Secret option for the poll [`0`: `public` `1`: `secret`]
      10. `StakeOption`: `QUANTITY` -  Stake strict option for each voter to vote poll [`0`: `free stake` `*`: `stake limit for each voter with given amount`]
      11. `TotalVoters`: `QUANTITY` -  Allowed total voters for the poll.
      12. `Quorum`: `QUANTITY` -  Minimum rate of participants for the poll to pass.
      13. `PassRate`: `QUANTITY` -  Minimum rate of agree for the poll to pass.

- output:
  - Transaction Hash
- validation:
  - `Receipt`.`contractAddress` - sct51 Poll Contract Address:`Address`
  - `sct_getVoteContract` - checkout `polls` index
  - `sct_getPollContract` - checkout sct51 Poll Contract Information

#### Example

```javascript
[rlp encode input data]
inputdata = rlp.encode(["0x33", "0x0", ["0x0",	// Poll number
    				"0x0",						// Category
                    "Agenda1",					// Agenda
                    "Agenda1 is about ...",		// Description
                    "0x00020000000000020002",	// OwnerSymId
                    "0x5e4b36c9",				// Start Unix time
                    "0x5e50b509",				// End Unix time
                    "0x83c4b863539810ef20f4",	// SCT50 Vote Contract Address
                    "0x0",						// Secret option
                    "0x1",						// Stake option
                    "0x64",						// TotalVoters
                    "0x64",						// Quorum
                    "0x64"]])					// PassRate
```



### SCT51_VOTE_IN_POLL

`sct51VoteInPoll` make user vote in poll. It should be preceded staking tokens in poll.

- Type : 51
- Method : 1
- Parameters:
      1. `Stake`: `QUANTITY` -  SCT20 balance to stake for voting in poll contract (weighted vote)
      2. `Decision`: `QUANTITY` -  decision about Poll ( `0` - Disagree `1` - Agree)
- output:
  - Transaction Hash
- validation:
  - `sct_getVotingPaper` - checkout voting paper information

#### Example

```javascript
[rlp encode input data]
inputdata = rlp.encode(["0x33", "0x1", ["0x1",		// Stake
					"0x1"]])						// Decision
```



### SCT51_UNSTAKE_TOKENS

`sct51UnstakeTokens` unstakes given amount from the balance that sender staked in Poll Contract. Unstaked balance will return to sender's SCT20 balance.

- Type : 51
- Method : 2
- Parameters:
      1. `Amount`: `QUANTITY` -  SCT20 balance to unstake in poll contract
- output:
  - Transaction Hash
- validation:
  - `sct_getVotingPaper` - checkout voting paper information
  - `sct_getContractAccount` - checkout balance

#### Example

```javascript
[rlp encode input data]
inputdata = rlp.encode(["0x33", "0x2", ["0x1"]])	// Unstake Amount
```



### SCT51_EMERGENCY_STOP_POLL

`sct51EmergencyStopPoll` make poll inactive. Only SCT50 Vote Creator(or owner) can make it. Hence poll state turns to inactive, user can not stake and vote in poll.

- Type : 51
- Method : 3
- Parameters: Null
- output:
  - Transaction Hash
- validation:
  - `sct_getPollContract` - checkout `pollState` index

#### Example

```javascript
[rlp encode input data]
inputdata = rlp.encode(["0x33", "0x3", [])
```



### SCT51_FINISH_POLL

`sct51RevokePoll` make poll expired. Only SCT50 Vote Creator can make it. Hence poll state turns to expired, the staked balance from users will be returned to them.

- Type : 51
- Method : 4
- Parameters: Null
- output:
  - Transaction Hash
- validation:
  - `sct_getPollContract` - checkout `pollState` index
  - `sct_getContractAccount` - checkout balance

#### Example

```javascript
[rlp encode input data]
inputdata = rlp.encode(["0x33", "0x4", [])
```



### SCT51_WRITE_POLL_RESULTS

`sct51WritePollResults` write result to the poll contract. Only SCT50 Vote Creator can write it. 

- Type : 51
- Method : 5
- Parameters:
      1. `Result`: `QUANTITY` -  poll result code
      2. `Code`: `QUANTITY` -  extra poll result code
      3. `Agrees`: `QUANTITY` -  the number of stake voted as agree for the poll
      4. `Disagrees`: `QUANTITY` -  the number of stake voted as disagree for the poll
      5. `Nullities`: `QUANTITY` -  the number of stake holders not voted for the poll
      6. `TotalVoters`: `QUANTITY` -  the number of stake holders for the poll
      7. `VotingRate`: `QUANTITY` -  the rate of holders voted for the poll (unsigned integer value)
- output:
  - Transaction Hash
- validation:
  - `sct_getPollContract` - checkout `pollState` index
  - `sct_getContractAccount` - checkout balance

#### Example

```javascript
[rlp encode input data]
inputdata = rlp.encode(["0x33", "0x5", ["0x1",	// result
                                       	"0x0",	// result code
                                       	"0x62",	// agrees
                                       	"0x2",	// disagrees
                                       	"0x2",	// nullities
                                       	"0x64",	// total voters
                                       	"0x62"])// voting rate(unsigned integer)

```

# SymVerse V3 JSON-RPC API

> **Status:** Baseline import  
> **Date:** 2026-05-16  
> **Document Role:** General JSON-RPC API documentation carried into the SymVerse V3 documentation set

---

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

**Contents**

- [JSON RPC API](#json-rpc-api)
  - [JavaScript API](#javascript-api)
  - [JSON-RPC Endpoint](#json-rpc-endpoint)
  - [Output HEX values](#output-hex-values)
  - [The default block parameter](#the-default-block-parameter)
  - [Oracle parameters](#oracle-parameters)
  - [Curl Examples Explained](#curl-examples-explained)
  - [JSON RPC API Reference](#json-rpc-api-reference)
    - [web3_clientVersion](#web3_clientversion)
      - [Parameters](#parameters)
      - [Returns](#returns)
      - [Example](#example)
    - [web3_sha3](#web3_sha3)
      - [Parameters](#parameters-1)
      - [Returns](#returns-1)
      - [Example](#example-1)
    - [net_version](#net_version)
      - [Parameters](#parameters-2)
      - [Returns](#returns-2)
      - [Example](#example-2)
    - [net_listening](#net_listening)
      - [Parameters](#parameters-3)
      - [Returns](#returns-3)
      - [Example](#example-3)
    - [net_peerCount](#net_peercount)
      - [Parameters](#parameters-4)
      - [Returns](#returns-4)
      - [Example](#example-4)
    - [sym_protocolVersion](#sym_protocolversion)
      - [Parameters](#parameters-5)
      - [Returns](#returns-5)
      - [Example](#example-5)
    - [sym_syncing](#sym_syncing)
      - [Parameters](#parameters-6)
      - [Returns](#returns-6)
      - [Example](#example-6)
    - [sym_symbase](#sym_symbase)
      - [Parameters](#parameters-7)
      - [Returns](#returns-7)
      - [Example](#example-7)
    - [sym_gasPrice](#sym_gasprice)
      - [Parameters](#parameters-8)
      - [Returns](#returns-8)
      - [Example](#example-8)
    - [sym_accounts](#sym_accounts)
      - [Parameters](#parameters-9)
      - [Returns](#returns-9)
      - [Example](#example-9)
    - [sym_blockNumber](#sym_blocknumber)
      - [Parameters](#parameters-10)
      - [Returns](#returns-10)
      - [Example](#example-10)
    - [sym_getBalance](#sym_getbalance)
      - [Parameters](#parameters-11)
      - [Returns](#returns-11)
      - [Example](#example-11)
    - [sym_getTransactionCount](#sym_gettransactioncount)
      - [Parameters](#parameters-12)
      - [Returns](#returns-12)
      - [Example](#example-12)
    - [sym_getBlockTransactionCountByHash](#sym_getblocktransactioncountbyhash)
      - [Parameters](#parameters-13)
      - [Returns](#returns-13)
      - [Example](#example-13)
    - [sym_getBlockTransactionCountByNumber](#sym_getblocktransactioncountbynumber)
      - [Parameters](#parameters-14)
      - [Returns](#returns-14)
      - [Example](#example-14)
    - [sym_getCode](#sym_getcode)
      - [Parameters](#parameters-15)
      - [Returns](#returns-15)
      - [Example](#example-15)
    - [sym_sign](#sym_sign)
      - [Parameters](#parameters-16)
      - [Returns](#returns-16)
      - [Example](#example-16)
    - [sym_sendTransaction](#sym_sendtransaction)
      - [Parameters](#parameters-17)
      - [Returns](#returns-17)
      - [Example](#example-17)
    - [sym_sendRawTransaction](#sym_sendrawtransaction)
      - [Parameters](#parameters-18)
      - [Returns](#returns-18)
      - [Example](#example-18)
    - [sym_call](#sym_call)
      - [Parameters](#parameters-19)
      - [Returns](#returns-19)
      - [Example](#example-19)
    - [sym_estimateGas](#sym_estimategas)
      - [Parameters](#parameters-20)
      - [Returns](#returns-20)
      - [Example](#example-20)
    - [sym_getBlockByHash](#sym_getblockbyhash)
      - [Parameters](#parameters-21)
      - [Returns](#returns-21)
      - [Example](#example-21)
    - [sym_getBlockByNumber](#sym_getblockbynumber)
      - [Parameters](#parameters-22)
      - [Returns](#returns-22)
      - [Example](#example-22)
    - [sym_getTransactionByHash](#sym_gettransactionbyhash)
      - [Parameters](#parameters-23)
      - [Returns](#returns-23)
      - [Example](#example-23)
    - [sym_getTransactionByBlockHashAndIndex](#sym_gettransactionbyblockhashandindex)
      - [Parameters](#parameters-24)
      - [Returns](#returns-24)
      - [Example](#example-24)
    - [sym_getTransactionByBlockNumberAndIndex](#sym_gettransactionbyblocknumberandindex)
      - [Parameters](#parameters-25)
      - [Returns](#returns-25)
      - [Example](#example-25)
    - [sym_getTransactionReceipt](#sym_gettransactionreceipt)
      - [Parameters](#parameters-26)
      - [Returns](#returns-26)
      - [Example](#example-26)
    - [sym_getReward](#sym_getReward)
      - [Parameters](#parameters-27)
      - [Returns](#returns-27)
      - [Example](#example-27)
    - [sym_getDeposit](#sym_getdeposit)
      - [Parameters](#parameters-28)
      - [Returns](#returns-28)
      - [Example](#example-28)
    - [sym_setDeposit](#sym_setdeposit)
      - [Parameters](#parameters-29)
      - [Returns](#returns-29)
      - [Example](#example-29)
    - [sym_restoreDeposit](#sym_restoredeposit)
      - [Parameters](#parameters-30)
      - [Returns](#returns-30)
      - [Example](#example-30)
    - [warrant_blockNumber](#warrant_blocknumber)
      - [Parameters](#parameters-31)
      - [Returns](#returns-31)
      - [Example](#example-31)
    - [warrant_getWarrantsByBlockHash](#warrant_getwarrantsbyblockhash)
      - [Parameters](#parameters-32)
      - [Returns](#returns-32)
      - [Example](#example-32)
    - [warrant_getWarrantsByBlockNumber](#warrant_getwarrantsbyblocknumber)
      - [Parameters](#parameters-33)
      - [Returns](#returns-33)
      - [Example](#example-33)
    - [warrant_getBlockByHash](#warrant_getblockbyhash)
      - [Parameters](#parameters-34)
      - [Returns](#returns-34)
      - [Example](#example-34)
    - [warrant_getBlockByNumber](#warrant_getblockbynumber)
      - [Parameters](#parameters-35)
      - [Returns](#returns-35)
      - [Example](#example-35)
    - [warrant_GetRewardAddr](#warrant_GetRewardAddr)
      - [Parameters](#parameters-36)
      - [Returns](#returns-36)
      - [Example](#example-36)
    - [citizen_sendCitizen](#citizen_sendcitizen)
      - [Parameters](#parameters-37)
      - [Returns](#returns-37)
      - [Example](#example-37)
    - [citizen_sendRawCitizen](#citizen_sendrawcitizen)
      - [Parameters](#parameters-38)
      - [Returns](#returns-38)
      - [Example](#example-38)
    - [citizen_getCitizenByHash](#citizen_getcitizenbyhash)
      - [Parameters](#parameters-39)
      - [Returns](#returns-39)
      - [Example](#example-39)
    - [citizen_getRawCitizenByHash](#citizen_getrawcitizenbyhash)
      - [Parameters](#parameters-40)
      - [Returns](#returns-40)
      - [Example](#example-40)
    - [citizen_getCitizenBySymID](#citizen_getcitizenbysymid)
      - [Parameters](#parameters-41)
      - [Returns](#returns-41)
      - [Example](#example-41)
    - [citizen_getRawCitizenBySymID](#citizen_getrawcitizenbysymid)
      - [Parameters](#parameters-42)
      - [Returns](#returns-42)
      - [Example](#example-42)
    - [citizen_getCitizensByBlockNumber](#citizen_getcitizensbyblocknumber)
      - [Parameters](#parameters-43)
      - [Returns](#returns-43)
      - [Example](#example-43)
    - [citizen_getCitizenCount](#citizen_getcitizencount)
      - [Parameters](#parameters-44)
      - [Returns](#returns-44)
      - [Example](#example-44)
    - [citizen_pendingCitizens](#citizen_pendingcitizens)
      - [Parameters](#parameters-45)
      - [Returns](#returns-45)
      - [Example](#example-45)
    - [citizen_blockNumber](#citizen_blocknumber)
      - [Parameters](#parameters-46)
      - [Returns](#returns-46)
      - [Example](#example-46)
    - [citizen_getBlockByHash](#citizen_getblockbyhash)
      - [Parameters](#parameters-47)
      - [Returns](#returns-47)
      - [Example](#example-47)
    - [citizen_getBlockByNumber](#citizen_getblockbynumber)
      - [Parameters](#parameters-48)
      - [Returns](#returns-48)
      - [Example](#example-48)
    - [citizen_getBlockCitizenCountByHash](#citizen_getblockcitizencountbyhash)
      - [Parameters](#parameters-49)
      - [Returns](#returns-49)
      - [Example](#example-49)
    - [citizen_getBlockCitizenCountByNumber](#citizen_getblockcitizencountbynumber)
      - [Parameters](#parameters-50)
      - [Returns](#returns-50)
      - [Example](#example-50)
    - [oracle_sendOracle](#oracle_sendoracle)
      - [Parameters](#parameters-51)
      - [Returns](#returns-51)
      - [Example](#example-51)
    - [oracle_sendRawOracle](#oracle_sendraworacle)
      - [Parameters](#parameters-52)
      - [Returns](#returns-52)
      - [Example](#example-52)
    - [oracle_sendOracleJSON](#oracle_sendoraclejson)
      - [Parameters](#parameters-53)
      - [Returns](#returns-53)
      - [Example](#example-53)
    - [oracle_getOracleByHash](#oracle_getoraclebyhash)
      - [Parameters](#parameters-54)
      - [Returns](#returns-54)
      - [Example](#example-54)
    - [oracle_getRawOracleByHash](#oracle_getraworaclebyhash)
      - [Parameters](#parameters-55)
      - [Returns](#returns-55)
      - [Example](#example-55)
    - [oracle_getOracleCount](#oracle_getoraclecount)
      - [Parameters](#parameters-56)
      - [Returns](#returns-56)
      - [Example](#example-56)
    - [oracle_pendingOracles](#oracle_pendingoracles)
      - [Parameters](#parameters-57)
      - [Returns](#returns-57)
      - [Example](#example-57)
    - [oracle_blockNumber](#oracle_blocknumber)
      - [Parameters](#parameters-58)
      - [Returns](#returns-58)
      - [Example](#example-58)
    - [oracle_getBlockByHash](#oracle_getblockbyhash)
      - [Parameters](#parameters-59)
      - [Returns](#returns-59)
      - [Example](#example-59)
    - [oracle_getBlockByNumber](#oracle_getblockbynumber)
      - [Parameters](#parameters-60)
      - [Returns](#returns-60)
      - [Example](#example-60)
    - [sct_getContract](#sct_getcontract)
      - [Parameters](#parameters-61)
      - [Returns](#returns-61)
      - [Example](#example-61)
    - [sct_getContractItem](#sct_getcontractitem)
      - [Parameters](#parameters-62)
      - [Returns](#returns-62)
      - [Example](#example-62)
    - [sct_getContractAccount](#sct_getcontractaccount)
      - [Parameters](#parameters-63)
      - [Returns](#returns-63)
      - [Example](#example-63)
    - [sct_getAllowance](#sct_getAllowance)
      - [Parameters](#parameters-64)
      - [Returns](#returns-64)
      - [Example](#example-64)
    - [sct_getContractItemsByCategory](#sct_getcontractitemsbycategory)
      - [Parameters](#parameters-65)
      - [Returns](#returns-65)
      - [Example](#example-65)
    - [sct_getVoteContract](#sct_getVoteContract)
      - [Parameters](#parameters-66)
      - [Returns](#returns-66)
      - [Example](#example-66)
    - [sct_getPollContract](#sct_getpollcontract)
      - [Parameters](#parameters-67)
      - [Returns](#returns-67)
      - [Example](#example-67)
    - [sct_getVoteResult](#sct_getvoteresult)
      - [Parameters](#parameters-68)
      - [Returns](#returns-68)
      - [Example](#example-68)
    - [sct_getVotingPaper](#sct_getvotingpaper)
      - [Parameters](#parameters-69)
      - [Returns](#returns-69)
      - [Example](#example-69)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# JSON RPC API

[JSON](http://json.org/) is a lightweight data-interchange format. It can represent numbers, strings, ordered sequences of values, and collections of name/value pairs.

[JSON-RPC](http://www.jsonrpc.org/specification) is a stateless, light-weight remote procedure call (RPC) protocol. Primarily this specification defines several data structures and the rules around their processing. It is transport agnostic in that the concepts can be used within the same process, over sockets, over HTTP, or in many various message passing environments. It uses JSON ([RFC 4627](http://www.ietf.org/rfc/rfc4627.txt)) as data format.

**go-symverse** supports `JSON-RPC 2.0`

## JavaScript API

To talk to an symverse node from inside a JavaScript application offering API library with the [web3.js](https://github.com/ethereum/web3.js) library (from Ethereum)

the library gives an convenient interface for the RPC methods. See the [JavaScript API](https://github.com/symverse-lab/Document/blob/master/Doc-APIs/JSON_RPC_API_github.md) for more.

## JSON-RPC Endpoint

Default JSON-RPC endpoints:

```
  http://localhost:8545
```

You can start the HTTP JSON-RPC with the `--rpc` flag

```bash
  > gsym --rpc
```

change the default port (8545) and listing address (localhost) with:

```bash
  > gsym --rpc --rpcaddr <ip> --rpcport <portnumber>
```

If accessing the RPC from a browser, CORS will need to be enabled with the appropriate domain set. Otherwise, JavaScript calls are limit by the same-origin policy and requests will fail:

```bash
  > gsym --rpc --rpccorsdomain "http://localhost:3000"
```

The JSON RPC can also be started from the gsym console using the `admin.startRPC(addr, port)` command.

## Output HEX values

At present there are two key datatypes that are passed over JSON: unformatted byte arrays and quantities. Both are passed with a hex encoding, however with different requirements to formatting:

When encoding **QUANTITIES** (integers, numbers): encode as hex, prefix with "0x", the most compact representation (slight exception: zero should be represented as "0x0"). Examples:

- 0x41 (65 in decimal)
- 0x400 (1024 in decimal)
- WRONG: 0x (should always have at least one digit - zero is "0x0")
- WRONG: 0x0400 (no leading zeroes allowed)
- WRONG: ff (must be prefixed 0x)

When encoding **UNFORMATTED DATA** (byte arrays, account addresses, hashes, bytecode arrays): encode as hex, prefix with "0x", two hex digits per byte. Examples:

- 0x41 (size 1, "A")
- 0x004200 (size 3, "\0B\0")
- 0x (size 0, "")
- WRONG: 0xf0f0f (must be even number of digits)
- WRONG: 004200 (must be prefixed 0x)

Currently **go-symverse** provides JSON-RPC communication only over http.

## The default block parameter

Some methods have a extra default block parameter.

The following options are possible for the defaultBlock parameter:

- `HEX String` - an integer block number

- `String "earliest"` - for the earliest/genesis block

- `String "latest"` - for the latest mined block

- `String "pending"` - for the pending state/transactions

## Curl Examples Explained

The curl options below might return a response where the node complains about the content type, this is because the --data option sets the content type to application/x-www-form-urlencoded . If your node does complain, manually set the header by placing -H "Content-Type: application/json" at the start of the call.

The examples also do not include the URL/IP & port combination which must be the last argument given to curl e.x. 127.0.0.1:8545

## Oracle parameters

Oracle parameters are used for reward calculation in the blockchain. The parameters are applied by Oraclizer through oracle_sendOracle method.

Following parameters are defined.

- `price`: `float` - SYM price from outside Oracle Service

- `costNodeA`: `uint` - Reference A group warrant node cost per month

- `costNodeB"`: `uint` - Reference B group warrant node cost per month

- `costNodeCA`: `uint` - Reference CA node cost per month

- `costNodeOra`: `uint` - Reference Oraclizer node cost per month

- `numNodeA`: `uint` - Number of Reference A group warrant node

- `numNodeB`: `uint` - Number of Reference B group warrant node

- `numNodeCA`: `uint` - Number of Reference CA node

- `numNodeOra`: `uint` - Number of Reference Oraclizer node

- `qualDappGas`: `int` - Minimum collateral for gas discount

- `qualNodeA`: `int` - Minimum collateral for A group warrant node

- `qualNodeB`: `int` - Minimum collateral for B group warrant node

- `qualNodeCA`: `int` - Minimum collateral for CA node

- `qualNodeOraclizer`: `int` - Minimum collateral for Oraclizer node

- `qualRewardGas`: `int` - Minimum account balance for reward

- `numRewardGas`: `uint` - Number of reward receivers who'd used gas one more per day = M

- `totalSupply`: `uint` - Total supply amount of new coin after genesis

- `degreeOfFreedom`: `float` - Factor of changing supply amount

- `minTxContribution`: `uint` - Minimum transaction count for reward

- `maxGasDiscountRate`: `float` - Maximum discount rate using at gas discount function

- `normPrice`: `float` - Normalized SYM price for initial value

## JSON RPC API Reference

#### web3_clientVersion

Returns the current client version.

##### Parameters

none

##### Returns

`String` - The current client version.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result": "Gsym/v0.0.6-Develope-074c4306/linux-amd64/go1.10.4"
}
```

---

#### web3_sha3

Returns the standardized SHA3-256 of the given data.

##### Parameters

1. `DATA` - the data to convert into a SHA3 hash.

```js
params: ["0x68656c6c6f20776f726c64"];
```

##### Returns

`DATA` - The SHA3 result of the given string.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"web3_sha3","params":["0x68656c6c6f20776f726c64"],"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": "0x5b2c76da96136d193336fad3fbc049867b8ca157da22f69ae0e4923648250acc"
}
```

---

#### net_version

Returns the current network id.

##### Parameters

none

##### Returns

`String` - The current network id.

- `"1"`: Symverse Mainnet
- `"2"`: Testnet

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_version","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc": "2.0",
  "result": "2"
}
```

---

#### net_listening

Returns `true` if client is actively listening for network connections.

##### Parameters

none

##### Returns

`Boolean` - `true` when listening, otherwise `false`.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_listening","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result":true
}
```

---

#### net_peerCount

Returns number of peers currently connected to the client.

##### Parameters

none

##### Returns

`QUANTITY` - integer of the number of connected peers.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":74}'

// Result
{
  "id":74,
  "jsonrpc": "2.0",
  "result": "0x2" // 2
}
```

---

#### sym_protocolVersion

Returns the current symverse protocol version.

##### Parameters

none

##### Returns

`String` - The current symverse protocol version.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_protocolVersion","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc": "2.0",
  "result": "0x40"
}
```

---

#### sym_syncing

Returns an object with data about the sync status or `false`.

##### Parameters

none

##### Returns

`Object|Boolean`, An object with sync status data or `FALSE`, when not syncing:

- `currentBlock`: `QUANTITY` - The current block, same as sym_blockNumber
- `startingBlock`: `QUANTITY` - The block at which the import started (will only be reset, after the sync reached his head)
- `highestBlock`: `QUANTITY` - The estimated highest block
- `pulledStates`: `QUANTITY` - Number of state trie entries already downloaded
- `knownStates`: `QUANTITY` - Total number of state trie entries known about

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_syncing","params":[],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": {
      startingBlock: "0x0", //0
      currentBlock: "0xe8", //232,
      highestBlock: "0x129", //297,
      pulledStates: "0x10", //16,
      knownStates: "0x10", //16,
  }
}
// Or when not syncing
{
  "id":1,
  "jsonrpc": "2.0",
  "result": false
}

```

---

#### sym_symbase

Returns the client coinbase address.

##### Parameters

none

##### Returns

`DATA`, 10 bytes - the current coinbase address.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_symbase","params":[],"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": "0x00000000000000001001"
}
```

---

#### sym_gasPrice

Returns the current price per gas in hug.

##### Parameters

none

##### Returns

`QUANTITY` - integer of the current gas price in hug.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_gasPrice","params":[],"id":73}'

// Result
{
  "id":73,
  "jsonrpc": "2.0",
  "result": "0x5d21dba00" // 25000000000
}
```

---

#### sym_accounts

Returns a list of addresses owned by client.

##### Parameters

none

##### Returns

`Array of DATA`, 10 Bytes - addresses owned by the client.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_accounts","params":[],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": ["0x00000000000000000901"]
}
```

---

#### sym_blockNumber

Returns the number of most recent block.

##### Parameters

none

##### Returns

`QUANTITY` - integer of the current block number the client is on.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_blockNumber","params":[],"id":1}'

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0xc94" // 1207
}
```

---

#### sym_getBalance

Returns the balance of the account of given address.

##### Parameters

1. `DATA`, 10 Bytes - address to check for balance.
2. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

```js
params: ["0x00000000000000001001", "latest"];
```

##### Returns

`QUANTITY` - integer of the current balance in hug.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getBalance","params":["0x00000000000000001001", "latest"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x0234c8a3397aab58" // 158972490234375000
}
```

---

#### sym_getTransactionCount

Returns the number of transactions sent from an address.

##### Parameters

1. `DATA`, 10 Bytes - address.
2. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

```js
params: [
  "0x00000000000000001001",
  "latest", // state at the latest block
];
```

##### Returns

`QUANTITY` - integer of the number of transactions sent from this address.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getTransactionCount","params":["0x00000000000000001001","pending"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

---

#### sym_getBlockTransactionCountByHash

Returns the number of transactions in a block from a block matching the given block hash.

##### Parameters

1. `DATA`, 32 Bytes - hash of a block.

```js
params: ["0x442839b4c7d6ae3bfe52e02e5f1ecca75946b02fe6b721277ab3b5ef64d459db"];
```

##### Returns

`QUANTITY` - integer of the number of transactions in this block.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getBlockTransactionCountByHash","params":["0x442839b4c7d6ae3bfe52e02e5f1ecca75946b02fe6b721277ab3b5ef64d459db"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xc" // 11
}
```

---

#### sym_getBlockTransactionCountByNumber

Returns the number of transactions in a block matching the given block number.

##### Parameters

1. `QUANTITY|TAG` - integer of a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter).

```js
params: [
  "0xe8", // 232
];
```

##### Returns

`QUANTITY` - integer of the number of transactions in this block.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getBlockTransactionCountByNumber","params":["0xe8"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xa" // 10
}
```

---

#### sym_getCode

Returns code at a given address.

##### Parameters

1. `DATA`, 10 Bytes - address.
2. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter).

```js
params: [
  "0x00000000000000000901",
  "0x2", // 2
];
```

##### Returns

`DATA` - the code from the given address.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getCode","params":["0x00000000000000000901", "0x2"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x"
}

```

---

#### sym_sign

The sign method calculates an Symverse specific signature with: `sign(sha256("\x19Symverse Signed Message:\n" + len(message) + message)))`.

By adding a prefix to the message makes the calculated signature recognisable as an Symverse specific signature. This prevents misuse where a malicious DApp can sign arbitrary data (e.g. transaction) and use the signature to impersonate the victim.

**Note** the address to sign with must be unlocked.

##### Parameters

1. `DATA`, 10 Bytes - address.
2. `DATA`, N Bytes - message to sign.

##### Returns

`DATA`: Signature

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_sign","params":["0x00000000000000000901", "0xdeadbeaf"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x55e5413cd051640f16b8a8c9659aef7357af5590ebcd51cdf10d3c866fd2ae3c14d9a9aa083f65e8d9a156f1dbea8e3dda72ee8736a4b810b9910c99875a03061c"
}

```

---

#### sym_sendTransaction

Creates new message call transaction or a contract creation, if the data field contains code.

##### Parameters

1. `Object` - The transaction object

- `from`: `DATA`, 10 Bytes - the address the transaction is send from.
- `to`: `DATA`, 10 Bytes - (optional when creating new contract) the address the transaction is directed to.
- `gas`: `QUANTITY` - (optional, default: 90000) integer of the gas provided for the transaction execution. It will return unused gas.
- `gasPrice`: `QUANTITY` - (optional, default: To-Be-Determined) integer of the gasPrice used for each paid gas.
- `value`: `QUANTITY` - (optional) integer of the value sent with this transaction.
- `type`:`QUANTITY` - (optional, default: 0) type of the transaction(0: original, 1: sct, 2: deposit)
- `input`: `DATA` - the compiled code of a contract OR the hash of the invoked method signature and encoded Parameters.
- `nonce`: `QUANTITY` - (optional) integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
- `workNodes`: `Array` - selected work node's SymID to send this transaction. (min: 1, max: 3)

```js
params: [
  {
    from: "0x00000000000000000101",
    to: "0x00000000000000000901",
    value: "0x9184e72a",
  },
];
```

##### Returns

`DATA`, 32 Bytes - the transaction hash, or the zero hash if the transaction is not yet available.

Use [sym_getTransactionReceipt](#sym_gettransactionreceipt) to get the contract address, after the transaction was mined, when you created a contract.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_sendTransaction","params":[{see above}],"id":100}'

// Result
{
  "id":100,
  "jsonrpc": "2.0",
  "result": "0x97719876eb686bcd5bd92f41d9646686c4240796c660f04a8d33f88bf6d26424"
}

```

---

#### sym_sendRawTransaction

Creates new message call transaction or a contract creation for signed transactions.

##### Parameters

1. `DATA`, The signed transaction data.

```js
params: [
  "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675",
];
```

##### Returns

`DATA`, 32 Bytes - the transaction hash, or the zero hash if the transaction is not yet available.

Use [sym_getTransactionReceipt](#sym_gettransactionreceipt) to get the contract address, after the transaction was mined, when you created a contract.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_sendRawTransaction","params":[{see above}],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
}

```

---

#### sym_call

Executes a new message call immediately without creating a transaction on the block chain.

##### Parameters

1. `Object` - The transaction call object

- `from`: `DATA`, 10 Bytes - The address the transaction is sent from.
- `to`: `DATA`, 10 Bytes - The address the transaction is directed to.
- `gas`: `QUANTITY` - (optional) Integer of the gas provided for the transaction execution. sym_call consumes zero gas, but this parameter may be needed by some executions.
- `gasPrice`: `QUANTITY` - (optional) Integer of the gasPrice used for each paid gas
- `value`: `QUANTITY` - (optional) Integer of the value sent with this transaction
- `data`: `DATA` - (optional) Hash of the method signature and encoded parameters.
- `type`:`QUANTITY` - (optional, default: 0) type of the transaction(0: original, 1: sct, 2: deposit)

2. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

##### Returns

`DATA` - the return value of executed contract.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_call","params":[{see above}],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x0"
}

```

---

#### sym_estimateGas

Generates and returns an estimate of how much gas is necessary to allow the transaction to complete. The transaction will not be added to the blockchain. Note that the estimate may be significantly more than the amount of gas actually used by the transaction, for a variety of reasons including EVM mechanics and node performance.

##### Parameters

See [sym_call](#sym_call) parameters, expect that all properties are optional. If no gas limit is specified gsym uses the block gas limit from the pending block as an upper bound. As a result the returned estimate might not be enough to executed the call/transaction when the amount of gas is higher than the pending block gas limit.

##### Returns

`QUANTITY` - the amount of gas used.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_estimateGas","params":[{see above}],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x5208" // 21000
}

```

---

#### sym_getBlockByHash

Returns information about a main block, specified by the hash.

##### Parameters

1. `DATA`, 32 Bytes - Hash of a block.
2. `Boolean` - If `true` it returns the full transaction objects, if `false` only the hashes of the transactions.

```js
params: [
  "0xbc26bf7324c622b934da753d016768373032b8bbf5488f8be9ad5bbfafd2bd12",
  true,
];
```

##### Returns

`Object` - A block object, or `null` when no block was found:

- `activeWbNum` : `QUANTITY` – warrant block number which is used for creating the block.
- `cbHash` : `DATA` , 32 Bytes - hash of a citizen block which is created with the main block. null when it’s not created.
- `cbNum` : `QUANTITY` - latest citizen block number which is created with the main block.
- `extraData`: `DATA` - the "extra data" field of this block.
- `gasLimit`: `QUANTITY` - the maximum gas allowed in this block.
- `gasUsed`: `QUANTITY` - the total used gas by all transactions in this block.
- `hash`: `DATA`, 32 Bytes - hash of the block. `null` when its pending block.
- `logsBloom`: `DATA`, 256 Bytes - the bloom filter for the logs of the block. `null` when its pending block.
- `number`: `QUANTITY` - the block number. `null` when its pending block.
- `parentHash`: `DATA`, 32 Bytes - hash of the parent block.
- `primary` : `DATA` ,10 Bytes – address of the primary node.
- `receiptsRoot`: `DATA`, 32 Bytes - the root of the receipts trie of the block.
- `rewards`: `Array` - array of reward information which created this time.
- `sctRoot`: `DATA`, 32 Bytes - the root of the final SCT's state trie of the block.
- `signInfo` : Object –
  - `mbNum` : a main block's number targeted to sign.
  - `sign` : a aggregate signature of a block by primary and warrants which in the `validatorInfos`.
  - `extraData`: `DATA` - the "extra data" related with signature.
- `size`: `QUANTITY` - integer the size of this block in bytes.
- `stateRoot`: `DATA`, 32 Bytes - the root of the final state trie of the block.
- `tickStamp`: `QUANTITY` - a number which is related with block generate epoch.
- `timestamp`: `QUANTITY` - the unix timestamp for when the block was collated.
- `transactions`: `Array` - array of transaction objects, or 32 Bytes transaction hashes depending on the last given parameter.
- `transactionsRoot`: `DATA`, 32 Bytes - the root of the transaction trie of the block.
- `validatorInfos` : `Array` - array of SymIDs which agree with make current block.
- `wbhash` : `DATA` , 32 Bytes - hash of the warrant block which is created with the main block. null when it’s not created.
- `wbnum` : `QUANTITY` - the warrant block number which is created with the main block. null when it’s not created.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getBlockByHash","params":["0xb592cb08492f8cd797af67c720b0da34939917abe6f0cf71894e3d6c3d6c792d", true],"id":100}'

// Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": {
        "activeCbNum": "0x0",
        "activeObNum": "0x0",
        "activeWbNum": "0x193",
        "cbHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "cbNum": "0x0",
        "extraData": "0xd508846773796d88676f312e31302e34856c696e7578",
        "gasLimit": "0xb3c36a0",
        "gasUsed": "0x0",
        "hash": "0x9d6c699063bb896cd646220db0bd1d30bf609d8d1cf1fd28a100cd59d0d8cea8",
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "number": "0x1f89",
        "obHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "obNum": "0x0",
        "parentHash": "0xa893358d4d335e6dda1c22ce7b70b7faa4860e15cf7599410906055c5c15da53",
        "primary": "0x00000000000000000401",
        "receiptsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
        "rewardRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
        "rewards": [],
        "sctRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
        "signInfo": {
            "mbNum": "0x1f89",
            "sign": "0x3c6ddf6922780f1cd941b557422c7564822426df4db4fa6143e6bbc4ed85d359331dc207dbdb470c82a80a26816a3e741ddfb4880666371305f2429d2285f55b",
            "extraData": "0x"
        },
        "size": "0x309",
        "stateRoot": "0xb8fd81babf4f075dd08bffd56d3f07530b7b52e2f780c80c843695f1f5e7a6c9",
        "tickStamp": "0x1f89",
        "timeStamp": "0x5d9afa71",
        "transactions": [],
        "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
        "validatorInfos": [
            "0x00000000000000000601",
            "0x00000000000000000101",
            "0x00000000000000000501",
            "0x00000000000000000201",
            "0x00000000000000000701",
            "0x00000000000000000301"
        ],
        "wbHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "wbNum": "0x193"
    }
}
```

---

#### sym_getBlockByNumber

Returns information about a block by block number.

##### Parameters

1. `QUANTITY|TAG` - Integer of a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter).
2. `Boolean` - If `true` it returns the full transaction objects, if `false` only the hashes of the transactions.

```js
params: [
  "0x1", // 1
  true,
];
```

##### Returns

See [sym_getBlockByHash](#sym_getblockbyhash)

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getBlockByNumber","params":["0x1", true],"id":100}'

```

Result see [sym_getBlockByHash](#sym_getblockbyhash)

---

#### sym_getTransactionByHash

Returns information regarding transaction requested by transaction hash.

##### Parameters

1. `DATA`, 32 Bytes - Hash of a transaction

```js
params: ["0x623ecfee09d2614630966768df9403806ac566f5d2c1bfb809123cd688e0f580"];
```

##### Returns

`Object` - A transaction object, or `null` when no transaction was found:

- `blockHash`: `DATA`, 32 Bytes - hash of the block where this transaction was in. `null` when its pending.
- `blockNumber`: `QUANTITY` - block number where this transaction was in. `null` when its pending.
- `from`: `DATA`, 10 Bytes - address of the sender.
- `gas`: `QUANTITY` - gas provided by the sender.
- `gasPrice`: `QUANTITY` - gas price provided by the sender in Hug.
- `hash`: `DATA`, 32 Bytes - hash of the transaction.
- `input`: `DATA` - the data send along with the transaction.
- `nonce`: `QUANTITY` - the number of transactions made by the sender prior to this one.
- `to`: `DATA`, 10 Bytes - address of the receiver. `null` when its a contract creation transaction.
- `transactionIndex`: `QUANTITY` - integer of the transaction's index position in the block. `null` when its pending.
- `type`:`QUANTITY` - type of the transaction (0: original, 1: sct, 2: deposit)
- `workNodes`: `Array` - Array of selected work nodes via SymID when sending transactions . (min: 1, max: 3)
- `value`: `QUANTITY` - value transferred in Hug.
- `extraData`: `DATA` - the "extra data" field of this transaction.
- `v`: `QUANTITY` - ECDSA recovery id
- `r`: `DATA`, 32 Bytes - ECDSA signature r
- `s`: `DATA`, 32 Bytes - ECDSA signature s

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getTransactionByHash","params":["0x623ecfee09d2614630966768df9403806ac566f5d2c1bfb809123cd688e0f580"],"id":100}'

// Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": {
        "blockHash": "0xfca1fb9d1f0d88ca95e2e2a6d183de81927db868284a9547cafc54f727c7e177",
        "blockNumber": "0x1a",
        "from": "0x00000000000000000301",
        "gas": "0x15f90",
        "gasPrice": "0x5d21dba00",
        "hash": "0x623ecfee09d2614630966768df9403806ac566f5d2c1bfb809123cd688e0f580",
        "input": "0x",
        "nonce": "0x0",
        "to": "0x00000000000000001301",
        "transactionIndex": "0x0",
        "type": "0x0",
        "workNodes": [
            "0x00000000000000000301"
        ],
        "value": "0xe8d4a51000000000000000000",
        "extraData": "0x",
        "v": "0x1",
        "r": "0x9e5d14ac9abe55a032427bae4b146bd9e141454d156429a56b255070592c3344",
        "s": "0x22ce1ffd30d080ade57c613c85fb68c3a147917588fa8527818945cc27258b6b"
    }
}

```

---

#### sym_getTransactionByBlockHashAndIndex

Returns information about a transaction by block hash and transaction index position.

##### Parameters

1. `DATA`, 32 Bytes - hash of a block.
2. `QUANTITY` - integer of the transaction index position.

```js
params: [
  "0x2d01dcb69ea3b02d441b6214da5d68a9a0c6c548ac3c17ec388ea7d2b0ff8279",
  "0x2", // 0
];
```

##### Returns

See [sym_getTransactionByHash](#sym_gettransactionbyhash)

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getTransactionByBlockHashAndIndex","params":["0x2d01dcb69ea3b02d441b6214da5d68a9a0c6c548ac3c17ec388ea7d2b0ff8279", "0x2"],"id":1}'

```

Result see [sym_getTransactionByHash](#sym_gettransactionbyhash)

---

#### sym_getTransactionByBlockNumberAndIndex

Returns information about a transaction by block number and transaction index position.

##### Parameters

1. `QUANTITY|TAG` - a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter).
2. `QUANTITY` - the transaction index position.

```js
params: [
  "0xd65a", // 54874
  "0x2", // 2
];
```

##### Returns

See [sym_getTransactionByHash](#sym_gettransactionbyhash)

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getTransactionByBlockNumberAndIndex","params":["0xd65a", "0x2"],"id":1}'

```

Result see [sym_getTransactionByHash](#sym_gettransactionbyhash)

---

#### sym_getTransactionReceipt

Returns the receipt of a transaction by transaction hash.

**Note** That the receipt is not available for pending transactions.

##### Parameters

1. `DATA`, 32 Bytes - hash of a transaction

```js
params: ["0x77c684a89b85adc04fdf1908347e11705b45d2ad1b4e6237f65e6332b9a08ac4"];
```

##### Returns

`Object` - A transaction receipt object, or `null` when no receipt was found:

- `blockHash`: `DATA`, 32 Bytes - hash of the block where this transaction was in.
- `blockNumber`: `QUANTITY` - block number where this transaction was in.
- `contractAddress`: `DATA`, 10 Bytes - The contract address created, if the transaction was a contract creation, otherwise `null`.
- `cumulativeGasUsed`: `QUANTITY` - The total amount of gas used when this transaction was executed in the block.
- `from`: `DATA`, 10 Bytes - address of the sender.
- `gasUsed`: `QUANTITY` - The amount of gas used by this specific transaction alone.
- `logs`: `Array` - Array of log objects, which this transaction generated.
- `logsBloom`: `DATA`, 256 Bytes - Bloom filter for light clients to quickly retrieve related logs.
- `status`: `QUANTITY` either `1` (success) or `0` (failure)
- `to`: `DATA`, 10 Bytes - address of the receiver. null when it's a contract creation transaction.

- `transactionHash`: `DATA`, 32 Bytes - hash of the transaction.
- `transactionIndex`: `QUANTITY` - integer of the transaction's index position in the block.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getTransactionReceipt","params":["0x77c684a89b85adc04fdf1908347e11705b45d2ad1b4e6237f65e6332b9a08ac4"],"id":100}'

// Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": {
        "blockHash": "0x2d01dcb69ea3b02d441b6214da5d68a9a0c6c548ac3c17ec388ea7d2b0ff8279",
        "blockNumber": "0xd65a",
        "contractAddress": null,
        "cumulativeGasUsed": "0xf618",
        "from": "0x00000000000000001001",
        "gasUsed": "0x5208",
        "logs": [],
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "status": "0x1",
        "to": "0x00000000000000000901",
        "transactionHash": "0x77c684a89b85adc04fdf1908347e11705b45d2ad1b4e6237f65e6332b9a08ac4",
        "transactionIndex": "0x2"
    }
}

```

---
#### sym_getReward

Returns the reward by reward hash.

##### Parameters

1. `DATA`, 32 Bytes - hash of a reward

```js
params: ["0x77c684a89b85adc04fdf1908347e11705b45d2ad1b4e6237f65e6332b9a08ac4"];
```

##### Returns

`Object` - A reward object, or `null` when no reward was found::

- `blockHash`: `DATA`, 32 Bytes - hash of the block where this reward was in.
- `blockNumber`: `QUANTITY` - block number where this transaction was in.
- `extra`: `DATA` 10 Bytes - reward delegation account for that warrant node.
- `rewardHash`: `DATA`, 32 Bytes - hash of the reward.
- `rewardIndex`: `QUANTITY` - integer of the reward's index position in the block.
- `symId`: `DATA`, 10 Bytes - address of the warrant node.
- `type`: `QUANTITY`, - device type. (wallet : 0, work : 1, warrant :2, ca : 3, oraclizer : 4)
- `value`: `QUANTITY` - reward amount in Hug.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getReward","params":["0x77c684a89b85adc04fdf1908347e11705b45d2ad1b4e6237f65e6332b9a08ac4"],"id":100}'

// Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": {
        "blockHash": "0x020d2d8e4a59dcb53a3187d615960e6b59e246328fea5880ead7032b442171d6",
        "blockNumber": 43895200,
        "extra": "0x0002aea7a09991500002",
        "hash": "0x6fbebf239ffba49e3caf4086485ad7ac61dbbcb80838c278fb8ba2551966ff7c",
        "rewardIndex": 0,
        "symId": "0x0002282119120b040002",
        "type": 2,
        "value": 514285714285714285714
    }
}

```
---

#### sym_getDeposit

Returns the deposit related information of the account of given address.

##### Parameters

1. `DATA`, 10 Bytes - address to check for deposit related information

##### Returns

`Object` - A deposit related information object:

- `account`: `DATA`, 10 Bytes - address to check for balance.
- `balance`: `QUANTITY` - integer of the current balance in hug.
- `deposit`: `QUANTITY` - integer of the allocated deposit in hug.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_getDeposit","params":["0x00000000000000000401"],"id":100}'

// Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": {
        "account": "0x00000000000000000401",
        "balance": "0xc097ce7bc8f202c96816a6724359b7",
        "deposit": "0x152d02c7e14af6800000"
    }
}
```

---

#### sym_setDeposit

Returns the transaction hash of set deposit result.

##### Parameters

1. `DATA`, 10 Bytes - address to check for deposit related information
2. `QUANTITY` - integer of the value want to assign as deposit.

##### Returns

`DATA`, 32 Bytes - the transaction hash, or the zero hash if the transaction is not yet available.

**Note** the address to set deposit must be unlocked.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_setDeposit","params":["0x00000000000000000401","0x10000000000000"],"id":100}'

// Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": "0xebbe46ee6157c0c43d64988d4595fc794202587adf016a78e9b930a4deb9e379"
}
```

---

#### sym_restoreDeposit

Returns the transaction hash of restored deposit result.

##### Parameters

1. `DATA`, 10 Bytes - address which restore the deposit.

##### Returns

`DATA`, 32 Bytes - the transaction hash, or the zero hash if the transaction is not yet available.

**Note** the address to set restore must be unlocked.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"sym_restoreDeposit","params":["0x00000000000000000401"],"id":100}'

// Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": "0xf24d14f4acbf343d512e277897044070924ee0a5a0b21ea3c0d9b443739e2d8a"
}
```

---

#### warrant_blockNumber

Returns the current warrant block number.

##### Parameters

None

##### Returns

`QUANTITY` - Integer of the current warrant block number the client is on.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{ "jsonrpc":"2.0", "method":"warrant_blockNumber", "params":[], "id":100}' http://127.0.0.1:8545

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": "0x97" //151
}
```

---

#### warrant_getWarrantsByBlockHash

Returns information about warrant nodes by the block hash.

##### Parameters

1. `DATA`, 32 Bytes - Hash of a warrant block.

##### Returns

`Array of Object` , N Bytes - Array of the existing warrant nodes in the designated block or null when failed.

- `symId`:`DATA`, 10 Bytes - the warrant node's identity.
- `bKeyPub`: `DATA`, 128 Bytes - public key of the warrant node.
- `bKeyPubSigned`: `DATA`, 65 Bytes - sign of block public key made with account key.
- `group`: `QUANTITY` - integer denoting for the group type of warrant node. (0: group A, 1: group B)
- `elected`: `QUANTITY` - main block number when the warrant elected as a warrant.
- `finished`: `QUANTITY` - main block number when the warrant finished its term.
- `extraData`: `DATA`, 100 Bytes - extra information for the warrant.
- `hash`: `DATA`, 32 Bytes - hash of the warrant node.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{ "jsonrpc":"2.0", "method":"warrant_getWarrantsByBlockHash", "params":["0x4018ae70b6aa9f603dcee234d52f08e1039fb0ca2ccdcd254bf11159f7711f8e"], "id":100 }' http://127.0.0.1:8545

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": [
        {
            "symId": "0x00000000000000000101",
            "bKeyPub": "0x0eba8ced17621964dcd19457711749a62242de60cc8dacc300c627875c11aa88874e1cd0a483e605166fb22cf66e63c9c2433a03608afdf2343413d951a37f550a1600904f745e8133deae615b40b4c37f4b8f3fa0d7de052078fa96025dfeab84b5de720273ecbbee3db6758071bb160b40b8c2465110d0bf99c858bbfe640a",
            "bKeyPubSigned": "0x9e2abc6bfb2751af5368022717d31978c8feb0dc8263ee03fc4ce469529c57c20577dab31527a918f0410c03e7e7876091ce9e92052696706c5be95ac023ad2901",
            "group": "0x1",
            "elected": "0x1",
            "finished": "0x50",
            "extraData": "0x",
            "hash": "0xfeea462f903c109ef7cc15ca646b553371f256cb1b01a05a48bf4499286e217f"
        },
        {
            "symId": "0x00000000000000000201",
            "bKeyPub": "0x1e52c8421d8b0edd52ea4a3eb7e0c028958cb1463e05bce78b662e46f5aff1f98ea1d76334283f6ddc3e0f81b7abcfc7ad46e5769fa8891d6e26a30577cce6f1365275a96418d0e065c435ee30101d5a09e6a04ffc86246302f2217f5325b99c0f1dd95035621cc8ace596b82fcbf592183f3472acb71569b121edde13ef59fb",
            "bKeyPubSigned": "0xfdbe28e70516f8dca8b2f82e2c4eccbee075a1dd4b696a91cfc6e258131f9b7818543791d7300f758820099e01d5030b3c568fc8dd08765c4577c5ba8d5e7bf301",
            "group": "0x1",
            "elected": "0x1",
            "finished": "0x50",
            "extraData": "0x",
            "hash": "0x4ae192986aec09e75eb308fcfd3e928a556d1d50ef2b7985e42831dab3021c51"
        },
        {
            "symId": "0x00000000000000000301",
            "bKeyPub": "0x246c1ab9913ebe51726ca3c1ce0a99305a03cb090e446a685197c29793f5e95724016209259830027d046c04127c09bb0289d2c802ea99f794293b270ec77b02258a559f243a7fdf5d0e6d8befcfd5b4d3b77f5936da3a9a88cabf256e0601fc1cca3dc4aba8a09192c6c66846413cd09fbdd97044521850afde40c46d62e89c",
            "bKeyPubSigned": "0xbf3d83975d9e1d4274e7d754beb620ff61c9dfa69ec3ccf07a60bf68017e59ec1ac84b07075d55bba5e8d2f85fc8cf98b5a87b873493a591cd0c948d8fd72ff401",
            "group": "0x1",
            "elected": "0x1",
            "finished": "0x50",
            "extraData": "0x",
            "hash": "0xe9e5c9c10ef4ff567ea022668f0206330d99d912e404b682e4a6bf226e58b91f"
        },
        {
            "symId": "0x00000000000000000401",
            "bKeyPub": "0x84da52e3189de4a1a268b955d6331c36aef5e436d5bd13c441ff2a5da4b598b91f6ea50d290a79545a0ebcdcfd6daf6ad11bee200eceb6b310c9205b9be4dc6e86c7c70ba617edfa90fd4904478375254f0aaf23dd85bddb0ec43409cda3228715c430b317676a29394dc2c503b5dd23f75f7fbbdcc33f724b820a9043492c9f",
            "bKeyPubSigned": "0x89124bf7c4fd6cc013bd58202c0d6ac189c3eaae72fce5b99d4faaf04895089c3e5febaf6fdb99539fe4a6f3ad04f76344893caccf953346b6369fbc48f3fb3e01",
            "group": "0x1",
            "elected": "0x1",
            "finished": "0x50",
            "extraData": "0x",
            "hash": "0xd511e514a73a4b009a5ef4cb2439bb6a78b783c42968099cb94e887b7330f207"
        }
    ]
}
```

---

#### warrant_getWarrantsByBlockNumber

Returns information about warrant nodes specified by the block number.

##### Parameters

1. `QUANTITY` - Integer of a block number.

##### Returns

See [warrant_getWarrantsByBlockHash](#warrant_getWarrantsByBlockHash)

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0", "method":"warrant_getWarrantsByBlockNumber", "params":["0x0"], "id":100}' http://127.0.0.1:8545

//Result
see warrant_getWarrantsByBlockHash
```

#### warrant_getBlockByHash

Returns information about warrant nodes specified by the block hash.

##### Parameters

1. `DATA`, 32 Bytes - Hash of a warrant block.
2. `Boolean` - If `true` it returns the full warrant Information objects, if `false` only the hashes of the warrants.

##### Returns

`Object` - A warrant block object, or `null` when no warrant block was found:

- `beginMbNum` : `QUANTITY` - beginning number of the period.
- `endMbNum` : `QUANTITY` - ending number of the period.
- `hash`: `DATA`, 32 Bytes - hash of the block. `null` when its pending block.
- `number`: `QUANTITY` - the block number. `null` when its pending block.
- `parentHash`: `DATA`, 32 Bytes - hash of the parent block.
- `relatedMbNum`: `QUANTITY` - main block number when this warrant block was created. `null` when its pending block.
- `size`: `QUANTITY` - integer the size of this block in bytes.
- `warrants` : `Array` – Information of warrant nodes in the designated block, or 32 Bytes warrant information’s hashes depending on the last given parameter.
- `warrantsRoot`: `DATA`, 32 Bytes - the root of the warrant node's Informations trie of the block.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{ "jsonrpc":"2.0", "method":"warrant_getBlockByHash", "params":["0x2b440afed4852f577ea9607b514eaaf0e62f1b5f1c601a89fb1918161f32cd87",true], "id":100 }' http://127.0.0.1:8545


//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": {
        "beginMbNum": "0xd71",
        "endMbNum": "0xd84",
        "hash": "0x2b440afed4852f577ea9607b514eaaf0e62f1b5f1c601a89fb1918161f32cd87",
        "number": "0xac",
        "parentHash": "0xdd5316e4d78e90976d487f031277a720236b91ddb68eeaa85f4626cd15ba45a5",
        "relatedMbNum": "0xd6b",
        "size": "0x90a",
        "warrants":  [{...},{...}],
        "warrantsRoot": "0x1c2554ed414944ebd1c1bd1a8ae82b906da14285c619fe47ab92dc2a640128b0"
    }
}
```

---

#### warrant_getBlockByNumber

Returns information about a warrant block specified by the block number.

##### Parameters

1. `QUANTITY|TAG` - Integer of a block number, or the string `"earliest"`,`"pending"` or `"latest"`
2. `Boolean` - If `true` it returns the full warrant Information objects, if `false` only the hashes of the warrants.

##### Returns

See [warrant_getBlockByHash](#warrant_getBlockByHash)

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0",  "method":"warrant_getBlockByNumber", "params":["0x0",true], "id":100}' http://127.0.0.1:8545

//Result
see warrant_getblockByHash
```

---

#### warrant_GetRewardAddr

Returns information about the reward delegation account for that warrant node based on the block hash or number and the warrant node account.

##### Parameters

1. `symId`:`DATA`, 10 Bytes - the warrant node's identity. 
2. `DATA`, 32 Bytes - Hash of a warrant block  or `QUANTITY|TAG`  - Integer of a block number, or the string `"earliest"`,`"pending"` or `"latest"`

##### Returns

`symId:DATA, 10 Bytes` -  Reward delegation account for that warrant node.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{ "jsonrpc":"2.0", "method":"warrant_getRewardAddr", "params":["0x0002fdf5eee7e0d80002","latest"], "id":100 }' <http://127.0.0.1:8545>
// or
// curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{ "jsonrpc":"2.0", "method":"warrant_getRewardAddr", "params":["0x0002fdf5eee7e0d80002",762], "id":100 }' <http://127.0.0.1:8545>
// or
// curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{ "jsonrpc":"2.0", "method":"warrant_getRewardAddr", "params":["0x0002fdf5eee7e0d80002","0xeafdd6c6d79a2e3cb41064bcbee9ee8416c4bf97987d859eaab9ef34e9223073"], "id":100 }' <http://127.0.0.1:8545>
//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": "0x0002aea7a09991500002"
}
```

---

#### citizen_sendCitizen

The message call in order to create new citizen or change citizen information.

##### Parameters

1. `Object` - the Symverse identifier & account properties.

- `from` : `DATA` , 10 Bytes - the address of the CA server that creates SymID.

- `to` : `DATA` , 10 Bytes - (optional) the address creation transaction is directed to.

- `symId` : `DATA` , 10 Bytes - hexadecimal SymID identifying an account.

- `aKeyPubH` : `DATA` , 20 Bytes - lower bytes of hashed public key.

- `vFlag` : `DATA` , 1 Byte - (optional ,default:0) veriﬁcation strength of an account represented by bits.

- `country` : `DATA` , 1 Byte - (optional, default:0) country code.

- `status` : `DATA` , 1 Byte - (optional, default:1, active) account status of an account. (0x1: Active, 0x2: Locked, 0x3: Marked, 0x4: Revoked)

- `credit` : `DATA` , 1 Byte - (optional, default:0) credit rating of an account (0~15)

- `role` : `DATA` , 2 Bytes - (optional, default: 0x1) account role (0x1: general, 0xf0f0: Master CA, 0xf0f1: CA, 0xf0f2: Oracle)

- `refCode` : `DATA` , 4 Bytes - (optional) issuer's reference code.

- `nonce` : `QUANTITY` - (optional) integer of a nonce.

  **Note : See Symverse identifier & account properties in detail.**

```json
params:[{
    "from": "0x00000000000000000901",
    "to": "0x00000000000000000000",
    "vFlag":"0x0",
    "symId":"0x00010000000000010003",
    "aKeyPubH":"0x484E0b5D12913CeC0B4e942828F6168E9F464A09",
    "country": "0x0",
    "status": "0x1",
    "credit": "0xf",
    "role": "0x1"
}]
```

##### Returns

`DATA`, 32 Bytes - hash of a citizen.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_sendCitizen","params":[{see above}],"id":100}' http://127.0.0.1:8545

//Result
{"jsonrpc":"2.0","id":100,"result":"0x4841852ea5d07be83b6ffc087619664b4609405c286c7fe325492c16ca53f2c5"}
```

---

#### citizen_sendRawCitizen

The message call in order to create new citizen or change citizen information for signed citizen.

##### Parameters

1. `Object` - signed citizen, RLP encoded value of signed citizen.

##### Returns

`DATA`, 32 Bytes - hash of a citizen.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_sendRawCitizen","params": "0x20ba02f3659983a51a761ccf7e4d2e4a62610a3e20ba02f3659983a51a761ccf7e4d2e4a62610a3e","id":100}' http://127.0.0.1:8545

//Result
{"jsonrpc":"2.0","id":100,"result":"0x09e67b2f1bd21f6043368f97ebff4f4a2b238111c5a5767b75abde08845bda6e"

```

---

#### citizen_getCitizenByHash

Returns information about a citizen publication requested by the citizen hash.

##### Parameters

1. `DATA`, 32 Bytes - hash of a citizen.

##### Returns

`Object` - A block object, or `null` when it was failed:

- `blockHash`: `DATA`, 32 Bytes - hash of block.
- `blockNumber`: `QUANTITY` - number of block.
- `citizenIndex`: `QUANTITY` - integer of the citizen's index position in the block. `null` when its pending.
- `from` : `DATA` , 10 Bytes - the address of the CA server that creates SymID.
- `to` : `DATA` , 10 Bytes - the address creation transaction is directed to.
- `nonce` : `QUANTITY` - integer of a nonce.
- `symId` : `DATA` , 10 Bytes - hexadecimal SymID identifying an account.
- `aKeyPubH` : `DATA` , 20 Bytes - lower bytes of hashed public key.
- `vFlag` : `DATA` , 1 Byte - veriﬁcation strength of an account represented by bits.
- `country` : `DATA` , 1 Byte - country code.
- `status` : `DATA` , 1 Byte - account status of an account. (0x1: Active, 0x2: Locked, 0x3: Marked, 0x4: Revoked)
- `credit` : `DATA` , 1 Byte - credit rating of an account (0~15)
- `role` : `DATA` , 2 Bytes - account role (0x1: general, 0xf0f0: Master CA, 0xf0f1: CA)
- `refCode` : `DATA` , 4 Bytes - issuer's reference code.
- `writeTime` : `QUANTITY` - the unix timestamp for the citizen requested time.
- `hash` : `DATA` , Bytes - citizen hash.
- `v`: `QUANTITY` - ECDSA recovery id
- `r`: `DATA`, 32 Bytes - ECDSA signature r
- `s`: `DATA`, 32 Bytes - ECDSA signature s

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_getCitizenByHash","params":["0x289a9abcf00a5959ee7fe11bc157cd3a5882b7ae2f86f4ce338bbe00d07c98a1"],"id":100}' http://127.0.0.1:8545

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": {
        "blockHash": "0x486dda2c8470ca2a885cd4cef5a96a4a561cb0f78fa1faad23a60be7d9797acd",
        "blockNumber": "0x1",
        "citizenIndex": "0x0",
        "from": "0x00000000000000000901",
        "to": "0x00000000000000000901",
        "nonce": "0x0",
        "symId": "0x00000000000000002001",
        "aKeyPubH": "0x0000000000000000000000000000000000000000",
        "vFlag": "0x0",
        "country": "0x0",
        "status": "0x1",
        "credit": "0x0",
        "role": "0x1",
        "refCode": "0x0",
        "writeTime": "0x5d9bf803",
        "hash": "0x289a9abcf00a5959ee7fe11bc157cd3a5882b7ae2f86f4ce338bbe00d07c98a1",
        "v": "0x0",
        "r": "0xe2ad882dfce94e0b04b8239d8ccdd61c43fe309daa980d6226380fc7f2787fd1",
        "s": "0x762c3710daff628557aa91e12e36618c9071a99a7c7cd7e9b90e9eb7cd548276"
    }
}
```

---

#### citizen_getRawCitizenByHash

Returns an RLP encoded value of a signed citizen specified by the hash.

##### Parameters

1. `DATA`, 32 Bytes - hash of a citizen.

##### Returns

`DATA` - RLP encoded value of a signed citizen.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_getRawCitizenByHash","params":["0x7496b0869fce231bbdc2f68f48dfe3929bdfd1e52c9c87d41716cd83f18cc2d1"],"id":100}' http://127.0.0.1:8545

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": "0xf8828a000000000000000009018a00000000000000001101808a0000000000000000011194484e0b5d12913cec0b4e942828f6168e9f464a098080010f0180808080a03fae1c96dc82545c966e3601141d30edf5ef8abfebf368d7c434de9c9f993253a05bddfcba41de55066e62b96d0248b3620aa791e2c8b9597549d62ec08a8bc90b"
}

```

---

#### citizen_getCitizenBySymID

Returns the information of a citizen by the SymID.

##### Parameters

1. `DATA`, 10Bytes - SymID.
2. `QUANTITY|TAG` - Integer of a block number, or the string `"earliest"` or `"latest"`

##### Returns

`Object` , N Bytes - For existing citizen information or `null` when it was failed

- `ca`: `DATA`, 10 Bytes - the issuer's SymID of the citizen.
- `nonce`: `QUANTITY` - integer of a nonce.
- `aKeyPubH` : `DATA`, 20 Bytes - lower bytes of hashed public key.
- `vFlag` : `DATA` , 1 Byte - veriﬁcation strength of an account represented by bits.
- `country`: `DATA`, 1 Byte - country code.
- `status` : `DATA` , 1 Byte - account status of an account. (0x1: Active, 0x2: Locked, 0x3: Marked, 0x4: Revoked)
- `credit` : `DATA`, 1 Byte - account credit rating(0~15)
- `role` : `DATA` , 2 Bytes - account role (0x1: general, 0xf0f0: Master CA, 0xf0f1: CA, 0xf0f2: Oracle)
- `refCode`: `DATA`, 4 Bytes - issuer's reference code.
- `writeTime` : `QUANTITY` - the unix timestamp for the citizen requested time.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_getCitizenBySymID", "params":["0x00000000000000001001","latest"],"id":100}' http://127.0.0.1:8545

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": {
        "ca": "0x00000000000000000801",
        "aKeyPubH": "0xf6231e6649cdf3aa13002b659026dba31156036f",
        "nonce": "0x0",
        "vFlag": "0x0",
        "country": "0x0",
        "status": "0x1",
        "credit": "0x0",
        "role": "0xf0f1",
        "refCode": "0x0",
        "writeTime": "0x0"
    }
}
```

---

#### citizen_getRawCitizenBySymID

Returns the RLP encoded value of a signed citizen specified by SymID.

##### Parameters

1. `DATA`, 10Bytes - SymID.
2. `QUANTITY|TAG` - Integer of a block number, or the string `"earliest"` or `"latest"`

##### Returns

`DATA` - RLP encoded value of signed citizen.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_getRawCitizenBySymID","params":["0x00000000000000001001","latest"],"id":100}' http://127.0.0.1:8545

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": "0xe98a000000000000000008018094cb670a6d98a5dfa7963ebba66ece1001a5e87c2a8080010f82f0f280"
}

```

---

#### citizen_getCitizensByBlockNumber

Returns all citizens at the designated citizen block number.

##### Parameters

1.`QUANTITY|TAG` - Integer of a block number, or the string `"earliest"` or `"latest"`

##### Returns

`Array of Object` , N Bytes - For existing citizen information or `null` when it was failed or none.

- `blockHash`: `DATA`, 32 Bytes - hash of block.
- `blockNumber`: `QUANTITY` - number of block.
- `citizenIndex`: `QUANTITY` - integer of the citizen's index position in the block. `null` when its pending.
- `from`: `DATA`, 10 Bytes - the address the CA who creates this SymID.
- `to`: `DATA`, 10 Bytes - the address is directed to.
- `nonce`: `QUANTITY` - integer of a nonce.
- `symId` : `DATA`, 10 Bytes - created SymID.
- `aKeyPubH` : `DATA`, 20 Bytes - lower bytes of hashed public key.
- `vFlag`: `DATA`, 1 Byte - each bit means verification strength of this SymID.
- `country`: `DATA`, 1 Byte - country code.
- `status` : `DATA`, 1 Byte - account status (0x1: Active, 0x2: Locked, 0x3: Marked, 0x4: Revoked).
- `credit` : `DATA`, 1 Byte - account credit rating(0~15)
- `role`: `DATA`, 2 Bytes - account role (0x1: general, 0xf0f0: Master CA, 0xf0f1: CA)
- `refCode`: `DATA`, 4 Bytes - issuer's reference code.
- `writeTime` : `QUANTITY` - the unix timestamp for the citizen requested time.
- `hash` : `DATA` , 32 Bytes - citizen hash.
- `v`: `QUANTITY` - ECDSA recovery id
- `r`: `DATA`, 32 Bytes - ECDSA signature r
- `s`: `DATA`, 32 Bytes - ECDSA signature s

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_getCitizensByBlockNumber","params":["latest"],"id":100}' http://127.0.0.1:8545}

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": [
         {
            "blockHash": "0xd3bb1c3e121f790502a22fb64f0d806efae11f7d1de3a5e227380787637f2baa",
            "blockNumber": "0x0",
            "citizenIndex": "0x0",
            "from": "0x00000000000000000901",
            "to": "0x00000000000000000000",
            "nonce": "0x0",
            "symId": "0x00000000000000000101",
            "aKeyPubH": "0x18c4fdb750cf3cf60e58fdac3a67419cd5e05836",
            "vFlag": "0x0",
            "country": "0x0",
            "status": "0x1",
            "credit": "0x0",
            "role": "0x1",
            "refCode": "0x0",
            "writeTime": "0x0",
            "hash": "0x32c636492df42933bf55082c38efdab4a9b68de23843043ca617b6772ff4443e",
            "v": "0x0",
            "r": "0x0",
            "s": "0x0"
        },
        {
            "blockHash": "0xd3bb1c3e121f790502a22fb64f0d806efae11f7d1de3a5e227380787637f2baa",
            "blockNumber": "0x0",
            "citizenIndex": "0x1",
            "from": "0x00000000000000000901",
            "to": "0x00000000000000000000",
            "nonce": "0x0",
            "symId": "0x00000000000000000201",
            "aKeyPubH": "0x03c223f461d55d0f098261a1a51b01f396807d2d",
            "vFlag": "0x0",
            "country": "0x0",
            "status": "0x1",
            "credit": "0x0",
            "role": "0x1",
            "refCode": "0x0",
            "writeTime": "0x0",
            "hash": "0xfa24f8a6fb071f26c860eeb8330df97dabf33dbc7d19db0b4a9a8596f4d7bbff",
            "v": "0x0",
            "r": "0x0",
            "s": "0x0"
        }
    ]
}
```

---

#### citizen_getCitizenCount

Returns the number of citizens sent from an address.

##### Parameters

1. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

##### Returns

`QUANTITY` - integer of the number of citizens in current state

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"citizen_getCitizenCount","params":["latest"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

---

#### citizen_pendingCitizens

Returns citizen objects in the pool which the entity is currently on.

##### Parameters

none

##### Returns

`Array of Object` , N Bytes - For existing citizen information or `null` when it was failed or none.

- `blockHash`: `DATA`, 32 Bytes - hash of block.
- `blockNumber`: `QUANTITY` - number of block.
- `citizenIndex`: `QUANTITY` - integer of the citizen's index position in the block. `null` when its pending.
- `from`: `DATA`, 10 Bytes - the address the CA who creates this SymID.
- `to`: `DATA`, 10 Bytes - the address is directed to.
- `nonce`: `QUANTITY` - integer of a nonce.
- `symId` : `DATA`, 10 Bytes - created SymID.
- `aKeyPubH` : `DATA`, 20 Bytes - lower bytes of hashed public key.
- `vFlag`: `DATA`, 1 Byte - each bit means verification strength of this SymID.
- `country`: `DATA`, 1 Byte - country code.
- `status` : `DATA`, 1 Byte - account status (0x1: Active, 0x2: Locked, 0x3: Marked, 0x4: Revoked).
- `credit` : `DATA`, 1 Byte - account credit rating(0~15)
- `role`: `DATA`, 2 Bytes - account role (0x1: general, 0xf0f0: Master CA, 0xf0f1: CA)
- `refCode`: `DATA`, 4 Bytes - issuer's reference code.
- `writeTime` : `QUANTITY` - the unix timestamp for the citizen requested time.
- `hash` : `DATA` , 32 Bytes - citizen hash.
- `v`: `QUANTITY` - ECDSA recovery id
- `r`: `DATA`, 32 Bytes - ECDSA signature r
- `s`: `DATA`, 32 Bytes - ECDSA signature s

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_pendingCitizens","params":[],"id":100}' http://127.0.0.1:3001

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": [
        {
            "blockHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
            "blockNumber": null,
            "citizenIndex": "0x0",
            "from": "0x00000000000000000901",
            "to": "0x00000000000000000901",
            "nonce": "0x9",
            "symId": "0x00000000000000002301",
            "aKeyPubH": "0x0000000000000000000000000000000000000000",
            "vFlag": "0x0",
            "country": "0x0",
            "status": "0x1",
            "credit": "0x0",
            "role": "0x1",
            "refCode": "0x0",
            "writeTime": "0x5d9bfc8c",
            "hash": "0x5e2db90376a99f99f0bb370236ddb93992d1987bbecc2f977e4ef5d628ef41ca",
            "v": "0x0",
            "r": "0xbfb0e1520c08601c849d49ab9b2b2ee0551b497832eb3e17392f61eb33664806",
            "s": "0x1c688b8b36dd90d4a86c29e8278d01b51a88d069f484b651659aad2dbb3b675"
        },
        {
            "blockHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
            "blockNumber": null,
            "citizenIndex": "0x0",
            "from": "0x00000000000000000901",
            "to": "0x00000000000000000901",
            "nonce": "0xa",
            "symId": "0x00000000000000002201",
            "aKeyPubH": "0x0000000000000000000000000000000000000000",
            "vFlag": "0x0",
            "country": "0x0",
            "status": "0x1",
            "credit": "0x0",
            "role": "0x1",
            "refCode": "0x0",
            "writeTime": "0x5d9bfc90",
            "hash": "0x2c60daf1a88c5c286234f54be7b39e4ffff3d54cd1622d8b4f6eea8883c8eba5",
            "v": "0x0",
            "r": "0x43faa787967a5b09e9297a6fb351af35b4ba20918a3b5ddba1df299835bd982e",
            "s": "0x4af3b2829a2e7a9fae6c52b2a833817d329c7e3178fb932b522c9e01e183afe2"
        },
        {
            "blockHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
            "blockNumber": null,
            "citizenIndex": "0x0",
            "from": "0x00000000000000000901",
            "to": "0x00000000000000000901",
            "nonce": "0xb",
            "symId": "0x00000000000000007701",
            "aKeyPubH": "0x0000000000000000000000000000000000000000",
            "vFlag": "0x0",
            "country": "0x0",
            "status": "0x1",
            "credit": "0x0",
            "role": "0x1",
            "refCode": "0x0",
            "writeTime": "0x5d9bfc95",
            "hash": "0x82816e1fab43a2d20004c0f471f095ef74fa45df42641f9f13b177f27b20d114",
            "v": "0x0",
            "r": "0x946ac3bbe6baf890aa95a7b307c3444839d755d6447999865ea5dab557b34a96",
            "s": "0x48445d73d04eaddd791b73d17c462bd79ccd86502d994a434b9ed59046ca8d91"
        }
    ]
}
```

---

#### citizen_blockNumber

Returns the number of the most recent citizen block.

##### Parameters

none

##### Returns

`QUANTITY` - integer of the current citizen block number the client is on.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_blockNumber","params":[],"id":100}' http://127.0.0.1:8545

//Result

{"jsonrpc":"2.0","id":100,"result":"0x97" //151}
```

---

#### citizen_getBlockByHash

Returns information about a citizen block, specified by the hash.

##### Parameters

1. `DATA`, 32 Bytes - Hash of a citizen block.
2. `Boolean` - If `true` it returns the full citizen objects, if `false` only the hashes of the citizens.

##### Returns

`Object` - A citizen block object, or `null` when no citizen block was found:

- `citizens`: `Array` - citizens in this block, or 32 Bytes citizen hashes depending on the last given parameter.
- `citizensRoot`: `DATA`, 32 Bytes - the root of the citizen trie of the block.
- `hash`: `DATA`, 32 Bytes - hash of the block. `null` when its pending block.
- `number`: `QUANTITY` - the block number. `null` when its pending block.
- `parentHash`: `DATA`, 32 Bytes - hash of the parent block.
- `relatedMbNum`: `QUANTITY` - main block number when this citizen block was created. `null` when its pending block.
- `stateRoot`: `DATA`, 32 Bytes - the root of the final state trie of the block.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_getBlockByHash","params":["0x486dda2c8470ca2a885cd4cef5a96a4a561cb0f78fa1faad23a60be7d9797acd", true],"id":100}’ http://127.0.0.1:8545

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": {
        "citizens": [
            {
                "blockHash": "0x486dda2c8470ca2a885cd4cef5a96a4a561cb0f78fa1faad23a60be7d9797acd",
                "blockNumber": "0x1",
                "citizenIndex": "0x0",
                "from": "0x00000000000000000901",
                "to": "0x00000000000000000901",
                "nonce": "0x0",
                "symId": "0x00000000000000002001",
                "aKeyPubH": "0x0000000000000000000000000000000000000000",
                "vFlag": "0x0",
                "country": "0x0",
                "status": "0x1",
                "credit": "0x0",
                "role": "0x1",
                "refCode": "0x0",
                "writeTime": "0x5d9bf803",
                "hash": "0x289a9abcf00a5959ee7fe11bc157cd3a5882b7ae2f86f4ce338bbe00d07c98a1",
                "v": "0x0",
                "r": "0xe2ad882dfce94e0b04b8239d8ccdd61c43fe309daa980d6226380fc7f2787fd1",
                "s": "0x762c3710daff628557aa91e12e36618c9071a99a7c7cd7e9b90e9eb7cd548276"
            }
        ],
        "citizensRoot": "0xf42472230967d1d14e41da263334663514e504b0df967672c8cd4026d1e09486",
        "hash": "0x486dda2c8470ca2a885cd4cef5a96a4a561cb0f78fa1faad23a60be7d9797acd",
        "number": "0x1",
        "parentHash": "0xd3bb1c3e121f790502a22fb64f0d806efae11f7d1de3a5e227380787637f2baa",
        "relatedMbNum": "0x61c",
        "size": "0xf6",
        "stateRoot": "0xf87e330922c1379fc88872b0dc3c8471203adf1362e1f71eec32b5bacec43010"
    }
}
```

---

#### citizen_getBlockByNumber

Returns information about a citizen block, specified by the citizen block number.

##### Parameters

1. `QUANTITY|TAG` - Integer of a block number, or the string `"earliest"`,`"pending"` or `"latest"`
2. `Boolean` - If `true` it returns the full citizen objects, if `false` only the hashes of the citizens.

##### Returns

See [citizen_getBlockByHash](#citizen_getBlockByHash)

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_getBlockByNumber","params":["0x1", true],"id":100}’ http://127.0.0.1:8545

//Result
see citizen_getCitizenBlockByHash
```

---

#### citizen_getBlockCitizenCountByHash

Returns the number of citizens in a block from a block matched with the given block hash.

##### Parameters

1. `DATA`, 32 Bytes - hash of a block.

```js
params: ["0x2a80f9f09749fd6e8af257586da0469091839c4365cd20a0e5bbd086d48e3adc"];
```

##### Returns

`QUANTITY` - integer of the number of citizens in this block.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"citizen_getBlockCitizenCountByHash","params":["0x2a80f9f09749fd6e8af257586da0469091839c4365cd20a0e5bbd086d48e3adc"],"id":1}'

//

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xa" // 10
}
```

---

#### citizen_getBlockCitizenCountByNumber

Returns the number of citizens in a block from a block matched with the given block number.

##### Parameters

1. `QUANTITY|TAG` - integer of a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter).

```js
params: [
  "0xa", // 10
];
```

##### Returns

`QUANTITY` - integer of the number of citizens in this block.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"citizen_getBlockCitizenCountByNumber","params":["0xe8"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xa" // 10
}
```

---

#### oracle_sendOracle

The message call in order to create new oracle.

##### Parameters

1. `Object` - the oracle.

- `from` : `DATA` , 10 Bytes - the address of the CA server that creates SymID.
- `nonce` : `QUANTITY` - (optional) integer of a nonce.
- `data` : `DATA` , N Bytes - JSON encoded Oracle parameters, see the [Oracle Parameters](#oracle-parameters)

```json
params:[{
    "from": "0x00000000000000000901",
    "nonce": "0x1",
    "data": "0x7b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d"
}]
```

##### Returns

`DATA`, 32 Bytes - hash of a oracle.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"oracle_sendOracle","params":[{see above}],"id":100}' http://127.0.0.1:8545

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": "0x598aaa06e0d2b643fee20fed8e95435cc692e2a9d84c4355a4bd82675300db74"
}
```

---

#### oracle_sendRawOracle

The message call in order to create new oracle.

##### Parameters

1. `Object` - signed oracle, RLP encoded value of signed oracle.

##### Returns

`DATA`, 32 Bytes - hash of a oracle.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"oracle_sendRawOracle","params": "0x20ba02f3659983a51a761ccf7e4d2e4a62610a3e20ba02f3659983a51a761ccf7e4d2e4a62610a3e","id":100}' http://127.0.0.1:8545

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": "0x598aaa06e0d2b643fee20fed8e95435cc692e2a9d84c4355a4bd82675300db74"
}

```

---

#### oracle_sendOracleJSON

The message call in order to create new oracle with JSON formatted oracle parameters. This method calls oracle_sendOracle after making "data" of sendOracle with second parameter.

Returns the hash of oracle message sent from the address.

##### Parameters

1. `DATA`, 10 Bytes - address.
2. `STRING` - JSON formatted oracle parameters, see the [Oracle Parameters](#oracle-parameters)

##### Returns

`DATA`, 32 Bytes - hash of a oracle.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"oracle_sendOracleJSON","params":["0x0002A000000000010002","{\"price\":0.15,\"numNodeOra\":3}"],"id":1}'

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0x3d779f7abd08afe48cdbc0d930a95927551b963b33b210eef16c900560836bb9"
}
```

---

#### oracle_getOracleByHash

Returns an RLP encoded value of a signed oracle specified by the hash.

##### Parameters

1. `DATA`, 32 Bytes - hash of a oracle.

##### Returns

`DATA` - RLP encoded value of a signed oracle.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"oracle_getOracleByHash","params":["0x16ba4078270c64d8f1628a7089e423fd5875aa0d72712b97327c1d5b376f0148"],"id":100}' http://127.0.0.1:8545

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": {
        "blockHash": "0xa60c416b18027139ad81c967287fd7d556cfc0e832b7445c193d9a2065ef2fa4",
        "blockNumber": "0x93",
        "oracleIndex": "0x1",
        "from": "0x00000000000000001001",
        "to": "0x00000000000000000000",
        "nonce": "0x1",
        "data": "0x7b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d",
        "extra": "0x",
        "hash": "0x16ba4078270c64d8f1628a7089e423fd5875aa0d72712b97327c1d5b376f0148",
        "v": "0x0",
        "r": "0x7d0639920255815451311469cab18328b7e0d5daaecf300eb1ecb4924d5d72a8",
        "s": "0x4652d078a28439364adfc3891453b8a6c618e3db63182403a44cbc7aa062dfbf"
    }
}
```

---

#### oracle_getRawOracleByHash

Returns an RLP encoded value of a signed oracle specified by the hash.

##### Parameters

1. `DATA`, 32 Bytes - hash of a oracle.

##### Returns

`DATA` - RLP encoded value of a signed oracle.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"oracle_getRawOracleByHash","params":["0x7496b0869fce231bbdc2f68f48dfe3929bdfd1e52c9c87d41716cd83f18cc2d1"],"id":100}' http://127.0.0.1:8545

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": "0xf8828a000000000000000009018a00000000000000001101808a0000000000000000011194484e0b5d12913cec0b4e942828f6168e9f464a098080010f0180808080a03fae1c96dc82545c966e3601141d30edf5ef8abfebf368d7c434de9c9f993253a05bddfcba41de55066e62b96d0248b3620aa791e2c8b9597549d62ec08a8bc90b"
}

```

---

#### oracle_getOracleCount

Returns the number of oracles sent from an address.

##### Parameters

1. `DATA`, 10 Bytes - address.
2. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

##### Returns

`QUANTITY` - integer of the number of oracles in current state

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"oracle_getOracleCount","params":["0x00000000000000001001","latest"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}

```

---

#### oracle_pendingOracles

Returns oracle objects in the pool which the entity is currently on.

##### Parameters

none

##### Returns

`Array of Object` , N Bytes - For existing oracle information or `null` when it was failed or none.

- `blockHash`: `DATA`, 32 Bytes - hash of block.
- `blockNumber`: `QUANTITY` - number of block.
- `oracleIndex`: `QUANTITY` - integer of the oracles index position in the block. `null` when its pending.
- `from`: `DATA`, 10 Bytes - the address the CA who creates this SymID.
- `to`: `DATA`, 10 Bytes - the address is directed to.
- `nonce`: `QUANTITY` - integer of a nonce.
- `data` : `DATA`, ? Bytes - //////
- `extra`: `DATA`, ? Bytes - //
- `hash` : `DATA` , 32 Bytes - citizen hash.
- `v`: `QUANTITY` - ECDSA recovery id
- `r`: `DATA`, 32 Bytes - ECDSA signature r
- `s`: `DATA`, 32 Bytes - ECDSA signature s

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"oracle_pendingOracles","params":[],"id":100}' http://127.0.0.1:3001

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": [
        {
            "blockHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
            "blockNumber": null,
            "oracleIndex": "0x0",
            "from": "0x00000000000000001001",
            "to": "0x00000000000000000000",
            "nonce": "0x0",
            "data": "0x7b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d",
            "extra": "0x",
            "hash": "0x598aaa06e0d2b643fee20fed8e95435cc692e2a9d84c4355a4bd82675300db74",
            "v": "0x1",
            "r": "0xd29f4a17aa3fd6787a084d3679bc929e9d13a4ee1d873c615d53c5ea2faa4a41",
            "s": "0x23f9f92d389ab932c8eae2092577a5267061b33adcd615b7be604269f468f6fe"
        },
        {
            "blockHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
            "blockNumber": null,
            "oracleIndex": "0x0",
            "from": "0x00000000000000001001",
            "to": "0x00000000000000000000",
            "nonce": "0x1",
            "data": "0x7b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d",
            "extra": "0x",
            "hash": "0x16ba4078270c64d8f1628a7089e423fd5875aa0d72712b97327c1d5b376f0148",
            "v": "0x0",
            "r": "0x7d0639920255815451311469cab18328b7e0d5daaecf300eb1ecb4924d5d72a8",
            "s": "0x4652d078a28439364adfc3891453b8a6c618e3db63182403a44cbc7aa062dfbf"
        }
    ]
}
```

---

#### oracle_blockNumber

Returns the number of the most recent oracle block.

##### Parameters

none

##### Returns

`QUANTITY` - integer of the current oracle block number the client is on.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"oracle_blockNumber","params":[],"id":100}' http://127.0.0.1:8545

//Result

{"jsonrpc":"2.0","id":100,"result":"0x97" //151}

```

---

#### oracle_getBlockByHash

Returns information about a oracle block, specified by the hash.

##### Parameters

1. `DATA`, 32 Bytes - Hash of a oracle block.
2. `Boolean` - If `true` it returns the full oracle objects, if `false` only the hashes of the oracles.

##### Returns

`Object` - A citizen block object, or `null` when no oracle block was found:

- `hash`: `DATA`, 32 Bytes - hash of the block. `null` when its pending block.
- `number`: `QUANTITY` - the block number. `null` when its pending block.
- `oracles`: `Array` - oracless in this block, or 32 Bytes oracle hashes depending on the last given parameter.
- `oraclesRoot`: `DATA`, 32 Bytes - the root of the oracle trie of the block.
- `parentHash`: `DATA`, 32 Bytes - hash of the parent block.
- `relatedMbNum`: `QUANTITY` - main block number when this oracle block was created. `null` when its pending block.
- `size`: `QUANTITY` - size of the block.
- `stateRoot`: `DATA`, 32 Bytes - the root of the final state trie of the block.

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"oracle_getBlockByHash","params":["0x9f72f9945a11b37964dc595d5aabc0bf4598a9ee444c2bf7b51ecd09beb711f5", true],"id":100}’ http://127.0.0.1:8545

//Result
{
    "jsonrpc": "2.0",
    "id": 100,
    "result": {
        "hash": "0x9f72f9945a11b37964dc595d5aabc0bf4598a9ee444c2bf7b51ecd09beb711f5",
        "number": "0x0",
        "oracles": [
            {
                "blockHash": "0x9f72f9945a11b37964dc595d5aabc0bf4598a9ee444c2bf7b51ecd09beb711f5",
                "blockNumber": "0x0",
                "oracleIndex": "0x0",
                "from": "0x00000000000000001001",
                "to": "0x00000000000000001001",
                "nonce": "0x0",
                "data": "0x7b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a313030302c22636f73744e6f64654341223a313030302c22636f73744e6f64654f7261223a313030302c226e756d4e6f646541223a392c226e756d4e6f646542223a31362c226e756d4e6f64654341223a322c226e756d4e6f64654f7261223a312c227175616c44617070476173223a313030303030303030303030303030303030303030302c227175616c4e6f646541223a313030303030303030303030303030303030303030303030302c227175616c4e6f646542223a3130303030303030303030303030303030303030303030302c227175616c4e6f64654341223a3130303030303030303030303030303030303030303030302c227175616c4f7261636c697a6572223a313030303030303030303030303030303030303030303030302c227175616c526577617264476173223a313030303030303030303030303030303030302c226e756d526577617264476173223a31302c22746f74616c537570706c79223a313030303030303030302c226465677265654f6646726565646f6d223a33382c226d696e5478436f6e747269627574696f6e223a332c226d6178476173446973636f756e7452617465223a302e372c22626173655072696365223a317d",
                "extra": "0x",
                "hash": "0x25cbcb2869131eff81c5249eca58ce4f9aeb8fb9ba6dd070c6fd4c3d980a72c7",
                "v": "0x0",
                "r": "0x0",
                "s": "0x0"
            }
        ],
        "oraclesRoot": "0x0ed15cb7adaabe71ea58166c61dc4a0bd7c9a06637448c3dbdc27fee8eef23f5",
        "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "relatedMbNum": "0x0",
        "size": "0x27c",
        "stateRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421"
    }
}
```

---

#### oracle_getBlockByNumber

Returns information about a oracle block, specified by the oracle block number.

##### Parameters

1. `QUANTITY|TAG` - Integer of a block number, or the string `"earliest"`,`"pending"` or `"latest"`
2. `Boolean` - If `true` it returns the full oracle objects, if `false` only the hashes of the oracles.

##### Returns

See [oracle_getBlockByHash](#oracle_getBlockByHash)

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"oracle_getBlockByNumber","params":["0x0", true],"id":100}’ http://127.0.0.1:8545

//Result
see oracle_getBlockByHash

```



---

#### sct_getContract

Return information about created contract by a transaction of SCT type.

##### Parameters

1. `DATA`, 10 Bytes - SCT contract address.

##### Returns

`Object` - information about the contract.

- `name`: `DATA` - SCT name.
- `symbol`: `DATA`, 3 Bytes - SCT symbol.
- `total`: `DATA` - total Supply.
- `type`: `DATA` - SCT type.
- `creator`: `DATA`, 10 Bytes - contract creator's address.
- `owner`: `DATA`, 10 Bytes - contract owner's address.

It also returns if it is SCT40:

- `cpoint`: `QUANTITY` - amount of coupon point.

##### Example

```js
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"stm_getContract ","params": "0x0ced1024eed02b234df2"],"id":1}' http://127.0.0.1:8545

//Result
{
    "jsonrpc":"2.0",
    "id":1,
    "result":{
        "cpoint": 10000000000000,
        "creator": "0x00000000000000000001",
        "name": "Coupon",
        "owner": "0x00000000000000000001",
        "state": "active",
        "symbol": "CCC",
        "type": "SCT40"
    }
}
```

---

#### sct_getContractItem

Return information about coupon item in created contract by a transaction of SCT type.

##### Parameters

1. `DATA`, 10 Bytes - SCT contract address.
2. `QUANTITY` - integer of the index position.

##### Returns

`Object` -information about the contract item in that position.

- `state`: `QUANTITY` - Item state(0:active 1: lock)
- `type`: `DATA` - Item type.
- `name`: `DATA` - Item name.
- `value|usepoint`: `QUANTITY` - value of item if SCT40, use amount of point.
- `property`: `DATA` - item property(unique)
- `category`: `DATA`, 10 Bytes - Item category
- `description`: `DATA`, 10 Bytes - Item description

##### Example

```js
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"sym_getContractItem","params":[" 0x0ced1024eed02b234df2”,”1”],"id":1}' http://127.0.0.1:8545

//Result
{
    "jsonrpc":"2.0",
    "id":1,
    "result": {
    	"couponNo": "2",
    	"etc": "",
      	"groupNo": "1",
      	"name": "coupon2",
      	"state": 0,
      	"type": "123",
      	"usepoint": 500
	}
}
```

---

#### sct_getContractAccount

Retrun balance or index of item depends on the contract type.

##### Parameters

1. `DATA`, 10 Bytes - SCT contract address.
2. `DATA`, 10 Bytes - SymID.

##### Returns

`QUANTITY` - either balance or index of item(It depends on the SCT type)

##### Example

```js
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"src_getContractAccount","params":[”0x0ced1024eed02b234df2
”, ”0x00000000000000000001"],"id":1}' http://127.0.0.1:8545

//Result
{
    "jsonrpc":"2.0",
    "id":1,
    "result":{
        "balance": 10000000000000
    }
}
```

---

#### sct_getAllowance

Retrun balance or index of item depends on the contract type.

##### Parameters

1. `DATA`, 10 Bytes - SCT contract address.
2. `DATA`, 10 Bytes - owner SymID.
3. `DATA`, 10 Bytes - spender SymID.

##### Returns

`QUANTITY` - either balance or index of item(It depends on the SCT type)

##### Example

```js
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"src_getAllowance","params":[”0x0ced1024eed02b234df2
”, ”0x00000000000000000001”, “0x00000000000000000002”],"id":1}' http://127.0.0.1:8545

//Result
{
    "jsonrpc":"2.0",
    "id":1,
    "result":{
        "balance": 10000000000000
    }
}
```

---

#### sct_getContractItemsByCategory

Returns Item list which is created by SCT30 or 40.

##### Parameters

1. `DATA`, 10 Bytes - SCT contract address.
2. `QUANTITY` - category or group number.

##### Returns

`Array of Object` , N Bytes - Array of Item list

- `state`: `QUANTITY` - state of item(0:active 1: lock)
- `type`: `DATA` - item type.
- `name`: `DATA` - item name(alias)
- `value|usepoint`: `QUANTITY` - value of item if SCT40, use amount of point.
- `Index`: `DATA` - unique key of internal item.
- `couponNo`: `DATA` - unique key of external item.
- `groupNo`: `DATA` - category of item.
- `etc`: `DATA` - extra description of item.

##### Example

```js
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"src_getContractItemsByCategory","params":[“0x0ced1024eed02b234df2”,”1"],"id":1}' http://127.0.0.1:8545

//Result
{
    "jsonrpc":"2.0",
    "id":1,
    "result":[
        {
            "couponNo": "1",
            "etc": "",
            "groupNo": "1",
            "index": 0,
            "name": "coupon1",
            "state": 0,
            "type": "123",
            "usepoint": 500
        }, {
            "couponNo": "2",
            "etc": "",
            "groupNo": "1",
            "index": 1,
            "name": "coupon2",
            "state": 0,
            "type": "123",
            "usepoint": 500
        }
    ]
}
```

### sct_getVoteContract

`sct_getVoteContract` returns given SCT50 Vote Contract's Information.

##### Parameters

1. `VoteContractAddress`: `DATA` - SCT50 Vote Contract Address
2. `BlockNumber`: `QUANTITY` - Last block number

##### Returns

- `party`: `STRING` - Party Name
- `owner`: `DATA` - Owner SymId
- `creator` - `DATA` - Creator SymId
- `token`: `DATA` - SCT20 Token Address
- `pollCreators`: `ARRAY` - registered poll creators list
- `polls`: `ARRAY` - created polls list

##### Example

```json
// Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"sct_getVoteContract","params":["0xa0c7316397c51441448a", "0x2234"],"id":100}’ http://127.0.0.1:8545

// Result: check whether vote contract information is valid
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "creator": "0x00020000000000020002",
        "owner": "0x00020000000000020002",
        "party": "PartyA",
        "pollCreators": [],
        "polls": [],
        "token": "0x7ceb501abaeaef5ff714"
    }
}


```

### sct_getPollContract

`sct_getPollContract` returns givens SCT51 Poll Contract's Information.

##### Parameters

1. `PollContractAddress`: `DATA` - SCT51 Poll Contract Address
2. `BlockNumber`:`QUANTITY` - Last block number

##### Returns

- `pollNumber` - `QUANTITY` - Poll number
- `category` - `QUANTITY` - Category for Agenda
- `agenda` - `STRING` - Adenda name
- `description` - `STRING` - Agenda description
- `start` - `QUANTITY` - Start UNIX time
- `end` - `QUANTITY` - End UNIX time
- `state` - `STRING` - Poll State
- `votingPaper` - `ARRAY` - Voting paper lists. `VotingPaper` - {`voter`, `stake`, `votes`, `decision`}
- `voteResult` - `VoteResult` - Vote result. `VoteResult`: {`result`, `resultCode`, `agrees`, `disagrees`, `totalVoters`, `votingRate`}
- `voteContract` - `ADDRESS` - SCT50 Vote Contract address.
- `secretOption` - `STRING` - secret option
- `stakeOption` - `STRING` - stake option
- `totalVoters` - `QUANTITY` - the number of total voters
- `quorum` - `QUANTITY` - minimum rate of participants for the poll
- `passRate` - `QUANTITY` - minimum rate of agree for the poll
- `owner` - `ADDRESS` - Owner SymId
- `creator` - `ADDRESS` - Creator SymId

##### Example

```json
// Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"sct_getPollContract","params":["0x59b3fa15e5298fecdced", "0x24eb"],"id":100}’ http://127.0.0.1:8545

// Result: check whether poll contract information is valid
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "agenda": "Agenda1",
        "category": "0",
        "creator": "0x00020000000000020002",
        "description": "Agenda1 is about ...",
        "end": 1582347529,
        "owner": "0x00020000000000020002",
        "start": 1581987529,
        "state": "active",
        "voteContract": "0x83c4b863539810ef20f4",
        "voteResult": {
            "agrees": 0,
            "disagrees": 0,
            "result": 0
        },
        "votingPaper": []
    }
}

```

### sct_getVoteResult

`sct_getVoteResult` returns given SCT51 Poll Contract's vote result.

##### Parameters

1. `PollContractAddress`: `DATA` - SCT51 Poll Contract Address
2. `BlockNumber`: `QUANTITY` - Last block number

##### Returns

- `result` - `QUANTITY` - final result code for the given poll
- `code` - `QUANTITY` - extra result code for the given poll
- `agrees` - `QUANTITY` - total agrees for the given poll
- `disagrees` - `QUANTITY` - total disagrees for the given poll
- `nullities` - `QUANTITY` - total nullities for the given poll
- `totalVoters` - `QUANTITY` - total voters for the given poll
- `votingRate` - `QUANTITY` - voting rate for the given poll

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"sct_getVoteResult","params":["0x6bddbfcb14f6fa2d6d8a", "0x24eb"],"id":100}’ http://127.0.0.1:8545


//Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "agrees": "0x62",
    "code": "0x0",
    "disagrees": "0x2",
    "nullities": "0x2",
    "result": "0x1",
    "totalvoters": "0x64",
    "votingRate": "0x62"
  }
}
```

### sct_getVotingPaper

`sct_getVotingPaper` returns given user's vote information in poll.

##### Parameters

1. `PollContractAddress`: `DATA` - SCT51 Poll Contract Address
2. `SymId`: `DATA` - SymId to request.
3. `BlockNumber`: `QUANTITY` - Last block number

##### Returns

- `stake`: `QUANTITY` - Staked balance in poll
- `votes`: `QUANTITY` - Used stake for voting
- `decision`: `STRING` - decision about poll

##### Example

```json
//Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"sct_getVotingPaper","params":["0x6bddbfcb14f6fa2d6d8a","0x00020000000000010002" ,"0x24eb"],"id":100}’ http://127.0.0.1:8545


//Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "decision": "agree",
    "stake": "0x1",
    "voter": "0x00020000000000010002"
  }
}
```

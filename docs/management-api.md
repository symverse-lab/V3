# SymVerse V3 Management API

> **Status:** Baseline import  
> **Date:** 2026-05-16  
> **Document Role:** Management API documentation carried into the SymVerse V3 documentation set

---

Beside the official [RPC APIs](https://github.com/ethereum/wiki/wiki/JSON-RPC) interface go-symverse has support for additional management APIs. Similar to the official APIs, these are also provided using [JSON-RPC](http://www.jsonrpc.org/specification) and follow exactly the same conventions. Gsym comes with a console client which has support for all additional APIs described here.



## Enabling the management APIs

To offer these APIs over the Gsym RPC endpoints, please specify them with the `--${interface}api`
command line argument (where `${interface}` can be `rpc` for the HTTP endpoint, `ws` for the WebSocket
endpoint and `ipc` for the unix socket (Unix) or named pipe (Windows) endpoint).

For example: `gsym --ipcapi admin,sym --rpcapi sym,web3 --rpc`

* Enables the admin and official sym API over the IPC interface
* Enables the official sym and web3 API over the HTTP interface

The HTTP RPC interface must be explicitly enabled using the `--rpc` flag.

Please note, offering an API over the HTTP (`rpc`) or WebSocket (`ws`) interfaces will give everyone
access to the APIs who can access this interface (DApps, browser tabs, etc). Be careful which APIs
you enable. By default Gsym enables all APIs over the IPC (`ipc`) interface and only the  `sym`,
`net` and `web3` APIs over the HTTP and WebSocket interfaces.

To determine which APIs an interface provides, the `modules` JSON-RPC method can be invoked. For
example over an `ipc` interface on unix systems:

```json
echo '{"jsonrpc":"2.0","method":"rpc_modules","params":[],"id":1}' | nc -U $datadir/gsym.ipc
```

will give all enabled modules including the version number:

```json
{  
   "id":1,
   "jsonrpc":"2.0",
   "result":{  
      "admin":"1.0",
      "citizen":"1.0",
      "sym":"1.0",
      "net":"1.0",
      "oracle":"1.0",
      "personal":"1.0",
      "pon":"1.0",
      "txpool":"1.0",
      "warrant":"1.0",
      "web3":"1.0"
   }
}
```

## Consuming the management APIs

These additional APIs follow the same conventions as the official RPC APIs. Web3 can be
[extended](https://github.com/ethereum/web3.js/pull/229) and used to consume these additional APIs. 

The different functions are split into multiple smaller logically grouped APIs. Examples are given
for the [JavaScript console](https://github.com/ethereum/go-ethereum/wiki/JavaScript-Console) but can easily be converted to an RPC request.

**example:** 

- Console: `admin.stopRPC()`
- IPC: `echo '{"jsonrpc":"2.0","method":"admin_stopRPC","params":[],"id":1}' | nc -U $datadir/gsym.ipc`
- HTTP: `curl -X POST --data '{"jsonrpc":"2.0","method":"admin_stopRPC","params":[],"id":74}' localhost:8545`



## List of management APIs

Beside the officially exposed official RPC API namespaces (`citizen`, `sym`, `warrant`,`oracle`,`web3`), Gsym provides the following extra management API namespaces:

* `admin`: Gsym node management
* `personal`: Account management
* `txpool`: Transaction pool inspection

| [admin](#admin)              | [personal](#personal)                        | [pon](#pon)                           | [txpool](#txpool)           | [debug](#debug)                     |
| :--------------------------- | :------------------------------------------- | ------------------------------------- | --------------------------- | ----------------------------------- |
| [addPeer](#admin_addpeer)    | [importRawKey](#personal_importrawkey)       | [currentPrimary](#pon_currentPrimary) | [content](#txpool_content)  | [logSwitch](#debug_logSwitch)       |
| [datadir](#datadir)          | [listAccounts](#personal_listaccounts)       | [start](#pon_start)                   | [inspect](#txpool_inspect)  | [flogLife](#debug_flogLife)         |
| [nodeInfo](#admin_nodeinfo)  | [lockAccount](#personal_lockaccount)         | [stop](#pon_stop)                     | [status](#txpool_status)    | [flogPath](#debug_flogPath)         |
| [peers](#admin_peers)        | [newAccount](#personal_newaccount)           | [working](#pon_working)               |                             | [vflag](#debug_vflag)               |
| [startRPC](#admin_startrpc)  | [newSymAccount](#personal_newsymaccount)     |                                       |                             | [verbosity](#debug_verbosity)       |
| [startWS](#admin_startws)    | [unlockAccount](#personal_unlockaccount)     |                                       |                             | [vmodule](#debug_vmodule)           |
| [stopRPC](#admin_stoprpc)    | [sendTransaction](#personal_sendtransaction) |                                       |                             | [vstatus](#debug_vstatus)           |
| [stopWS](#admin_stopws)      |                                              |                                       |                             |[getTransactionRlp](#debug_getTransactionRlp) |
|  |  |  |  |[decodeTransactionRlp](#debug_decodeTransactionRlp) |
|  |  |  |  |[getCitizenRlp](#debug_getCitizenRlp)               |
|  |  |  |  |[decodeCitizenRlp](#debug_decodeCitizenRlp)         |
|  |  |  |  |[getOracleRlp](#debug_getOracleRlp)                 |
|  |  |  |  |[decodeOracleRlp](#debug_decodeOracleRlp)           |


## Admin

The `admin` API gives you access to several non-standard RPC methods, which will allow you to have
a fine grained control over your Gsym instance, including but not limited to network peer and RPC
endpoint management.

### admin_addPeer

The `addPeer` administrative method requests adding a new remote node to the list of tracked static
nodes. The node will try to maintain connectivity to these nodes at all times, reconnecting every
once in a while if the remote connection goes down.

The method accepts a single argument, the [`enode`](https://github.com/ethereum/wiki/wiki/enode-url-format) URL of the remote peer to start tracking and returns a `BOOL` indicating whether the peer was accepted
for tracking or some error occurred.

| Client  | Method invocation                              |
|:-------:|------------------------------------------------|
| Go      | `admin.AddPeer(url string) (bool, error)`      |
| Console | `admin.addPeer(url)`                           |
| RPC     | `{"method": "admin_addPeer", "params": [url]}` |

#### Example

```javascript
> admin.addPeer("enode://a979fb575495b8d6db44f750317d0f4622bf4c2aa3365d6af7c284339968eef29b69ad0dce72a4d8db5ebb4968de0e3bec910127f134779fbcb0cb6d3331163c@52.16.188.185:30303")
true
```



### admin_datadir

The `datadir` administrative property can be queried for the absolute path the running Gsym node
currently uses to store all its databases.

| Client  | Method invocation                 |
|:-------:|-----------------------------------|
| Go      | `admin.Datadir() (string, error`) |
| Console | `admin.datadir`                   |
| RPC     | `{"method": "admin_datadir"}`     |

#### Example

```javascript
> admin.datadir
"/home/yunee/.symverse"
```

### admin_nodeInfo

The `nodeInfo` administrative property can be queried for all the information known about the running
Gsym node at the networking granularity. These include general information about the node itself as a
participant of the P2P overlay protocol, as well as specialized information added by each of the running application protocols (e.g. `sym`).

| Client  | Method invocation                         |
|:-------:|-------------------------------------------|
| Go      | `admin.NodeInfo() (*p2p.NodeInfo, error`) |
| Console | `admin.nodeInfo`                          |
| RPC     | `{"method": "admin_nodeInfo"}`            |

#### Example

```javascript
> admin.nodeInfo
{
  enode: "enode://ab71c0bb288c1a801bf0ffe4d76e6686ba79e45178e9b080d8a9ee26a69bbe2e9738d26c2f2d2d39a64a1c7e7bc1ef821e27338bc00529e965ec702e197ef5d9@[::]:2007?discport=0",
  id: "ab71c0bb288c1a801bf0ffe4d76e6686ba79e45178e9b080d8a9ee26a69bbe2e9738d26c2f2d2d39a64a1c7e7bc1ef821e27338bc00529e965ec702e197ef5d9",
  ip: "::",
  listenAddr: "[::]:2007",
  name: "Gsym/v0.0.7-Develope-44e71d31/linux-amd64/go1.10.4",
  ports: {
    discovery: 0,
    listener: 2007
  },
  protocols: {
    sym: {
      config: {
        chainId: 8131,
        masterCA: "0x00000000000000000801",
        oraclizer: "0x00000000000000000801"
      },
      genesis: "0xb8082b2f699d9f2bceb98b6f26bcd1891dad1b0df58c37b98d0061f675cc1c62",
      head: "0xeeb4b16557d2a594e9f0db0775b8f5a9c933dccc5d43de965bbdc691f3618ae0",
      network: 8131
    }
  }
}
```

### admin_peers

The `peers` administrative property can be queried for all the information known about the connected
remote nodes at the networking granularity. These include general information about the nodes themselves as participants of the P2P overlay protocol, as well as specialized information added by each of the running application protocols (e.g. `sym`).

| Client  | Method invocation                        |
|:-------:|------------------------------------------|
| Go      | `admin.Peers() ([]*p2p.PeerInfo, error`) |
| Console | `admin.peers`                            |
| RPC     | `{"method": "admin_peers"}`              |

#### Example

```javascript
> admin.peers
[{
    caps: ["sym/31"],
    id: "12af89114e7e6a98e6436afc91cc16990a3a8e4b07bf41fc549c4aa59da3ef50035a8bf2dc4f03da34b3cb309d963b8d0a822ba68f734e778724d7d29e8a8858",
    name: "Gsym/v0.0.7-Develope-44e71d31/linux-amd64/go1.10.4",
    network: {
      inbound: false,
      localAddress: "127.0.0.1:49434",
      remoteAddress: "127.0.0.1:2001",
      static: true,
      trusted: false
    },
    protocols: {
      sym: {
        blocknum: 6028,
        head: "0x17db02e5c80e239803516e0ecf88b7f019ef1ccce1d73a8cac16114c1dfbce98",
        version: 31
      }
    }
}, /* ... */ {
    caps: ["sym/31"],
    id: "f8a38bcf4123f7467be97628aedf817f92b40776a3beb0cf99a3831e12f429a045e8ec0dfb8a1d2e2783afc3e866a87f5a21c6cab52f30c0e136c8e3f4bc7128",
    name: "Gsym/v0.0.7-Develope-44e71d31/linux-amd64/go1.10.4",
    network: {
      inbound: true,
      localAddress: "127.0.0.1:2007",
      remoteAddress: "127.0.0.1:49548",
      static: false,
      trusted: false
    },
    protocols: {
      sym: {
        blocknum: 6030,
        head: "0x1fd492c192a954f6e874961e16bbd01de2b27a8690f9b0b1feaf3e4c17af5372",
        version: 31
      }
    }
}]
```

### admin_startRPC

The `startRPC` administrative method starts an HTTP based [JSON RPC](http://www.jsonrpc.org/specification)
API webserver to handle client requests. All the parameters are optional:

* `host`: network interface to open the listener socket on (defaults to `"localhost"`)
* `port`: network port to open the listener socket on (defaults to `8545`)
* `cors`: [cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) header to use (defaults to `""`)
* `apis`: API modules to offer over this interface (defaults to `"sym,net,web3"`)

The method returns a boolean flag specifying whether the HTTP RPC listener was opened or not. Please note, only one HTTP endpoint is allowed to be active at any time.

| Client  | Method invocation                                                                             |
|:-------:|-----------------------------------------------------------------------------------------------|
| Go      | `admin.StartRPC(host *string, port *rpc.HexNumber, cors *string, apis *string) (bool, error)` |
| Console | `admin.startRPC(host, port, cors, apis)`                                                      |
| RPC     | `{"method": "admin_startRPC", "params": [host, port, cors, apis]}`                            |

#### Example

```javascript
> admin.startRPC("127.0.0.1", 8545)
true
```

### admin_startWS

The `startWS` administrative method starts an WebSocket based [JSON RPC](http://www.jsonrpc.org/specification)
API webserver to handle client requests. All the parameters are optional:

* `host`: network interface to open the listener socket on (defaults to `"localhost"`)
* `port`: network port to open the listener socket on (defaults to `8546`)
* `cors`: [cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) header to use (defaults to `""`)
* `apis`: API modules to offer over this interface (defaults to `"sym,net,web3"`)

The method returns a boolean flag specifying whether the WebSocket RPC listener was opened or not. Please note, only one WebSocket endpoint is allowed to be active at any time.

| Client  | Method invocation                                                                             |
|:-------:|-----------------------------------------------------------------------------------------------|
| Go      | `admin.StartWS(host *string, port *rpc.HexNumber, cors *string, apis *string) (bool, error)` |
| Console | `admin.startWS(host, port, cors, apis)`                                                      |
| RPC     | `{"method": "admin_startWS", "params": [host, port, cors, apis]}`                            |

#### Example

```javascript
> admin.startWS("127.0.0.1", 8546)
true
```

### admin_stopRPC

The `stopRPC` administrative method closes the currently open HTTP RPC endpoint. As the node can only have a single HTTP endpoint running, this method takes no parameters, returning a boolean whether the endpoint was closed or not.

| Client  | Method invocation                           |
| :-----: | ------------------------------------------- |
|   Go    | `admin.StopRPC() (bool, error`)             |
| Console | `admin.stopRPC()`                           |
|   RPC   | `{"method": "admin_stopRPC", "params": []}` |

#### Example

```javascript
> admin.stopRPC()
true
```

### admin_stopWS

The `stopWS` administrative method closes the currently open WebSocket RPC endpoint. As the node can only have a single WebSocket endpoint running, this method takes no parameters, returning a boolean whether the endpoint was closed or not.

| Client  | Method invocation                          |
| :-----: | ------------------------------------------ |
|   Go    | `admin.StopWS() (bool, error`)             |
| Console | `admin.stopWS()`                           |
|   RPC   | `{"method": "admin_stopWS", "params": []}` |

#### Example

```javascript
> admin.stopWS()
true
```


## Personal

The personal API manages private keys in the key store.

### personal_importRawKey

Imports the given unencrypted private key (hex string) into the key store,
encrypting it with the passphrase.

Returns the address of the new account.

| Client    | Method invocation                                                 |
| :-------: | ----------------------------------------------------------------- |
| Console   | `personal.importRawKey(keydata, passphrase)`                      |
| RPC       | `{"method": "personal_importRawKey", "params": [string, string]}` |

### personal_listAccounts

Returns all the Symverse account addresses of all keys
in the key store.

| Client    | Method invocation                                   |
| :-------: | --------------------------------------------------- |
| Console   | `personal.listAccounts`                             |
| RPC       | `{"method": "personal_listAccounts", "params": []}` |

#### Example

``` javascript
> personal.listAccounts
["0x00000000000000000901","0x00010000000000010009"]
```

### personal_lockAccount

Removes the private key with given address from memory.
The account can no longer be used to send transactions.

| Client    | Method invocation                                        |
| :-------: | -------------------------------------------------------- |
| Console   | `personal.lockAccount(address)`                          |
| RPC       | `{"method": "personal_lockAccount", "params": [string]}` |

### personal_newAccount

Generates new private keys and stores them in the key store directory. 
The key ﬁle is encrypted with a given passphrase. 
Returns the SymID of the new account.

At the gsym console, `newAccount` will prompt for SymID and a passphrase when
it is not provided in the argument.

| Client  | Method invocation                                            |
| :-----: | ------------------------------------------------------------ |
| Console | `personal.newAccount()`                                      |
|   RPC   | `{"method": "personal_newAccount", "params": [address,string]}` |

#### Example

```javascript
>  personal.newAccount()
Sym ID: 0x00010000000000010003
Passphrase:
Repeat passphrase:
"0x00010000000000010003"
```

The passphrase can also be supplied as a string.

```javascript
> personal.newAccount("0x00010000000000010003","yunee")
"0x00010000000000010003"
```

### personal_unlockAccount

Decrypts the key with the given address from the key store.

Both passphrase and unlock duration are optional when using the JavaScript console.
If the passphrase is not supplied as an argument, the console will prompt for
the passphrase interactively.

The unencrypted key will be held in memory until the unlock duration expires.
If the unlock duration defaults to 300 seconds. An explicit duration
of zero seconds unlocks the key until gsym exits.

The account can be used with `sym_sign` and `sym_sendTransaction` while it is unlocked.

| Client    | Method invocation                                                          |
| :-------: | -------------------------------------------------------------------------- |
| Console   | `personal.unlockAccount(address, passphrase, duration)`                    |
| RPC       | `{"method": "personal_unlockAccount", "params": [string, string, number]}` |

#### Examples

``` javascript
> personal.unlockAccount("0x00000000000000000901")
Unlock account 0x00000000000000000901
Passphrase:
true
```

Supplying the passphrase and unlock duration as arguments:

``` javascript
> personal.unlockAccount("0x00000000000000000901","yunee",30)
true
```

If you want to type in the passphrase and stil override the default unlock duration,
pass `null` as the passphrase.

```javascript
> personal.unlockAccount("0x00000000000000000901",null,30)
Unlock account 0x00000000000000000901
Passphrase:
true
```

### personal_sendTransaction

Validate the given passphrase and submit transaction.

The transaction is the same argument as for `sym_sendTransaction` and contains the `from` address. If the passphrase can be used to decrypt the private key belogging to `tx.from` the transaction is verified, signed and send onto the network. The account is not unlocked globally in the node and cannot be used in other RPC calls.

| Client    | Method invocation                                                |
| :-------: | -----------------------------------------------------------------|
| Console   | `personal.sendTransaction(tx, passphrase)`                       |
| RPC       | `{"method": "personal_sendTransaction", "params": [tx, string]}` |

#### Examples

``` javascript
> var tx = {from: "0x00000000000000000901", to: "0x00010000000000010003", value: web3.toHug(920920, "sym")}
undefined
> personal.sendTransaction(tx, "passphrase")
0x8474441674cdd47b35b875fd1a530b800b51a5264b9975fb21129eeb8c18582f
```

## Pon

The `pon` loop management API.

### pon_currentPrimary

If a pon loop is running, returns the current primary.

| Client  | Method invocation                |
| ------- | -------------------------------- |
| Console | pon.currentPrimary()             |
| RPC     | {"method": "pon_currentPrimary"} |

### pon_start

Run pon loop.

The followed all parameters are needed:

- `passphrase`:  pon password
- `addr`: network ip
- `n2nPort`: port for using n2n protocol
- `n2nboot`: boot ID for n2n protocol

```js
params: [{
    "passphrase": "yunee",
    "addr" 		: "172.30.1.11",
    "n2nPort"	: "7009",
    "n2nboot" 	: "0x00000000000000000701@172.30.1.11:7007" 
}]
```

| Client  | Method invocation                              |
| ------- | ---------------------------------------------- |
| Console | pon.start(params)                              |
| RPC     | {"method": "pon_start", "params": [see above]} |

### pon_stop

Stop pon loop. If it is a warrant or candidate for next warrant, it will not end.

| Client  | Method invocation      |
| ------- | ---------------------- |
| Console | pon.stop()             |
| RPC     | {"method": "pon_stop"} |

### pon_working

Returns whether the pon loop is running or not.

| Client  | Method invocation         |
| ------- | ------------------------- |
| Console | pon.working()             |
| RPC     | {"method": "pon_working"} |

## Txpool

The `txpool` API gives you access to several non-standard RPC methods to inspect the contents of the
transaction pool containing all the currently pending transactions as well as the ones queued for
future processing.

### txpool_content

The `content` inspection property can be queried to list the exact details of all the transactions
currently pending for inclusion in the next block(s), as well as the ones that are being scheduled
for future execution only.

The result is an object with two fields `pending` and `queued`. Each of these fields are associative
arrays, in which each entry maps an origin-address to a batch of scheduled transactions. These batches
themselves are maps associating nonces with actual transactions.

Please note, there may be multiple transactions associated with the same account and nonce. This can
happen if the user broadcast mutliple ones with varying gas allowances (or even complerely different
transactions).

| Client  | Method invocation                                            |
| :-----: | ------------------------------------------------------------ |
|   Go    | `txpool.Content() (map[string]map[string]map[string][]*RPCTransaction)` |
| Console | `txpool.content`                                             |
|   RPC   | `{"method": "txpool_content"}`                               |

#### Example

```javascript
> txpool.content
{
  pending: {
     0x00000000000000000401: {
      0: {
        blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        blockNumber: null,
        from: "0x00000000000000000401",
        gas: "0x15f90",
        gasPrice: "0x5d21dba00",
        hash: "0x7db76e4b4e342c940e2de044a61704e2df8c3b3f3f29f3a45094c5c62c90e889",
        input: "0x",
        nonce: "0x0",
        r: "0x6cd17c232184b39a9c7527fcd3b198cfd127ec71730af022f2926267e78ff154",
        s: "0x8b819fe36540a0ae4aafdbdc4d7308d3065c2f74d5790fc343685db26cb8b19",
        to: "0x00000000000000000901",
        transactionIndex: "0x0",
        type: "0x0",
        v: "0x1",
        value: "0xde0b6b3a7640000",
        workNodes: [...]
      },
    0x00000000000000000501:   
     9: {
        blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        blockNumber: null,
        from: "0x00000000000000000501",
        gas: "0x15f90",
        gasPrice: "0x5d21dba00",
        hash: "0x4c661f9a520b3675da93e9c7b8027cb60ccd31be2f50555ed76dd439b7972a5f",
        input: "0x",
        nonce: "0x9",
        r: "0xea69651392676698f4a1758bd531164bdb37a2200df6e60a76e467d9920489ee",
        s: "0x465e4314ef26341fecb2e55cdb24c2a1e8100666cdd9c39abebc24ccf2297590",
        to: "0x00000000000000000901",
        transactionIndex: "0x0",
        type: "0x0",
        v: "0x0",
        value: "0xde0b6b3a7640000",
        workNodes: [...]
      }
    }
  },
   queued: {
    0x00000000000000000201: {
      50005: {
        blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        blockNumber: null,
        from: "0x00000000000000000201",
        gas: "0x15f90",
        gasPrice: "0x5d21dba00",
        hash: "0xa7e10ad03086957049f9583d6d2b3f4e72476bb1362918aa1326601ab1c8880a",
        input: "0x",
        nonce: "0xc355",
        r: "0xc1143ab8428d1c7470a1f9f599a616bb05698ed7aa60b030f1bacb310ea09bf4",
        s: "0x5ffcdbd272b7bc1e0e4b7aaca4f01187f24e8bc7c0b5c100d8cd33115483f1b",
        to: "0x00000000000000000901",
        transactionIndex: "0x0",
        type: "0x0",
        v: "0x0",
        value: "0xde0b6b3a7640000",
        workNodes: [...]
      },{
        blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        blockNumber: null,
        from: "0x00000000000000000201",
        gas: "0x15f90",
        gasPrice: "0x5d21dba00",
        hash: "0x7960f26a2b360e158f9bd39611cd86ddb44d8df67c8015a66fb78b69dd463542",
        input: "0x",
        nonce: "0xc355",
        r: "0x5834553ba95cba6e3869fed958a444c9a22c3fc0409d14b3d42341ce378b30a6",
        s: "0x4a5288cbbc232b0b44ab0836ab4821e7a995dbc77855d41d1b3909fada07cdca",
        to: "0x00000000000000001001",
        transactionIndex: "0x0",
        type: "0x0",
        v: "0x1",
        value: "0xde0b6b3a7640000",
        workNodes: [...]
      }
    }
  }
}
```

### txpool_inspect

The `inspect` inspection property can be queried to list a textual summary of all the transactions
currently pending for inclusion in the next block(s), as well as the ones that are being scheduled
for future execution only. This is a method specifically tailored to developers to quickly see the
transactions in the pool and find any potential issues.

The result is an object with two fields `pending` and `queued`. Each of these fields are associative
arrays, in which each entry maps an origin-address to a batch of scheduled transactions. These batches
themselves are maps associating nonces with transactions summary strings.

Please note, there may be multiple transactions associated with the same account and nonce. This can
happen if the user broadcast mutliple ones with varying gas allowances (or even complerely different
transactions).

| Client  | Method invocation                                            |
| :-----: | ------------------------------------------------------------ |
|   Go    | `txpool.Inspect() (map[string]map[string]map[string][]string)` |
| Console | `txpool.inspect`                                             |
|   RPC   | `{"method": "txpool_inspect"}`                               |

#### Example

```javascript
> txpool.inspect
{
  pending: {
    0x00021000000000150002: {
      31813: ["0x00021000000000040002: 0 hug + 500000 × 20000000000 gas"]
    },
    0x00021000000000190002: {
      563662: ["0x00021000000000020002: 1051546810000000000 hug + 90000 × 20000000000 gas"],
      563663: ["0x00021000000000240002: 1051190740000000000 hug + 90000 × 20000000000 gas"],
      563664: ["0x00021000000000240002: 1050828950000000000 hug + 90000 × 20000000000 gas"],
      563665: ["0x00021000000000280002: 1050544770000000000 hug + 90000 × 20000000000 gas"]
    },
    0x00021000000000210002: {
      3: ["0x00021000000000240002: 30000000000000000000 hug + 85000 × 21000000000 gas"]
    },
    0x00021000000000280002: {
      777: ["0x00021000000000240002: 0 hug + 1000000 × 20000000000 gas"]
    },
    0x00021000000000290002: {
      2: ["0x00021000000000240002: 26000000000000000000 hug + 90000 × 20000000000 gas", "0x00021000000000310002: 26000000000000000000 hug + 90000 × 20000000000 gas"]
    },
    0x00021000000000310002: {
      0: ["0x00021000000000240002: 1000000000000000000 hug + 50000 × 1171602790622 gas"]
    }
  },
  queued: {
    0x00021000000000010002: {
      6: ["0x00021000000000240002: 9000000000000000000 hug + 21000 × 20000000000 gas"]
    },
    0x00021000000000030002: {
      6: ["0x00021000000000240002: 50000000000000000000 hug + 90000 × 50000000000 gas"]
    },
    0x00021000000000050002: {
      3: ["0x00021000000000240002: 140000000000000000 hug + 90000 × 20000000000 gas"]
    },
    0x00021000000000090002: {
      2: ["0x00021000000000010002: 17000000000000000000 hug + 90000 × 50000000000 gas"],
      6: ["0x00021000000000050002: 17990000000000000000 hug + 90000 × 20000000000 gas", "0x00021000000000070002: 16998950000000000000 hug + 90000 × 20000000000 gas"],
      7: ["0x00021000000000240002: 17900000000000000000 hug + 90000 × 20000000000 gas"]
    }
  }
}
```

### txpool_status

The `status` inspection property can be queried for the number of transactions currently pending for
inclusion in the next block(s), as well as the ones that are being scheduled for future execution only.

The result is an object with two fields `pending` and `queued`, each of which is a counter representing
the number of transactions in that particular state.

| Client  | Method invocation                             |
| :-----: | --------------------------------------------- |
|   Go    | `txpool.Status() (map[string]*rpc.HexNumber)` |
| Console | `txpool.status`                               |
|   RPC   | `{"method": "txpool_status"}`                 |

#### Example

```javascript
> txpool.status
{
  pending: 10,
  queued: 7
}
```

## Debug

The `debug` API gives you access to several non-standard RPC methods, which will allow you to inspect, debug and set certain debugging flags during runtime.

### debug_logSwitch

Sets the logging option.

- `0`: turns off logging
- `1`: turns on logging, only streaming
- `2`: turns on logging, only file saving
- `3`: turns on logging, both streaming and file saving

| Client  | Method invocation                                 |
| ------- | ------------------------------------------------- |
| Console | debug.logSwitch(number)                           |
| RPC     | {"method": "debug_logSwitch", "params": [number]} |

### debug_flogLife

Sets the period to hold the saved log files. [default: 3day]

| Client  | Method invocation                                |
| ------- | ------------------------------------------------ |
| Console | debug.flogLife(number)                           |
| RPC     | {"method": "debug_flogLife", "params": [number]} |

### debug_flogPath

Sets the file path to save the log.

| Client  | Method invocation                                |
| ------- | ------------------------------------------------ |
| Console | debug.flogPath(string)                           |
| RPC     | {"method": "debug_flogPath", "params": [stirng]} |

### debug_verbosity

Sets the logging verbosity ceiling. Log messages with level up to and including the given level will be printed.

The verbosity of individual packages and source files can be raised using `debug_vmodule`.

| Client  | Method invocation                                 |
| ------- | ------------------------------------------------- |
| Console | debug.verbosity(level)                            |
| RPC     | {"method": "debug_verbosity", "params": [number]} |

### debug_vflag

Extracting specific logs with the given flags.

| Client  | Method invocation                             |
| ------- | --------------------------------------------- |
| Console | debug.vflag(string)                           |
| RPC     | {"method": "debug_vflag", "params": [stirng]} |

#### Examples

If you want to restrict messages to a particular flag(e.g. "n2ndata") but exclude the other flags, use:

```
> debug.vmodule("n2ndata=6")
```

You can compose these basic patterns. If you want to see all output from "rewardlog" flag as well as output from "n2ndata" at level <= 5, use:

```
debug.vmodule("rewardlog=6,n2ndata=5")
```

### debug_vmodule

Sets the logging verbosity pattern.

| Client  | Method invocation                               |
| ------- | ----------------------------------------------- |
| Console | debug.vmodule(string)                           |
| RPC     | {"method": "debug_vmodule", "params": [stirng]} |

#### Examples

If you want to see messages from a particular Go package (directory) and all subdirectories, use:

```
> debug.vmodule("eth/*=6")
```

If you want to restrict messages to a particular package (e.g. p2p) but exclude subdirectories, use:

```
> debug.vmodule("p2p=6")
```

If you want to see log messages from a particular source file, use

```
> debug.vmodule("server.go=6")
```

You can compose these basic patterns. If you want to see all output from peer.go in a package below gsym(gsym/peer.go, gsym/downloader/peer.go) as well as output from package p2p at level <= 5, use:

```
debug.vmodule("gsym/*/peer.go=6,p2p=5")
```

### debug_vstatus

The `vstatus` inspection property can be queried about the current set restriction for the log messages such as `verbosity`, `vmodule` and `vflag` option.

| Client  | Method invocation           |
| ------- | --------------------------- |
| Console | debug.vstatus()             |
| RPC     | {"method": "debug_vstatus"} |

---

## RLP Encoding / Decoding APIs

We are currently offering 3 cases of rlp encoding/decoding APIs. Each cases are handling about Transaction, Citizen and Oracle format. Also, this document guides each encoding cases with following 3-steps: **1) Encode data, 2) Send Encoded Raw Data, 3) Validate with hash**. 

*caution) Do not use real coin for testing Sending APIs with main-network environment.*

### debug_getTransactionRlp

`getTransactionRlp` encodes the given input data with following Transaction parameters.

##### Parameters

1. `Object` - The transaction object.
   - `from`: `Hex(Byte[10])` - the address the transaction is send from.
   - `to`: `Hex(Byte[10])` - the address the transaction is directed to.
   - `gas`: `Hex(Uint64) ` - integer of the gas provided for the transaction execution. It will return unused gas.
   - `gasPrice`: `Hex(Int)`- integer of the gasPrice used for each paid gas.
   - `value`: `Hex(Int)`  - integer of the value sent with this transaction.
   - `nonce`: `Hex(Uint64)`- integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
   - `type`:`Hex(Uint)`  - type of the transaction(0: original, 1: sct, 2: deposit)
   - `workNodes`: `Array(Address)` - selected work node's SymID to send this transaction. (min: 1, max: 3)
   - `data`: `Hex(Byte)` - the compiled code of a contract OR the hash of the invoked method signature and encoded Parameters.
   - `extraData`: `Hex(Byte)` - the "extra data" field of this block.
   - `v`: `Hex(Int)` - ECDSA recovery id
   - `r`: `Hex(Int)` - ECDSA signature r
   - `s`: `Hex(Int)` - ECDSA signature s
2. `Passphrase` - Account password.

```js
"params": [{
	    "from": "0x00020000000000030002",
	    "to": "0x00020000000000040002",
	    "gas": "0x76c0", 
	    "gasPrice": "0x5d21dba00", 
	    "nonce": "0x0",
	    "type": "0x0",
	    "value": "0x9182a",
	    "workNodes": ["0x00020000000000030002"],
	    "data": "",
	    "extraData": "",
	    "v": "0x0",
	    "r": "0xe3271aec693dea032e589f02f77042876809437562dd1cfdac6eebd8ed74b0ad",
	    "s": "0x5c1b7deac74a2c9a918147195621566565115ad77b2d91b11b826c966a04e133"
		}]

```

##### Returns

1. `encodedResult`: `DATA` - RLP encoded Hex string about Transaction data.
2. `signature`: `DATA` - notice signature value whether it is available or not. If not, it informs proper `v`, `r`, and `s` values.

##### Example: JSON-RPC Format

```js
// RLP Encoding Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"debug_getTransactionRlp","params":[{see above}, passphrase],"id":1}' http://127.0.0.1:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "encodedResult": "0xf8778a00020000000000030002808609184e72a0008276c08a000200000000000400028309182a8080cb8a000200000000000300028080a0e3271aec693dea032e589f02f77042876809437562dd1cfdac6eebd8ed74b0ada05c1b7deac74a2c9a918147195621566565115ad77b2d91b11b826c966a04e133",
        "signature": "Success! Given signature is available."
    }
}
```

```js
// Send Raw Transaction Request
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"sym_sendRawTransaction","params":["0xf8778a00020000000000030002808609184e72a0008276c08a000200000000000400028309182a8080cb8a000200000000000300028080a0e3271aec693dea032e589f02f77042876809437562dd1cfdac6eebd8ed74b0ada05c1b7deac74a2c9a918147195621566565115ad77b2d91b11b826c966a04e133"],"id":1}' http://127.0.0.1:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0xb61068dd779875993d6e95ad95659f1c5bc9772b7d502955d574427716fa3047"
}
```

```js
// Get Transaction Receipt by Transaction hash
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"sym_getTransactionReceipt","params":["0xb61068dd779875993d6e95ad95659f1c5bc9772b7d502955d574427716fa3047"],"id":1}' http://127.0.0.1:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "blockHash": "0x5a697cd12387d8fdc5b15dbee986e3f12837e7bb643050f46d1ee86765cbb13d",
        "blockNumber": "0x38",
        "contractAddress": null,
        "cumulativeGasUsed": "0x5208",
        "from": "0x00020000000000030002",
        "gasUsed": "0x5208",
        "logs": [],
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "status": "0x1",
        "to": "0x00020000000000040002",
        "transactionHash": "0xb61068dd779875993d6e95ad95659f1c5bc9772b7d502955d574427716fa3047",
        "transactionIndex": "0x0"
    }
}
```

##### Example: Console command Format

```js
// Get RLP Encoded Transaction
> debug.getTransactionRlp({"from": "0x00020000000000030002", "to": "0x00020000000000040002", "gas": "0x76c0",  "gasPrice": "0x5d21dba00", "nonce": "0x0", "type": "0x0", "value": "0x9182a", "workNodes": ["0x00020000000000030002"], "data": "", "extraData": "", "v": "0x0", "r": "0xe3271aec693dea032e589f02f77042876809437562dd1cfdac6eebd8ed74b0ad", "s": "0x5c1b7deac74a2c9a918147195621566565115ad77b2d91b11b826c966a04e133"}, passphrase)

// Result
{ 
  encodedResult: "0xf8778a00020000000000030002808609184e72a0008276c08a000200000000000400028309182a8080cb8a000200000000000300028080a0e3271aec693dea032e589f02f77042876809437562dd1cfdac6eebd8ed74b0ada05c1b7deac74a2c9a918147195621566565115ad77b2d91b11b826c966a04e133",
  signature: "Success! Given signature is available."
}
```

```js
// Send Raw Transaction
sym.sendRawTransaction("0xf8778a00020000000000030002808609184e72a0008276c08a000200000000000400028309182a8080cb8a000200000000000300028080a0e3271aec693dea032e589f02f77042876809437562dd1cfdac6eebd8ed74b0ada05c1b7deac74a2c9a918147195621566565115ad77b2d91b11b826c966a04e133")

// Result
"0xb61068dd779875993d6e95ad95659f1c5bc9772b7d502955d574427716fa3047"
```

```js
// Transaction Receipt
sym.getTransactionReceipt("0xb61068dd779875993d6e95ad95659f1c5bc9772b7d502955d574427716fa3047")

// Result
{ 
  blockHash: "0x5490f8a74e9fb6ffdfc1b50524f4ea36c0d62eeaa96690a677821f661abe399c",
  blockNumber: 37,
  contractAddress: null,
  cumulativeGasUsed: 21000,
  from: "0x00020000000000030002",
  gasUsed: 21000,
  logs: [],
  logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  status: "0x1", 
  to: "0x00020000000000040002",
  transactionHash: "0xb61068dd779875993d6e95ad95659f1c5bc9772b7d502955d574427716fa3047",
  transactionIndex: 0
}
```

---

### debug_decodeTransactionRlp

`decodeTransactionRlp` decodes the given input data into Transaction data format.

##### Parameters

1. `DATA` - RLP encoded Hex string about Transaction data.

```js
params:["0xf8778a00020000000000030002808609184e72a0008276c08a000200000000000400028309182a8080cb8a000200000000000300028080a0e3271aec693dea032e589f02f77042876809437562dd1cfdac6eebd8ed74b0ada05c1b7deac74a2c9a918147195621566565115ad77b2d91b11b826c966a04e133"]

```

##### Returns

1. `Object` - see [getTransactionRlp](#debug_getTransactionRlp) Parameters.

##### Example: JSON-RPC Format

```js
// Decode RLP Encoded Transaction data
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"debug_decodeTransactionRlp","params":[{see above}],"id":1}' http://127.0.0.1:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "from": "0x00020000000000030002",
        "nonce": "0x0",
        "gasPrice": "0x5d21dba00",
        "gas": "0x76c0",
        "to": "0x00020000000000040002",
        "value": "0x9182a",
        "input": "0x",
        "type": "0x0",
        "workNodes": [
            "0x00020000000000030002"
        ],
        "extraData": "0x",
        "v": "0x0",
        "r": "0xe3271aec693dea032e589f02f77042876809437562dd1cfdac6eebd8ed74b0ad",
        "s": "0x5c1b7deac74a2c9a918147195621566565115ad77b2d91b11b826c966a04e133",
        "hash": "0xb61068dd779875993d6e95ad95659f1c5bc9772b7d502955d574427716fa3047"
    }
}
```

##### Example: Console command Format

```js
// Decode RLP Encoded Transaction data
> debug.decodeTransactionRlp("0xf8778a00020000000000030002808609184e72a0008276c08a000200000000000400028309182a8080cb8a000200000000000300028080a0e3271aec693dea032e589f02f77042876809437562dd1cfdac6eebd8ed74b0ada05c1b7deac74a2c9a918147195621566565115ad77b2d91b11b826c966a04e133")

// Result
{ 
  extraData: "0x",
  from: "0x00020000000000030002",
  gas: "0x76c0",
  gasPrice: "0x5d21dba00",
  hash: "0xb61068dd779875993d6e95ad95659f1c5bc9772b7d502955d574427716fa3047",
  input: "0x",
  nonce: "0x0",
  r: "0xe3271aec693dea032e589f02f77042876809437562dd1cfdac6eebd8ed74b0ad",
  s: "0x5c1b7deac74a2c9a918147195621566565115ad77b2d91b11b826c966a04e133",
  to: "0x00020000000000040002", 
  type: "0x0",
  v: "0x0",
  value: "0x9182a",
  workNodes: ["0x00020000000000030002"]
}
```

---

### debug_getCitizenRlp

`getCitizenRlp` encodes the given input data with following Citizen parameters.

##### Parameters

1. `Object` - the Symverse identifier & account properties.
   - `from` : `Hex(Byte[10])` - the address of the CA server that creates SymID. 
   - `to` : `Hex(Byte[10])` - the address creation transaction is directed to. 
   - `nonce` : `Hex(Uint64)` - integer of a nonce. 
   - `vFlag` : `Hex(Uint64)` - veriﬁcation strength of an account represented by bits. 
   - `symId` : `Hex(Byte[10])` - hexadecimal SymID identifying an account.
   - `aKeyPubH` : `Hex(Byte[20])` , 20 Bytes - lower bytes of hashed public key. 
   - `country` : `Hex(Uint64)` - country code.
   - `status` : `Hex(Uint64)` - account status of an account. (0x1: Active, 0x2: Locked, 0x3: Marked, 0x4: Revoked)
   - `credit` : `Hex(Uint64)` - credit rating of an account (0~15) 
   - `role` : `Hex(Uint64)` - account role (0x1: general, 0xf0f0: Master CA, 0xf0f1: CA, 0xf0f2: Oracle) 
   - `refCode` : `Hex(Uint64)` - issuer's reference code.
   - `writeTime` : `big.Int` - the unix timestamp for the citizen requested time.
   - `extraData`: `Hex(Byte)` - the "extra data" field of this block.
   - `v`: `Hex(Int)` - ECDSA recovery id
   - `r`: `Hex(Int)` - ECDSA signature r
   - `s`: `Hex(Int)` - ECDSA signature s
2. `Passphrase` - Account password.

```json
params: [{
	    "from": "0x00010000000000010002",
	    "to": "0x00020000000000030002",
	    "nonce": "0x0",
	    "VFlag":"0x12",
	    "symId":"0x00010000000000010003",
	    "aKeyPubH":"0x484E0b5D12913CeC0B4e942828F6168E9F464A09",
	    "country": "0x0",
	    "status": "0x1",
	    "credit": "0xf",
	    "role": "0x1",
	    "refcode": "0x0",
	    "writeTime": "0x89472",
	    "extraData": "",
	    "v": "0x0",
	    "r": "0xf88c087f7e398de09c0058621675897c6b6cb00536bb99fa4781bbb09b4e48db",
	    "s": "0x4aae13197615f911f6d08eee9aafbb7fbca51b5e70d65cd407ea78b45c3d0f6d"
	}]
```

##### Returns

1. `encodedResult`: `DATA` - RLP encoded Hex string about Citizen data.
2. `signature`: `DATA` - notice signature value whether it is available or not. If not, it informs proper `v`, `r`, and `s` values.

##### Example: JSON-RPC Format

```js
// Get RLP Encoded Citizen
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"debug_getCitizenRlp","params":[{see above}, passphrase],"id":1}' http://127.0.0.1:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "encodedResult": "0xf8858a000100000000000100028a00020000000000030002808a0001000000000001000394484e0b5d12913cec0b4e942828f6168e9f464a091280010f0180830894728001a022e1be4541ef47766c0c8673f7b44a9df9cef7995308717c50cee08939e3dee6a01cef746f3bfe29b339893b62bd2053ad2f03de0796e0660db101343b3062cf81",
        "signature": "Success! Given signature is available."
    }
}

```

```js
// Send Raw Citizen
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_sendRawCitizen","params":["0xf8858a000100000000000100028a00020000000000030002808a0001000000000001000394484e0b5d12913cec0b4e942828f6168e9f464a091280010f0180830894728001a022e1be4541ef47766c0c8673f7b44a9df9cef7995308717c50cee08939e3dee6a01cef746f3bfe29b339893b62bd2053ad2f03de0796e0660db101343b3062cf81"],"id":1}' http://127.0.0.1:8545

// Result 
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0x65fd2c06b3624e3e5f5b2f79beefcee25d41de84e8fcc48b310a879968033233"
}
```

```js
// Get Citizen By Hash
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"citizen_getCitizenByHash","params":[{"0x65fd2c06b3624e3e5f5b2f79beefcee25d41de84e8fcc48b310a879968033233"}, passphrase],"id":1}' http://127.0.0.1:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "blockHash": "0x83cc978fbddb0b448af9d59379526b1e36af65d4f343b23d3582cb3e5c9ad4eb",
        "blockNumber": "0x1",
        "citizenIndex": "0x0",
        "from": "0x00010000000000010002",
        "to": "0x00020000000000030002",
        "nonce": "0x0",
        "symId": "0x00010000000000010003",
        "aKeyPubH": "0x484e0b5d12913cec0b4e942828f6168e9f464a09",
        "vFlag": "0x12",
        "country": "0x0",
        "status": "0x1",
        "credit": "0xf",
        "role": "0x1",
        "refCode": "0x0",
        "writeTime": "0x89472",
        "hash": "0x65fd2c06b3624e3e5f5b2f79beefcee25d41de84e8fcc48b310a879968033233",
        "v": "0x1",
        "r": "0x22e1be4541ef47766c0c8673f7b44a9df9cef7995308717c50cee08939e3dee6",
        "s": "0x1cef746f3bfe29b339893b62bd2053ad2f03de0796e0660db101343b3062cf81"
    }
}
```

##### Example: Console command Format

```js
// Get RLP Encoded Citizen
> debug.getCitizenRlp({"from": "0x00010000000000010002", "to": "0x00020000000000030002", "nonce": "0x0", "VFlag":"0x12", "symId":"0x00010000000000010003", "aKeyPubH":"0x484E0b5D12913CeC0B4e942828F6168E9F464A09", "country": "0x0", "status": "0x1", "credit": "0xf", "role": "0x1", "refcode": "0x0", "writeTime": "0x89472", "extraData": "", "v": "0x0", "r": "0xf88c087f7e398de09c0058621675897c6b6cb00536bb99fa4781bbb09b4e48db", "s": "0x4aae13197615f911f6d08eee9aafbb7fbca51b5e70d65cd407ea78b45c3d0f6d"}, passphrase)

// Result
{ 
  encodedResult: "0xf8858a000100000000000100028a00020000000000030002808a0001000000000001000394484e0b5d12913cec0b4e942828f6168e9f464a091280010f0180830894728080a0f88c087f7e398de09c0058621675897c6b6cb00536bb99fa4781bbb09b4e48dba04aae13197615f911f6d08eee9aafbb7fbca51b5e70d65cd407ea78b45c3d0f6d",
  signature: "Success! Given signature is available."
}
```

```js
// Send Raw Citizen
citizen.sendRawCitizen("0xf8858a000100000000000100028a00020000000000030002808a0001000000000001000394484e0b5d12913cec0b4e942828f6168e9f464a091280010f0180830894728080a0f88c087f7e398de09c0058621675897c6b6cb00536bb99fa4781bbb09b4e48dba04aae13197615f911f6d08eee9aafbb7fbca51b5e70d65cd407ea78b45c3d0f6d")

// Result
"0x892fb2f775c756d854bf3edcc351499406b455f1ce9cb058e334a284c9a99df1" 
```

```js
// Get Citizen By Hash
citizen.getCitizenByHash("0x892fb2f775c756d854bf3edcc351499406b455f1ce9cb058e334a284c9a99df1")

// Result
{ 
  aKeyPubH: "0x484e0b5d12913cec0b4e942828f6168e9f464a09",
  blockHash: "0x31f52c81cf1f208375eae57781d115c182ebca4c00deeb2dc08d6f59357fa7d9",
  blockNumber: 1,
  citizenIndex: 0,
  country: "0x0",
  credit: "0xf",
  from: "0x00010000000000010002",
  hash: "0x892fb2f775c756d854bf3edcc351499406b455f1ce9cb058e334a284c9a99df1",
  nonce: 0,
  r: "0xf88c087f7e398de09c0058621675897c6b6cb00536bb99fa4781bbb09b4e48db", 
  refCode: "0x0",
  role: "0x1",
  s: "0x4aae13197615f911f6d08eee9aafbb7fbca51b5e70d65cd407ea78b45c3d0f6d",
  status: "0x1",
  symId: "0x00010000000000010003",
  to: "0x00020000000000030002",
  v: "0x0",
  vFlag: "0x12",
  writeTime: 562290
}
```



------

### debug_decodeCitizenRlp

`decodeCitizenRlp` decodes the given input data into Citizen data format.

##### Parameters

1. `DATA` - RLP encoded Hex string about Citizen data.

```js
params: ["0xf8858a000100000000000100028a00020000000000030002808a0001000000000001000394484e0b5d12913cec0b4e942828f6168e9f464a091280010f0180830894728080a0f88c087f7e398de09c0058621675897c6b6cb00536bb99fa4781bbb09b4e48dba04aae13197615f911f6d08eee9aafbb7fbca51b5e70d65cd407ea78b45c3d0f6d"]
```

##### Returns

1. `Object` - see [getCitizenRlp](#debug_getCitizenRlp) Parameters.

##### Example: JSON-RPC Format

```js
// Decode RLP Encoded Citizen data
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"debug_decodeCitizenRlp","params":[{see above}],"id":1}' http://127.0.0.1:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "from": "0x00010000000000010002",
        "to": "0x00020000000000030002",
        "nonce": 0,
        "symId": "0x00010000000000010003",
        "aKeyPubH": "0x484e0b5d12913cec0b4e942828f6168e9f464a09",
        "vFlag": 18,
        "country": 0,
        "status": 1,
        "credit": 15,
        "role": 1,
        "refCode": 0,
        "writeTime": 562290,
        "extraData": "",
        "v": 0,
        "r": 112421003688896064353707810670377479707972820062369944401272465448343647963355,
        "s": 33778714004048296868602750466575108995190736414514533981570025140840300351341,
        "hash": "0x892fb2f775c756d854bf3edcc351499406b455f1ce9cb058e334a284c9a99df1"
    }
}

```

##### Example: Console command Format

```js
// Decode RLP Encoded Citizen data
> debug.decodeCitizenRlp("0xf8858a000100000000000100028a00020000000000030002808a0001000000000001000394484e0b5d12913cec0b4e942828f6168e9f464a091280010f0180830894728080a0f88c087f7e398de09c0058621675897c6b6cb00536bb99fa4781bbb09b4e48dba04aae13197615f911f6d08eee9aafbb7fbca51b5e70d65cd407ea78b45c3d0f6d")

// Result
{ 
  aKeyPubH: "0x484e0b5d12913cec0b4e942828f6168e9f464a09",
  country: 0,
  credit: 15,
  extraData: "",
  from: "0x00010000000000010002",
  hash: "0x892fb2f775c756d854bf3edcc351499406b455f1ce9cb058e334a284c9a99df1",
  nonce: 0,
  r: 1.1242100368889607e+77,
  refCode: 0,
  role: 1,
  s: 3.3778714004048295e+76,
  status: 1, 
  symId: "0x00010000000000010003",
  to: "0x00020000000000030002",
  v: 0,
  vFlag: 18,
  writeTime: 562290
}
```

------

### debug_getOracleRlp

`getOracleRlp` encodes the given input data with following Oracle parameters.

##### Parameters

1. `Object` - the oracle.
   - `from` : `Hex(Byte[10])` - the address of the CA server that creates SymID. 
   - `nonce` : `Hex(Uint64)` - integer of a nonce. 
   - `data` : `Hex(Byte)` - JSON encoded Oracle parameters, see the [Oracle Parameters](#oracle-parameters)
   - `extraData`: `Hex(Byte)` - the "extra data" field of this block.
   - `v`: `Hex(Int)` - ECDSA recovery id
   - `r`: `Hex(Int)` - ECDSA signature r
   - `s`: `Hex(Int)` - ECDSA signature s
2. `Passphrase` - Account password.

```json
params: [{
		"from": "0x00010000000000020002",
	    "nonce": "0x0",
	    "data": "0x7b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d",
	    "extraData": "0x123123",
	    "v": "0x0",
        "r": "0x8e63053bb2eaa5dbd7061fae7ae329e7919b202f8a58026c3dd2fea5136ddd0b",
        "s": "0x226ae29e0ce8e17d2e779cfc7db24a7dd5bd7d2a7cb331c182cde96d02f5c4d4"
		}]
```

##### Returns

1. `encodedResult`: `DATA` - RLP encoded Hex string about Oracle data.
2. `signature`: `DATA` - notice signature value whether it is available or not. If not, it informs proper `v`, `r`, and `s` values.

##### Example: JSON-RPC Format

```js
// Get RLP Encoded Oracle
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"debug_getOracleRlp","params":[{see above}, passphrase],"id":100}' http://127.0.0.1:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "encodedResult": "0xf901c38a000100000000000200028a0000000000000000000080b901627b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d8312312380a0c7d3156181d220f2e0c25fc0d8c30cdaa81bf75d2c6275a30d79198e5951b9d2a018df1ca63c20726ddf205c5be89340a3ec5ff912eece804f0847354cc6a1cfa8",
        "signature": "Success! Given signature is available."
    }
}
```

```js
// Send Raw Oracle
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"oracle_sendRawOracle","params":["0xf901c38a000100000000000200028a0000000000000000000080b901627b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d8312312380a0c7d3156181d220f2e0c25fc0d8c30cdaa81bf75d2c6275a30d79198e5951b9d2a018df1ca63c20726ddf205c5be89340a3ec5ff912eece804f0847354cc6a1cfa8"],"id":100}' http://127.0.0.1:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0xb67fc605d9d4810a8239cadad05c8b3479d602bdb8d10167816b84932d655a52"
}
```

```js
// Get Oracle By Hash
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"oracle_getOralceByHash","params":["0xb67fc605d9d4810a8239cadad05c8b3479d602bdb8d10167816b84932d655a52"],"id":100}' http://127.0.0.1:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "blockHash": "0xeea4d6aa6f0d3d14c2502ca9e7d680d37e79e54ac29674ae6535f9a0752e8c02",
        "blockNumber": "0x1",
        "oracleIndex": "0x0",
        "from": "0x00010000000000020002",
        "to": "0x00000000000000000000",
        "nonce": "0x0",
        "data": "0x7b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d",
        "extra": "0x123123",
        "hash": "0xb67fc605d9d4810a8239cadad05c8b3479d602bdb8d10167816b84932d655a52",
        "v": "0x0",
        "r": "0xc7d3156181d220f2e0c25fc0d8c30cdaa81bf75d2c6275a30d79198e5951b9d2",
        "s": "0x18df1ca63c20726ddf205c5be89340a3ec5ff912eece804f0847354cc6a1cfa8"
    }
}
```

##### Example: Console command Format

```js
// Get RLP Encoded Oracle
> debug.getOracleRlp({"from": "0x00010000000000020002", "nonce": "0x0", "data": "0x7b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d", "extraData": "0x123123", "v": "0x0", "r": "0x8e63053bb2eaa5dbd7061fae7ae329e7919b202f8a58026c3dd2fea5136ddd0b", "s": "0x226ae29e0ce8e17d2e779cfc7db24a7dd5bd7d2a7cb331c182cde96d02f5c4d4"}, passphrase)

// Result
{ 
  encodedResult: "0xf901c38a000100000000000200028a0000000000000000000080b901627b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d8312312380a08e63053bb2eaa5dbd7061fae7ae329e7919b202f8a58026c3dd2fea5136ddd0ba0226ae29e0ce8e17d2e779cfc7db24a7dd5bd7d2a7cb331c182cde96d02f5c4d4",
  signature: "Success! Given signature is available."
}
```

```js
// Send Raw Oracle
oracle.sendRawOracle("0xf901c38a000100000000000200028a0000000000000000000080b901627b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d8312312380a08e63053bb2eaa5dbd7061fae7ae329e7919b202f8a58026c3dd2fea5136ddd0ba0226ae29e0ce8e17d2e779cfc7db24a7dd5bd7d2a7cb331c182cde96d02f5c4d4")

// Result
"0xa133bdef08dd54842cbe34203ad5da1ac855bb3ea82699f45b611a7714a65f14" 
```

```js
// Get Oracle By Hash
oracle.getOracleByHash("0xa133bdef08dd54842cbe34203ad5da1ac855bb3ea82699f45b611a7714a65f14")

// Result
{ 
  blockHash: "0x3a8b14e19fc77d8e5f93fd1cad6e093b15d31bd6bb10f025aa81156c30b57c3d",
  blockNumber: 1,
  data: "0x7b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d",
  extra: "0x123123",
  from: "0x00010000000000020002",
  hash: "0xa133bdef08dd54842cbe34203ad5da1ac855bb3ea82699f45b611a7714a65f14", 
  nonce: 0,
  oracleIndex: 0,
  r: "0x8e63053bb2eaa5dbd7061fae7ae329e7919b202f8a58026c3dd2fea5136ddd0b",
  rev: 0,
  s: "0x226ae29e0ce8e17d2e779cfc7db24a7dd5bd7d2a7cb331c182cde96d02f5c4d4",
  to: "0x00000000000000000000",
  v: "0x0"
}
```



------

### debug_decodeOracleRlp

`decodeOracleRlp` decodes the given input data into Oracle data format.

##### Parameters

1. `DATA` - RLP encoded Hex string about Oracle data.

```js
params: ["0xf901c38a000100000000000200028a0000000000000000000080b901627b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d8312312380a08e63053bb2eaa5dbd7061fae7ae329e7919b202f8a58026c3dd2fea5136ddd0ba0226ae29e0ce8e17d2e779cfc7db24a7dd5bd7d2a7cb331c182cde96d02f5c4d4"]

```

##### Returns

1. `Object` - see [getOracleRlp](#debug_getOracleRlp) Parameters.

##### Example: JSON-RPC Format

```js
// Decode RLP Encoded Oracle data
curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"debug_decodeOracleRlp","params":[{see above}],"id":1}' http://127.0.0.1:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "from": "0x00010000000000020002",
        "to": "0x00000000000000000000",
        "nonce": "0x0",
        "data": "0x7b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d",
        "extraData": "0x123123",
        "v": "0x0",
        "r": "0x8e63053bb2eaa5dbd7061fae7ae329e7919b202f8a58026c3dd2fea5136ddd0b",
        "s": "0x226ae29e0ce8e17d2e779cfc7db24a7dd5bd7d2a7cb331c182cde96d02f5c4d4",
        "hash": "0xa133bdef08dd54842cbe34203ad5da1ac855bb3ea82699f45b611a7714a65f14"
    }
}
```

##### Example: Console command Format

```js
// Decode RLP Encoded Oracle data
> debug.decodeOracleRlp("0xf901c38a000100000000000200028a0000000000000000000080b901627b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d8312312380a08e63053bb2eaa5dbd7061fae7ae329e7919b202f8a58026c3dd2fea5136ddd0ba0226ae29e0ce8e17d2e779cfc7db24a7dd5bd7d2a7cb331c182cde96d02f5c4d4")

// Result
{ 
  data: "0x7b227072696365223a302e312c22636f73744e6f646541223a313030302c22636f73744e6f646542223a302c22636f73744e6f64654341223a302c22636f73744e6f64654f7261223a302c226e756d4e6f646541223a302c226e756d4e6f646542223a302c226e756d4e6f64654341223a302c226e756d4e6f64654f7261223a302c227175616c44617070476173223a3130302c227175616c4e6f646541223a6e756c6c2c227175616c4e6f646542223a6e756c6c2c227175616c4e6f64654341223a6e756c6c2c227175616c4f7261636c697a6572223a6e756c6c2c227175616c526577617264476173223a6e756c6c2c226e756d526577617264476173223a302c22746f74616c537570706c79223a302c226465677265654f6646726565646f6d223a302c226d696e5478436f6e747269627574696f6e223a302c226d6178476173446973636f756e7452617465223a302c22626173655072696365223a307d",
  extraData: "0x123123",
  from: "0x00010000000000020002",
  hash: "0xa133bdef08dd54842cbe34203ad5da1ac855bb3ea82699f45b611a7714a65f14",
  nonce: "0x0",
  r: "0x8e63053bb2eaa5dbd7061fae7ae329e7919b202f8a58026c3dd2fea5136ddd0b",
  s: "0x226ae29e0ce8e17d2e779cfc7db24a7dd5bd7d2a7cb331c182cde96d02f5c4d4",
  to: "0x00000000000000000000",
  v: "0x0" 
}
```

### debug_getSCTRlp

`debug_getSCTRlp` API is debugging api to validate the rlp encoded string. It's parameter is configured with following:

##### Parameters

* `type` : `Hex(Int)` - Type to be processed among SCT 20/21/30/40.
* `method` : `Hex(Uint64)` - Method corresponding to SCT Type.
* `name` : `string` - Name is about token name to be created.
* `symbol` : `string` - Symbol is about toke symbol to be created
* `owner` : `Address` - Owner address(SymID) of the contract.
* `amount` : `Hex(Uint64)` - Amount is about the value for method.
* `lockAmount` : `Hex(Uint64)` - LockAmount is about locked amount for sct21 token.
* `from` : `Address` - From is the address(SymID) of sender of contract methods.
* `to` : `Address` - To is the address(SymID) of the receiver of contract methods.
* `index` : `Hex(Uint64)` - index is about item or coupon index for sct30/40.
* `items`: `Array[Object]` - Items is about array of SCT30 item object.
  * `type` : `string` - type of item
  * `name` :  `string` - name of item
  * `value`: `Hex(Uint64)` -  the unique value of item
  * `groupID` :  `string` - category of item (search index value)
  * `itemID` :  `string` -  unique attribute of item
  * `description` :  `string` -  Additional explanation of `DATA`, item
* `coupons`: `Array[Object]` - Coupons is about array of SCT40 coupon object.
  - `type` : `string` - type of item
  - `name` :  `string` - name of item
  - `point` : `Hex(Uint64)` -  the unique value of item
  - `groupID` :  `string` - category of item (search index value)
  - `couponID` :  `string` -  unique attribute of item
  - `description` :  `string` -  Additional explanation of `DATA`, item


------

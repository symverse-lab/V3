# SymVerse V3 Bootnode API

> **Status:** Baseline import  
> **Date:** 2026-05-16  
> **Document Role:** Bootnode management API documentation carried into the SymVerse V3 documentation set

---

Beside the official [RPC APIs](https://github.com/ethereum/wiki/wiki/JSON-RPC) interface go-symverse has support for additional management APIs about bootnode. Similar to the official APIs, these are also provided using [JSON-RPC](http://www.jsonrpc.org/specification) and follow exactly the same conventions.



## Enabling the management APIs

To offer these APIs over the Bootnode RPC endpoints, please specify them with the `--${interface}api`
command line argument (where `${interface}` can be `rpc` for the HTTP endpoint, `ws` for the WebSocket
endpoint.

For example: `bootnode --rpcapi "bootnode" --rpc`

* Enables the official bootnode API over the HTTP interface

The HTTP RPC interface must be explicitly enabled using the `--rpc` flag.

Please note, offering an API over the HTTP (`rpc`) or WebSocket (`ws`) interfaces will give everyone
access to the APIs who can access this interface (DApps, browser tabs, etc). Be careful which APIs
you enable. 

To determine which APIs an interface provides, the `modules` JSON-RPC method can be invoked. 

## List of management APIs

Beside the officially exposed official RPC API namespaces, Bootnode provides the following extra management API namespaces:

* `bootnode`: bootnodenode management

| [Bootnode](#Bootnode) |
| :--------------------------- |
| [getNodes](#bootnode_getNodes) |
| [getCloseNodes](#bootnode_getCloseNodes) |



## Bootnode

The `admin` API gives you access to several non-standard RPC methods, which will allow you to have
a fine grained control over your bootnode instance, including but not limited to network peer and RPC
endpoint management.

### bootnode_getNodes

The `getNodes` method requests nodes information that is alive in network.
The bootnode will try to maintain connectivity to these nodes at all times, reconnecting every
once in a while if the remote connection goes down.


| Client | Method invocation                                          |
| :----: | ---------------------------------------------------------- |
|   Go   | `bootnode.GetNodes() ([]NodesData, error)`                 |
|  RPC   | {"jsonrpc":"2.0" , "method": "bootnode_getNodes", "id": 1} |

#### Example

```javascript
> curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"bootnode_getNodes","id":1}' http://127.0.0.1:8547

{
    "jsonrpc": "2.0",
    "id": 0,
    "result": [
        {
            "id": 1,
            "rpcaddress": "127.0.0.1",
            "rpcport": "8007",
            "enodeId": "ecbd7d1e3b7636f2463f987c810a8ea9f3df3da45a94e5ebd1588b27c302498edb57899e19f046d671b2ff8061b3c819312b2be10a14f9f1994083874e82994f"
        },
        {
            "id": 2,
            "rpcaddress": "127.0.0.1",
            "rpcport": "8004",
            "enodeId": "f80a50d360dfb6ac886d268ec51584a0c9c6fdde9a016a10cd2b1bd06663647bdf6ce7e35f36bb98f45201890077999da91b1164a813281992443031040feed6"
        },
        {
            "id": 3,
            "rpcaddress": "127.0.0.1",
            "rpcport": "8002",
            "enodeId": "6db63b7a017abec62dbe3e51b6185ee023d7f06dba0b66a4b62b8db5f819938ecc02b5006c4943b1e5e8dab5bf329f255665eb2c0235cf0bfed99c69a28a93a5"
        },
        {
            "id": 4,
            "rpcaddress": "127.0.0.1",
            "rpcport": "8006",
            "enodeId": "9afc947fb6491c7bdad572c0036e6b7bad5ffa98cfff0b5c8078c16cf10c51dd252cd6fc56931e13dd5e1a0c0a11cf8970132269a518c1db1bf1395cc21dac87"
        },
        {
            "id": 5,
            "rpcaddress": "127.0.0.1",
            "rpcport": "8001",
            "enodeId": "17b2c3851a8008b879f7b9d2dd8c2a9412b3ee28bc10dc6c152baf8c9ffc6a37c20932f9a94e4fb8b216d9ed32d71e1d77a82b3eb82ade50a2b10eb641d51761"
        },
        {
            "id": 6,
            "rpcaddress": "127.0.0.1",
            "rpcport": "8003",
            "enodeId": "95914f59f6545970381795dc026ae4d3b0e8307aab2739ca089bdd2663809aac80dd0cf6b1ffbcf8cf7e27983846714daa55c931fb20e513a1ad9f0968332a3f"
        },
        {
            "id": 7,
            "rpcaddress": "127.0.0.1",
            "rpcport": "8005",
            "enodeId": "4a2eaedd3ca6d36c66c23e431c3a32be436dc8727bbff5668577032e7f4ca85e3b0fa57b8916de9548243a2b89975d4d94a91dd5d658ccae5f1daea443c7c6e7"
        }
    ]
}
```

### bootnode_getCloseNodes

The `getCloseNodes` method requests nodes information that are geographically close from the given IP information.

| Client | Method invocation                                            |
| :----: | ------------------------------------------------------------ |
|   Go   | `bootnode.GetCloseNodes(ip string) ([]NodesData, error)`     |
|  RPC   | {"jsonrpc":"2.0" , "method": "bootnode_getCloseNodes", "params": [string], "id": 1} |

#### Example

```javascript
> curl -X POST -H "Content-Type: application/json; charset=utf-8" --data '{"jsonrpc":"2.0","method":"bootnode_getCloseNodes","params":["112.51.172.160"],"id":1}' http://127.0.0.1:8547

{
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        {
            "id": 7,
            "rpcaddress": "127.0.0.1",
            "rpcport": "8003",
            "enodeId": "95914f59f6545970381795dc026ae4d3b0e8307aab2739ca089bdd2663809aac80dd0cf6b1ffbcf8cf7e27983846714daa55c931fb20e513a1ad9f0968332a3f"
        },
        {
            "id": 6,
            "rpcaddress": "127.0.0.1",
            "rpcport": "8006",
            "enodeId": "9afc947fb6491c7bdad572c0036e6b7bad5ffa98cfff0b5c8078c16cf10c51dd252cd6fc56931e13dd5e1a0c0a11cf8970132269a518c1db1bf1395cc21dac87"
        },
        {
            "id": 5,
            "rpcaddress": "127.0.0.1",
            "rpcport": "8005",
            "enodeId": "4a2eaedd3ca6d36c66c23e431c3a32be436dc8727bbff5668577032e7f4ca85e3b0fa57b8916de9548243a2b89975d4d94a91dd5d658ccae5f1daea443c7c6e7"
        }
    ]
}
```

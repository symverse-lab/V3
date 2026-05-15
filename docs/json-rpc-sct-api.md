# SymVerse V3 JSON-RPC SCT API

> **Status:** Baseline import  
> **Date:** 2026-05-16  
> **Document Role:** JSON-RPC SCT API documentation carried into the SymVerse V3 documentation set without PQCFork-specific changes  
> **Source Basis:** Existing SymVerse `JSON RPC SCT API` wiki documentation

---

# 1. Reference

Related references:

- SCT Introduction
- SCT Transaction
- JSON-RPC 2.0

---

# 2. Enabling the SCT APIs

SCT write operations and SCT query operations are handled differently.

- Write operations are submitted as SCT transactions.
- Stored SCT contract state can be queried through the SCT JSON-RPC namespace.

To expose SCT APIs through Gsym RPC endpoints, enable the `sct` API namespace.

```bash
gsym --ipcapi sct --rpcapi sct --wsapi sct --ws --rpc
```

This enables:

| Interface | Enabled API |
|---|---|
| IPC | Official SCT API |
| HTTP RPC | Official SCT API |
| WebSocket | Official SCT API |

The HTTP RPC interface must be explicitly enabled with:

```bash
--rpc
```

---

# 3. SCT API Format

SCT write operations are submitted using SCT transaction input data.

Debug RLP helper:

```text
debug_getsctrlp
```

The debug helper is used to build or inspect SCT RLP-encoded input payloads.

---

# 4. SCT API Families

| SCT Type | Description |
|---|---|
| `SCT20` | ERC20-like fungible token template |
| `SCT21` | SCT20 with lock-related functions |
| `SCT22` | Authorized transfer token template |
| `SCT30` | ERC721-like non-fungible item template |
| `SCT40` | SCT30-based coupon template |
| `SCT50` | Vote contract template |
| `SCT51` | Poll contract template |

---

# 5. SCT Method Index

## 5.1 SCT20

| Method |
|---|
| `SCT20_CREATE` |
| `SCT20_TRANSFER` |
| `SCT20_TRANSFER_FROM` |
| `SCT20_APPROVE` |
| `SCT20_DECREASE_APPROVE` |
| `SCT20_MINT` |
| `SCT20_BURN` |
| `SCT20_PAUSE` |
| `SCT20_UNPAUSE` |
| `SCT20_TRANSFER_OWNER` |

---

## 5.2 SCT21

| Method |
|---|
| `SCT21_CREATE` |
| `SCT21_TRANSFER` |
| `SCT21_TRANSFER_FROM` |
| `SCT21_APPROVE` |
| `SCT21_DECREASE_APPROVE` |
| `SCT21_MINT` |
| `SCT21_BURN` |
| `SCT21_PAUSE` |
| `SCT21_UNPAUSE` |
| `SCT21_TRANSFER_OWNER` |
| `SCT21_LOCK_TRANSFER` |
| `SCT21_UNLOCK_AMOUNT` |
| `SCT21_RESTORE_LOCK_AMOUNT` |
| `SCT21_ADD_LOCK_AMOUNT` |
| `SCT21_SUB_LOCK_AMOUNT` |
| `SCT21_ACCOUNT_LOCK` |
| `SCT21_ACCOUNT_UNLOCK` |

---

## 5.3 SCT22

| Method |
|---|
| `SCT22_CREATE` |
| `SCT22_TRANSFER` |
| `SCT22_TRANSFER_FROM` |
| `SCT22_MINT` |
| `SCT22_BURN` |
| `SCT22_PAUSE` |
| `SCT22_UNPAUSE` |
| `SCT22_TRANSFER_OWNER` |
| `SCT22_SET_AUTHORITY` |

---

## 5.4 SCT30

| Method |
|---|
| `SCT30_CREATE` |
| `SCT30_CREATE_ITEM` |
| `SCT30_TRANSFER` |
| `SCT30_TRANSFER_FROM` |
| `SCT30_APPROVE` |
| `SCT30_ITEM_PAUSE` |
| `SCT30_ITEM_UNPAUSE` |
| `SCT30_TRANSFER_OWNER` |

---

## 5.5 SCT40

| Method |
|---|
| `SCT40_CREATE` |
| `SCT40_CREATE_COUPON` |
| `SCT40_TRANSFER` |
| `SCT40_TRANSFER_FROM` |
| `SCT40_APPROVE` |
| `SCT40_COUPON_USE` |
| `SCT40_COUPON_PAUSE` |
| `SCT40_COUPON_UNPAUSE` |
| `SCT40_TRANSFER_OWNER` |

---

## 5.6 SCT50

| Method |
|---|
| `SCT50_CREATE` |
| `SCT50_ADD_POLL_CREATORS` |
| `SCT50_REMOVE_POLL_CREATORS` |

---

## 5.7 SCT51

| Method |
|---|
| `SCT51_CREATE_POLL` |
| `SCT51_VOTE_IN_POLL` |
| `SCT51_UNSTAKE_TOKENS` |
| `SCT51_EMERGENCY_STOP_POLL` |
| `SCT51_FINISH_POLL` |
| `SCT51_WRITE_POLL_RESULTS` |

---

# 6. SCT20 API

SCT20 implements ERC20-style fungible token functionality.

## 6.1 `SCT20_CREATE`

Create an SCT20 contract.

| Item | Value |
|---|---|
| Type | `20` |
| Method | `0` |

Parameters:

| Parameter | Type | Description |
|---|---|---|
| `Name` | `STRING` | Contract/token name |
| `Symbol` | `STRING` | Contract/token symbol; length 3–10 |
| `Amount` | `QUANTITY` | Total supply |
| `Owner` | `DATA`, 10 bytes | Contract owner address |

Example:

```javascript
debug.getSCTRlp({
  "type": "0x14",
  "method": "0x0",
  "params": {
    "name": "SymToken",
    "symbol": "STK",
    "amount": "0x56bc75e2d63100000",
    "owner": "0x00020000000000070002"
  }
})
```

```javascript
sym.sendTransaction({
  "from": "0x00020000000000070002",
  "gas": "0x76cff0",
  "gasPrice": "0x5d21dba00",
  "nonce": "0x0",
  "type": "0x1",
  "input": "0xe51480e28853796d546f6b656e8353544b89056bc75e2d631000008a00020000000000070002"
})
```

---

## 6.2 `SCT20_TRANSFER`

Transfer SCT20 tokens.

| Item | Value |
|---|---|
| Type | `20` |
| Method | `1` |
| Authorization | All |

Parameters:

| Parameter | Type | Description |
|---|---|---|
| `To` | `DATA`, 10 bytes | Recipient address |
| `Amount` | `QUANTITY` | Transfer amount |

---

## 6.3 `SCT20_TRANSFER_FROM`

Transfer delegated SCT20 tokens.

| Item | Value |
|---|---|
| Type | `20` |
| Method | `2` |
| Authorization | All |

Parameters:

| Parameter | Type | Description |
|---|---|---|
| `From` | `DATA`, 10 bytes | Source address |
| `To` | `DATA`, 10 bytes | Recipient address |
| `Amount` | `QUANTITY` | Transfer amount |

---

## 6.4 `SCT20_APPROVE`

Delegate token allowance.

| Item | Value |
|---|---|
| Type | `20` |
| Method | `3` |
| Authorization | All |

Parameters:

| Parameter | Type | Description |
|---|---|---|
| `To` | `DATA`, 10 bytes | Spender address |
| `Amount` | `QUANTITY` | Allowance amount |

---

## 6.5 `SCT20_DECREASE_APPROVE`

Decrease an existing allowance.

| Item | Value |
|---|---|
| Type | `20` |
| Method | `4` |
| Authorization | All |

Parameters:

| Parameter | Type | Description |
|---|---|---|
| `To` | `DATA`, 10 bytes | Spender address |
| `Amount` | `QUANTITY` | Allowance decrease amount |

---

## 6.6 `SCT20_MINT`

Mint additional SCT20 tokens.

| Item | Value |
|---|---|
| Type | `20` |
| Method | `5` |
| Authorization | Owner, Creator |

Parameters:

| Parameter | Type | Description |
|---|---|---|
| `To` | `DATA`, 10 bytes | Mint recipient |
| `Amount` | `QUANTITY` | Mint amount |

---

## 6.7 `SCT20_BURN`

Burn SCT20 tokens.

| Item | Value |
|---|---|
| Type | `20` |
| Method | `6` |
| Authorization | Owner, Creator |

Parameters:

| Parameter | Type | Description |
|---|---|---|
| `From` | `DATA`, 10 bytes | Source account |
| `Amount` | `QUANTITY` | Burn amount |

---

## 6.8 `SCT20_PAUSE`

Pause the SCT20 contract.

| Item | Value |
|---|---|
| Type | `20` |
| Method | `7` |
| Authorization | Owner, Creator |
| Parameters | `Null` |

---

## 6.9 `SCT20_UNPAUSE`

Resume a paused SCT20 contract.

| Item | Value |
|---|---|
| Type | `20` |
| Method | `8` |
| Authorization | Owner, Creator |
| Parameters | `Null` |

---

## 6.10 `SCT20_TRANSFER_OWNER`

Transfer SCT20 ownership.

| Item | Value |
|---|---|
| Type | `20` |
| Method | `9` |
| Authorization | Owner, Creator |

Parameters:

| Parameter | Type | Description |
|---|---|---|
| `To` | `DATA`, 10 bytes | New owner |

---

# 7. SCT21 API

SCT21 extends SCT20 with amount locks and account locks.

## 7.1 `SCT21_CREATE`

Create an SCT21 contract.

| Item | Value |
|---|---|
| Type | `21` |
| Method | `0` |

Parameters:

| Parameter | Type | Description |
|---|---|---|
| `Name` | `STRING` | Contract/token name |
| `Symbol` | `STRING` | Symbol; length 3–10 |
| `Amount` | `QUANTITY` | Total supply |
| `LockAmount` | `QUANTITY` | Locked amount |
| `Owner` | `DATA`, 10 bytes | Contract owner |

---

## 7.2 SCT21 Transfer and Approval Operations

| Method | Type | Method No. | Purpose |
|---|---:|---:|---|
| `SCT21_TRANSFER` | 21 | 1 | Transfer SCT21 tokens |
| `SCT21_TRANSFER_FROM` | 21 | 2 | Delegated token transfer |
| `SCT21_APPROVE` | 21 | 3 | Delegate token allowance |
| `SCT21_DECREASE_APPROVE` | 21 | 4 | Decrease allowance |

---

## 7.3 SCT21 Supply and Contract State Operations

| Method | Type | Method No. | Purpose |
|---|---:|---:|---|
| `SCT21_MINT` | 21 | 5 | Mint additional tokens |
| `SCT21_BURN` | 21 | 6 | Burn tokens |
| `SCT21_PAUSE` | 21 | 7 | Pause contract |
| `SCT21_UNPAUSE` | 21 | 8 | Resume contract |
| `SCT21_TRANSFER_OWNER` | 21 | 9 | Transfer ownership |

---

## 7.4 SCT21 Lock Operations

| Method | Type | Method No. | Purpose |
|---|---:|---:|---|
| `SCT21_LOCK_TRANSFER` | 21 | 10 | Locked token transfer |
| `SCT21_UNLOCK_AMOUNT` | 21 | 11 | Unlock amount |
| `SCT21_RESTORE_LOCK_AMOUNT` | 21 | 12 | Restore locked amount |
| `SCT21_ADD_LOCK_AMOUNT` | 21 | 13 | Add locked amount |
| `SCT21_SUB_LOCK_AMOUNT` | 21 | 14 | Subtract locked amount |
| `SCT21_ACCOUNT_LOCK` | 21 | 15 | Lock account |
| `SCT21_ACCOUNT_UNLOCK` | 21 | 16 | Unlock account |

---

# 8. SCT22 API

SCT22 is an authorized-transfer token template.

| Method | Type | Method No. | Purpose |
|---|---:|---:|---|
| `SCT22_CREATE` | 22 | 0 | Create SCT22 contract |
| `SCT22_TRANSFER` | 22 | 1 | Transfer tokens |
| `SCT22_TRANSFER_FROM` | 22 | 2 | Delegated token transfer |
| `SCT22_MINT` | 22 | 3 | Mint tokens |
| `SCT22_BURN` | 22 | 4 | Burn tokens |
| `SCT22_PAUSE` | 22 | 5 | Pause contract |
| `SCT22_UNPAUSE` | 22 | 6 | Resume contract |
| `SCT22_TRANSFER_OWNER` | 22 | 7 | Transfer ownership |
| `SCT22_SET_AUTHORITY` | 22 | 8 | Set authority |

---

# 9. SCT30 API

SCT30 implements NFT-style unique item handling.

## 9.1 `SCT30_CREATE`

Create an SCT30 contract.

| Item | Value |
|---|---|
| Type | `30` |
| Method | `0` |

Parameters:

| Parameter | Type | Description |
|---|---|---|
| `Name` | `STRING` | Contract name |
| `Symbol` | `STRING` | Contract symbol; length 3–10 |
| `Owner` | `DATA`, 10 bytes | Contract owner |

Example:

```javascript
debug.getSCTRlp({
  "type": "0x1e",
  "method": "0x0",
  "params": {
    "name": "SymContract",
    "symbol": "SCN",
    "owner": "0x00020000000000060002"
  }
})
```

---

## 9.2 SCT30 Operations

| Method | Type | Method No. | Purpose |
|---|---:|---:|---|
| `SCT30_CREATE_ITEM` | 30 | 1 | Create item |
| `SCT30_TRANSFER` | 30 | 2 | Transfer item |
| `SCT30_TRANSFER_FROM` | 30 | 3 | Transfer delegated item |
| `SCT30_APPROVE` | 30 | 4 | Approve delegated item transfer |
| `SCT30_ITEM_PAUSE` | 30 | 5 | Pause item |
| `SCT30_ITEM_UNPAUSE` | 30 | 6 | Resume item |
| `SCT30_TRANSFER_OWNER` | 30 | 7 | Transfer ownership |

---

# 10. SCT40 API

SCT40 is a coupon-oriented contract template based on SCT30.

## 10.1 `SCT40_CREATE`

Create an SCT40 contract.

| Item | Value |
|---|---|
| Type | `40` |
| Method | `0` |

Parameters:

| Parameter | Type | Description |
|---|---|---|
| `Name` | `STRING` | Contract name |
| `Symbol` | `STRING` | Contract symbol; length 3–10 |
| `CPoint` | `QUANTITY` | Coupon point |
| `Owner` | `DATA`, 10 bytes | Contract owner |

---

## 10.2 SCT40 Operations

| Method | Type | Method No. | Purpose |
|---|---:|---:|---|
| `SCT40_CREATE_COUPON` | 40 | 1 | Create coupon items |
| `SCT40_TRANSFER` | 40 | 2 | Transfer coupon item |
| `SCT40_TRANSFER_FROM` | 40 | 3 | Delegated coupon transfer |
| `SCT40_APPROVE` | 40 | 4 | Approve delegated coupon transfer |
| `SCT40_COUPON_USE` | 40 | 5 | Use coupon |
| `SCT40_COUPON_PAUSE` | 40 | 6 | Pause coupon |
| `SCT40_COUPON_UNPAUSE` | 40 | 7 | Resume coupon |
| `SCT40_TRANSFER_OWNER` | 40 | 8 | Transfer ownership |

---

# 11. SCT50 API — Vote Contract

SCT50 handles Vote Contract creation and poll-creator management.

## 11.1 `SCT50_CREATE`

Create an SCT50 Vote Contract.

| Item | Value |
|---|---|
| Type | `50` |
| Method | `0` |

Parameters:

| Parameter | Type | Description |
|---|---|---|
| `Party Name` | `STRING` | Digital party name |
| `SCT20 Token Address` | `DATA` | Token address used for vote staking |
| `Owner` | `DATA` | Owner SymID |

---

## 11.2 `SCT50_ADD_POLL_CREATORS`

Register poll creators.

| Item | Value |
|---|---|
| Type | `50` |
| Method | `1` |

---

## 11.3 `SCT50_REMOVE_POLL_CREATORS`

Remove poll creators.

| Item | Value |
|---|---|
| Type | `50` |
| Method | `2` |

---

# 12. SCT51 API — Poll Contract

SCT51 handles poll creation and vote processing.

| Method | Type | Method No. | Purpose |
|---|---:|---:|---|
| `SCT51_CREATE_POLL` | 51 | 0 | Create poll contract |
| `SCT51_VOTE_IN_POLL` | 51 | 1 | Vote in poll |
| `SCT51_UNSTAKE_TOKENS` | 51 | 2 | Unstake poll tokens |
| `SCT51_EMERGENCY_STOP_POLL` | 51 | 3 | Emergency stop poll |
| `SCT51_FINISH_POLL` | 51 | 4 | Finish poll |
| `SCT51_WRITE_POLL_RESULTS` | 51 | 5 | Write poll results |

---

# 13. PQCFork Compatibility

This JSON-RPC SCT API documentation is carried into the SymVerse V3 documentation set without PQCFork-specific changes.

The SCT API namespace, SCT type/method model, and contract-template RPC conventions remain aligned with the existing SCT JSON-RPC API documentation.

---

# 14. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-16 | Imported the existing SymVerse JSON-RPC SCT API into the V3 documentation set as `json-rpc-sct-api.md`; no PQCFork-specific changes were introduced |

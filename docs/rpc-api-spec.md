# SymVerse V3 RPC/API Specification

> **Status:** Draft v0.1  
> **Date:** 2026-05-14  
> **Scope:** Public API documentation baseline for citizen, membership, and PQC-related access

---

# 1. Purpose

This document establishes the first API documentation structure for SymVerse V3.

It is intended to organize:

- Citizen query APIs
- Membership query APIs
- PQC-aware account/query behavior
- Transaction submission references
- Diagnostics and verification helpers

---

# 2. API Design Principles

V3 public APIs SHOULD be:

- Queryable through stable method names
- Explicit in parameter shape
- Consistent in return schema
- Suitable for scripting and test automation
- Able to expose membership relationships clearly

---

# 3. Citizen APIs

## 3.1 Query Citizen by SymID

Representative method:

```text
citizen_getCitizenBySymID
```

Representative parameters:

```json
[
  "0x...",
  "latest"
]
```

Expected use:

- Query citizen/account state
- Inspect membership-related fields if exposed
- Validate state after account creation or membership operations

---

## 3.2 Query Citizen by Nickname

Representative method:

```text
citizen_getCitizenByNick
```

Expected use:

- Resolve nickname to citizen state
- Validate unique nickname ownership
- Confirm nickname creation behavior

Exact method name and response schema should remain aligned with implementation.

---

# 4. Membership APIs

## 4.1 RefCode Query

The public API SHOULD expose a path to inspect a citizen's RefCode.

Potential behavior:

- Return storage-level numeric value
- Return formatted public string
- Or return both

This needs final standardization.

---

## 4.2 Referrer Query

The public API SHOULD expose who a citizen's referrer is, if set.

---

## 4.3 Link Query

A citizen query or dedicated membership API SHOULD expose:

- Forward `Link` targets

Example logical result:

```text
A links B, C, D
```

---

## 4.4 LinkedBy Query

A citizen query or dedicated membership API SHOULD expose:

- Reverse `LinkedBy` sources

Example logical result:

```text
B is linked by A
```

This reverse lookup is a required V3 membership behavior.

---

# 5. Transaction Submission APIs

V3 documentation SHOULD cover:

- Legacy transaction submission
- Raw transaction submission
- PQC transaction submission
- Membership transaction submission

Potential method families include:

```text
sym_sendTransaction
sym_sendRawTransaction
```

Actual method names and payloads must track current implementation.

---

# 6. Account Utility APIs

Developer workflows may rely on:

- Account creation
- Account unlock
- Balance query
- PQC account creation options

Representative examples in current operational scripts include:

```text
personal_newAccount
personal_unlockAccount
sym_getBalance
```

These APIs should be documented as environment/tooling references where relevant.

---

# 7. Response Documentation Style

Each finalized API entry SHOULD eventually include:

1. Method name
2. Purpose
3. Parameters
4. Example request
5. Example response
6. Error cases
7. Notes on version compatibility

---

# 8. Example Request Template

```bash
curl -s -X POST http://127.0.0.1:8545 \
  -H "Content-Type: application/json" \
  --data '{
    "jsonrpc":"2.0",
    "method":"citizen_getCitizenBySymID",
    "params":["0x...", "latest"],
    "id":1
  }'
```

---

# 9. Error Documentation

V3 API documentation SHOULD distinguish:

- Invalid parameters
- Unknown citizen
- Duplicate nickname
- Invalid membership operation
- Invalid signature or witness
- Insufficient authorization

---

# 10. Testing Alignment

API specification SHOULD align with `testing-guide.md`.

Every public operation documented here SHOULD ideally have:

- A minimal curl example
- A scriptable expected result
- A negative/failure scenario

---

# 11. Open Items

1. Final method inventory
2. Final response schema for citizen queries
3. RefCode display/query standard
4. Link and LinkedBy exposure shape
5. Membership transaction submission schema
6. PQC account query response schema

---

# 12. Revision History

| Version | Date | Notes |
|---|---|---|
| v0.1 | 2026-05-14 | Initial RPC/API specification draft |

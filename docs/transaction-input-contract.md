# Transaction Input Contract

This document defines the input contract for transactions received by AEGIS
for fraud analysis.

The contract specifies what information an external system must provide for a
transaction to be accepted. It does not describe internal fraud rules or decision
criteria.

---

## Purpose

- Define a clear and strict system entry boundary
- Protect the fraud engine from malformed or incomplete data
- Enable external integrations without exposing internal fraud rules
- Ensure deterministic and auditable fraud decisions

---

## Responsibility Model

- **External systems**
  - Provide valid transaction data
  - Comply with the defined contract

- **AEGIS**
  - Validates the contract
  - Normalizes and enriches technical context
  - Executes fraud analysis

External systems are not required to know fraud rules.

---

## Transaction Structure

A transaction represents a single financial event and contains:

- Identity
- Actor
- Monetary information
- Temporal information
- Origin context
- Destination context
- Optional risk-related metadata

---

## Required Fields

### Identity
- `transactionId`
  - Globally unique identifier
  - Used as an idempotency key

---

### Actor
- `userId`

---

### Monetary Information
- `amount`
  - Must be greater than zero

- `currency`
  - ISO 4217 currency code

- `transactionType`
  - Example values: `PAYMENT`, `TRANSFER_P2P`, `WITHDRAWAL`, `ONLINE_PURCHASE`

---

### Temporal Information
- `eventTimestamp`
  - Represents when the transaction occurred
  - Must not be in the future

---

### Origin
- `origin.originType`
  - Example values: `MOBILE_APP`, `WEB`, `ATM`, `POS`

---

### Destination
- `destination.destinationType`
- `destination.destinationId`

Every transaction must have a destination, even if it is logical rather than
physical.

---

## Optional Fields

### Actor
- `deviceId`

---

### Origin
- `origin.originLocation`
- `origin.originIp`

---

### Destination
- `destination.destinationLocation`
- `destination.destinationCountry`

---

### Context
- `channel`
  - Example values: `WEB`, `MOBILE`, `POS`, `API`

- `merchantCategory`

- `metadata`
  - Arbitrary key–value pairs for extensibility

---

## Validation Rules

AEGIS performs **contract validation** before fraud analysis.

A transaction is rejected if:
- Required fields are missing
- Field types are invalid
- `amount <= 0`
- Timestamps are invalid or inconsistent

Contract validation errors do not produce fraud decisions.

---

## Idempotency

- `transactionId` acts as the idempotency key
- Repeated submissions with the same identifier are ignored or rejected
- The system guarantees at-most-once ingestion semantics

---

## Example — P2P Transaction

```json
{
  "transactionId": "tx-9001",
  "userId": "user-123",
  "deviceId": "device-a81c",
  "amount": 150.00,
  "currency": "PEN",
  "transactionType": "TRANSFER_P2P",
  "eventTimestamp": "2026-01-31T14:22:10Z",
  "ingestionTimestamp": "2026-01-31T14:22:12Z",
  "origin": {
    "originType": "MOBILE_APP",
    "originLocation": {
      "latitude": -12.0464,
      "longitude": -77.0428
    },
    "originIp": "181.65.20.11"
  },
  "destination": {
    "destinationType": "ACCOUNT",
    "destinationId": "user-987",
    "destinationCountry": "PE"
  },
  "channel": "MOBILE",
  "metadata": {
    "note": "Lunch payment"
  }
}

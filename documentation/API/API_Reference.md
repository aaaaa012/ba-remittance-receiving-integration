# API Reference Guide

**Version:** v1.2  
**Base URL:** `https://parnterapi.gateway.integrate/v1/payouts`  
**Authentication:** Bearer Token (OAuth 2.0)

---

## 1. List Payouts
**Method:** `POST`  
**Endpoint:** `/list`  
**Description:** Fetches a paginated list of transactions available for payout (`READY_TO_PAY`).

### Request Body
| Field | Type | Required | Description |
|:---|:---|:---|:---|
| `acceptedOnly` | Boolean | No | Filter only pre-accepted transactions. Default: `false`. |
| `limit` | Integer | No | Max records to return. Default: `50`. |
| `offset` | Integer | No | Pagination offset. |

**Example:**
```json
{
  "acceptedOnly": true,
  "limit": 20,
  "offset": 0
}
```

### Response
```json
{
  "transactions": [
    {
      "transferNumber": "TX123456789",
      "amount": 500.00,
      "currency": "USD",
      "status": "READY_TO_PAY",
      "beneficiary": {
        "fullName": "Jane Doe",
        "account": "123-456-789"
      }
    }
  ]
}
```

---

## 2. Pre-Calculate Payout
**Method:** `POST`  
**Endpoint:** `/precalculate/{transferNumber}`  
**Description:** Calculates the exact payout amount after deducting fees and applying FX rates.

### Request Body
```json
{
  "payoutCurrency": "USD"
}
```

### Response
```json
{
  "transferNumber": "TX123456789",
  "grossAmount": 500.00,
  "fees": {
    "serviceFee": 5.00,
    "tax": 0.50
  },
  "netPayoutAmount": 494.50,
  "currency": "USD"
}
```

---

## 3. Prepare Payout (Lock)
**Method:** `POST`  
**Endpoint:** `/prepare/{transferNumber}`  
**Description:** Locks the transaction to prevent double spending. Updates status to `PREPARE_TO_PAY`.

**Example:**
```json
{
  "status": "PREPARE_TO_PAY",
  "lockId": "lock_987654321"
}
```

---

## 4. Execute Payout
**Method:** `POST`  
**Endpoint:** `/execute`  
**Description:** Triggers the final funds transfer to the beneficiary.

### Request Body
| Field | Type | Required | Description |
|:---|:---|:---|:---|
| `transferNumber` | String | Yes | Unique Transaction ID. |
| `lockId` | String | Yes | Lock ID received from Prepare step. |

**Example:**
```json
{
  "transferNumber": "TX123456789",
  "lockId": "lock_987654321"
}
```

### Response
```json
{
  "status": "COMPLETED",
  "transactionReference": "BANK_REF_001",
  "timestamp": "2023-10-25T10:00:00Z"
}
```

---

## Error Codes

| Code | Message | Description |
|:---|:---|:---|
| `400` | `INVALID_CURRENCY` | Currency mismatch with settlement account. |
| `404` | `TX_NOT_FOUND` | Transaction ID does not exist. |
| `409` | `TX_LOCKED` | Transaction is already being processed by another worker. |
| `422` | `LIMIT_EXCEEDED` | Amount exceeds beneficiary limit. |


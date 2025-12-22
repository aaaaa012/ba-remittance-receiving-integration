# Business Requirement Document (BRD)
**Project:** Remittance Receiving API Integration  
**Version:** 1.0  
**Status:** Approved  

---

## 1. Introduction
### 1.1 Purpose
The purpose of this document is to define the business rules, functional requirements, and constraints for integrating the inbound remittance (payout) API. This integration will enable the internal financial system to automatically receive and process money transfers from the partner network.

### 1.2 Scope
- **In Scope:** Transaction fetching, validation, payout processing, status reporting, and reconciliation.
- **Out of Scope:** Sending logic, FX rate management (handled by partner), and customer onboarding.

---

## 2. Business Goals
1. **Automation**: Achieve 100% STP (Straight-Through Processing) for valid transactions.
2. **Speed**: Reduce payout processing time to under 10 seconds per transaction.
3. **Compliance**: Ensure all payouts adhere to local AML and KYC regulations.

---

## 3. Business Rules (BR)

| Rule ID | Rule Name | Description |
|:---:|:---:|:---|
| **BR-01** | **Payout Eligibility** | Only transactions in `READY_TO_PAY` status are eligible for processing. |
| **BR-02** | **Pre-Payout Check** | The system must call the `PREPARE_TO_PAY` endpoint to lock the transaction before debiting any internal account. |
| **BR-03** | **Retry Logic** | If a payout fails due to a network error, the system must retry up to **2 times** with exponential backoff. |
| **BR-04** | **Failure Handling** | After 2 failed attempts or a hard logic failure (e.g., closed account), the system must mark the transaction as `ERROR`. |
| **BR-05** | **Finality** | A status of `ERROR` or `COMPLETED` is final. No further state transitions occur unless a manual refund is triggered. |
| **BR-06** | **Currency Match** | Payout currency must strictly match the settlement currency agreed upon in the partner contract. |
| **BR-07** | **Idempotency** | Every payout request must carry a unique `requestId` to prevent duplicate processing. |
| **BR-08** | **Audit Trail** | All state changes, API calls, and validation failures must be logged with timestamps for audit purposes. |

---

## 4. Functional Requirements

### 4.1 Transaction Polling
- **FR-01**: THe system shall poll the partner API every 60 seconds for new transactions.
- **FR-02**: The poll request must support pagination (`limit`, `offset`) to handle high volume.

### 4.2 Validation
- **FR-03**: The system must validate that the beneficiary account number exists and is active.
- **FR-04**: The system must check if the transaction amount exceeds the beneficiary's daily credit limit.

### 4.3 Payout Execution
- **FR-05**: On successful validation, the system shall credit the beneficiary's wallet/bank account.
- **FR-06**: Upon successful credit, the system must send a `COMPLETED` status update to the partner.

---

## 5. Assumptions & Constraints
- **A-01**: Partner API is available 99.9% of the time.
- **C-01**: Internal Core Banking System maintenance window is from 02:00 AM to 03:00 AM daily (payouts queued during this time).

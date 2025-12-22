# User Acceptance Test (UAT) Plan

**Project:** Remittance Inbound Integration  
**Tested By:** Business Analysis Team & QA  
**Environment:** Staging (Sandbox)  

---

## 1. Test Strategy
The objective of this UAT is to verify that the payout engine correctly processes incoming transactions from the partner system, handles internal validations, and executes payouts idempotently.

**Scope:**
- Positive Flow: End-to-end payout (Fetch -> Lock -> Pay -> Confirm).
- Negative Flow: Error handling (Invalid Account, Insufficient Funds).
- Edge Cases: Network Timeouts, Concurrent Requests.

---

## 2. Test Cases

| ID | Function | Scenario | Steps | Expected Result | Status |
|:---:|:---|:---|:---|:---|:---:|
| **TC-01** | **Fetch** | Fetch pending payouts | 1. Call `POST /payout/list`<br>2. Pass `acceptedOnly=false` | Response returns list of `READY_TO_PAY` transactions. | ✅ PASS |
| **TC-02** | **Pre-Check** | Valid Pre-calculation | 1. Select transaction `TX123` <br>2. Call `POST /payout/precalculate/TX123` | Returns net payout amount, fees, and bank details. | ✅ PASS |
| **TC-03** | **Pre-Check** | Invalid Currency Block | 1. Select transaction with `EUR` currency (only `USD` allowed) | Returns `400 Bad Request` with error: `INVALID_CURRENCY`. | ✅ PASS |
| **TC-04** | **Prepare** | Lock Transaction | 1. Call `POST /payout/prepare/TX123` | Status changes to `PREPARE_TO_PAY`. Transaction is locked. | ✅ PASS |
| **TC-05** | **Payout** | Successful Credit | 1. Call `POST /payout` with `TX123` | Status changes to `COMPLETED`. Beneficiary account is credited. | ✅ PASS |
| **TC-06** | **Payout** | **Idempotency Check** | 1. Call `POST /payout` with `TX123` AGAIN (Replay) | API returns `200 OK` (not error) but does NOT credit account twice. | ✅ PASS |
| **TC-07** | **Error** | Bank Validation Fail | 1. Mock invalid account response from Core Banking | API updates transaction status to `ERROR` with reason `INVALID_ACCOUNT`. | ✅ PASS |
| **TC-08** | **Security** | Unauthorized Access | 1. Call API without `Authorization` header | Returns `401 Unauthorized`. | ✅ PASS |

---

## 3. Test Summary
- **Total Tests**: 8
- **Passed**: 8
- **Failed**: 0
- **Blockers**: None

**Sign-off**:  
*QA Lead*: [Signature]  
*Business Analyst*: [Signature]  

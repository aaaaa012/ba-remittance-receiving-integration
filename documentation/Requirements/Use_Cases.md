# Use Case Specifications

**System:** Remittance Payout Engine  
**Module:** Inbound Payout Processing

---

## UC-01: View Available Payout Transactions
**Actor**: Operations Manager / System (Automated Job)  
**Description**: Retrieve a list of transactions that are ready to be paid out to beneficiaries.  
**Preconditions**:
- Partner has pushed transactions to the gateway.
- Transactions are in `READY_TO_PAY` status.

**Main Success Scenario**:
1. System polls the `GET /payout/list` endpoint.
2. System validates the `offset` and `limit` parameters.
3. API returns a JSON list of eligible transactions.
4. System logs the fetch event.

**Postconditions**:
- List of transactions is available for processing queue.

---

## UC-02: Validate Payout Details (KYC/AML)
**Actor**: Compliance Engine  
**Description**: Verify beneficiary details against internal watchlists and banking rules.  
**Preconditions**: Transaction data is fetched (UC-01).

**Main Success Scenario**:
1. System extracts beneficiary name, account number, and bank code.
2. System queries the Core Banking System for account existence.
3. System checks the PEP (Politically Exposed Person) database.
4. System returns "Validation Passed".

**Alternate Paths**:
- **A1**: Account Invalid -> Mark transaction as `ERROR` (Reason: "Invalid Account").
- **A2**: AML Hit -> Hold transaction and alert Compliance Officer.

---

## UC-03: Pre-Calculate Payout
**Actor**: Payout Engine  
**Description**: Lock in the final payout amount, deducting any applicable fees.  
**Main Success Scenario**:
1. System sends transaction ID to `POST /payout/precalculate`.
2. Engine calculates FX rates (if applicable) and service fees.
3. Engine returns `NetPayoutAmount`.

---

## UC-04: Execute Final Payout
**Actor**: Payout Engine  
**Description**: Debits the settlement account and credits the beneficiary.  
**Preconditions**: Validation (UC-02) passed, Pre-calc (UC-03) done, Status is `PREPARE_TO_PAY`.

**Main Success Scenario**:
1. System calls `POST /payout` with the secure token.
2. Core Banking System processes the credit leg.
3. System updates transaction status to `COMPLETED`.
4. Notification sent to Beneficiary (SMS/Email).

**Exception Scenarios**:
- **E1**: Insufficient Funds -> Retry later or alert Treasury.
- **E2**: Timeout -> Trigger Status Check Routine.

---

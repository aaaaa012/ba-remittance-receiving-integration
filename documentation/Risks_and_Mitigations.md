# Risks and Mitigations

## 1. Operational Risks

| Risk | Impact | Likelihood | Mitigation Strategy |
|:---|:---|:---:|:---|
| **Double Payout** | Financial loss due to paying the same transaction twice. | Low | Implement strict **idempotency keys** and a "Locking" state (`PREPARE_TO_PAY`) before execution. |
| **Incorrect Amount** | Customer receives wrong amount due to FX or fee calc error. | Medium | Centralized fee engine with unit tests; Pre-calculation step mandated before payout. |

## 2. Technical Risks

| Risk | Impact | Likelihood | Mitigation Strategy |
|:---|:---|:---:|:---|
| **Partner Downtime** | Inability to fetch new transactions. | Medium | Implement exponential backoff retry logic; Alerting system for downtime > 5 mins. |
| **Slow API Response** | Timeout during payout confirmation. | High | Async processing for heavy tasks; Webhook callbacks for status updates instead of long polling. |

## 3. Compliance Risks

| Risk | Impact | Likelihood | Mitigation Strategy |
|:---|:---|:---:|:---|
| **Sanctioned Entity Payout** | Heavy fines and loss of license. | Low | Real-time screening against OFAC/UN lists before any payout attempt (UC-02). |
| **Smurfing** | Money laundering via small frequent transactions. | Medium | Velocity checks: Limit number of transactions per beneficiary per day. |

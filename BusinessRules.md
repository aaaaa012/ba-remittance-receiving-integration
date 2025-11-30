# Business Rules – Receiving Integration

### BR-01: Only READY_TO_PAY transactions can enter payout flow  
### BR-02: PREPARE_TO_PAY must be called before payout  
### BR-03: After 2 failed attempts, system should allow setting ERROR  
### BR-04: Status ERROR is final unless refund is initiated  
### BR-05: Payout currency must match settlement rules  
### BR-06: Mandatory fields must be validated before payout  
### BR-07: Payout must be atomic and idempotent  
### BR-08: Audit logs must capture every state change  

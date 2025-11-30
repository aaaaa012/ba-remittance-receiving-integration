# Use Cases – Receiving Flow

## UC01 – View Available Payout Transactions
- Actor: Operations User  
- Goal: View all incoming remittances ready for payout  
- Preconditions: Transactions are in READY_TO_PAY  

---

## UC02 – Validate Payout Details
- Ensures receiver details are correct  
- Validates bank or wallet details  
- Checks required additional fields  

---

## UC03 – Pre-Calculate Payout Amount
- Get computed payout amount  
- Retrieve fees and final payable amount  
- Check disbursement currency consistency  

---

## UC04 – Prepare Payout
- Lock transaction  
- Move to PREPARE_TO_PAY  

---

## UC05 – Execute Payout
- Trigger final payout    
- Update status to COMPLETED  

---

## UC06 – Set Payout Error
- Marks transaction as ERROR  
- Moves to refund flow when applicable  

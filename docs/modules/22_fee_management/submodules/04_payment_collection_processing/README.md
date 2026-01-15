# PAYMENT COLLECTION & PROCESSING

**Module:** Fee Management  
**Submodule Code:** FEE-PAYMENT-004  
**Category:** Financial Core  
**Priority:** Critical (P0)

## Overview

Accepts and processes fee payments through multiple channels (online, cash, cheque, bank transfer), validates transactions, allocates payments to invoices, and generates receipts.

## Purpose

Provide flexible payment options to parents, ensure secure transaction processing, maintain accurate payment records, and automate payment allocation to outstanding invoices.

## Key Features

### 4.1 Payment Modes

**Online Payment Gateway:**
- Credit/Debit Card, Net Banking, UPI, Wallets
- Gateway Fee: 1.5-2%
- Instant Confirmation

**Cash:**
- Paid at school accounts office
- Receipt printed immediately
- Daily limit: ₹50,000

**Cheque:**
- Payable to school
- Clearance time: 2-3 days
- Post-dated cheques accepted

**Bank Transfer (NEFT/RTGS):**
- Direct to school account
- Reconciliation required

**Standing Instruction:**
- Auto-debit monthly
- Setup once

### 4.2 Payment Processing Workflow

```
FUNCTION process_payment(payment_details):
  // Validate payment
  IF payment_details.amount <= 0:
    RETURN ERROR "Invalid amount"
  END IF
  
  student = GET student(payment_details.student_id)
  IF student.status != "ACTIVE":
    RETURN ERROR "Student not active"
  END IF
  
  // Process based on mode
  IF payment_details.mode = "ONLINE":
    gateway_response = CALL payment_gateway_API(payment_details)
    IF gateway_response.status = "SUCCESS":
      transaction_id = gateway_response.transaction_id
    ELSE:
      RETURN ERROR "Payment failed"
    END IF
  ELSE IF payment_details.mode = "CASH":
    transaction_id = GENERATE_RECEIPT_NUMBER()
    PRINT physical_receipt()
  ELSE IF payment_details.mode = "CHEQUE":
    transaction_id = "CHQ/" + payment_details.cheque_number
    payment_details.status = "PENDING_CLEARANCE"
  END IF
  
  // Create payment record
  payment = NEW Payment
  payment.student = student
  payment.amount = payment_details.amount
  payment.mode = payment_details.mode
  payment.transaction_id = transaction_id
  payment.payment_date = TODAY
  
  // Allocate to invoices (oldest first)
  outstanding_invoices = GET invoices WHERE student = student AND status = "UNPAID"
  remaining_amount = payment_details.amount
  
  FOR each invoice IN outstanding_invoices:
    IF remaining_amount >= invoice.outstanding_amount:
      invoice.paid_amount += invoice.outstanding_amount
      remaining_amount -= invoice.outstanding_amount
      invoice.status = "PAID"
    ELSE:
      invoice.paid_amount += remaining_amount
      invoice.outstanding_amount -= remaining_amount
      remaining_amount = 0
      invoice.status = "PARTIALLY_PAID"
      BREAK
    END IF
  END FOR
  
  // Handle excess payment
  IF remaining_amount > 0:
    student.advance_payment = remaining_amount
  END IF
  
  // Send confirmation
  SEND SMS(student.parent_mobile, "Payment ₹{amount} received")
  SEND EMAIL(student.parent_email, payment_receipt.pdf)
  
  RETURN payment
END FUNCTION
```

### 4.3 Payment Receipt

```
═══════════════════════════════════════
       XYZ SCHOOL FEE RECEIPT
═══════════════════════════════════════
Receipt No: RCT/2024/001234
Date: July 10, 2024

Student: Rohan Sharma, Grade 8B
Admission No: 2018/08/0456

───────────────────────────────────────
Invoice: INV/2024/0001234
Invoice Amount: ₹28,500
Previous Dues: ₹5,000
───────────────────────────────────────
Amount Paid: ₹33,500
Payment Mode: Online (UPI)
Transaction ID: 424264859621
───────────────────────────────────────
Outstanding Balance: ₹0

Received by: Mrs. Gupta (Accounts)
═══════════════════════════════════════
```

### 4.4 Payment Allocation Logic

**Oldest Invoice First:**
- Outstanding: April (₹10,000), July (₹28,500)
- Payment: ₹30,000
- Allocation: April fully paid (₹10,000), July partially paid (₹20,000)

**Advance Payment:**
- Payment: ₹50,000, Total dues: ₹40,000
- Excess: ₹10,000 stored as advance
- Auto-adjusted against next invoice

### 4.5 Failed Payment Handling

**Online Failure:** Gateway timeout, insufficient balance
**Cheque Bounce:** Payment status "BOUNCED", bounce charges ₹500 added
**Refund Processing:** Duplicate payment refunded to original source (5-7 days)

## Data Fields

```
payment_id (PK)
student_id (FK)
payment_date
amount
payment_mode (CASH/CHEQUE/ONLINE/NEFT/RTGS)
transaction_id
gateway_transaction_id
cheque_number
cheque_date
bank_name
status (SUCCESS/PENDING/FAILED/BOUNCED)
allocated_to_invoices (JSON)
advance_amount
received_by
receipt_number
receipt_generated_date
```

## Integration Points

### Connects TO:
1. **Invoice Module** - Allocate payment to invoices
2. **Receipt Module** - Generate payment receipt
3. **Accounts Module** - Post to general ledger
4. **Communication Module** - Send payment confirmation

### Receives FROM:
1. **Payment Gateway** - Online payment status
2. **Bank** - Cheque clearance status

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

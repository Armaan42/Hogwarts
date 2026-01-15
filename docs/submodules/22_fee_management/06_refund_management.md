# REFUND MANAGEMENT

**Module:** Fee Management  
**Submodule Code:** FEE-REFUND-006  
**Category:** Financial Core  
**Priority:** High (P1)

## Overview

Processes fee refunds for student withdrawals, overpayments, duplicate transactions, and retroactive fee waivers.

## Purpose

Handle refund requests efficiently, calculate accurate refund amounts based on policy, process payments, and maintain audit trail.

## Key Features

### 6.1 Refund Scenarios

- Student Withdrawal (mid-year)
- Overpayment (parent paid twice)
- Duplicate Transaction (technical error)
- Fee Waiver Granted Retroactively

### 6.2 Refund Policy

**Refundable Components:**
- Tuition (pro-rata for unused months)
- Transport (pro-rata)
- Hostel (pro-rata)
- Security deposit (100%)

**Non-refundable:**
- Admission fee
- Registration fee
- Annual charges (if year started)
- Examination fee (if exams taken)

**Refund Calculation:**
```
Withdrawal Date: September 20, 2024
Months Attended: 6 months (Apr-Sep)
Months Unused: 6 months (Oct-Mar)

Annual Tuition: ₹1,20,000
Used (6 months): ₹60,000
Refundable: ₹60,000

Security Deposit: ₹10,000 (100%)
Total Refund: ₹70,000
```

### 6.3 Refund Process

```
FUNCTION process_refund(student, refund_request):
  // Verify eligibility
  IF student.status != "WITHDRAWN":
    RETURN ERROR "Only withdrawn students eligible"
  END IF
  
  // Check clearances
  IF NOT library_clearance(student):
    RETURN ERROR "Library books not returned"
  END IF
  
  IF student.outstanding_dues > 0:
    RETURN ERROR "Outstanding dues must be cleared"
  END IF
  
  // Calculate refundable amount
  months_attended = MONTHS_BETWEEN(academic_year_start, withdrawal_date)
  months_remaining = 12 - months_attended
  tuition_per_month = student.annual_tuition / 12
  refund_amount = tuition_per_month * months_remaining
  
  // Add security deposit
  refund_amount += student.security_deposit
  
  // Deduct damages/fines
  refund_amount -= student.damage_charges
  refund_amount -= student.library_fines
  
  // Create refund record
  refund = NEW Refund
  refund.student = student
  refund.amount = refund_amount
  refund.approved_by = principal
  refund.status = "APPROVED"
  refund.method = "NEFT"
  refund.expected_date = TODAY + 15 days
  
  SEND EMAIL(student.parent_email, refund_approval_letter.pdf)
  
  RETURN refund
END FUNCTION
```

### 6.4 Refund Timeline

- Approval: 7 working days
- Processing: 15 working days
- Payment: 30 days total
- Mode: Cheque or NEFT

## Data Fields

```
refund_id (PK)
student_id (FK)
refund_type (WITHDRAWAL/OVERPAYMENT/DUPLICATE/WAIVER)
refund_amount
calculation_breakdown (JSON)
requested_date
approved_by
approval_date
payment_method (CHEQUE/NEFT)
payment_date
status (REQUESTED/APPROVED/PROCESSING/COMPLETED/REJECTED)
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

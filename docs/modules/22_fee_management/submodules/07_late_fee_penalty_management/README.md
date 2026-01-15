# LATE FEE & PENALTY MANAGEMENT

**Module:** Fee Management  
**Submodule Code:** FEE-PENALTY-007  
**Category:** Financial Core  
**Priority:** High (P1)

## Overview

Automated penalty calculation for late payments, grace period management, late fee structure definition, and waiver processing.

## Key Features

### Grace Period
- Due date: July 15
- Grace period: 15 days (till July 30)
- No penalty if paid by July 30

### Late Fee Structure
- Days 16-30: ₹100/week
- Days 31-60: ₹200/week
- Days 60+: ₹300/week
- Maximum: ₹1,000 per invoice

### Calculation Example
```
Invoice Due: July 15
Payment Date: August 20
Days Overdue: 36 days

First 15 days: Grace (₹0)
Days 16-30: 2 weeks × ₹100 = ₹200
Days 31-36: 1 week × ₹200 = ₹200
Total Late Fee: ₹400
```

### Auto-calculation
- Daily job at midnight
- Checks unpaid invoices
- Calculates late fee
- Adds to student account
- Parent notified

### Waiver Requests
- Genuine reasons (medical emergency, job loss)
- Principal reviews
- Full or partial waiver
- Auto-waiver for technical issues

## Data Fields
```
late_fee_id (PK)
invoice_id (FK)
days_overdue
late_fee_amount
calculation_date
waiver_requested (boolean)
waiver_amount
waiver_approved_by
status
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

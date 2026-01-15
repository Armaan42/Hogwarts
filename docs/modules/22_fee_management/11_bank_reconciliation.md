# BANK RECONCILIATION

**Module:** Fee Management  
**Submodule Code:** FEE-RECON-011  
**Category:** Financial Operations  
**Priority:** Critical (P0)

## Overview

Matches ERP payment records with bank statements, identifies discrepancies, handles cheque clearances, and maintains reconciliation reports.

## Key Features

### Reconciliation Process

**Daily Bank Statement Import:**
- Download from bank portal
- Format: CSV/Excel
- Import into ERP

**Auto-matching:**
- Match criteria: Amount (exact), Date (±2 days), Reference
- Match rate: ~90%

**Manual Reconciliation:**
- Unmatched entries reviewed
- Common issues: Date mismatch, Amount mismatch, Missing reference

### Reconciliation Report
```
Date: July 31, 2024
ERP Payments:        ₹45,00,000
Bank Credits:        ₹44,85,000
Difference:          ₹15,000

Matched:             142 (95%)
Unmatched:           7 (5%)

Unmatched Details:
1. ERP: ₹28,500 (Rohan) - Not in bank
2. Bank: ₹32,000 - No reference
3. Amount mismatch: ERP ₹25,000, Bank ₹24,900
```

### Cheque Clearance Tracking
- Status: DEPOSITED, CLEARED, BOUNCED, PENDING
- Auto-update from bank statement
- Parent notified on clearance

## Data Fields
```
reconciliation_id (PK)
reconciliation_date
bank_statement_file
erp_payment_count
bank_credit_count
matched_count
unmatched_count
total_matched_amount
total_unmatched_amount
reconciled_by
status
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

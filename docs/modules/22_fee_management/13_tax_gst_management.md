# TAX & GST MANAGEMENT

**Module:** Fee Management  
**Submodule Code:** FEE-TAX-013  
**Category:** Compliance  
**Priority:** High (P1)

## Overview

Handles tax calculations, GST applicability, filing requirements, and tax certificate generation.

## Key Features

### GST Applicability

**Exempt (0% GST):**
- Pre-school education
- School education (Grades 1-12)
- Examination fees

**Taxable (18% GST):**
- Transport (commercial service)
- Hostel (boarding, lodging)
- Coaching classes

### Invoice with GST
```
Tuition Fee:          ₹80,000 (GST exempt)
Transport Fee:        ₹20,000
GST @ 18%:            ₹3,600
Total:                ₹1,03,600

GSTIN: 07ABCDE1234F1Z5
SAC Code: 996421
```

### GST Filing
- Monthly returns: GSTR-1, GSTR-3B
- Data auto-populated from ERP
- Reconciliation with GST portal

### Tax Certificates
- 80C Certificate for tuition fee
- Generated annually in April
- Parent can download from portal

## Data Fields
```
tax_record_id (PK)
invoice_id (FK)
taxable_amount
tax_rate
tax_amount
gstin
sac_code
filing_period
filed_date
status
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

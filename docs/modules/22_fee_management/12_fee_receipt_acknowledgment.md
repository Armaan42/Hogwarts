# FEE RECEIPT & ACKNOWLEDGMENT

**Module:** Fee Management  
**Submodule Code:** FEE-RECEIPT-012  
**Category:** Financial Operations  
**Priority:** Critical (P0)

## Overview

Issues official payment acknowledgments, manages receipt series, handles reprints, and maintains audit trail.

## Key Features

### Receipt Types
- **Payment Receipt:** For actual payments, Receipt: RCT/2024/001234
- **Acknowledgment Receipt:** For cheque/DD (not cleared), "Subject to realization"
- **Advance Receipt:** For excess payment, credits student account

### Receipt Series Management
- Financial year based: 2024-25: RCT/2024/XXXXXX
- Sequential numbering
- No gaps, audit-friendly

### Receipt Reprint
- Parent request via portal
- Marked: "DUPLICATE - NOT VALID FOR TAX"
- Watermarked
- Audit log maintained

## Data Fields
```
receipt_id (PK)
receipt_number
receipt_type
payment_id (FK)
student_id (FK)
amount
receipt_date
generated_by
reprint_count
reprint_log (JSON)
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

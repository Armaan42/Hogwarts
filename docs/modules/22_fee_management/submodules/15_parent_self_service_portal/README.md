# PARENT SELF-SERVICE PORTAL

**Module:** Fee Management  
**Submodule Code:** FEE-PORTAL-015  
**Category:** User Interface  
**Priority:** High (P1)

## Overview

Empowers parents with self-service fee management capabilities including dashboard, invoice viewing, online payment, receipt download, and query management.

## Key Features

### Fee Dashboard
- Current outstanding: ₹28,500
- Next due date: July 15
- Payment history: Last paid ₹30,000 on June 10
- Upcoming invoices: October (estimated ₹28,500)

### View Invoices
- All invoices (pending + paid)
- Download PDF
- Filter by date, status

### Pay Online
- One-click payment
- Select payment method
- Instant confirmation

### Payment History
- All past payments
- Download receipts
- Year-wise summary

### Payment Scheduling

**Auto-pay Setup:**
- Link credit card/bank account
- Auto-debit on due date
- Email notification 3 days before

**Scheduled Payments:**
- Schedule: "Pay ₹28,500 on July 15"
- System auto-processes

### Receipt Download
- All receipts from admission
- Searchable, filterable
- Bulk download option

### Support & Queries

**Raise Query:**
- "I paid but not reflected"
- Attach proof
- Response within 24 hours

**Live Chat:**
- Office hours
- Bot + Human support

## Data Fields
```
portal_session_id (PK)
parent_id (FK)
login_time
logout_time
actions_performed (JSON)
ip_address
device_type
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

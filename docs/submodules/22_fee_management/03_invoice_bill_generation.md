# INVOICE & BILL GENERATION

**Module:** Fee Management  
**Submodule Code:** FEE-INVOICE-003  
**Category:** Financial Core  
**Priority:** Critical (P0)

## Overview

Generates payment invoices for students based on their fee assignments, handles quarterly/annual billing cycles, manages invoice delivery, and supports amendments and credit notes.

## Purpose

Automate invoice generation for all students, ensure timely delivery through multiple channels, maintain invoice history, and handle corrections when needed.

## Key Features

### 3.1 Invoice Types

**Admission Invoice:**
- Generated at admission
- Components: Admission fee (₹15,000), Security deposit (₹10,000), First quarter tuition (₹30,000)
- Total: ₹55,000
- Due date: Within 7 days of admission

**Quarterly Invoice:**
- Generated at start of each quarter
- Example July invoice: Tuition (3 months): ₹20,000, Exam fee: ₹1,500, Activity fee: ₹1,000, Transport: ₹6,000
- Total: ₹28,500
- Due date: 15th of first month

**Annual Invoice:**
- For parents choosing annual payment
- All components for 12 months
- Early bird discount applied (5%)
- Due date: April 30

**Ad-hoc Invoice:**
- One-time charges
- Lost library book: ₹500, Damage to lab equipment: ₹2,000
- Due date: 15 days from invoice date

### 3.2 Invoice Generation Process

**Automatic Generation:**
- Scheduled job runs on 1st of each quarter
- Generates invoices for all active students
- Based on their fee assignment
- Adjusts for any concessions/discounts

**Invoice Content:**
```
Invoice Number: INV/2024/0001234
Invoice Date: July 1, 2024
Due Date: July 15, 2024

Student: Rohan Sharma, Grade 8B, Adm No: 2018/08/0456

Tuition Fee (July-Sep)         ₹20,000
Examination Fee                ₹1,500
Activity Fee                   ₹1,000
Transport Fee (July-Sep)       ₹6,000
─────────────────────────────
Subtotal                       ₹28,500
GST @ 0%                       ₹0
─────────────────────────────
Total Amount Due               ₹28,500
Previous Outstanding           ₹5,000
═════════════════════════════
TOTAL PAYABLE                  ₹33,500
```

### 3.3 Invoice Delivery

**Email:** PDF attachment to parent email
**SMS:** Short summary + payment link
**Parent Portal:** Available for download
**WhatsApp:** Invoice PDF via WhatsApp Business API

### 3.4 Bulk Invoice Generation

- 2,000 students processed in 10 minutes
- Queue-based processing
- Error handling for incomplete data
- Batch reports generated

### 3.5 Invoice Corrections

**Amendment Invoice:**
- Original invoice had error
- Amendment issued with corrections
- Original marked as "Superseded"

**Credit Note:**
- Refund scenario
- Credit note issued: CN/2024/0123
- Adjusted against future invoices

## Data Fields

```
invoice_id (PK)
invoice_number
invoice_date
due_date
student_id (FK)
academic_year
quarter
invoice_type (ADMISSION/QUARTERLY/ANNUAL/ADHOC)
line_items (JSON)
subtotal
tax_amount
total_amount
previous_outstanding
total_payable
status (DRAFT/ISSUED/PAID/PARTIALLY_PAID/CANCELLED)
generated_by
sent_date
payment_received_date
```

## Business Logic

```
FUNCTION generate_quarterly_invoice(student, quarter):
  // Get student fee assignment
  fee_assignment = GET student_fee_assignment(student)
  
  // Calculate quarterly amount
  quarterly_tuition = fee_assignment.tuition_fee / 4
  quarterly_transport = fee_assignment.transport_fee / 4
  
  // Create invoice
  invoice = NEW Invoice
  invoice.student = student
  invoice.quarter = quarter
  invoice.invoice_number = GENERATE_INVOICE_NUMBER()
  invoice.invoice_date = QUARTER_START_DATE(quarter)
  invoice.due_date = invoice.invoice_date + 15 days
  
  // Add line items
  invoice.add_line_item("Tuition Fee", quarterly_tuition)
  invoice.add_line_item("Transport Fee", quarterly_transport)
  
  // Add exam fee if applicable
  IF quarter IN [1, 3]:  // Term exams in Q1 and Q3
    invoice.add_line_item("Examination Fee", fee_assignment.exam_fee / 2)
  END IF
  
  // Calculate totals
  invoice.subtotal = SUM(invoice.line_items)
  invoice.tax_amount = CALCULATE_GST(invoice.line_items)
  invoice.total_amount = invoice.subtotal + invoice.tax_amount
  
  // Add previous outstanding
  invoice.previous_outstanding = GET student_outstanding(student)
  invoice.total_payable = invoice.total_amount + invoice.previous_outstanding
  
  invoice.status = "ISSUED"
  
  // Save and deliver
  SAVE(invoice)
  DELIVER_INVOICE(invoice)
  
  RETURN invoice
END FUNCTION
```

## Integration Points

### Connects TO:
1. **Student Fee Assignment** - Get fee breakdown
2. **Communication Module** - Deliver invoices via email/SMS/WhatsApp
3. **Payment Module** - Link invoice to payments
4. **Parent Portal** - Display invoices

### Receives FROM:
1. **Fee Assignment Module** - Student fee details
2. **Outstanding Module** - Previous dues

## User Workflows

### Workflow: Generate Quarterly Invoices

**Actor:** System (Automated)

**Steps:**
1. Scheduled job triggers on July 1st
2. System retrieves all active students (2,000 students)
3. For each student:
   - Get fee assignment
   - Calculate quarterly amount
   - Generate invoice
   - Add previous outstanding
4. Batch process completes in 10 minutes
5. Invoices delivered via email/SMS/portal
6. Summary report generated
7. Finance team notified

**Expected Outcome:** All students receive quarterly invoices on time.

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

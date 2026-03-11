# INVOICE & BILL GENERATION

**Module:** Fee Management  
**Submodule Code:** FEE-BILL-003  
**Category:** Financial Operations  
**Priority:** Critical (P0)  
**Owners:** Accounts Manager, IT Administrator

---

## OVERVIEW

The Invoice Generation submodule handles the creation, scheduling, and distribution of fee demands (bills) to parents. It transforms the fee assignments into formal, legally compliant financial documents. This system supports bulk generation for thousands of students simultaneously, handles installment logic, applies taxes where necessary, and manages the lifecycle of a bill from "Draft" to "Overdue".

### Purpose

To automate the massive recurring task of billing, ensuring that every student receives the correct demand at the correct time. It minimizes revenue leakage, ensures tax compliance (GST), and provides parents with clear, detailed breakdowns of their payment obligations.

### Scope

-   **Bulk Generation:** Generating quarterly/monthly bills for the entire school in one click.
-   **Ad-Hoc Billing:** Creating single invoices for specific charges (e.g., lost book fine).
-   **Installment Management:** Breaking annual fees into agreed payment schedules.
-   **Document Formatting:** Generating PDF invoices with school branding and QR codes.
-   **Distribution:** Automating delivery via Email, SMS, and Parent Portal push notifications.
-   **Taxation:** Applying accurate tax rules to taxable line items.

---

## KEY FEATURES

### 1. Unified Billing Engine

**Feature Description:**
A robust engine capable of processing thousands of invoices in minutes. It pulls data from the "Student Fee Assignment" module and generates a formal "Fee Demand".

** Capabilities:**
*   **Batch Processing:** "Generate Q3 Fees for Grades 1-5" as a single job.
*   **Preview Mode:** Allows financing team to preview sample invoices before finalizing.
*   **Consolidation:** Combines Tuition, Transport, and pending Arrears into a single "Net Payable" invoice.
*   **Rounding Logic:** Configurable rounding (Human-friendly numbers) or exact decimals.

### 2. Flexible Installment Scheduling

**Feature Description:**
Supports various payment frequencies to accommodate different parent capabilities.

**Plans:**
1.  **Standard:** 4 Quarterly Installments (April, July, Oct, Jan).
2.  **Monthly:** 12 Installments (for staff/special cases).
3.  **One-Shot:** 100% Upfront (often incentivized with a discount).
4.  **Custom:** Special arrangement for financial hardship (e.g., 50% now, 50% in 2 months).

**Algorithm: Due Date Logic**
*   Invoices are generated *before* the due date (e.g., generated on 20th, Due on 1st).
*   Logic handles "Grace Period" before verifying "Late Fee".

### 3. Smart Tax Calculation (GST)

**Feature Description:**
For schools operating in jurisdictions with Service Tax/GST.
*   **Logic:** Checks `fee_head.is_taxable`.
*   **Calculation:** `Tax = Base_Amount * Tax_Rate`.
*   **Reporting:** invoice clearly separates "Taxable Value" and "Tax Amount" for accounting transparency.
*   **Exemptions:** Automatically handles items like "Tuition" (often exempt) vs. "Uniform" (taxable).

### 4. Digital Distribution & Tracking

**Feature Description:**
Ensures bills reach the payer.
*   **Multi-Channel:** Email (PDF attachment), SMS (Short link), App Notification.
*   **Read Receipts:** Tracks if the parent opened the invoice email/portal page.
*   **Reminders:** Auto-triggers follow-ups 3 days before Due Date.

---

## DATABASE SCHEMA

### 1. Invoices (`fee_invoices`)
The header record for a bill.

```sql
CREATE TABLE fee_invoices (
    invoice_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    invoice_number VARCHAR(50) UNIQUE NOT NULL, -- e.g. INV-2026-00123
    student_id INT NOT NULL,
    academic_year_id INT NOT NULL,
    
    -- Dates
    generation_date DATE DEFAULT CURRENT_DATE,
    due_date DATE NOT NULL,
    valid_until DATE, -- optional expiry
    
    -- Financials
    total_base_amount DECIMAL(12,2),
    total_tax_amount DECIMAL(12,2),
    total_discount_amount DECIMAL(12,2),
    net_payable_amount DECIMAL(12,2),
    
    -- Status
    payment_status ENUM('UNPAID', 'PARTIALLY_PAID', 'PAID', 'OVERDUE', 'CANCELLED'),
    amount_paid DECIMAL(12,2) DEFAULT 0.00,
    balance_amount DECIMAL(12,2),
    
    -- Metadata
    installment_number INT, -- e.g. 3 of 4
    remarks TEXT,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Invoice Line Items (`fee_invoice_items`)
Detailed breakdown of the bill.

```sql
CREATE TABLE fee_invoice_items (
    item_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    invoice_id BIGINT NOT NULL,
    fee_head_id INT NOT NULL,
    
    description VARCHAR(255), -- e.g. "Tuition Fee - Q3"
    
    amount DECIMAL(12,2) NOT NULL,
    tax_rate DECIMAL(5,2) DEFAULT 0.00,
    tax_amount DECIMAL(12,2) DEFAULT 0.00,
    
    -- Links to master assignment
    source_map_id INT, 
    
    FOREIGN KEY (invoice_id) REFERENCES fee_invoices(invoice_id),
    FOREIGN KEY (fee_head_id) REFERENCES fee_heads(head_id)
);
```

### 3. Installment Definitions (`fee_installment_plans`)
Configuration for when bills should be generated.

```sql
CREATE TABLE fee_installment_plans (
    plan_id INT PRIMARY KEY AUTO_INCREMENT,
    plan_name VARCHAR(50), -- "Quarterly Standard"
    
    total_installments INT,
    frequency ENUM('MONTHLY', 'QUARTERLY', 'TERM'),
    
    is_default BOOLEAN DEFAULT FALSE
);

CREATE TABLE fee_installment_schedules (
    schedule_id INT PRIMARY KEY AUTO_INCREMENT,
    plan_id INT,
    academic_year_id INT,
    
    installment_seq INT, -- 1, 2, 3...
    generation_date DATE,
    due_date DATE,
    
    grade_applicability JSON -- e.g. [1,2,3,4,5]
);
```

---

## BUSINESS RULES

### Rule 1: Invoice Immutability
Once an invoice is "Published" and sent to a parent, it **cannot** be edited.
*   **Correction:** Use Credit Notes (to reduce amount) or Supplementary Invoices (to increase amount).
*   **Deletion:** Only "Draft" invoices can be deleted. Published invoices must be "Cancelled".

### Rule 2: Overdue Logic
*   Status automatically flips to `OVERDUE` if `CurrentDate > DueDate` AND `Balance > 0`.
*   Trigger: Nightly cron job runs `check_overdue_invoices()`.

### Rule 3: Sequential Payment
*   Parents must ideally pay the oldest invoice first.
*   System enforces: "You have pending dues from Q1. Please clear Q1 before paying Q2" OR allocates payment to Q1 automatically.

### Rule 4: Rounding
*   Final Net Payable is usually rounded to the nearest integer.
*   Round-off difference is stored in a separate accounting bucket ("Round Off Account") to ensure ledger balancing.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Payment Gateway:** Pushes the `InvoiceID` and `NetPayable` to the PG for online collection.
*   **To Communication Module:** Content for Email/SMS templates.
*   **To Ledger:** Debit "Accounts Receivable" (Asset) and Credit "Fee Income" (Revenue) upon generation.

### Inbound Relationships
*   **From Student Assignment:** Reads `student_fee_maps` to know what to bill.
*   **From Payment Module:** Updates `payment_status` when money is received.

---

## USER WORKFLOWS

### Workflow 1: Bulk Generation for Q3 (Standard)
**Actor:** Accounts Officer

1.  **Dashboard:** Navigate to Invoices > Bulk Generate.
2.  **Filter:** Select "Quarter 3 (Oct-Dec)" and "All Grades".
3.  **Scan:** System scans 2,500 students. Indentifies 2,480 active students eligible for billing.
4.  **Preview:** Shows Summary: "Total Expected Revenue: ₹5.2 Crores".
5.  **Run:** Click "Generate".
6.  **Processing:** Background job runs (takes ~5 mins).
7.  **Result:** 2,480 Invoices created in DRAFT state.
8.  **Publish:** User reviews sample 5 invoices. Clicks "Publish & Send".
9.  **Action:** System emails PDFs to all parents.

### Workflow 2: Generating a Fine Invoice (Ad-Hoc)
**Actor:** Librarian / Admin

1.  **Context:** Student lost a library book.
2.  **Dashboard:** Go to Student Profile > Fees > Create Invoice.
3.  **Head:** Select "Library Fine".
4.  **Amount:** Enter ₹450.
5.  **Remark:** "Lost copy of 'Advanced Physics', Accession #3092".
6.  **Create:** Invoice #INV-ADHOC-99 generates immediately.
7.  **Outcome:** Shows up in Parent Portal instantly as "Due Now".

---

## REAL-WORLD SCENARIOS

### Scenario A: The "Partial" Fee Payer
*   **Context:** Parent receives Q1 Bill for ₹50,000. Pays only ₹20,000.
*   **System Action:**
    *   Invoice Status: `PARTIALLY_PAID`.
    *   Balance: ₹30,000.
    *   **Next Month:** System generates "Statement of Account" reminder showing the ₹30k carry-forward.

### Scenario B: Invoice Cancellation
*   **Context:** Invoice generated for "Transport Fee" but student withdrew from bus yesterday.
*   **Action:** Admin "Cancels" Invoice #101.
*   **Effect:**
    *   Invoice status -> CANCELLED.
    *   Accounting Reversal (Credit Note) passed automatically.
    *   Parent sees "Cancelled" watermark on the bill in portal.

---

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** January 20, 2026

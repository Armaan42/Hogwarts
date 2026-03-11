# ACCOUNTING & TALLY INTEGRATION

**Module:** Fee Management  
**Submodule Code:** FEE-ACC-015  
**Category:** Financial Control & Compliance  
**Priority:** High (P1)  
**Owners:** Chartered Accountant (CA), Finance Controller

---

## OVERVIEW

The Accounting Integration submodule is the strict, compliance-driven backend of the Fee system. While the Front-Office cares about "Invoices" and "Receipts", the Back-Office (Auditors and CAs) cares about "Debits", "Credits", "Ledgers", and "Journals". This module translates every single operational action in the Fee module into standard Double-Entry Bookkeeping vouchers and continuously synchronizes this data with enterprise accounting software like Tally ERP 9, Tally Prime, SAP, or QuickBooks.

### Purpose

To ensure statutory financial compliance without duplicating data entry work. It bridges the gap between Student-centric tracking (the ERP) and Chart of Accounts-centric tracking (the Accounting Software), ensuring Trial Balances tally perfectly at the end of every day.

### Scope

-   **Chart of Accounts (CoA) Mapping:** Linking ERP Fee Heads (e.g., "Grade 1 Tuition") to Accounting Ledgers (e.g., `Income - Primary Tuition`).
-   **Voucher Generation:** creating Sales, Receipt, Journal, and Credit Note vouchers.
-   **Accrual vs. Cash Accounting:** Managing the realization of revenue over time versus cash collection.
-   **Export / API Sync:** Generating standard XML files for Tally import or pushing data via direct REST APIs to modern accounting suites.
-   **Day-Book Synchronization:** Verifying that daily totals in the ERP mathematically match daily totals in the accounting software.

---

## KEY FEATURES

### 1. Dynamic Ledger Mapping Engine

**Feature Description:**
The configuration layer ensuring data flows into the right buckets.
*   **Income Ledgers:** "Tuition Fee" -> `Income_Tuition_A/C`.
*   **Asset Ledgers:** "Unpaid Fees" -> `Sundry_Debtors_StudentFees`.
*   **Liability Ledgers:** "Caution Deposit" -> `Security_Deposit_Liab_A/C`.
*   **Expense Ledgers:** "Sibling Discount" -> `Concessions_Expense_A/C`.
*   The system allows many-to-one mapping (e.g., Grade 1, 2, 3 Tuition all map to a single `Primary_Tuition` ledger based on school preference).

### 2. Automated Voucher Engine

**Feature Description:**
Translating real-world events into accounting journal entries automatically.

**Example 1: Raising an Invoice (Accrual)**
Event: Generated Q1 Fee Bill for Rs. 10,000.
Voucher type: Journal / Sales
*   Debit: `Sundry Debtors A/C` - Rs. 10,000
*   Credit: `Tuition Fee Income A/C` - Rs. 10,000

**Example 2: Collecting Cash (Realization)**
Event: Parent pays Rs. 10,000 cash.
Voucher Type: Receipt
*   Debit: `Cash-in-Hand A/C` - Rs. 10,000
*   Credit: `Sundry Debtors A/C` - Rs. 10,000

### 3. Tally ERP XML Exporter

**Feature Description:**
Tally is the industry standard in India. This feature builds Tally-compliant XML strings.
*   **Batches:** Creates daily/weekly/monthly batches payload.
*   **Master Sync:** Before pushing vouchers, the ERP pushes "Masters" (creating a Ledger for "New Student Rohit" automatically in Tally if student-wise ledgers are preferred).
*   **Validation:** Checks for unbalanced vouchers before exporting.

### 4. Advanced Advance/Refund Handling

**Feature Description:**
Handling the messiest parts of school accounting.
*   **Advances:** If a parent pays Rs. 50k for next year, it is **not** Income. It is an Advance.
    *   Debit: Bank. Credit: `Advance_Fee_Liability`.
*   **Refunds:** Requires careful reversal of income or reduction of liability, plus a bank payout voucher.

---

## DATABASE SCHEMA

To translate and prepare data for external systems.

### 1. Ledger Maps (`fee_acc_ledger_mappings`)
The Rosetta Stone between ERP and Tally.

```sql
CREATE TABLE fee_acc_ledger_mappings (
    mapping_id INT PRIMARY KEY AUTO_INCREMENT,
    
    erp_entity_type ENUM('FEE_HEAD', 'PAYMENT_MODE', 'DISCOUNT_RULE', 'TAX_TYPE'),
    erp_entity_id INT, -- E.g., ID for "Transport Fee"
    
    acc_ledger_name VARCHAR(150), -- E.g., "Transport Revenue A/C"
    acc_ledger_group VARCHAR(150), -- E.g., "Direct Incomes"
    
    is_active BOOLEAN DEFAULT TRUE
);
```

### 2. Accounting Vouchers (`fee_acc_vouchers`)
The staged, double-entry records waiting to be exported.

```sql
CREATE TABLE fee_acc_vouchers (
    voucher_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    voucher_date DATE NOT NULL,
    voucher_type ENUM('RECEIPT', 'JOURNAL', 'PAYMENT', 'CONTRA', 'SALES', 'CREDIT_NOTE'),
    
    erp_reference_id BIGINT, -- Links back to `fee_payments` or `fee_invoices`
    narration TEXT,
    
    total_amount DECIMAL(15,2),
    sync_status ENUM('PENDING', 'EXPORTED_XML', 'SYNCED_API', 'ERROR'),
    synced_at DATETIME
);
```

### 3. Voucher Lines (`fee_acc_voucher_lines`)
The Debits and Credits making up the voucher.

```sql
CREATE TABLE fee_acc_voucher_lines (
    line_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    voucher_id BIGINT NOT NULL,
    
    ledger_name VARCHAR(150) NOT NULL,
    entry_type ENUM('DEBIT', 'CREDIT') NOT NULL,
    amount DECIMAL(15,2) NOT NULL,
    
    FOREIGN KEY (voucher_id) REFERENCES fee_acc_vouchers(voucher_id)
);
```

---

## BUSINESS RULES

### Rule 1: Zero-Sum Integrity (Dual Aspect)
*   For every voucher created in `fee_acc_vouchers`, the sum of `DEBIT` lines in `fee_acc_voucher_lines` MUST exactly equal the sum of `CREDIT` lines. Complete failure to save otherwise.

### Rule 2: Ledger Level Granularity Matrix
*   Schools choose how they track debtors:
    *   **Option A (Student-as-Ledger):** creates 5000 ledgers in Tally (e.g., `A/R - Aryan Sharma`). Extremely precise, but makes Tally slow.
    *   **Option B (Class-as-Ledger):** 50 ledgers (e.g., `A/R - Grade 10A`).
    *   **Option C (Consolidated Ledger):** 1 ledger (`Sundry Debtors - Students`). The ERP acts as the sub-ledger handling the student-level breakdown.
*   The system must support adapting its voucher generation to the selected granularity level.

### Rule 3: Irrevocable Locking
*   Once a voucher's `sync_status` changes to `SYNCED` or `EXPORTED`, the underlying ERP transaction (e.g., a Receipt) is **Locked**.
*   A user cannot edit or delete a synced receipt in the ERP. They must issue a formal Cancellation/Reversal transaction, which generates a new inverse voucher (e.g., a Debit Note) to be synced on the next run.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Tally Prime (Local):** Generates `.xml` format files based on TDL (Tally Definition Language) specs.
*   **To Cloud Accounting (Zoho Books / QuickBooks):** Uses REST JSON APIs `POST /api/v3/journals`.

### Inbound Relationships
*   **From ERP Core Engine:** Listens globally to all inserts/updates in Invoice, Payment, Refund, and Concession tables via an Event Bus to trigger the voucher builder logic asynchronously.

---

## USER WORKFLOWS

### Workflow 1: The Daily Tally Export
**Actor:** Head Accountant

1.  **End of Day:** Cashier closes their drawer, bank reconciliation is marked up-to-date.
2.  **Navigation:** Accountant goes to Setup -> Tally Integration -> Export Vouchers.
3.  **Filter:** Selects Date: "10-Oct-2025".
4.  **Verification:** Screen shows a summary: "340 Receipts, 12 Credit Notes, Total Value Rs. 14.5 Lakhs". Let's Accountant check for "Unmapped Ledgers" errors.
5.  **Action:** Clicks "Generate XML".
6.  **Tally Import:** Accountant opens Tally ERP 9 on their desktop -> Import Data -> Selects XML file.
7.  **Result:** Hundreds of entries populate in Tally instantly. Accountant checks Tally Day-Book against ERP Day-Book; both show identical closing balances.

### Workflow 2: Correcting a Mapping Error
**Actor:** IT Admin / CA

1.  **Context:** A new fee head "Robotics Kit" was created but forgotten to be mapped to a Tally ledger.
2.  **Trigger:** The nightly background API sync to Zoho Books fails. `fee_acc_vouchers` shows `ERROR`.
3.  **Alert:** Admin dashboard shows "5 Vouchers stuck in Sync Queue".
4.  **Fix:** Admin clicks the error. Reads "Missing Ledger mapping for fee_head_id 45".
5.  **Action:** Admin goes to Ledger Mappings -> Maps "Robotics Kit" to `Academic_Consumables_Income`.
6.  **Retry:** Clicks "Re-process Error Queue". Vouchers rebuild and sync successfully to Zoho Books.

---

## REAL-WORLD SCENARIOS

### Scenario A: Complex Concession Accounting
*   **Context:** Student billed Rs. 100K. Scholarship given Rs. 20K. Parent pays Rs. 80K.
*   **Accountant Requirement:** The CA needs to show the gross income to the Trust board, and the scholarship as a specific expense.
*   **Voucher Generation logic:**
    *   **Sales Voucher:** Debit AR (100k), Credit Tuition Income (100k).
    *   **Journal Voucher (Discount):** Debit Scholarship Expense (20k), Credit AR (20k).
    *   **Receipt Voucher:** Debit Bank (80k), Credit AR (80k).
*   **Result:** AR balance = 0. Income = 100k. Expense = 20k. Net Profit = 80k. Perfect GAAP compliance.

### Scenario B: Payment Gateway Commission (MDR)
*   **Context:** Parent pays Rs. 10,000 via Credit Card. Razorpay keeps Rs. 200, sends Rs. 9,800 to school bank.
*   **Voucher Complexity:** The school must recognize the parent paid in full (Rs. 10,000), but bank only has Rs. 9,800.
*   **Execution:** System generates a compound receipt voucher:
    *   Credit: AR (Rs. 10,000)
    *   Debit: Bank (Rs. 9,800)
    *   Debit: Bank Charges / MDR Expense (Rs. 200).
*   **Audit Check:** This exact structure prevents the auditor from asking "Why is Rs. 200 missing from the bank statement?".

---

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** March 2026

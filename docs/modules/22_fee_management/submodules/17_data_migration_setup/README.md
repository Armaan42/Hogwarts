# DATA MIGRATION & SETUP

**Module:** Fee Management  
**Submodule Code:** FEE-MIGRATE-017  
**Category:** Implementation & Onboarding  
**Priority:** Medium (P2)  
**Owners:** IT Administrator, Onboarding Specialist (Vendor)

---

## OVERVIEW

The Data Migration & Setup submodule is the critical toolset used only once (or during major system transitions) to bring a school onto the new Fee Management ERP. It exists to solve the hardest problem in software adoption: moving years of historical financial data, complex carry-forward balances, and active installment plans from a legacy system (or Excel sheets) into the highly structured relational database of the new platform.

### Purpose

To ensure a smooth "Go-Live" experience without losing a single rupee of historical data or pending dues. It transforms messy, unstructured legacy exports into clean, validated, and normalized data structures that the new fee engine requires.

### Scope

-   **Opening Balance Migration:** Importing the net outstanding (Arrears) or advances for every student as of the cutoff date.
-   **Structure Mapping:** Translating legacy fee heads (e.g., "Misc_Charges") to standardized new fee heads (e.g., "Lab Consumables").
-   **Historical Ledger Import:** Optionally porting the last 3 years of detailed invoice/receipt data for continuity.
-   **Automated Validation:** Running sanity checks on the imported data (e.g., "Does Total Due - Paid = Balance?").
-   **Rollback Mechanism:** The ability to undo a migration batch if critical errors are discovered post-import.

---

## KEY FEATURES

### 1. Smart CSV/Excel Uploader Engine

**Feature Description:**
The primary interface for mass data ingestion.
*   **Dynamic Mapping:** Admin uploads legacy Excel file. The system asks "Map your column 'StdName' to our field 'student_name'".
*   **Pre-Flight Checks:** Before touching the database, the engine scans the 5,000 rows. It flags errors instantly: "Row 45: Parent Email is invalid format. Row 102: Grade 13 does not exist."
*   **Dry Run:** Simulates the import and provides a summary report ("Will create 4000 ledgers and insert Rs. 2.5 Cr in Opening Balances") before committing.

### 2. Opening Balance Initialization (The Cut-Off Strategy)

**Feature Description:**
The standard accounting process for transitioning systems mid-year or year-end.
*   **Cut-off Date:** e.g., March 31, 2025.
*   **Logic:** The system does NOT need to import every invoice from 2018. It simply creates an `OPENING_BALANCE_ARREARS` entry (Debit) or `OPENING_ADVANCE` entry (Credit) for each student effective April 1, 2025.
*   **Reconciliation:** The sum of all Opening Balances in the new system MUST exactly match the closing "Sundry Debtors" figure in the school's audited balance sheet for March 31.

### 3. Fee Structure Cloning & Versioning

**Feature Description:**
Setting up the new academic year's pricing.
*   **Clone Tool:** "Copy Grade 5 Fee Structure from AY 24-25 to AY 25-26".
*   **Bulk Increment:** "Apply a 10% hike to all Tuition heads across all grades".
*   **Validation:** System checks for missing configurations (e.g., "Grade 11 Science has no Lab Fee defined").

### 4. Legacy Invoice Hydration (Optional)

**Feature Description:**
For schools that demand full historical continuity in the Parent Portal.
*   Imports raw invoice and receipt data as `READ-ONLY` historical records.
*   These records do NOT trigger accounting vouchers (as they were already accounted for in the old system), but they DO appear in the Parent Portal's "Past Transactions" tab so parents can download old receipts.

---

## DATABASE SCHEMA

Note: This submodule heavily relies on temporary staging tables before validating and moving data to the core tables (like `fee_invoices` and `student_fee_maps`).

### 1. Migration Batches (`fee_migration_batches`)
Tracking the import jobs.

```sql
CREATE TABLE fee_migration_batches (
    batch_id INT PRIMARY KEY AUTO_INCREMENT,
    batch_name VARCHAR(100), -- "Grade 1-5 Opening Balances"
    
    upload_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    uploaded_by INT,
    
    total_records INT,
    parsed_successfully INT,
    failed_records INT,
    
    status ENUM('UPLOADED', 'VALIDATING', 'VALIDATED', 'COMMITTING', 'COMPLETED', 'ROLLED_BACK')
);
```

### 2. Staging Ledgers (`fee_staging_opening_balances`)
The intermediate table where data sits during validation.

```sql
CREATE TABLE fee_staging_opening_balances (
    row_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    batch_id INT,
    
    legacy_student_id VARCHAR(50), -- Admission No from old system
    student_name VARCHAR(100),
    
    arrears_amount DECIMAL(15,2),
    advance_amount DECIMAL(15,2),
    
    validation_status ENUM('PASS', 'FAIL'),
    error_message TEXT, -- e.g., "Student ID not found in core system"
    
    FOREIGN KEY (batch_id) REFERENCES fee_migration_batches(batch_id)
);
```

---

## BUSINESS RULES

### Rule 1: The "No Orphans" Rule
*   The migration engine will categorically REJECT any financial data linked to a student who does not exist in the new Student Management Module.
*   **Dependency:** The `Student Core Module` migration MUST be 100% complete and signed off before the `Fee Module` migration can even begin.

### Rule 2: Double-Entry Initialization
*   When injecting Rs. 10 Lakhs of Student Arrears as Opening Balances, the system must create an accompanying Journal Voucher to balance the books.
*   Entry: `Debit: Sundry Debtors (Rs. 10L) | Credit: Historical Opening Balance Setup A/C (Rs. 10L)`.

### Rule 3: Irreversibility Post-Transactions
*   A user can "Rollback" a migration batch as long as no *new* transactions have been processed against those accounts.
*   Once a school "Goes Live" and a parent makes a payment against an imported opening balance, the migration batch is permanently locked. To correct an error after this point, a manual Credit/Debit note must be passed.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Accounting System (Tally/SAP):** Requires pushing the "Opening Trial Balance" to ensure both systems start the new financial year in perfect sync.

### Inbound Relationships
*   **From Legacy ERP:** Takes CSV/SQL dumps as raw input.
*   **From Core Student Module:** Validates against the fresh `students` table to map `Admission_Number` to the new internal `student_id` database keys.

---

## USER WORKFLOWS

### Workflow 1: The "Go-Live" Weekend (Opening Balances)
**Actor:** Onboarding Specialist

1.  **Friday 5 PM:** School shuts down old system. CA exports "Outstanding Dues as on Date.xlsx".
2.  **Saturday Morning:** Specialist uploads Excel to Migration Engine.
3.  **Mapping:** System maps Old `AdmNo` to New `AdmissionNumber`. Maps `TotalDue` to `Arrears`.
4.  **Validation Check:** Engine flags 5 students. "Error: AdmNo 4099 not found".
5.  **Correction:** Specialist discovers 4099 was a typo in the old system (should be 4009). Edits data in staging directly.
6.  **Dry Run:** System reports: "Will update 1,200 student ledgers. Total Arrears: Rs. 45,20,000".
7.  **Sign-off:** CA manually verifies the Rs. 45 Lakh figure against old system.
8.  **Commit:** Specialist clicks "Execute". Data is permanently written to `fee_invoices` as an `OPENING_BALANCE` record.
9.  **Monday 8 AM:** School opens. Cashier logs in. Sees perfect opening balances for all students.

### Workflow 2: Bulk Concession Import
**Actor:** IT Admin

1.  **Context:** The school has 150 students on permanent "Staff Ward" scholarships from previous years.
2.  **Upload:** Admin uploads "staff_wards.csv" (`student_id`, `discount_percentage`).
3.  **Engine Action:** System reads the file, skips the UI forms, and directly populates `fee_scholarship_grants` for all 150 students simultaneously.
4.  **Verification:** Admin checks the "Concession Register" report to ensure exactly 150 active grants are listed.

---

## REAL-WORLD SCENARIOS

### Scenario A: Mismatched Academic Years
*   **Context:** Legacy system grouped Grade 10 fees as "Term 1, 2, 3". New system uses "Quarter 1, 2, 3, 4".
*   **Resolution:** The migration team decides NOT to port detailed historical invoices because the structures are incompatible.
*   **Execution:** They opt for the "Summary Line" approach. They import a single consolidated line item called `Legacy Dues (Pre-2025)` with the total balance, foregoing itemized historical breakdowns.

### Scenario B: The Overpaid Parent (Advance Carry Forward)
*   **Context:** A parent accidentally paid Rs. 10,000 extra in the old system in March. It was sitting as an unadjusted advance.
*   **Migration:** The engine imports this with a negative balance (Credit).
*   **Go-Live:** In April, the new system generates the Q1 invoice for Rs. 25,000.
*   **Auto-Resolution:** The system's billing engine detects the Rs. 10,000 advance in the ledger and automatically adjusts it against the new bill. The parent portal shows "Q1 Bill: 25k. Adjusted: 10k. Net Payable Now: 15k."

---

## EDGE CASES

### Edge Case 1: Duplicate Students in Legacy Data
*   **Scenario:** The old system has "Rohit Sharma (ID: 1001)" and "Rohit Sharma (ID: 1001A)" because the student re-enrolled after withdrawing. Both have financial history.
*   **Resolution:** The migration engine's "Deduplication Wizard" flags potential duplicates based on Name + Parent Phone matching. The admin manually confirms which record to keep as the primary and which to merge. Financial histories from both records are consolidated under one new `student_id`.

### Edge Case 2: Legacy System Uses Different Financial Year
*   **Scenario:** The old system ran on a Calendar Year (Jan-Dec) basis, but the new ERP uses the Indian Financial Year (April-March).
*   **Resolution:** The migration engine allows setting a `legacy_fy_start_month = 1`. The Opening Balance import translates the legacy's "Dec 31 closing balance" into the new system's "Opening Balance as of Jan 1" and bridges the gap until the next April 1 when the new FY starts natively.

### Edge Case 3: Partially Paid Installment Plan
*   **Scenario:** In the old system, a student's Annual Fee of Rs. 1,00,000 was split into 4 installments. Q1 and Q2 are paid (Rs. 50,000). Q3 and Q4 are pending (Rs. 50,000).
*   **Resolution:** The migration engine imports:
    *   Two `PAID` historical invoices (Q1, Q2) as `READ_ONLY` records.
    *   Two `OUTSTANDING` invoices (Q3, Q4) as **ACTIVE** records in the new system, which immediately appear on the parent portal and are subject to the new system's overdue logic.

### Edge Case 4: Data Import with Foreign Characters
*   **Scenario:** Student names in the old system contain regional language characters (e.g., Hindi, Tamil) that were stored in a non-UTF8 encoding.
*   **Resolution:** The CSV uploader detects encoding mismatches and offers a "Character Encoding Selector" (UTF-8, UTF-16, ISO-8859-1, Windows-1252). A preview pane shows the first 10 rows in the selected encoding so the admin can visually verify that `aeS?` is actually `Hindi Name` before committing.

---

## ADDITIONAL REAL-WORLD SCENARIOS

### Scenario C: Multi-Branch Staggered Go-Live
*   **Context:** A school group with 5 branches is going live one branch at a time (Branch 1 in April, Branch 2 in May, etc.).
*   **Challenge:** Branches 2-5 continue using the old system while Branch 1 is already on the new ERP.
*   **Workflow:**
    1.  Each branch has its own migration batch.
    2.  The central accounting system (Tally) receives data from BOTH the old and new systems during the transition.
    3.  The migration engine supports "Incremental Sync" for the transition period where pending payments made in the old system after the cutoff are periodically synced to the new ERP until full switchover.

### Scenario D: The "Excel-Only" School
*   **Context:** A small school (200 students) with zero existing software. All records are in Excel sheets maintained by the Principal's assistant.
*   **Challenge:** Data is extremely unstructured. Column headers change per sheet. Some years have data, others don't.
*   **Approach:**
    1.  Onboarding specialist provides a "Standardized Template.xlsx" with the exact column headers and formats expected.
    2.  School staff manually copies data from their messy sheets into the template.
    3.  The migration engine imports from this clean template.
    4.  For historical data that's too messy to clean, the specialist advises: "Don't import. Start fresh with Opening Balances only."

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `migration_cutoff_date` | `2025-03-31` | The date separating "history" from "live" data |
| `migration_dry_run_enabled` | `true` | Always simulate before committing? |
| `migration_auto_map_threshold` | 85% | Fuzzy match % to auto-map student IDs between systems |
| `migration_allow_rollback` | `true` | Can a committed batch be reversed? |
| `migration_historical_import_mode` | `SUMMARY_LINE` | Options: `FULL_DETAIL`, `SUMMARY_LINE`, `NONE` |
| `migration_encoding_default` | `UTF-8` | Default character encoding for CSV uploads |
| `migration_max_validation_errors` | 50 | Max errors before batch is auto-rejected |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

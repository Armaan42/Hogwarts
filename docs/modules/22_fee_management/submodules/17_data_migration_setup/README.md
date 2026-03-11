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

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** March 2026

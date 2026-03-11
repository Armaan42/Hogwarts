# BANK RECONCILIATION

**Module:** Fee Management  
**Submodule Code:** FEE-RECON-006  
**Category:** Financial Control  
**Priority:** Critical (P0)  
**Owners:** Chartered Accountant (CA), Senior Accountant

---

## OVERVIEW

The Bank Reconciliation submodule is the final truth-check in the financial cycle. It ensures that the money the school *thinks* it has collected (System Ledger) matches the money actually sitting in the bank account (Bank Statement). It automatically matches thousands of digital transactions while providing tools for manual reconciliation of complex cases like bounced cheques or direct bank transfers.

### Purpose

To identify discrepancies between internal records and external bank records. It detects fraud, errors (e.g., double entry), failed settlements, and accounting gaps. It is essential for statutory audits and financial integrity.

### Scope

-   **Statement Import:** Parsing CSV/Excel/PDF statements from banks (HDFC, SBI, ICICI, etc.).
-   **Auto-Reconciliation:** Algorithms to match transactions based on Ref No, Date, and Amount.
-   **Exception Handling:** Managing "Unreconciled" items (e.g., Bank Charges deducted directly).
-   **Settlement Tracking:** Verifying that the Payment Gateway (Razorpay) has settled the funds into the bank account (T+2 days).
-   **Audit Reports:** Generating "Bank Reconciliation Statement (BRS)" for auditors.

---

## KEY FEATURES

### 1. Statement Parser Engine

**Feature Description:**
A tool to upload and interpret raw bank statements.
*   **Format Agnostic:** Can handle standard formats (MT940) or custom CSVs from Indian banks.
*   **Intelligence:** Identifies "Credit" vs. "Debit" columns, ignores header rows, and extracts "Narration" text.

### 2. Intelligent Auto-Match Algorithm

**Feature Description:**
The core logic that attempts to pair system records with bank rows.

**Matching Criteria (in order):**
1.  **Exact Match:** Unique Transaction ID (UTR / Cheque No) + Exact Amount. (Confidence: 100%)
2.  **Fuzzy Match:** Same Amount + Same Date (+/- 1 day). (Confidence: 80%)
3.  **Batch Match:** Sum of 5 system receipts = 1 Bank Deposit (Cash Deposit Slip logic).

**Action:**
*   High confidence matches are "Reconciled" automatically.
*   Low confidence matches are flagged for "Review".

### 3. Payment Gateway Settlement Reconciliation

**Feature Description:**
Specifically for online payments.
*   **Problem:** Parents pay ₹100. PG takes ₹2 fee. Bank receives ₹98. System records ₹100 credit to student.
*   **Solution:** submodule reconciles the "Net Settlement Amount".
    *   Expected: ₹10,000 (Sum of 100 transactions).
    *   Received: ₹9,850.
    *   Variance: ₹150 (MDR / Commission).
    *   **Auto-Journal:** System posts ₹150 to "Bank Charges" expense account to balance the ledger.

### 4. Direct Bank Transfer Resolution

**Feature Description:**
Handling parents who do NEFT directly to the school account without logging into the portal.
*   **Scenario:** Bank Statement shows "NEFT from A. Sharma ₹50,000". System has no record.
*   **Tool:** "Create Receipt from Statement".
*   **Action:** Accountant selects the bank row -> Searches student "A. Sharma" -> Maps it -> Creates Receipt.

---

## DATABASE SCHEMA

### 1. Bank Statements (`fee_bank_statements`)
The uploaded files.

```sql
CREATE TABLE fee_bank_statements (
    stmt_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    bank_account_id INT NOT NULL,
    
    file_name VARCHAR(255),
    upload_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    status ENUM('PROCESSING', 'PARSED', 'ERROR'),
    
    period_start DATE,
    period_end DATE,
    opening_balance DECIMAL(15,2),
    closing_balance DECIMAL(15,2),
    
    uploaded_by INT
);
```

### 2. Statement Rows (`fee_statement_entries`)
Individual lines from the bank statement.

```sql
CREATE TABLE fee_statement_entries (
    entry_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    stmt_id BIGINT NOT NULL,
    
    transaction_date DATE,
    value_date DATE,
    
    description VARCHAR(500), -- Narration
    ref_no VARCHAR(100), -- UTR / Cheque No
    
    debit_amount DECIMAL(15,2) DEFAULT 0,
    credit_amount DECIMAL(15,2) DEFAULT 0,
    balance DECIMAL(15,2),
    
    reconciled_status ENUM('UNRECONCILED', 'RECONCILED', 'IGNORED'),
    matched_payment_id BIGINT, -- Link to system payment
    
    FOREIGN KEY (stmt_id) REFERENCES fee_bank_statements(stmt_id)
);
```

### 3. Reconciliation Batches (`fee_recon_batches`)
Groups of reconciled items.

```sql
CREATE TABLE fee_recon_batches (
    batch_id INT PRIMARY KEY AUTO_INCREMENT,
    reconciled_date DATE DEFAULT CURRENT_DATE,
    reconciled_by INT,
    
    total_system_amount DECIMAL(15,2),
    total_bank_amount DECIMAL(15,2),
    difference_amount DECIMAL(15,2), -- Should be 0 ideally
    
    adjustment_journal_id INT -- Link to Accounting Journal for write-offs
);
```

---

## BUSINESS RULES

### Rule 1: Segregation of Duties
*   The person who *collects* cash (Cashier) should not be the one who *reconciles* the bank statement (Accountant).
*   System enforces permission separation.

### Rule 2: Variance Threshold
*   Auto-reconciliation allows a variance of ₹0.00 to ₹1.00 (to handle rounding errors).
*   Variances > ₹1.00 require manual approval and journal entry.

### Rule 3: Unidentified Credits
*   Money received in bank but owner unknown (e.g., narration "CASH DEPOSIT").
*   Action: Posted to "Suspense Account" (Liability) until claimed by a parent with proof.
*   Money is *not* recognized as Revenue until identified.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Financial Accounting (General Ledger):**
    *   Posts "Bank Charges" (Expense).
    *   Posts "Interest Income" (Revenue).
*   **To Dashboard:** Updates "Actual Cash Position" widget.

### Inbound Relationships
*   **From Payment Collection:** Pulls the `fee_payments` records to match against `fee_statement_entries`.

---

## USER WORKFLOWS

### Workflow 1: Monthly Reconciliation
**Actor:** Senior Accountant

1.  **Download:** Logs into SBI Corporate Banking -> Downloads XLS statement for January.
2.  **Upload:** Uploads file to ERP "Bank Recon" module.
3.  **Parse:** System identifies 450 transactions.
4.  **Auto-Match:** System matches 420 transactions instantly (Online payments & Cheques).
5.  **Manual Work:** 30 items remain "Unreconciled".
    *   5 are "Bank Charges" -> Bulk Select -> "Post to Expense".
    *   20 are "Cash Deposits" -> Matches against "Cash Deposit Sliips" created by Cashier.
    *   5 are "Unknown NEFT" -> Move to Suspense.
6.  **Finalize:** Click "Close Reconciliation". Status -> "Reconciled".

### Workflow 2: Handling a Dishonoured Cheque (Delayed)
**Actor:** Accountant

1.  **Context:** A cheque was marked "Cleared" in system 3 days ago based on trust.
2.  **Statement:** Bank statement today shows "Debit: Cheque Return - Insufficient Funds".
3.  **Action:**
    *   Match the "Debit" entry to the original "Credit" entry.
    *   Select Action: "Reverse Payment & Mark Bounced".
4.  **Result:** System automatically undoes the receipt and applies penalty (as per submodule 04/05 logic).

---

## REAL-WORLD SCENARIOS

### Scenario A: The "Nodal Account" Split
*   **Context:** School uses a Nodal account.
*   **Flow:**
    1.  Parents pay ₹1 Cr total.
    2.  PG settles ₹90 Lakhs to "School Trust" (Tuition).
    3.  PG settles ₹10 Lakhs to "Speedy Transport Co" (Transport).
*   **Recon:** The module can fetch settlement reports from the PG and reconcile *both* bank accounts simultaneously against the single student payment source.

### Scenario B: Double Payment Refund
*   **Context:** Parent clicked "Pay" twice. Two debits of ₹5,000.
*   **Bank:** Shows 2 Credits of ₹5,000.
*   **System:** Shows 2 Receipts.
*   **Recon:** Both match.
*   **Action:** Accountant sees excess payment. Initiates "Refund" for one transaction.
*   **Next Month:** Bank statement shows "Debit ₹5,000 (Refund)". Reconciles against the Refund Voucher.

---


## EDGE CASES

### Edge Case 1: Same Amount, Same Day, Different Students
*   **Scenario:** Three parents each pay Rs. 25,000 via NEFT on the same day. Bank statement shows 3 identical credits with no student-identifying information in the narration.
*   **Resolution:** The system lists all three as "Ambiguous Matches" and asks the admin to manually assign each bank entry to the correct student, cross-referencing the UTR number against parent-submitted payment proofs.

### Edge Case 2: PG Settlement Delay Over Weekend
*   **Scenario:** Parent pays online on Friday evening. PG settles to the school bank on Monday (T+2). The parent immediately complains: "I paid but receipt says provisional."
*   **Resolution:** The system generates the receipt immediately (based on PG webhook). The bank reconciliation module marks the transaction as "Awaiting Settlement" until Monday's bank statement confirms the credit. The parent-facing UI shows "Payment Confirmed" (based on PG), while the internal accounting shows "Pending Bank Credit" until reconciled.

### Edge Case 3: Bank Charges Auto-Deduction
*   **Scenario:** The bank deducts Rs. 5,000 quarterly as "Account Maintenance Charges." This debit appears in the bank statement but has no corresponding ERP transaction.
*   **Resolution:** The system supports a "Non-Student Transactions" category. Admin maps these debits to `Bank Charges Expense A/C`. These reconciled entries appear in the accounting voucher export but are excluded from student fee reports.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `recon_auto_match_tolerance` | Rs. 1 | Amount difference tolerated for auto-matching |
| `recon_bank_statement_format` | `MT940` | Supported: `MT940`, `CSV`, `OFX`, `CUSTOM_EXCEL` |
| `recon_pg_settlement_lag_days` | 2 | Expected T+N settlement days for gateway |
| `recon_stale_cheque_days` | 90 | Days after which uncleared cheques are flagged |
| `recon_daily_close_mandatory` | `true` | Force daily reconciliation before next day's work? |
| `recon_auto_match_enabled` | `true` | Allow system to auto-reconcile perfect matches? |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

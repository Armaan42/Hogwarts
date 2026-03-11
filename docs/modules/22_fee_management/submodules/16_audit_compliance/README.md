# AUDIT & COMPLIANCE

**Module:** Fee Management  
**Submodule Code:** FEE-AUDIT-016  
**Category:** Financial Control & Governance  
**Priority:** Medium (P2)  
**Owners:** External Auditor, Trust Board, Finance Controller

---

## OVERVIEW

The Audit & Compliance submodule acts as the incorruptible "black box" of the Fee Management system. While the operational modules focus on collecting and accounting for money, this module focuses on *how* those actions were performed, *who* performed them, and *when*. It ensures that the software inherently enforces the internal controls necessary to pass a statutory financial audit by external agencies (like Big 4 accounting firms).

### Purpose

To prevent financial fraud, embezzlement, and data manipulation. It guarantees that the financial records presented to the Board of Trustees and government tax authorities are a true and fair view of the school's operations, backed by an immutable trail of evidence.

### Scope

-   **Data Immutability Locking:** Preventing back-dated entries in closed financial periods.
-   **Maker-Checker Workflows:** Enforcing dual-authorization for sensitive actions (e.g., massive discounts or refunds).
-   **Audit Trails (System Logs):** Tracking every INSERT, UPDATE, and DELETE at the database row level.
-   **Exception Reporting:** Highlighting anomalous behavior (e.g., a cashier who frequently cancels receipts at 5:00 PM).
-   **Compliance Checklists:** Ensuring GST/TDS calculations are applied logically where legally required.

---

## KEY FEATURES

### 1. The Immutable Audit Trail (Log-Structured Storage)

**Feature Description:**
A silent, undeletable background observer.
*   **Mechanism:** Every time a user modifies a critical table (`fee_invoices`, `fee_payments`), the system creates a carbon copy of the *old* data and the *new* data in an append-only `audit_log` table.
*   **Capture Data:** User ID, IP Address, Timestamp, Old Value, New Value, Action Type.
*   **Tamper-Proofing:** In high-security setups, the audit logs are cryptographically hashed so that a rogue Database Administrator cannot manually modify the logs without breaking the chain.

### 2. Period Locking (Financial Year Closure)

**Feature Description:**
The digital equivalent of closing the physical ledger books.
*   **Soft Lock (Monthly):** At month-end (e.g., Jan 31st), the CA runs the "Monthly Close". After this, no user can back-date a receipt to January.
*   **Hard Lock (Annual):** On March 31st, the Year-End process runs. The balance sheet is finalized.
*   **Period Re-opening:** If an error is discovered in a locked period, a formal "Unlock Request" must be initiated by the Accountant, approved by the Principal, and logged as an exception for the auditor to review.

### 3. Maker-Checker Enforcement

**Feature Description:**
Separation of duties. One person cannot both initiate and approve a high-risk financial transaction.
*   **Scenario: Scholarship Grant.**
    *   *Maker:* Admission Counselor enters "50% Waiver for Aryan" into the system (Status: PENDING_APPROVAL). The system will NOT calculate the discount yet.
    *   *Checker:* Principal logs in, reviews the attached Income Certificate, and clicks "APPROVE".
*   **Applicability:** Refunds > Rs. 5000, Manual Ledger Adjustments, and Bank Reconciliation finalizations.

### 4. Anomaly Detection Engine

**Feature Description:**
Proactive flagging of suspicious patterns for internal audit.
*   **Pattern 1: High Velocity Reversals.** A specific cashier accepts cash, prints receipt, then cancels the receipt 10 minutes later, claiming "Parent changed mind". If this happens > 3 times a week, flag it. (Pattern implies pocketing cash and destroying evidence).
*   **Pattern 2: Night-Time Activity.** Financial transactions logged after 8:00 PM (when office is closed) trigger an immediate email to the Controller.
*   **Pattern 3: Round-Number Discounts.** System flags discounts that are manually entered as exactly Rs. 5000 or Rs. 10000 instead of system-calculated percentages.

---

## DATABASE SCHEMA

Note: True audit tables are often handled at the database trigger level, but here is the application-level schema representation.

### 1. System Audit Logs (`fee_audit_logs`)
The master tracker for all changes.

```sql
CREATE TABLE fee_audit_logs (
    log_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    
    entity_type VARCHAR(50), -- e.g., 'FEE_INVOICE', 'FEE_PAYMENT'
    entity_id BIGINT, -- The ID of the row that was changed
    
    action ENUM('CREATE', 'UPDATE', 'DELETE', 'CANCEL', 'REVERSE'),
    
    old_values JSON, -- Snapshot before change
    new_values JSON, -- Snapshot after change
    
    user_id INT, -- Who made the change
    ip_address VARCHAR(45),
    performed_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_entity (entity_type, entity_id),
    INDEX idx_user_time (user_id, performed_at)
);
```

### 2. Financial Periods (`fee_financial_periods`)
Managing the locks.

```sql
CREATE TABLE fee_financial_periods (
    period_id INT PRIMARY KEY AUTO_INCREMENT,
    academic_year_id INT,
    
    period_name VARCHAR(50), -- 'FY 25-26 Q1', 'January 2026'
    start_date DATE,
    end_date DATE,
    
    is_locked BOOLEAN DEFAULT FALSE,
    locked_on DATETIME,
    locked_by INT,
    
    -- If reopened
    unlock_reason TEXT,
    unlocked_by INT
);
```

### 3. Maker-Checker Queues (`fee_approval_requests`)
The holding pen for sensitive transactions.

```sql
CREATE TABLE fee_approval_requests (
    request_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    
    request_type ENUM('REFUND', 'WAIVER', 'MANUAL_ADJUSTMENT', 'CHEQUE_BOUNCE_WAIVER'),
    reference_id BIGINT, -- Points to the temp/draft record
    
    requested_amount DECIMAL(15,2),
    justification TEXT,
    supporting_document_url VARCHAR(255),
    
    maker_user_id INT NOT NULL,
    requested_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    status ENUM('PENDING', 'APPROVED', 'REJECTED'),
    checker_user_id INT,
    checked_at DATETIME,
    checker_remarks TEXT
);
```

---

## BUSINESS RULES

### Rule 1: The "No True Delete" Principle
*   A financial record (Invoice or Payment) is NEVER executed via a `DELETE FROM` SQL statement.
*   If an invoice is wrong, it is flagged as `status = 'CANCELLED'`. It remains in the database forever. The UI simply filters out `CANCELLED` records from standard views, but they appear in Audit Views.

### Rule 2: Sequential Receipt Numbering
*   Receipt numbers (e.g., `RCPT-25-0010`) must be strictly sequential with no gaps.
*   If a receipt generation fails mid-process, the system must either recover that number or formally log it as a "Spoiled Receipt" to satisfy tax auditors who look for missing numbers as a sign of unrecorded cash sales.

### Rule 3: Session Expiry for Cashiers
*   Cashier terminals are strictly configured to auto-logout after 5 minutes of inactivity to prevent an unauthorized person from jumping on a logged-in terminal while the cashier stepped away.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Reporting (Submodule 09):** Feeds the "Audit Trail Report" dashboard used by external auditors.
*   **To System Security Module:** Triggers global security alerts if mass-data manipulation is attempted by an admin account.

### Inbound Relationships
*   **From All Modules:** Every submodule (1 through 15) must implicitly call the `log_audit()` function whenever it writes to the database.

---

## USER WORKFLOWS

### Workflow 1: The Statutory Audit Visit
**Actor:** External Auditor (KPMG/PwC)
**Setting:** End of Financial Year.

1.  **Request:** "Show me all manual ledger adjustments (credits) > Rs. 10,000 for the year, and who approved them."
2.  **Action:** The Controller logs in, goes to "Audit & Compliance -> Exception Reports -> Manual Adjustments".
3.  **Filter:** `Amount > 10000`.
4.  **Output:** The system generates a PDF displaying 14 transactions.
5.  **Drill Down:** Auditor picks one: "Credit of Rs. 15,000 to Student X on Oct 14th". Auditor clicks the "View Trail" button.
6.  **Evidence:** The system displays:
    *   Maker: `Clerk_Rahul` (Oct 14, 10:00 AM)
    *   Justification: "Sibling discount applied late per Principal instruction."
    *   Checker: `Principal_Meera` (Oct 14, 11:30 AM)
    *   Document: Scanned copy of Principal's signed memo attached.
7.  **Result:** Auditor is satisfied. Control test passed.

### Workflow 2: Re-opening a Month
**Actor:** CA / Finance Controller

1.  **Context:** It's November 5th. The October period is locked. An accountant discovers a bank charge of Rs. 50 from October 28th was missed in the system.
2.  **Attempt:** Accountant tries to back-date a journal to Oct 28. System blocks: "Period Locked".
3.  **Request:** Accountant raises "Period Unlock Request" for October.
4.  **Approval:** Controller reviews. Clicks "Approve temporary unlock (2 hours)".
5.  **Action:** Accountant enters the entry.
6.  **Auto-Lock:** System automatically re-locks the October period after 2 hours and logs the entire sequence.

---

## REAL-WORLD SCENARIOS

### Scenario A: GST Rate Change Mid-Year
*   **Context:** Government announces GST on Transport increased from 5% to 12% effective October 1st.
*   **Action:** Admin opens "Tax Configurations". Creates a new rule `Transport_GST_12%` with `effective_from = '2025-10-01'`.
*   **System Engine:**
    *   Any invoice generated for dates *before* Oct 1 is stamped with 5%.
    *   Any invoice generated for dates *after* Oct 1 is stamped with 12%.
    *   Even if the bill is generated in November (late back-billing) for September usage, the system correctly applies the 5% historical rate, ensuring perfect tax compliance during the GST audit.

### Scenario B: The Cash Float Discrepancy
*   **Context:** Cashier A's drawer is short by Rs. 2000 at the end of the day.
*   **Workflow:**
    *   Cashier closes the shift in the system. Enters `Actual_Cash_Count = 48,000`.
    *   System Calculates `Expected_Cash = 50,000`.
    *   System logs a `CASH_SHORTAGE` exception of Rs. 2000.
    *   The Rs. 2000 is transferred to a temporary "Cash Shortage Suspense" ledger. The cashier must either find the error or have it deducted from their salary after an HR investigation.

---

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** March 2026

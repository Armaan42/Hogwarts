# STAFF PAYROLL INTEGRATION

**Module:** Fee Management  
**Submodule Code:** FEE-PAY-014  
**Category:** Financial Operations  
**Priority:** Medium (P2)  
**Owners:** HR Manager, Payroll Officer, Accounts Manager

---

## OVERVIEW

The Staff Payroll Integration submodule is a specialized financial bridge that connects the Human Resources (Payroll) domain with the Student Ledger (Fee) domain. In many educational institutions, staff members enroll their own children (Staff Wards) in the school. The school often provides fee concessions, and the remaining fee is routinely deducted directly from the staff member's monthly salary. This module automates this complex tripartite relationship (Employee Salary -> School Revenue -> Student Fee).

### Purpose

To completely eliminate the manual effort of tracking staff ward fees and manually creating salary deduction vouchers. It guarantees that the school recovers its dues reliably before the salary is disbursed, while providing the employee with a seamless, "zero-click" payment experience for their child's education.

### Scope

-   **Staff-Ward Mapping:** Cryptographically linking an `employee_id` to one or more `student_id`s.
-   **Concession Auto-Application:** Verifying employment status before applying the "Staff Discount" rule on the fee invoice.
-   **Payroll Deduction Instructions:** Sending the "Net Payable Fee" as a deduction line-item to the HR/Payroll engine.
-   **Receipt Generation (Contra Entry):** Instantly generating a paid fee receipt without any cash exchanging hands.
-   **Termination Logic:** Handling fee restructuring if the staff member resigns mid-session.

---

## KEY FEATURES

### 1. Robust Staff-Ward Linkage

**Feature Description:**
The foundational data structure verifying familial relationships.
*   When admitting a student, a flag `is_staff_ward` is checked.
*   System requires mapping to an active `employee_id`.
*   Supports mapping multiple children to one employee or even splitting fees across two employees (if both parents work at the school).

### 2. Auto-Deduction Engine

**Feature Description:**
The core calculation interface running prior to monthly salary processing.
*   **Trigger:** On the 25th of the month, the Payroll module queries the Fee module: "What are the pending dues for Employee E123's mapped wards?"
*   **Response:** "Student A owes Rs. 4000 for Q3. Total Deduction = Rs. 4000."
*   **Execution:** Payroll deducts Rs. 4000 under the head "Staff Ward Fee Deduction".

### 3. "Contra" Receipt Generation

**Feature Description:**
Accounting magic to balance the books without a physical bank transaction.
*   Once Payroll confirms the salary has been processed (Rs. 4000 deducted), the Fee module automatically generates a Fee Receipt for Student A.
*   The Payment Mode is marked as `SALARY_DEDUCTION`.
*   The accounting journal bypasses the "Bank" ledger and routes the funds directly via an inter-departmental "Salary Clearing" ledger.

### 4. Resignation/Termination Recalculation

**Feature Description:**
Protecting the school's financial interests when an employee leaves.
*   **Trigger:** HR marks employee status as `RESIGNED`.
*   **Action 1 (Stop Deduction):** The link to Payroll is severed.
*   **Action 2 (Revoke Benefit):** The "Staff Discount (e.g., 50%)" is automatically revoked for the remaining months of the academic year.
*   **Action 3 (Invoice Generation):** System calculates the difference and raises a new, standard fee invoice to the parent (ex-employee) for the undiscounted balance, payable via standard online methods.

---

## DATABASE SCHEMA

To link the HR and Student domains.

### 1. Staff Ward Mappings (`fee_staff_ward_links`)
The central junction table.

```sql
CREATE TABLE fee_staff_ward_links (
    link_id INT PRIMARY KEY AUTO_INCREMENT,
    employee_id INT NOT NULL, -- FK to HR/Payroll Module
    student_id INT NOT NULL,  -- FK to Student Module
    
    deduction_percentage DECIMAL(5,2) DEFAULT '100.00', -- E.g., deduct 100% of the fee from salary
    
    is_active BOOLEAN DEFAULT TRUE,
    activation_date DATE,
    deactivation_date DATE, -- Populated upon resignation/transfer
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Payroll Deduction Ledger (`fee_payroll_deduction_logs`)
Audit trail of what was sent to HR and what was acknowledged.

```sql
CREATE TABLE fee_payroll_deduction_logs (
    log_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    employee_id INT NOT NULL,
    associated_invoice_id BIGINT,
    
    payroll_month VARCHAR(10), -- '2025-10'
    requested_amount DECIMAL(10,2),
    
    status ENUM('SENT_TO_PAYROLL', 'DEDUCTED', 'FAILED_INSUFFICIENT_SALARY', 'SKIPPED'),
    deducted_amount DECIMAL(10,2), -- Might be less than requested if salary is low
    
    processed_at DATETIME
);
```

---

## BUSINESS RULES

### Rule 1: The "Insufficient Net Pay" Problem
*   **Legal Constraint:** Labor laws often dictate that an employee's take-home pay cannot drop below a certain threshold (e.g., Minimum Wage).
*   **Resolution:** If "Gross Salary - PF - Tax = Rs. 15,000", but the "Ward Fee Due = Rs. 20,000", the Payroll system will only deduct up to the legal maximum (e.g., Rs. 5000). The Fee module will record a partial receipt of Rs. 5000, leaving Rs. 15,000 as `Outstanding` on the student's ledger, which the employee must pay manually via cash/online.

### Rule 2: EMI / Installment Smoothing
*   Quarterly fees create huge spikes (e.g., Rs. 30,000 due in April). Deducting this from one month's salary causes hardship.
*   **Rule:** For Staff Wards, the system automatically converts Quarterly or Annual fees into 12 equal Monthly Installments (EMI) to ensure a smooth deduction profile across the year.

### Rule 3: Tax Implications (Perquisite Value)
*   In some jurisdictions, a "Free Education" benefit provided to an employee's child is considered a taxable "Perquisite" (fringe benefit).
*   The system calculates the "Market Value of Fee - Actual Paid" and sends this "Perk Value" to the Payroll module to add to the employee's taxable income calculation (e.g., Form 16 in India).

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Payroll Module:** API endpoint `POST /payroll/deductions` sending an array of `[employee_id, amount, reason]`.
*   **To Accounting System:** Posts the complex journal entry:
    `Dr. Salary Expense (Gross)`
    `Cr. Provident Fund Payable`
    `Cr. TDS Payable`
    `Cr. Fee Revenue (The Deduction)`
    `Cr. Bank Account (Net Take Home)`

### Inbound Relationships
*   **From Payroll Engine:** Webhook acknowledging "Salary Run Complete", returning the exact amount successfully deducted per employee, triggering the fee receipt generation.

---

## USER WORKFLOWS

### Workflow 1: The Monthly Salary Run
**Actor:** Payroll Manager

1.  **Initiation:** On 26th of the month, Manager clicks "Generate Draft Payroll" in HR Module.
2.  **API Call:** HR system pings Fee system for deductions.
3.  **Calculation:** Fee system checks `fee_staff_ward_links`. Finds Employee #005 has 2 kids. Total pending monthly installment = Rs. 6000.
4.  **Draft:** Payroll shows Employee #005: Salary Rs. 50,000. Fee Deduction Rs. 6,000. Net Rs. 44,000.
5.  **Finalization:** Manager clicks "Process Payroll & Disburse".
6.  **Receipts:** Fee module receives success ping -> Generates two Rs. 3000 receipts for the two children -> Emails receipts to Employee #005.

### Workflow 2: Employee Resignation Handling
**Actor:** HR Officer

1.  **Event:** Employee #005 submits resignation, final working day is Nov 30.
2.  **Action:** HR Officer processes "Exit/Full & Final Settlement".
3.  **Trigger:** System flags `is_active = FALSE` on the staff-ward link.
4.  **Reversal:** The "50% Staff Waiver" rule is removed from the student profile effective Dec 1st.
5.  **Billing:** System generates an invoice for the child for Dec-Mar at the **Full Public Rate**.
6.  **Demand:** This invoice is pushed to the parent portal. Since there is no salary to deduct from, the parent must now pay via the online payment gateway.

---

## EXTENDED DATABASE SCHEMA

### 3. Staff Ward Concession Audit (`fee_staff_concession_audit`)
Tracks the financial impact of staff ward benefits for trust board reporting.

```sql
CREATE TABLE fee_staff_concession_audit (
    audit_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    employee_id INT NOT NULL,
    student_id INT NOT NULL,
    academic_year_id INT NOT NULL,
    
    gross_fee_applicable DECIMAL(15,2), -- What the public would pay
    concession_amount DECIMAL(15,2),    -- What was waived
    net_fee_charged DECIMAL(15,2),      -- What the employee actually pays
    deducted_from_salary DECIMAL(15,2), -- Of the net, how much was salary-deducted
    paid_externally DECIMAL(15,2),      -- Any gap paid via cash/online
    
    perquisite_value DECIMAL(15,2),     -- Taxable perk value sent to HR
    
    snapshot_date DATE DEFAULT CURRENT_DATE,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 4. EMI Smoothing Schedule (`fee_staff_emi_schedule`)
The pre-calculated monthly deduction plan for each staff ward.

```sql
CREATE TABLE fee_staff_emi_schedule (
    emi_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    link_id INT NOT NULL, -- FK to fee_staff_ward_links
    
    payroll_month VARCHAR(10), -- '2026-04'
    emi_amount DECIMAL(10,2),
    
    source_invoice_ids JSON, -- [101, 102] -- which invoices this EMI covers
    
    status ENUM('SCHEDULED', 'DEDUCTED', 'SKIPPED', 'ADJUSTED'),
    
    FOREIGN KEY (link_id) REFERENCES fee_staff_ward_links(link_id)
);
```

---

## ADDITIONAL BUSINESS RULES

### Rule 4: Dual-Employment Benefit
*   If both Father (Employee #010) and Mother (Employee #020) work at the school, only ONE parent can claim the Staff Ward discount.
*   The system flags the duplicate and asks: "Two employees mapped to Student A. Which employee should receive the concession? The other will receive a standard invoice."
*   Logic uses `PRIMARY_CLAIMANT` flag on `fee_staff_ward_links`.

### Rule 5: Probation Period Exclusion
*   New employees in their probation period (first 6 months) may NOT be eligible for the Staff Ward discount.
*   The system checks `employee.probation_end_date`. If `CURRENT_DATE < probation_end_date`, the concession rule is suppressed.
*   Once the employee is confirmed, the system retroactively calculates the discount for the probation months and issues a Credit Note.

### Rule 6: Leave Without Pay (LWP) Impact
*   If an employee is on extended LWP (e.g., medical leave > 30 days), the Payroll module returns `FAILED_INSUFFICIENT_SALARY` because the net pay might be Rs. 0.
*   The Fee module keeps the invoice as `OUTSTANDING` and does NOT revoke the staff ward discount. It simply waits for the employee to return and resume deductions through arrears recovery.

### Rule 7: Contract vs. Permanent Staff
*   Contractual staff (security guards, housekeeping) may receive different discount tiers than permanent faculty.
*   The concession engine reads `employee.employment_type` and applies the correct rule:
    *   `PERMANENT`: 100% Tuition Waiver.
    *   `CONTRACTUAL`: 50% Tuition Discount.
    *   `OUTSOURCED`: No discount (not eligible).

---

## EDGE CASES

### Edge Case 1: Staff Member with Children in Multiple Schools
*   **Scenario:** Employee's elder child is in the High School campus, younger child is in the Junior School campus (separate legal entities under the same Trust).
*   **Resolution:** The system supports `multi_campus_link`. The deduction can be split:
    *   High School campus raises deduction instruction for Rs. 3000.
    *   Junior School campus raises deduction instruction for Rs. 2000.
    *   Payroll (centralized) deducts Rs. 5000 total in one salary slip, splitting the credit across two campus bank accounts.

### Edge Case 2: Mid-Month Resignation
*   **Scenario:** Employee resigns effective 15th of the month. The full month's EMI (Rs. 5000) was scheduled.
*   **Resolution:** The system pro-rates the final deduction: `(15/30) * Rs. 5000 = Rs. 2500` deducted from the final settlement. The remaining Rs. 2500 is converted into a standalone invoice sent to the ex-employee's registered email.

### Edge Case 3: Child Expelled / Withdrawn
*   **Scenario:** Employee's child is expelled for disciplinary reasons mid-term.
*   **Resolution:**
    1.  Student Management module triggers `WITHDRAWAL` event.
    2.  Fee module deactivates the staff-ward link.
    3.  Remaining pre-paid EMIs (if any) are refunded via Credit Note to the employee's next salary.
    4.  The staff ward concession for the remaining period is reversed and posted as revenue recovery.

### Edge Case 4: Salary Advance Offset
*   **Scenario:** Employee has already taken a salary advance of Rs. 20,000. After deducting the advance, PF, and tax, the remaining salary is only Rs. 8,000. The ward's EMI is Rs. 10,000.
*   **Resolution:** Payroll deducts Rs. 8,000 (the maximum legally permissible after minimum wage protection). The Fee module records a partial receipt and carries forward Rs. 2,000 as a "Deduction Shortfall" to be recovered in next month's salary run automatically.

---

## REAL-WORLD SCENARIOS

### Scenario A: The Teacher Who Gets Promoted
*   **Context:** A Junior Teacher (contractual, 50% discount) gets confirmed as Senior Faculty (permanent, 100% waiver) effective January 1st.
*   **HR Action:** Employment type changes from `CONTRACTUAL` to `PERMANENT`.
*   **Fee System Response:**
    1.  The concession rule for the ward automatically upgrades from 50% to 100%.
    2.  For January onwards, invoices are generated with 100% waiver.
    3.  For October-December (already billed at 50%), the system calculates the retrospective benefit: `(50% of Quarterly Fee)` and issues a Credit Note to the employee's next salary.

### Scenario B: Trust Board Audit of Staff Benefits
*   **Context:** The Board asks: "How much revenue are we forgoing due to Staff Ward benefits?"
*   **Report Engine:**
    1.  Queries `fee_staff_concession_audit`.
    2.  Aggregates: Total Staff Children = 85. Total Gross Fee = Rs. 1.2 Crores. Total Concession = Rs. 95 Lakhs. Net Realized = Rs. 25 Lakhs.
    3.  Breakdown by employment type: Permanent staff cost the trust Rs. 80L, Contractual staff Rs. 15L.
    4.  Year-over-year trend: Staff ward concessions grew by 12% over last year due to 8 new hires with children.
*   **Board Decision:** Cap staff ward admissions at 100 per academic year.

### Scenario C: The Inter-School Transfer
*   **Context:** A teacher transfers from Hogwarts North (where the child studied) to Hogwarts South.
*   **Action:**
    1.  HR processes the inter-school transfer effective Nov 1.
    2.  The system deactivates the staff-ward link at Hogwarts North.
    3.  A new enrollment request triggers at Hogwarts South.
    4.  The system auto-creates a new `fee_staff_ward_links` entry at Hogwarts South.
    5.  The child's fee at North is settled up to Oct 31 (final deduction from North salary run).
    6.  The child's fee at South starts from Nov 1 (deducted from the new campus's payroll).

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `staff_ward_max_children` | 3 | Maximum number of children eligible per employee |
| `staff_ward_probation_eligible` | `false` | Whether probationary employees get the discount |
| `staff_ward_emi_months` | 12 | Number of months to spread quarterly/annual fees |
| `staff_ward_perk_calc_method` | `MARKET_RATE_MINUS_PAID` | How to calculate taxable perquisite value |
| `staff_ward_minimum_wage_guard` | `true` | Block deductions that violate minimum wage laws |
| `staff_deduction_day_of_month` | 25 | Day payroll queries fee module for pending dues |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

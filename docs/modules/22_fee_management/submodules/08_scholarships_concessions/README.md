# SCHOLARSHIPS & CONCESSIONS MANAGEMENT

**Module:** Fee Management  
**Submodule Code:** FEE-SCHOL-008  
**Category:** Core Processing  
**Priority:** High (P1)  
**Owners:** Principal, Trust Board, Accounts Officer

---

## OVERVIEW

The Scholarships & Concessions Management submodule facilitates the equitable and regulated distribution of financial aid. It moves away from ad-hoc discounts to a structured "Grant & Approval" workflow. It handles merit-based scholarships, need-based financial aid, sibling discounts, and staff ward benefits, ensuring that every rupee waived is accounted for as "Scholarship Expense" rather than just lost revenue.

### Purpose

To maximize enrollment by offering financial incentives while maintaining strict budgetary control. It prevents revenue leakage by preventing unauthorized discounts and tracking the academic performance of scholarship recipients (to enforce renewal conditions).

### Scope

-   **Rule Engine:** DEFINING eligibility criteria (e.g., "Sibling > 2nd Child gets 50%").
-   **Application Workflow:** Parents applying for aid -> Document Submission -> Review -> Approval/Rejection.
-   **Budgeting:** Setting a cap (e.g., "Max ₹10 Lakhs scholarship for Grade 10").
-   **Auto-Renewal:** Checking if the student maintained the required GPA/Attendance to keep the scholarship.
-   **donor Management:** Tracking external sponsors who fund specific students.

---

## KEY FEATURES

### 1. Unified Concession Rule Engine

**Feature Description:**
A logic layer that automatically applies standard discounts during Fee Assignment.

**Types:**
*   **Flat Amount:** "₹5000 off for Early Bird Admission".
*   **Percentage:** "25% off Tuition for Teaching Staff".
*   **Composite:** "50% off Tuition AND 100% off Admission Fee".

**Hierarchy:**
*   **Stacking Rules:** Can "Sibling Discount" and "Staff Discount" be claimed together? (Configurable: Yes/No/Max-Cap).

### 2. Scholarship Lifecycle Management

**Feature Description:**
Treating financial aid as a contract.
*   **Application:** Student submits application with "Incoming Income Certificate" or "Last Yeat Grade Sheet".
*   **Grant Letter:** System generates a formal letter stating: "Awarded 50% waiver for AY 2025-26, subject to maintaining 85% attendance."
*   **Disbursement:** Instead of reducing the fee directly, the system creates a "Credit Note" funded by the "Scholarship Fund Ledger".

### 3. Performance-Linked Renewal

**Feature Description:**
Ensures scholarships are earned, not entitled.
*   **Integration:** Connects to Exam Module.
*   **Trigger:** At end of Term 1, system scans scholars.
*   **Action:**
    *   Student A (92%): Auto-Renew.
    *   Student B (65%): Flag for "Scholarship Warning".
    *   Student C (40%): Auto-Revoke for Term 2.

### 4. Sponsor & Donor Tracking

**Feature Description:**
For NGOs or classic "Adopt a Student" models.
*   **Scenario:** corporate CSR pays for 50 girls.
*   **Action:**
    1.  Create internal invoices for 50 girls.
    2.  Map them to Sponsor "TechCorp Ltd".
    3.  Generate a single Consolidated Bill for TechCorp.
    4.  Parents see "Paid by Sponsor" on their portal.

---

## DATABASE SCHEMA

### 1. Concession Rules (`fee_concession_rules`)
The definitions of available discounts.

```sql
CREATE TABLE fee_concession_rules (
    rule_id INT PRIMARY KEY AUTO_INCREMENT,
    rule_name VARCHAR(100), -- "Sibling Discount"
    
    discount_type ENUM('PERCENTAGE', 'FLAT'),
    value DECIMAL(10,2),
    
    applicable_heads JSON, -- IDs of fee heads [1, 5]
    criteria_script TEXT, -- Custom logic if complex
    
    budget_limit DECIMAL(15,2), -- Total amount allowed per year
    consumed_amount DECIMAL(15,2) DEFAULT 0
);
```

### 2. Scholarship Grants (`fee_scholarvention_grants`)
The actual assignment to a student.

```sql
CREATE TABLE fee_scholarship_grants (
    grant_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    rule_id INT NOT NULL,
    
    academic_year_id INT,
    grant_date DATE,
    valid_until DATE,
    
    status ENUM('ACTIVE', 'REVOKED', 'EXPIRED'),
    
    conditions TEXT, -- "Maintain GPA > 8.0"
    sponsor_id INT, -- Optional external funder
    
    approved_by INT,
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 3. Sponsor Accounts (`fee_sponsors`)
External entities paying fees.

```sql
CREATE TABLE fee_sponsors (
    sponsor_id INT PRIMARY KEY AUTO_INCREMENT,
    sponsor_name VARCHAR(255),
    contact_person VARCHAR(100),
    
    billing_address TEXT,
    gst_number VARCHAR(20),
    
    pledged_amount DECIMAL(15,2),
    utilized_amount DECIMAL(15,2)
);
```

---

## BUSINESS RULES

### Rule 1: Approval Hierarchy
*   **Level 1 (Clerk):** Can apply "Sibling Discount" (Standard).
*   **Level 2 (Principal):** Can approve "Merit Scholarship" up to 50%.
*   **Level 3 (Trustee):** Must approve "Full Waiver" or "Special Cases".

### Rule 2: Accounting Treatment
*   **Method A (Net Off):** Invoice is generated for Net Amount (e.g., Fee 100 - Disc 20 = Bill 80).
*   **Method B (Gross Up):** Invoice 100. Credit Note 20 (Expense). Payment 80.
    *   System defaults to Method B for better transparency in P&L Account.

### Rule 3: Conflict Resolution
*   **Max Benefit:** If a student qualifies for both "Sports Quota" (50%) and "Staff Ward" (100%), the system applies the **Higher** benefit (100%).
*   It does NOT sum them to 150% (Refund).

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Fee Assignment:** The engine runs *during* assignment to calculate the final demand.
*   **To Report Card:** Adds a footnote "Scholarship Student" (visible only to teachers depending on privacy policy).

### Inbound Relationships
*   **From Exam Results:** Pulls GPA/Marks for renewal checks.
*   **From HR Module:** Checks "Employee Status" to validate Staff Ward benefits (e.g., if staff resigns, discount stops).

---

## USER WORKFLOWS

### Workflow 1: Applying for Financial Aid
**Actor:** Parent

1.  **Portal:** Parent logs in -> "Apply for Scholarship".
2.  **Form:** Selects "EWS (Economically Weaker Section)".
3.  **Upload:** Uploads Income Tax Return (ITR) and Salary Slip.
4.  **Submit:** Application Status: "Under Review".
5.  **Offline:** Admin verifies documents physically.
6.  **Decision:** Admin updates status to "Approved - 75% Waiver".
7.  **Impact:** Provide opens "Fee View" -> Sees reduced amount.

### Workflow 2: Corporate Sponsorship Billing
**Actor:** Accounts Manager

1.  **Setup:** Create Sponsor "Rotary Club".
2.  **Map:** Select 20 deserving students -> Map to "Rotary Club".
3.  **Bill:** Click "Generate Sponsor Invoice".
4.  **Output:** A single invoice for ₹5,00,000 sent to Rotary Club.
5.  **Payment:** Rotary pays via Cheque.
6.  **Resolution:** System automatically marks all 20 students' Q1 fees as "PAID (by Sponsor)".

---

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** January 20, 2026

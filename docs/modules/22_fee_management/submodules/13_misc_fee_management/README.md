# MISCELLANEOUS FEE MANAGEMENT

**Module:** Fee Management  
**Submodule Code:** FEE-MISC-013  
**Category:** Optional Services & Penalties  
**Priority:** Medium (P2)  
**Owners:** Accounts Officer, Department Heads (Library, Lab, Sports)

---

## OVERVIEW

The Miscellaneous Fee Management submodule acts as a catch-all for non-standard, ad-hoc, or deeply departmental financial transactions. While Tuition is predictable and bulk-assigned, items like "Lost ID Card Fines", "External Olympiad Exam Fees", or "Annual Day Costume Charges" are unpredictable and student-specific. This submodule decentralizes the *request* for a fee to the relevant department while centralizing the *collection* through the Accounts office.

### Purpose

To ensure that every minor charge levied by the school is properly recorded, invoiced, and collected without creating a chaotic, undocumented cash economy within the school (e.g., teachers collecting cash directly from students).

### Scope

-   **Ad-Hoc Invoicing:** Raising single invoices for specific events or penalties.
-   **Event Ticketing/Billing:** Charging students for optional field trips or seminars.
-   **Consumable Billing:** Billing for broken lab equipment or lost library books.
-   **Departmental Requisitions:** Allowing a librarian to "Request a Invoice" which the accounts team approves.
-   **Store/Uniform Purchase:** Integrating with the school tuck shop or uniform vendor for digital billing.

---

## KEY FEATURES

### 1. Decentralized Fee Requisition Workflow

**Feature Description:**
Empowering non-finance staff to initiate a charge securely.

**Workflow:**
1.  **Initiation:** The Sports Teacher notices a student lost a school-issued Jersey. The teacher logs into the portal and raises a "Charge Request".
2.  **Details:** Selects Student Name, Category: "Sports Fine", Amount: Rs. 500, Reason: "Lost Jersey".
3.  **Approval (Optional):** If the amount > Rs. 1000, it routes to the Principal for approval to prevent arbitrary fining.
4.  **Invoicing:** The system automatically generates `INV-MISC-2025-9922` and publishes it to the Parent Portal.

### 2. Bulk Event Billing (The "Field Trip" Scenario)

**Feature Description:**
Simplifying mass optional charges.
*   **Setup:** Admin creates an Event: "NASA Space Camp" -> Cost Rs. 85,000 -> Open to Grades 11-12.
*   **Consent & Demand:** Instead of forcing the invoice on everyone, the system sends a "Consent Form" to the portal.
*   **Execution:** When a parent clicks "I Consent", the system instantly generates the invoice for Rs. 85,000 and demands payment.

### 3. Inventory to Invoice Linkage

**Feature Description:**
Connecting the physical store to the parent ledger.
*   If a boarding student goes to the central store and receives 3 notebooks and 2 pens, the Store Manager logs the issue in the Inventory Module.
*   The system translates the inventory value (Rs. 250) into a "Misc Fee" invoice appended to the student's monthly bill or deducted from their Imprest account (if residential).

### 4. Categorization & Ledger Routing

**Feature Description:**
Ensuring ₹500 for a lost book goes to the "Library Fund" and ₹500 for a broken beaker goes to the "Science Dept Fund".
*   Every Misc Fee item must be tagged with a specific General Ledger (GL) Code.
*   This ensures the CFO can see exactly how much revenue was generated from "Fines" vs "Extracurriculars".

---

## DATABASE SCHEMA

### 1. Misc Fee Templates (`fee_misc_templates`)
Standardized catalog of common charges.

```sql
CREATE TABLE fee_misc_templates (
    template_id INT PRIMARY KEY AUTO_INCREMENT,
    item_name VARCHAR(100), -- "Duplicate ID Card", "Prospectus Form"
    default_amount DECIMAL(10,2),
    
    gl_account_id INT, -- Maps to Accounting Chart of Accounts
    department_id INT, -- E.g., Library
    
    is_taxable BOOLEAN DEFAULT FALSE,
    tax_rate DECIMAL(5,2) DEFAULT 0.00
);
```

### 2. Ad-Hoc Invoices (`fee_adhoc_invoices`)
The actual charges raised. (Note: Often this is merged into `fee_invoices` with a specific tag, but separated here for clarity).

```sql
CREATE TABLE fee_adhoc_invoices (
    adhoc_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    template_id INT, -- Null if completely custom
    
    amount DECIMAL(10,2) NOT NULL,
    reason TEXT,
    
    raised_by INT, -- Staff ID (e.g., Librarian)
    raised_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    status ENUM('PENDING_APPROVAL', 'APPROVED_UNPAID', 'PAID', 'CANCELLED'),
    
    linked_main_invoice_id BIGINT, -- If consolidated into the Term Bill
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## BUSINESS RULES

### Rule 1: Cashless Campus Policy
*   To enforce financial discipline, the system blocks the `raised_by` person (e.g., the teacher) from marking the invoice as "Paid via Cash".
*   Only designated Cashiers or the Online Payment Gateway can update the payment status of a Misc Fee.

### Rule 2: Consolidation vs. Standalone
*   **Standalone:** A fine for breaking a window must be paid immediately. The system generates an instant invoice with Due Date = `Today + 3 Days`.
*   **Consolidation:** A charge for "Annual Magazine" (Rs. 300) is aggregated. When the Quarter 2 mass billing runs, the system scoops up all pending consolidated charges and merges them into the main tuition invoice to reduce transaction fatigue for parents.

### Rule 3: Reversal Constraints
*   If a Librarian raises a Rs. 500 fine, and the student later finds the lost book, the Librarian *cannot* delete the invoice if it has already been "Approved".
*   They must raise a "Reversal Request" which creates a Credit Note, ensuring a proper audit trail of the reversal.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To General Ledger:** Maps every specific sub-type of misc fee to its correct revenue bucket for P&L reporting.
*   **To Parent App:** Sends push notification: "A new charge of Rs. 150 for 'Late Library Return' has been added to your account."

### Inbound Relationships
*   **From Library/Inventory Modules:** APIs exposed so that when a book is marked "Lost" in the Library software, it automatically POSTs a json payload to create the fee demand.

---

## USER WORKFLOWS

### Workflow 1: The Duplicate ID Card Process
**Actor:** Front Desk / Admin

1.  **Request:** Student comes to front desk: "I lost my ID card."
2.  **Initiation:** Admin opens Student Profile -> Actions -> "Issue Misc Charge".
3.  **Selection:** Selects Template: "Duplicate ID Card". Cost: Rs. 200 auto-fills.
4.  **Generation:** System generates Invoice.
5.  **Payment:** Student pulls out phone, scans QR code on Admin's screen (UPI integration).
6.  **Clearance:** System registers Rs. 200 receipt.
7.  **Fulfillment:** Only after status confirms `PAID`, the Admin clicks "Print New ID Card".

### Workflow 2: Olympiad Exam Registration
**Actor:** Science HOD & Parents

1.  **Setup:** HOD asks IT to create Misc Item: "National Science Olympiad" (Rs. 150), tagged to Gl: External Exams.
2.  **Broadcast:** IT sends an alert to all Grade 4-10 parents.
3.  **Action:** Interested parents click "Enroll & Pay" on their portal.
4.  **Tracking:** HOD opens "Olympiad Report" which shows a real-time list of 120 paid students.
5.  **Reconciliation:** School pays the external organization a lump sum of Rs. 18,000 (120 x 150) from the collected funds.

---

## EXTENDED DATABASE SCHEMA

### 3. Event/Activity Fee Registrations (`fee_event_registrations`)
Tracks consent and payment for optional events.

```sql
CREATE TABLE fee_event_registrations (
    reg_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    event_name VARCHAR(150),
    event_date DATE,
    
    fee_amount DECIMAL(10,2) NOT NULL,
    consent_given BOOLEAN DEFAULT FALSE,
    consent_date DATETIME,
    
    payment_status ENUM('PENDING', 'PAID', 'REFUNDED', 'CANCELLED'),
    linked_invoice_id BIGINT,
    
    organizer_department_id INT,
    external_vendor_name VARCHAR(150), -- E.g., "Trekking Co."
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 4. Department Charge Requisitions (`fee_dept_requisitions`)
The formal request pipeline for non-finance departments.

```sql
CREATE TABLE fee_dept_requisitions (
    req_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    department_id INT NOT NULL,
    raised_by_staff_id INT NOT NULL,
    
    student_id INT NOT NULL,
    charge_category VARCHAR(100), -- 'FINE', 'CONSUMABLE', 'EVENT'
    description TEXT,
    amount DECIMAL(10,2) NOT NULL,
    
    supporting_evidence_url VARCHAR(255), -- Photo of broken equipment
    
    approval_status ENUM('DRAFT', 'SUBMITTED', 'APPROVED', 'REJECTED'),
    approved_by INT,
    approved_at DATETIME,
    rejection_reason TEXT,
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## ADDITIONAL BUSINESS RULES

### Rule 4: Maximum Fine Cap
*   No single ad-hoc fine raised by a department can exceed Rs. 5,000 without mandatory Principal approval.
*   Cumulative fines on a single student exceeding Rs. 10,000 in a quarter trigger an automatic "Student Welfare Review" notification to the counselor.

### Rule 5: Event Cancellation & Refund
*   If the school cancels an event (e.g., rain washes out the field trip), the system must auto-refund all registered parents.
*   Logic: Bulk update `fee_event_registrations.payment_status = 'REFUNDED'` and generate Credit Notes for all linked invoices.
*   Refund is non-negotiable; the school cannot retain the fees for a cancelled activity.

### Rule 6: Statute of Limitations on Fines
*   A fine for a lost library book cannot be raised more than 90 days after the due date of the book return.
*   After 90 days, the system suggests "Write Off" as the item is considered irrecoverable. This prevents stale fines appearing on student accounts during year-end settlements.

### Rule 7: Parental Dispute Right
*   Parents must be given a 7-day window to dispute any ad-hoc charge before it becomes "Final".
*   During the 7-day window, the invoice status is `UNDER_REVIEW`. Automated overdue reminders are suppressed.

---

## EDGE CASES

### Edge Case 1: Duplicate Fine Prevention
*   **Scenario:** The Librarian marks "Book Lost" in the Library Module, which auto-creates a fine. The front desk admin, unaware, also manually raises a fine for the same book.
*   **Resolution:** The system checks for existing `fee_adhoc_invoices` with the same `student_id + template_id + amount` within the last 30 days. If found, the second request is flagged as "Potential Duplicate" and requires explicit confirmation ("A similar charge of Rs. 500 was raised on Jan 15. Proceed anyway?").

### Edge Case 2: Student Transferred Between Sections
*   **Scenario:** Student moves from Section A to Section B mid-term. Section A's teacher raised a fine but the student now sits in Section B.
*   **Resolution:** The system links the charge to the `student_id`, not the section. The fine follows the student regardless of section changes.

### Edge Case 3: Zero-Amount Charges
*   **Scenario:** School wants to track "Uniform Issued" for audit purposes without charging because uniforms are sponsored.
*   **Resolution:** The system allows `amount = 0.00` invoices tagged with category `RECORD_ONLY`. These appear in the inventory audit trail but are excluded from the parent's financial dashboard.

### Edge Case 4: Bulk Fine for Class Misbehavior
*   **Scenario:** The Vice Principal decides to fine an entire class of 40 students Rs. 200 each for a collective discipline issue.
*   **Resolution:** The system supports "Bulk Charge" mode. Admin selects Grade 8A, selects template "Discipline Fine", enters Rs. 200. System generates 40 individual invoices with a shared `batch_reference_id` for easy reversal if the decision is overturned.

---

## REAL-WORLD SCENARIOS

### Scenario A: Annual Day Costume Collection
*   **Context:** The school's Annual Day function requires each participating student to purchase a costume (Rs. 800).
*   **Flow:**
    1.  Cultural Dept creates Event: "Annual Day 2026 Costume" (Rs. 800).
    2.  System sends consent forms to 200 participating students' parents.
    3.  150 parents consent and pay within 1 week.
    4.  30 parents consent but don't pay yet (status: APPROVED_UNPAID).
    5.  20 parents decline (no invoice generated).
    6.  School orders 180 costumes (150 paid + 30 committed).
    7.  Outstanding Rs. 24,000 (30 x 800) appears in the department's pending collection report.

### Scenario B: Lab Equipment Breakage
*   **Context:** During a Chemistry practical, a student accidentally breaks a titration apparatus worth Rs. 1,200.
*   **Workflow:**
    1.  Lab Technician logs damage in the Inventory Module with a photo.
    2.  Inventory Module auto-triggers a POST to Fee API: `{student_id: 3092, template: 'LAB_BREAKAGE', amount: 1200}`.
    3.  Fee system creates an ad-hoc invoice and routes it to the Science HOD for approval.
    4.  HOD reviews the damage photo and approves.
    5.  Parent receives notification: "A charge of Rs. 1,200 for damaged lab equipment has been added. See details."
    6.  Parent disputes within 7 days: "My child says the equipment was already cracked."
    7.  HOD investigates, agrees partially, and reduces the charge to Rs. 600 via a Credit Note.

### Scenario C: Uniform Store Digital Billing
*   **Context:** Instead of the uniform vendor collecting cash directly, the school integrates the store's POS with the ERP.
*   **Flow:**
    1.  Parent selects 2 shirts (Rs. 400 each) and 1 pair of shoes (Rs. 1,200) at the school uniform store.
    2.  Store clerk scans items. POS creates a misc invoice for Rs. 2,000 against the student's account.
    3.  Parent can either pay immediately at the counter (POS card swipe) or choose "Add to My Account" where the Rs. 2,000 gets consolidated into the next quarterly fee bill.
    4.  This eliminates cash handling at the store entirely.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `misc_auto_approval_threshold` | Rs. 1,000 | Charges below this don't need Principal sign-off |
| `misc_dispute_window_days` | 7 | Days a parent has to challenge a charge |
| `misc_consolidation_mode` | `QUARTERLY` | When pending misc charges merge into the main bill |
| `misc_fine_staleness_days` | 90 | Max days after incident a fine can be raised |
| `misc_duplicate_check_window` | 30 | Days to check for duplicate charges |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

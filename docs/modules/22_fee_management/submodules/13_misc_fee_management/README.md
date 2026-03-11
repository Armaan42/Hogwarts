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

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** March 2026

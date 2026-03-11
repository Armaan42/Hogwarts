# PAYMENT COLLECTION & PROCESSING

**Module:** Fee Management  
**Submodule Code:** FEE-COLL-004  
**Category:** Financial Operations  
**Priority:** Critical (P0)  
**Owners:** Cashier, Accounts Manager

---

## OVERVIEW

The Payment Collection & Processing submodule handles the incoming flow of revenue. It acts as the gateway for receiving money via multiple channels (Online, Cash, Cheque, UPI) and reconciling it against pending invoices. It is designed to be rigorous, ensuring that every rupee received is accounted for, receipted, and mapped to the student's ledger instantly.

### Purpose

To provide a secure, error-free, and flexible mechanism for parents to pay fees while ensuring real-time ledger updates for the school. It reduces manual data entry, prevents fraud/theft, and offers convenience through digital payment options.

### Scope

-   **Multi-Mode Collection:** Handling Cash, Cheques, Demand Drafts (DD), NEFT/RTGS, Credit/Debit Cards, UPI, and Wallets.
-   **Payment Gateway Integration:** Seamless connection with providers like Razorpay, Stripe, BillDesk.
-   **Cashier Operations:** Interface for school office staff to collect physical payments.
-   **Receipt Generation:** Instant digital and physical receipts.
-   **Payment Allocation:** Logic to distribute a single lump-sum payment across multiple invoice heads (Tuition vs. Transport vs. Tax).
-   **Failed Payment Handling:** Managing bounced cheques and failed online transactions.

---

## KEY FEATURES

### 1. Online Payment Gateway (PG) Integration

**Feature Description:**
The primary mode of collection (aiming for >90% usage).
*   **Seamless Flow:** Parent clicks "Pay Now" in portal -> Redirected to PG -> Enters Details -> Redirected back with Success/Failure.
*   **Split Settlements:** Supports splitting funds into different bank accounts (e.g., Trust Account vs. Transport Company Account) if PG supports Nodal Accounts.
*   **Real-Time Status:** Webhooks ensure that even if the user closes the browser, the server gets the payment confirmation.

### 2. POS / Cashier Console

**Feature Description:**
For parents paying at the school counter.
*   **Search:** Quick lookup by Student Name, ID, or Parent Phone.
*   **Dues Display:** Shows clearly what is pending (Q1, Q2, Transport).
*   **Mode Selection:** Cash, Cheque (Requires Cheque No, Bank Name, Date), or POS Machine (Card Swipe).
*   **Drawer Management:** Tracks opening and closing cash balance for the cashier.

### 3. Cheque & DD Lifecycle Management

**Feature Description:**
Unlike instant payments, Cheques have a lifecycle.
1.  **Received:** Entetred into system. Status: `PENDING_CLEARANCE`. Receipt status: `PROVISIONAL`.
2.  **Deposited:** Grouped into a "Deposit Slip" and sent to bank.
3.  **Cleared:** Mark as `CLEARED` upon bank confirmation. Ledger updated fully.
4.  **Bounced:** Mark as `BOUNCED`. System reverses the payment and auto-applies a "Cheque Bounce Penalty".

### 4. Smart Payment Allocation

**Feature Description:**
When a parent pays ₹50,000 against a total due of ₹50,000, it's simple. But if they pay ₹25,000 (Partial) or ₹60,000 (Excess), rules apply.
*   **FIFO:** Clear oldest invoices first.
*   **Head Priority:** Clear Penalties -> Tuition -> Annual Charges.
*   **Excess handling:** Store extra amount in a "Student Wallet" (Advance) for future adjustment.

---

## DATABASE SCHEMA

### 1. Payments (`fee_payments`)
Master record of an incoming transaction.

```sql
CREATE TABLE fee_payments (
    payment_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    
    -- Transaction Details
    amount_received DECIMAL(12,2) NOT NULL,
    payment_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    payment_mode ENUM('CASH', 'CHEQUE', 'DD', 'ONLINE', 'POS', 'NEFT') NOT NULL,
    
    -- References
    transaction_ref_no VARCHAR(100), -- PG Transaction ID or Cheque No
    bank_name VARCHAR(100), -- For Cheques/NEFT
    instrument_date DATE,   -- For Cheques
    
    -- Status
    status ENUM('SUCCESS', 'PENDING', 'FAILED', 'BOUNCED', 'REFUNDED') DEFAULT 'SUCCESS',
    
    -- Metadata
    collected_by INT, -- Cashier User ID (null if Online)
    remarks TEXT,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Payment Allocations (`fee_payment_allocations`)
Mapping payment to invoices.

```sql
CREATE TABLE fee_payment_allocations (
    alloc_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    payment_id BIGINT NOT NULL,
    invoice_id BIGINT NOT NULL,
    
    amount_allocated DECIMAL(12,2) NOT NULL,
    
    FOREIGN KEY (payment_id) REFERENCES fee_payments(payment_id),
    FOREIGN KEY (invoice_id) REFERENCES fee_invoices(invoice_id)
);
```

### 3. Receipts (`fee_receipts`)
The document given to the parent.

```sql
CREATE TABLE fee_receipts (
    receipt_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    receipt_number VARCHAR(50) UNIQUE NOT NULL, -- e.g., RCPT/26/1001
    payment_id BIGINT NOT NULL,
    
    generated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    pdf_path VARCHAR(255),
    
    is_cancelled BOOLEAN DEFAULT FALSE,
    cancellation_reason TEXT,
    
    FOREIGN KEY (payment_id) REFERENCES fee_payments(payment_id)
);
```

---

## BUSINESS RULES

### Rule 1: Cash Handling Limit
*   System warns or blocks Cash Payments > ₹20,000 (or as per locak Income Tax laws).
*   Requires Supervisor Override for high-value cash transactions to ensure Anti-Money Laundering (AML) compliance logic.

### Rule 2: Cheque Bouncing
*   If a Cheque is marked `BOUNCED`:
    1.  The original receipt is Cancelled.
    2.  The Invoice Balance is restored (Status -> Unpaid).
    3.  A new Invoice is generated for "Cheque Bounce Charge" (e.g., ₹500).
    4.  Parent is notified and blocked for future Cheque payments (must pay Cash/DD).

### Rule 3: Online Convenience Fee
*   School can configure who bears the PG Transaction Rate (MDR).
    *   **Surcharge Model:** Parent pays (Fee + 1.5%).
    *   **Absorption Model:** School pays (School receives Fee - 1.5%).
    *   System records the "Gross" and "Net" appropriately.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Bank Reconciliation:** Sends daily collection logs to match against bank statement.
*   **To SMS Gateway:** "Dear Parent, Payment of Rs. X received via Y. Receipt No: Z."

### Inbound Relationships
*   **From Invoice Module:** Fetches `NetPayable` to determine how much to collect.
*   **From Payment Gateway:** Webhooks trigger the `record_online_payment()` function.

---

## USER WORKFLOWS

### Workflow 1: Cash Collection at Counter
**Actor:** Cashier

1.  **Parent Arrival:** Parent comes to pay ₹10,000 cash for "Rohan Grade 5".
2.  **Lookup:** Cashier types "Rohan" in search. Selects correct student.
3.  **View Dues:** System shows Balance ₹12,500.
4.  **Entry:** Cashier selects "Partial Payment", enters ₹10,000.
5.  **Mode:** Selects "CASH".
6.  **Confirm:** System prompts "Collect ₹10,000?". Click Yes.
7.  **Receipt:** Receipt #8099 prints on thermal printer. 
8.  **Balance:** Updated balance ₹2,500 shown.

### Workflow 2: Cheque Clearing Process
**Actor:** Accountant

1.  **Deposit:** Accountant takes 50 physical cheques to bank. 
2.  **System Action:** Bulk selects these 50 entries and marks "Deposited". using "Create Pay-in Slip" feature.
3.  **Clearance (3 Days Later):** Accountant downloads Bank Statement.
4.  **Reconciliation:** 49 Cheques cleared. 1 Cheque (No. 445521) bounced "Insufficient Funds".
5.  **Action:**
    *   Select 49 -> Mark "CLEARED".
    *   Select 1 -> Mark "BOUNCED".
6.  **Outcome:** The system automates ledger updates and penalty generation for the bounce.

---

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** January 20, 2026

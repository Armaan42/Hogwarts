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

## ADDITIONAL BUSINESS RULES

### Rule 4: Advance Payment Handling
*   If a parent pays Rs. 60,000 against a total due of Rs. 50,000, the excess Rs. 10,000 is booked into a `STUDENT_ADVANCE` wallet.
*   This advance is automatically consumed when the next invoice (e.g., Q2) is generated.
*   Advance Ledger is classified as "Liability" (school owes the parent), not as "Revenue".

### Rule 5: Cashier Shift Reconciliation
*   At the end of each shift, the Cashier MUST close their session.
*   **Closing Process:** The system shows "Expected Cash = Rs. 1,50,000" (sum of all CASH receipts). The Cashier counts their physical drawer and enters "Actual Cash = Rs. 1,48,000".
*   **Shortage (Rs. 2,000):** Logged as `CASH_SHORT`. Requires Manager sign-off. Investigated within 24 hours.
*   **Excess (Unlikely):** Logged as `CASH_OVER`. Also investigated (possible double-count or unreturned change).

### Rule 6: NEFT/RTGS Payment Detection
*   Parents sometimes directly transfer funds to the school bank account via NEFT without notifying the school.
*   **Auto-Detection:** During Bank Reconciliation (Submodule 06), unmatched credits in the bank statement are flagged as "Unidentified Credits".
*   **Manual Resolution:** The accountant calls the parent, confirms identity, and creates a retroactive Receipt linked to the student's pending invoice.

---

## EDGE CASES

### Edge Case 1: Currency Denomination Mismatch
*   **Scenario:** Parent hands over Rs. 10,000 in old, damaged Rs. 500 notes that the bank may reject.
*   **Resolution:** Cashier can refuse damaged notes. System provides a "Payment Declined" reason code: `DAMAGED_CURRENCY`. Receipt is NOT generated. Only valid collected cash is entered.

### Edge Case 2: Post-Dated Cheque (PDC)
*   **Scenario:** Parent provides a cheque dated 3 months in the future.
*   **Resolution:** System accepts the cheque but marks it as `POST_DATED`. It does NOT reduce the student's outstanding balance immediately. A "Reminder to Deposit" alert fires on the cheque date.

### Edge Case 3: The "Receipt Reprint" Request
*   **Scenario:** Parent lost their physical receipt and asks for a reprint a year later.
*   **Resolution:** System allows unlimited reprints of any historical receipt. The reprinted document is watermarked "DUPLICATE" to prevent misuse for double tax deductions.

### Edge Case 4: Payment During System Downtime
*   **Scenario:** Parent comes to pay cash at 10 AM, but the ERP server is temporarily down for maintenance.
*   **Resolution:** Cashier issues a manual "Temporary Handwritten Acknowledgment" with a pre-printed unique serial number. Once the system is back online, the cashier enters the payment using the "Offline Entry Mode" which auto-stamps the original 10 AM timestamp (not the entry time), linking it to the physical serial number for audit.

### Edge Case 5: Overpayment by Sibling Mix-Up
*   **Scenario:** Parent has 2 children. Pays Rs. 50,000 intending it for Child A (Grade 5), but the cashier accidentally applies it to Child B (Grade 8) whose fee is only Rs. 30,000.
*   **Resolution:** The system shows Child B as "PAID" with Rs. 20,000 excess in Advance Ledger. The Admin uses a "Transfer Advance" feature to move the Rs. 20,000 from Child B's wallet to Child A's wallet. Audit trail records the inter-student transfer with the Manager's approval.

---

## REAL-WORLD SCENARIOS

### Scenario A: The Peak Collection Day Rush
*   **Context:** It is the last day before the Due Date. 500 parents flood the office simultaneously.
*   **Challenge:** Only 3 Cashier terminals available.
*   **Resolution:**
    1.  School deploys "Token Queue" system. Parents get token # via SMS upon arrival.
    2.  System displays "Estimated Wait: 45 mins".
    3.  School sends mass Push Notification: "Avoid the queue! Pay online instantly from your phone."
    4.  Result: 400 parents switch to online payment. Only 100 wait in the physical queue.

### Scenario B: The Multi-Mode Payment
*   **Context:** Total Due = Rs. 1,00,000. Parent wants to pay Rs. 50,000 CASH and Rs. 50,000 via UPI because they don't have full cash.
*   **Workflow:**
    1.  Cashier starts a "Split Payment" transaction.
    2.  Enters Mode 1: CASH = Rs. 50,000.
    3.  Enters Mode 2: UPI = Rs. 50,000. Generates a UPI QR code on screen.
    4.  Parent scans QR, authorizes UPI.
    5.  System waits for UPI callback. Once both legs confirmed, generates a SINGLE consolidated receipt showing both modes.

### Scenario C: End-of-Year Collection Drive
*   **Context:** School wants to push collection efficiency from 92% to 98% before March 31.
*   **Approach:**
    1.  System generates a "168 Students with Outstanding Dues" report.
    2.  Bulk SMS sent: "Clear dues by March 25 to avoid admin hold on Report Card."
    3.  System opens a "Payment Link" valid for 7 days sent directly via SMS deep-link.
    4.  Result: 120 parents pay within 48 hours via the one-click link.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `cash_payment_limit` | Rs. 20,000 | Max cash accepted without supervisor override |
| `cheque_bounce_penalty` | Rs. 500 | Auto-charged penalty for returned cheques |
| `receipt_auto_print` | `true` | Whether thermal receipt prints automatically |
| `partial_payment_allowed` | `true` | Can parents pay less than total due? |
| `advance_auto_adjust` | `true` | Auto-consume student advance on next invoice? |
| `cashier_shift_close_mandatory` | `true` | Block new transactions until shift is closed |
| `split_payment_modes_max` | 2 | Max number of payment modes in one transaction |
| `pg_convenience_fee_bearer` | `SCHOOL` | Who pays the MDR? `SCHOOL` or `PARENT` |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

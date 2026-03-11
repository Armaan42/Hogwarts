# PARENT PORTAL INTEGRATION

**Module:** Fee Management  
**Submodule Code:** FEE-PORTAL-010  
**Category:** User Experience  
**Priority:** High (P1)  
**Owners:** IT Administrator, Communications Officer

---

## OVERVIEW

The Parent Portal Integration submodule acts as the front-end interface for parents to interact with the Fee Management system. It is a critical touchpoint that provides transparency, self-service capabilities, and instant access to financial records related to their children. Instead of calling the school office for an invoice or receipt, parents can access everything securely via the web portal or mobile app.

### Purpose

To empower parents with a self-service financial dashboard, reducing administrative overhead for the school's accounting team. It ensures that parents are always aware of their financial obligations, payment histories, and upcoming due dates.

### Scope

-   **Dashboard View:** A consolidated view of total dues, upcoming installments, and recent payments.
-   **Invoice Access:** Downloading and viewing detailed, itemized invoices.
-   **Receipt Download:** Access to historical payment receipts for tax purposes (e.g., Section 80C declarations in India).
-   **Payment Initiation:** Direct link to the Online Payment Gateway for instant fee clearance.
-   **Dispute/Query Raising:** A mechanism for parents to raise financial queries directly from a specific invoice.
-   **Tax Certificates:** Auto-generation of annual fee certificates for income tax filing.
-   **Wallet/Advance Access:** Viewing any excess funds lying in the student's advance account.

---

## KEY FEATURES

### 1. Unified Financial Dashboard

**Feature Description:**
When a parent logs into the portal, the "Fee" section provides an immediate, simplified summary of their account.

**Key Elements:**
*   **Total Outstanding:** A prominent bold figure showing immediate dues.
*   **Next Installment:** Details of the next upcoming bill (e.g., "Q3 Fee: Rs. 25,000 Due on 10 Oct").
*   **Sibling Consolidation:** If a parent has 3 children in the school, the dashboard aggregates the dues or allows a single-click "Pay All" feature.
*   **Status Indicators:** Color-coded badges (Green = Paid, Yellow = Pending, Red = Overdue).

### 2. Document Repository

**Feature Description:**
A secure digital vault for all financial documents, eliminating the need for physical paper trails.

**Capabilities:**
*   **Invoices:** Downloadable PDFs of all generated invoices, including historical ones.
*   **Receipts:** Instant generation of PDF receipts immediately after a successful online payment or manual cashier entry.
*   **Annual Fee Certificate:** A consolidated statement generated at the end of the financial year (e.g., April to March) certifying the total tuition fee paid, specifically formatted to meet local tax authority requirements for deductions.

### 3. Payment Workflow Integration

**Feature Description:**
Seamless transition from viewing dues to paying them.

**Process:**
*   **Cart System:** Parents can select multiple invoices to pay at once.
*   **Partial Payment Support:** If enabled by the school, parents can choose to pay a portion of the due amount.
*   **Mode Selection:** Interface gracefully hands off the user to the Payment Gateway (Razorpay/Stripe) and waits for the return webhook to update the UI instantly (Socket.io integration).

### 4. Query & Dispute Management

**Feature Description:**
A structured communication channel for financial disagreements.

**Workflow:**
*   If a parent notices a discrepancy (e.g., charged for transport despite withdrawing last month), they can click "Raise Query" directly on that invoice line item.
*   This creates a ticket routed specifically to the Accounts Department with the exact context attached.
*   The invoice status may temporarily change to "Under Dispute" to halt automated overdue penalties while the issue is resolved.

---

## DATABASE SCHEMA

Note: The Parent Portal does not have its own massive fee tables; it acts as a View layer over the core Fee tables (`fee_invoices`, `fee_payments`, etc.). However, it does require tables for UI preferences and Query management.

### 1. Fee Queries (`fee_parent_queries`)
Tracks disputes raised via the portal.

```sql
CREATE TABLE fee_parent_queries (
    query_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    parent_user_id INT NOT NULL,
    student_id INT NOT NULL,
    invoice_id BIGINT, -- The invoice in dispute (optional)
    
    query_type ENUM('INCORRECT_AMOUNT', 'MISSING_CONCESSION', 'PAYMENT_NOT_REFLECTING', 'OTHER'),
    description TEXT,
    attachment_url VARCHAR(255), -- E.g., screenshot of bank statement
    
    status ENUM('OPEN', 'INVESTIGATING', 'RESOLVED', 'CLOSED'),
    resolution_remarks TEXT,
    resolved_by INT, -- Staff ID
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (parent_user_id) REFERENCES users(user_id),
    FOREIGN KEY (invoice_id) REFERENCES fee_invoices(invoice_id)
);
```

### 2. Portal Preferences (`fee_portal_preferences`)
Stores configuration for what the parent sees.

```sql
CREATE TABLE fee_portal_preferences (
    pref_id INT PRIMARY KEY AUTO_INCREMENT,
    parent_user_id INT NOT NULL,
    
    currency_display_preference VARCHAR(10) DEFAULT 'INR',
    auto_pay_enabled BOOLEAN DEFAULT FALSE,
    notification_preferences JSON, -- e.g., {"email": true, "sms": false, "push": true}
    
    FOREIGN KEY (parent_user_id) REFERENCES users(user_id)
);
```

---

## BUSINESS RULES

### Rule 1: Data Visibility
*   A parent user account can only ever view financial data for `student_id`s that are explicitly linked to them via the `parent_student_mapping` core table.
*   In cases of divorced parents where only one parent is the financial sponsor, the school admin can revoke "Fee View" access for the secondary parent while retaining academic view access.

### Rule 2: Instant Receipt Availability
*   Receipts for online payments must be available for download within 5 seconds of the PG returning a success status.
*   Receipts for Cheque payments will show a "Provisional" watermark until the cheque status changes to `CLEARED` in the back-office.

### Rule 3: Tax Certificate Generation
*   The system restricts the generation of the Annual Tax Certificate to only the actual amount *realized* (paid and cleared) during that specific financial year window (e.g., 01-Apr to 31-Mar), regardless of which academic year the fee belonged to.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Helpdesk/Ticketing Module:** Fee queries raised in the portal are pushed to the central helpdesk system under the "Accounts" category.

### Inbound Relationships
*   **From Core Fee Engine:** Fetches real-time balances, invoices, and payment history to render the dashboard.
*   **From Notification Engine:** Receives alerts (e.g., "New Invoice Generated") to display in the portal's notification bell icon.

---

## USER WORKFLOWS

### Workflow 1: Paying Term Fees Online
**Actor:** Parent

1.  **Login:** Parent logs into the desktop portal or mobile app.
2.  **Notification:** Sees a red badge on the "Fees" icon.
3.  **Dashboard:** Clicks "Fees". Dashboard shows "Term 2 Fee: Rs. 40,000 Due in 5 Days".
4.  **Detail View:** Parent clicks "View Invoice" to ensure sibling discount is applied correctly.
5.  **Action:** Parent clicks "Pay Now".
6.  **Gateway:** System hands off to Razorpay. Parent completes transaction via NetBanking.
7.  **Return:** Parent is redirected back to the portal.
8.  **Confirmation:** Screen shows "Payment Successful". A "Download Receipt" button appears immediately.
9.  **Verification:** The "Total Outstanding" on the dashboard instantly updates to Rs. 0.

### Workflow 2: Downloading Tax Proofs in March
**Actor:** Parent

1.  **Context:** Parent's company HR requires proof of tuition fee payment for tax deduction.
2.  **Navigation:** Parent goes to Fees -> Documents -> Tax Certificates.
3.  **Selection:** Selects "Financial Year 2025-2026" and clicks Generate.
4.  **System Action:** Engine aggregates all cleared payments between Apr 1, 2025, and Mar 31, 2026, filtering strictly for the "Tuition Fee" head (ignoring transport/meals).
5.  **Output:** A digitally signed PDF is downloaded, stating the school's PAN number and the total tuition fee paid by that specific parent for their ward.

---

## REAL-WORLD SCENARIOS

### Scenario A: The "Payment Deducted but Showing Due" Panic
*   **Context:** Network failure occurs right after the bank deducts money, preventing the gateway from redirecting back to the school portal.
*   **Impact:** Parent logs into portal and still sees "Due Rs. 50,000".
*   **Resolution Design:** The portal displays a disclaimer: "If your amount was deducted but is not reflecting here, please wait 30 minutes before trying again." Meanwhile, the backend Webhook handler receives the delayed success signal from the PG, updates the database, and pushes a real-time notification to the Parent Portal App saying, "Your delayed payment of Rs. 50,000 has been successfully recorded."

### Scenario B: Split Payment Between Separated Parents
*   **Context:** Court order mandates Father pays 60% and Mother pays 40% of the school fees.
*   **Portal Logic:** Both parents have separate portal logins.
*   **Action:** When Father logs in and clicks "Pay", they use the "Partial Payment" feature to enter exactly 60% of the invoice value. Once paid, when the Mother logs in, she sees the Invoice showing as "Partially Paid" with exactly her 40% remainder showing as the outstanding balance.

---

## EXTENDED DATABASE SCHEMA

### 3. Auto-Pay Mandates (`fee_autopay_mandates`)
For parents who choose automatic recurring payments.

```sql
CREATE TABLE fee_autopay_mandates (
    mandate_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    parent_user_id INT NOT NULL,
    
    pg_mandate_id VARCHAR(100), -- External mandate ID from Razorpay/Stripe
    payment_method ENUM('CARD_TOKEN', 'NACH', 'UPI_MANDATE'),
    
    max_amount DECIMAL(12,2), -- Upper limit per debit
    frequency ENUM('MONTHLY', 'QUARTERLY'),
    
    status ENUM('ACTIVE', 'PAUSED', 'REVOKED', 'EXPIRED'),
    valid_until DATE,
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (parent_user_id) REFERENCES users(user_id)
);
```

---

## ADDITIONAL BUSINESS RULES

### Rule 4: Auto-Pay Consent & Limits
*   Parents opting for Auto-Pay (e.g., NACH e-Mandate or UPI recurring) must set a maximum debit cap.
*   The system will NEVER debit more than the `max_amount` in a single cycle. If the invoice exceeds the mandate cap, the parent receives a notification requesting manual top-up for the difference.

### Rule 5: Session Security for Financial Pages
*   While the general portal session may last 30 days, navigating to the "Fees" tab re-prompts for OTP/password if the last authentication was > 15 minutes ago.
*   All financial API endpoints enforce additional rate limiting (max 5 payment initiations per hour per parent) to prevent automated abuse.

### Rule 6: Multi-Language Support
*   Fee invoices and receipts displayed on the portal must support at least the school's regional language alongside English.
*   The system stores `display_locale` in `fee_portal_preferences` and renders amounts in regional numeral systems if configured (e.g., Hindi numerals).

---

## EDGE CASES

### Edge Case 1: Concurrent Login from Multiple Devices
*   **Scenario:** Father is on the mobile app, Mother is on the desktop portal. Both try to pay the same Q2 invoice simultaneously.
*   **Resolution:** The system uses optimistic locking on the `fee_invoices.payment_status` column. The first payment attempt that reaches the server locks the invoice. The second attempt receives an error: "This invoice is currently being processed. Please wait or refresh."

### Edge Case 2: Parent with No Email/Phone
*   **Scenario:** A guardian (e.g., rural grandmother) has no email address and a basic feature phone.
*   **Resolution:** The system supports "Proxy Access" where the class teacher or school admin can generate and print physical copies of invoices and receipts on behalf of the parent. The portal marks this parent as "Offline Communication Only", and all notifications are routed to the school office for physical distribution.

### Edge Case 3: Disputed Invoice Blocking Payment
*   **Scenario:** Parent raises a legitimate dispute on 1 line item (e.g., Rs. 2000 Transport charge) but wants to pay the remaining Rs. 23,000 tuition immediately.
*   **Resolution:** The portal allows "Pay Undisputed Items" mode. It generates a partial payment request excluding the disputed line item. The disputed Rs. 2000 remains in limbo, exempt from overdue penalties, until the admin resolves it.

### Edge Case 4: Tax Certificate Across Financial Years
*   **Scenario:** Parent pays Rs. 30,000 on March 30 (FY 2025-26) and Rs. 20,000 on April 2 (FY 2026-27) for the same Q4 invoice.
*   **Resolution:** The Tax Certificate engine splits the reporting: FY 25-26 certificate shows Rs. 30,000. FY 26-27 certificate shows Rs. 20,000. Both certificates reference the same academic invoice but are strictly segregated by the payment realization date.

---

## ADDITIONAL REAL-WORLD SCENARIOS

### Scenario C: The NRI Parent Timezone Problem
*   **Context:** Parent lives in the USA (12-hour time difference). They try to pay at 2 AM IST (which is their 3 PM local time). The PG maintenance window runs 2-3 AM IST.
*   **Resolution:**
    *   Portal shows a banner: "Payment gateway is under scheduled maintenance (2:00 AM - 3:00 AM IST). Please try after 3:00 AM IST."
    *   Alternatively, the parent can pay via direct NEFT to the school account. The portal displays the school's bank details with a unique "Reference Code" per student to ensure easy identification during Bank Reconciliation.

### Scenario D: Portal Accessibility for Visually Impaired Parents
*   **Context:** A parent with limited vision uses a screen reader (JAWS / NVDA).
*   **Compliance:** All financial pages comply with WCAG 2.1 Level AA accessibility standards.
*   **Implementation:**
    *   All form inputs have proper `aria-labels`.
    *   Payment amounts are spoken back by the screen reader before confirmation: "You are about to pay Rupees Twenty-Five Thousand. Press Enter to confirm."
    *   Color-coded status badges also include text labels (e.g., "OVERDUE" next to the red badge).

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `portal_session_timeout_mins` | 15 | Re-auth required for fee pages after this idle time |
| `portal_partial_payment_enabled` | `true` | Can parents pay less than the full invoice? |
| `portal_sibling_consolidation` | `true` | Show aggregated dues for all children? |
| `portal_autopay_enabled` | `false` | Allow recurring payment mandate setup? |
| `portal_dispute_window_days` | 7 | Days to raise a query on a new invoice |
| `portal_receipt_download_format` | `PDF` | Options: `PDF`, `HTML` |
| `portal_tax_cert_auto_generate` | `true` | Auto-generate cert on March 31? |
| `portal_locale_support` | `['en', 'hi']` | Supported display languages |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

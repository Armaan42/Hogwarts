# ONLINE PAYMENT GATEWAY INTEGRATION

**Module:** Fee Management  
**Submodule Code:** FEE-PG-007  
**Category:** Digital & Collections  
**Priority:** High (P1)  
**Owners:** IT Administrator, Accounts Officer

---

## OVERVIEW

The Online Payment Gateway Integration submodule is the technical wrapper that connects the school's fee system with external Payment Gateways (PGs) like Razorpay, Stripe, BillDesk, Paytm, and CCAvenue. It abstracts the complexity of encryption, webhooks, and status polling, providing a unified interface for the rest of the ERP to "Request Payment" and "Receive Confirmation".

### Purpose

To enable secure, 24/7 fee collection from parents via digital channels. It ensures PCI-DSS compliance by offloading sensitive card handling to the PG while maintaining perfect synchronization with the school's ledgers.

### Scope

-   **Multi-Gateway Support:** Routing transactions to different PGs based on configuration (e.g., International cards to Stripe, UPI to Razorpay).
-   **Security:** Checksum generation, signature verification, and HTTPS enforcement.
-   **Status Synchronization:** Handling success, failure, pending, and cancelled states.
-   **Refund Processing:** Initiating refunds from the ERP back to the source account.
-   **Surcharge Management:** Calculating and passing on MDR (Merchant Discount Rate) if applicable.

---

## KEY FEATURES

### 1. Unified Payment Request API

**Feature Description:**
A standard internal method that any module (Hostel, Transport, Main Fees) can call to initiate a payment.

**Workflow:**
1.  **Input:** `InvoiceID`, `Amount`, `CustomerDetails`.
2.  **Process:** System selects the active PG -> Generates Order ID -> Signs the payload using the Secret Key.
3.  **Output:** Returns a "Payment Link" or "Checkout Payload" to the frontend.

### 2. Robust Webhook Handler

**Feature Description:**
The listener that waits for the PG to "call back" with the status. This is crucial for reliability (e.g., if user internet drops after payment but before redirection).

**Mechanism:**
*   **Whitelisting:** Only accepts requests from known PG IP addresses.
*   **Signature Check:** Verifies that the payload was truly signed by the PG secret (HMAC-SHA256).
*   **Idempotency:** Ensures that the same webhook event (retried by PG) doesn't credit the student account twice.
*   **Fallback:** If webhook fails, a "Status Polling" Cron job runs every 15 minutes to double-check `PENDING` orders.

### 3. Smart Routing (Nodal / Split Payments)

**Feature Description:**
Advanced logic for schools with complex legal structures.
*   **Scenario:** Tuition Fee goes to "Trust Society Account" (Tax Exempt), while Bus Fee goes to "Logistics Pvt Ltd" (Taxable).
*   **Execution:**
    *   If the PG supports "Split Settlements", the system sends a split instruction payload.
    *   The Parent sees one total transaction.
    *   The PG settles funds into two separate bank accounts automatically.

### 4. Saved Cards & EMI

**Feature Description:**
Enhancing parent convenience.
*   **Tokenization:** The system does not store Card Numbers. It stores "Tokens" returned by the PG for 1-click future payments.
*   **EMI Options:** Displays Bank EMI offers directly on the checkout page (e.g., "Pay in 3 months at 0% interest") if configured with the PG.

---

## DATABASE SCHEMA

### 1. Gateway Configurations (`fee_pg_configs`)
Stores credentials securely (encrypted).

```sql
CREATE TABLE fee_pg_configs (
    config_id INT PRIMARY KEY AUTO_INCREMENT,
    provider_name VARCHAR(50), -- 'RAZORPAY', 'STRIPE'
    
    merchant_id VARCHAR(255),
    api_key VARCHAR(255),
    secret_key_enc VARCHAR(500), -- Encrypted
    webhook_secret_enc VARCHAR(500), -- Encrypted
    
    is_active BOOLEAN DEFAULT FALSE,
    is_test_mode BOOLEAN DEFAULT TRUE,
    
    surcharge_percentage DECIMAL(5,2) DEFAULT 0.00,
    supported_methods JSON -- ['UPI', 'CARD', 'NETBANKING']
);
```

### 2. Payment Logs (`fee_pg_logs`)
Technical logs for debugging and audit.

```sql
CREATE TABLE fee_pg_logs (
    log_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    transaction_ref VARCHAR(100), -- Internal Ref
    pg_order_id VARCHAR(100), -- External Ref
    
    request_payload TEXT,
    response_payload TEXT,
    
    webhook_payload TEXT,
    webhook_received_at DATETIME,
    
    status_code INT,
    error_message TEXT,
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### 3. Settlement Reports (`fee_pg_settlements`)
Data regarding when the money actually hit the bank.

```sql
CREATE TABLE fee_pg_settlements (
    settlement_id VARCHAR(100) PRIMARY KEY, -- From PG
    external_utr VARCHAR(100),
    
    settlement_date DATE,
    total_amount DECIMAL(15,2),
    fees_deducted DECIMAL(15,2),
    tax_on_fee DECIMAL(15,2),
    net_amount_settled DECIMAL(15,2),
    
    status ENUM('PROCESSED', 'DISCREPANCY')
);
```

---

## BUSINESS RULES

### Rule 1: Security First
*   **No Card Data:** The application MUST NEVER store CVV or full Card Numbers in the DB.
*   **Encryption:** API Secrets must be encrypted at rest (AES-256).
*   **Logs:** Logs should mask sensitive PII (e.g., `email` and `mobile` visible, but auth tokens masked).

### Rule 2: Double Verification
*   Never trust the frontend success response alone.
*   Always verify the transaction status via a backend Server-to-Server call to the PG before marking the fee as "PAID".

### Rule 3: Refund Window
*   Refunds can only be initiated via API for transactions < 180 days old (varies by PG).
*   Older refunds must be processed manually via Bank Transfer.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Payment Provider:** REST API calls (POST /orders, POST /refunds).
*   **To Frontend App:** Emits socket events `payment_success` or `payment_failed` to update the UI instantly without refresh.

### Inbound Relationships
*   **From Invoice Generation:** Receives the "Payable Amount".
*   **From Payment Collection:** Updates the standard `fee_payments` table once a PG transaction is successful.

---

## USER WORKFLOWS

### Workflow 1: Parent Paying Fees
**Actor:** Parent

1.  **Select:** Parent selects "Q1 Fee" (₹25,000) on App.
2.  **Initiate:** Clicks "Pay Online".
3.  **Order:** Server creates Order #ORD_999 at Razorpay.
4.  **UI:** Browser opens Razorpay Modal.
5.  **Auth:** Parent uses UPI App to authorize.
6.  **Capture:** PG captures money.
7.  **Webhook:** PG sends `order.paid` webhook to Server.
8.  **Update:** Server verifies signature -> Updates Invoice to `PAID` -> Generates Receipt.
9.  **Feedback:** Parent sees "Payment Successful! Receipt #REC_001".

### Workflow 2: Handling a Failed Transaction
**Actor:** System / Parent

1.  **Scenario:** Parent money deducted, but browser crashed before return.
2.  **State:** Invoice is still `UNPAID` in DB.
3.  **Webhook:** 2 minutes later, Webhook arrives "Success".
4.  **Auto-Fix:** System processes webhook -> Updates state to `PAID`.
5.  **Notification:** Sends SMS: "We received your payment of X. Receipt generated."

---

## REAL-WORLD SCENARIOS

### Scenario A: International Payments
*   **Context:** NRI Parent wants to pay via Amex/International Card.
*   **Routing:** System detects "International" flag in PG config.
*   **Action:** Routes request to Stripe (better acceptance for Intl cards) instead of the default local gateway.
*   **Currency:** Displays amount in INR, but handles the dynamic currency conversion info if provided by PG.

### Scenario B: Chargeback Defense
*   **Context:** Parent claims "Fraudulent Transaction" with their bank.
*   **PG:** Notifies school of "Chargeback Raised".
*   **Action:** System flags the Student Account as "Disputed".
*   **Evidence:** Admin downloads "Access Logs" and "Fee Invoice" from ERP to submit to the bank as proof of service delivery.

---


## EDGE CASES

### Edge Case 1: Double Payment (Idempotency Failure)
*   **Scenario:** Parent clicks "Pay" twice rapidly. Two payment requests hit the PG. Both succeed. Student ledger shows double credit.
*   **Resolution:** The system generates a unique `idempotency_key` per invoice per session. If the PG returns two success webhooks for the same key, the second payment is auto-flagged for reversal. The system creates an immediate refund request via PG API.

### Edge Case 2: International Card with Currency Conversion
*   **Scenario:** NRI parent pays with a USD credit card. The PG converts USD to INR at a dynamic forex rate.
*   **Resolution:** The ERP always records the INR amount received (as reported by the PG webhook). Any forex discrepancy is borne by the parent. The receipt shows the INR amount only. The PG dashboard can be referenced for the exact USD amount charged.

### Edge Case 3: PG Webhook Failure (Server Downtime)
*   **Scenario:** The school's ERP server is down when Razorpay sends the success webhook.
*   **Resolution:** Razorpay retries webhooks with exponential backoff (5 min, 30 min, 2 hours, 24 hours). The system also runs a "PG Status Poller" cron job every 15 minutes that queries `GET /v1/orders?status=paid&unsettled=true` to catch any missed callbacks.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `pg_provider` | `RAZORPAY` | Options: `RAZORPAY`, `STRIPE`, `BILLDESK`, `PAYU` |
| `pg_test_mode` | `false` | Use sandbox/test keys? |
| `pg_webhook_secret` | `(encrypted)` | Signature verification key |
| `pg_auto_refund_on_duplicate` | `true` | Auto-initiate refund for duplicate payments? |
| `pg_settlement_account_id` | `acc_xxxx` | Bank account for PG settlements |
| `pg_surcharge_model` | `ABSORPTION` | Options: `ABSORPTION` (school pays), `SURCHARGE` (parent pays) |
| `pg_retry_poll_interval_mins` | 15 | Cron interval for missed webhook recovery |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

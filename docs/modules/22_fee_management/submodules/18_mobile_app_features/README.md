# MOBILE APP FEATURES (FEE MODULE)

**Module:** Fee Management  
**Submodule Code:** FEE-MOBILE-018  
**Category:** User Experience & Accessibility  
**Priority:** Medium (P2)  
**Owners:** IT Administrator, Product Manager (Mobile)

---

## OVERVIEW

The Mobile App Features submodule specifically addresses the extension of the Fee Management system to iOS and Android applications. While the web-based Parent Portal (FEE-PORTAL-010) serves as the comprehensive dashboard, the mobile app focuses on instant notifications, rapid 1-click payments, and on-the-go access to essential financial documents like receipts and boarding passes.

### Purpose

To maximize fee collection speed by meeting parents where they spend most of their time: their smartphones. It leverages native mobile capabilities (like Push Notifications, Biometric Authentication, and UPI Deep Linking) to reduce the friction of paying school fees down to seconds.

### Scope

-   **Push Notifications:** Instant, un-ignorable alerts for due dates, transaction successes, and bounced cheques.
-   **Native UPI Integration:** Direct app-to-app handoff for fast payments (India specific).
-   **Wallet Balance Widgets:** Home screen widgets showing Imprest or Transport balance.
-   **Offline Receipt Vault:** Caching paid receipts locally on the device for presentation without internet (e.g., at the school gate).
-   **Biometric Security:** Enforcing FaceID/TouchID before revealing sensitive financial history.

---

## KEY FEATURES

### 1. The 1-Click Payment Flow

**Feature Description:**
Minimizing the steps required to clear a due invoice.

**Workflow:**
*   Notification arrives: "Q2 Fee Due: Rs. 25,000. Tap to pay."
*   User taps notification -> App opens directly to the specific Invoice View.
*   User taps "Pay with UPI".
*   *Native API Magic:* The school app invokes the OS-level Intent to show installed UPI apps (GPay, PhonePe, Paytm).
*   User selects GPay, enters PIN.
*   Redirected back to School App: "Success! Receipt Downloaded."
*   Total time: Under 15 seconds.

### 2. Intelligent Push Notification Engine

**Feature Description:**
Moving beyond SMS (which is expensive and often ignored) to rich, actionable push notifications.

**Alert Types:**
*   **Actionable:** "Your Custom EMI is due tomorrow." (Includes 'Pay Now' button inside the notification banner).
*   **Informational:** "Rs. 150 deducted from Hostel Wallet for Tuck Shop."
*   **Urgent:** "Final Warning: Transport access will be blocked tomorrow due to unpaid dues."

### 3. Native Offline Document Vault

**Feature Description:**
Ensuring parents always have proof of payment, even with poor cellular connectivity inside the school campus.
*   When a receipt is generated, the PDF is silently downloaded and cached in the app's local encrypted storage.
*   Parent can open the app in "Airplane Mode" and display the QR-coded receipt to the Security Guard for event entry or clearance.

### 4. Interactive Fee Structure Viewer

**Feature Description:**
A mobile-optimized breakdown of complex fees.
*   Instead of a dense tabular PDF, the app uses collapsible "Accordions" UI components.
*   Parent sees "Total: Rs. 40,000". Taps it to reveal: "Tuition: Rs. 30k, Transport: Rs. 10k". Taps Transport to reveal: "Zone B (5 Months)".

---

## DATABASE SCHEMA

Note: The mobile app relies entirely on GraphQL/REST APIs exposed by the core fee engine. However, it requires a specific table for managing device tokens for push notifications related to financials.

### 1. Mobile Device Registry (`fee_mobile_devices`)
Mapping a parent's financial alerts to specific hardware.

```sql
CREATE TABLE fee_mobile_devices (
    device_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    parent_user_id INT NOT NULL,
    
    os_type ENUM('IOS', 'ANDROID'),
    fcm_token VARCHAR(500), -- Firebase Cloud Messaging Token
    device_model VARCHAR(100), -- 'iPhone 15 Pro'
    
    financial_alerts_enabled BOOLEAN DEFAULT TRUE,
    last_active DATETIME,
    
    FOREIGN KEY (parent_user_id) REFERENCES users(user_id)
);
```

### 2. App Notification Queue (`fee_push_queue`)
Staging table for the background worker sending alerts.

```sql
CREATE TABLE fee_push_queue (
    queue_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    target_fcm_token VARCHAR(500),
    
    title VARCHAR(100),
    body TEXT,
    deep_link_url VARCHAR(255), -- 'hogwarts://app/fees/invoice/1099'
    
    priority ENUM('HIGH', 'NORMAL'),
    status ENUM('PENDING', 'SENT', 'FAILED'),
    scheduled_for DATETIME
);
```

---

## BUSINESS RULES

### Rule 1: Security and Session Timeout
*   While the main app might keep a user logged in for 30 days, navigating to the "Fees" tab requires a fresh Biometric/PIN challenge if the app backgrounded for more than 5 minutes.
*   This prevents a child playing with the parent's phone from accidentally viewing financial data or initiating dummy transactions.

### Rule 2: App Version Deprecation
*   If a major security flaw or breaking API change occurs in the Payment Gateway SDK embedded in the app, the backend must be able to force a "Mandatory Update" block.
*   A parent on an old version (e.g., v1.1) clicking "Pay" will be blocked and redirected to the App/Play Store to download v2.0 before proceeding.

### Rule 3: Native App Surcharges (Avoidance)
*   **Apple/Google Tax:** Digital goods sold inside apps are subject to 15-30% platform fees.
*   **Physical Goods Exemption:** Educational tuition fees are classified as "Physical/Real-World Services". The system MUST use standard payment gateways (Stripe/Razorpay) and NEVER use Apple "In-App Purchases (IAP)" to avoid the massive 30% revenue loss.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Firebase / APNs:** Sending push notification payloads over secure HTTP/2 protocols.
*   **To Core ERP Engine:** Polling REST APIs `GET /v1/parents/{id}/invoices?status=UNPAID`.

### Inbound Relationships
*   **From Defaulter Checking Module:** Receives triggers to send "Overdue Warning" pushes to specific device tokens instead of firing an SMS.

---

## USER WORKFLOWS

### Workflow 1: The Morning of Due Date
**Actor:** Parent

1.  **7:00 AM:** Backend cron job detects 500 invoices due today.
2.  **7:05 AM:** System looks up FCM tokens for the 500 parents in `fee_mobile_devices`.
3.  **7:06 AM:** Firebase blasts 500 push notifications.
4.  **7:30 AM:** Parent wakes up, sees lock-screen alert: "Hogwarts ERP: Your child's Q3 fee is due today to avoid late fines."
5.  **Action:** Parent long-presses notification. iOS allows rich interaction. Parent taps "Pay Rs. 15k via Apple Pay".
6.  **Auth:** FaceID scans. Payment complete.
7.  **Result:** Fee collected before the parent even gets out of bed.

### Workflow 2: Canteen Wallet Top-Up
**Actor:** Parent

1.  **Context:** Child calls parent from school: "I have no money in my Imprest account for lunch."
2.  **Action:** Parent opens Mobile App -> Goes to Hostel/Wallet Widget.
3.  **UI:** Shows Balance: Rs. 15.
4.  **Action:** Taps "Quick Top-Up Rs. 500" -> Uses saved Card token.
5.  **Completion:** Money added instantly.
6.  **Feedback Loop:** Child swiping ID card at the canteen 1 minute later gets a "Transaction Approved" from the POS.

---

## REAL-WORLD SCENARIOS

### Scenario A: Multiple Wards in Different Branches
*   **Context:** Parent has a daughter in "Hogwarts North Campus" (Grade 5) and a son in "Hogwarts South Campus" (Grade 9). Both campuses use the same ERP but different sub-domains.
*   **App Logic:** The mobile app acts as a master aggregator. The parent logs in once using their registered mobile number.
*   **Dashboard:** The "Fees" tab shows a split view:
    *   Daughter (North): Due Rs. 10k.
    *   Son (South): Due Rs. 15k.
*   **Payment:** The system routes the Rs. 10k to the North Trust Bank Account and the Rs. 15k to the South Trust Bank Account transparently behind the scenes.

### Scenario B: The Phantom Payment (Network Drop)
*   **Context:** Parent pays via UPI. Bank SMS says "Rs. 5000 debited". But before the app re-opens, the parent switches to WhatsApp and the OS force-closes the School App.
*   **App Re-Launch:** Parent re-opens the app 5 mins later.
*   **Polling Magic:** The UI detects an "Orphaned Order ID" in local storage. It immediately silently polls the server `GET /v1/orders/ORD-123/status`.
*   **Resolution:** Server reports "SUCCESS". The UI clears the error state and shows the receipt, calming the panicked parent.

---

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** March 2026

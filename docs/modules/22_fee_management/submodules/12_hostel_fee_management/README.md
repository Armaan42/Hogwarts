# HOSTEL & BOARDING FEE MANAGEMENT

**Module:** Fee Management  
**Submodule Code:** FEE-HSLT-012  
**Category:** Optional Services  
**Priority:** High (P1)  
**Owners:** Hostel Warden, Accounts Officer

---

## OVERVIEW

The Hostel & Boarding Fee Management submodule governs the financial aspects of residential students. Boarding schools (or day-boarding schools) manage large sums of money for accommodation, daily meals, laundry, and medical escrow accounts. This submodule handles the billing of these physical resources, bridging the gap between Hostel Management (bed allocation) and Fee Management (invoicing).

### Purpose

To ensure accurate, room-category-based billing for residential scholars. It handles the complexities of "Pocket Money" ledgers (Imprest accounts), damage liabilities (Caution deposits), and pro-rata deductions for extended leaves or mid-term boarding withdrawals.

### Scope

-   **Tiered Accommodation Pricing:** Billing based on room type (e.g., AC vs. Non-AC, 2-bed vs. 4-bed dorm).
-   **Mess/Meal Fees:** Flat rate or daily-wage billing for food services.
-   **Imprest / Pocket Money Account:** A dedicated mini-ledger for the student to buy stationery, tuck-shop items, or pay for weekend outings.
-   **Damage Fines:** Ad-hoc charges raised by the warden for property damage (broken windows, lost keys).
-   **Night Out / Leave Adjustments:** Policy-based mess fee rebating if a student goes home for 15 days.

---

## KEY FEATURES

### 1. Complex Variable Billing

**Feature Description:**
Unlike standard tuition which is typically a flat rate for a grade, Hostel fees are combinations of multiple factors.

**Components:**
1.  **Room Rent:** Driven by the `bed_allocation_id`. Changes automatically if a student moves from a 4-seater to an AC Single Room.
2.  **Mess Charges:** Often separated from room rent because food might be subjected to different tax brackets (GST) than educational accommodation.
3.  **Laundry & Housekeeping:** Fixed periodic charges.

### 2. The Imprest Account (Student Wallet)

**Feature Description:**
A running advance ledger completely separate from the "Fee" ledger.
*   **Funding:** Parents deposit Rs. 10,000 into the "Imprest Account" at the start of the term.
*   **Consumption:** When the student buys a Rs. 50 notebook at the school store, the POS system deducts Rs. 50 from the Imprest balance.
*   **Threshold Alerts:** If the balance drops below Rs. 1000, an SMS is automatically fired to the parent to "Top-Up Imprest".

### 3. Caution Money & Damage Recovery

**Feature Description:**
Protecting school assets.
*   Parents pay a fully refundable "Security Deposit" during hostel admission.
*   If the Warden raises a "Damage Docket" (e.g., broke a mirror - Rs. 800 charge), the system can either raise a fresh ad-hoc invoice OR deduct it straight from the Security Deposit, requiring the parent to top-up the deposit back to its requisite baseline.

### 4. Pro-Rata and Refund Engine

**Feature Description:**
Dealing with unpredictable tenancies.
*   If a student is expelled or withdrawn from the hostel mid-term, the system calculates the "Nights Stayed".
*   Refund rules apply (e.g., "Minimum 3 months billed regardless. Remaining months refunded at 80%").

---

## DATABASE SCHEMA

Note: Core bed tracking happens in the Hostel Module. This schema handles the financial translation.

### 1. Hostel Fee Configurations (`fee_hostel_configurations`)
Pricing for the physical assets.

```sql
CREATE TABLE fee_hostel_configurations (
    config_id INT PRIMARY KEY AUTO_INCREMENT,
    building_id INT, -- FK to Facilities module
    room_type ENUM('SINGLE', 'DOUBLE', 'DORMITORY'),
    has_ac BOOLEAN,
    
    annual_rent DECIMAL(15,2),
    monthly_mess_charge DECIMAL(10,2),
    laundry_charge DECIMAL(10,2),
    
    effective_from DATE
);
```

### 2. Imprest Ledger (`fee_imprest_ledger`)
The pocket money passbook.

```sql
CREATE TABLE fee_imprest_ledger (
    transaction_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    
    transaction_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    type ENUM('DEPOSIT', 'EXPENSE', 'REFUND'),
    amount DECIMAL(10,2) NOT NULL,
    
    description VARCHAR(255), -- "Purchased Sci-Calculator at Tuck Shop"
    reference_id VARCHAR(100), -- Bill ID from the Shop POS
    
    running_balance DECIMAL(15,2),
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 3. Damage & Fines Log (`fee_hostel_fines`)
Ad-hoc penalty tracking.

```sql
CREATE TABLE fee_hostel_fines (
    fine_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    raised_by INT, -- Warden User ID
    
    incident_date DATE,
    fine_amount DECIMAL(10,2),
    reason TEXT,
    
    -- Status
    invoice_id BIGINT, -- The generated ad-hoc bill
    is_paid BOOLEAN DEFAULT FALSE,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## BUSINESS RULES

### Rule 1: The "Non-Tuition" Hierarchy
*   In boarding scenarios, if a parent makes a partial payment, the system allocation rules strongly prefer clearing **Tuition & Boarding** first, leaving "Imprest" or "Optional classes" unpaid.
*   A student cannot be denied a bed (Hostel Rent) for partial payment, but they can be denied access to the Tuck Shop (Imprest).

### Rule 2: Mess Rebate on Leave
*   If a student takes an authorized medical leave for > 10 consecutive days, the Warden marks "Away" in the hostel attendance.
*   The system automatically calculates a "Mess Rebate" (e.g., 10 days * Rs. 200/day = Rs. 2000) and credits this as an advance against next term's mess fee.
*   Leaves < 10 days do not qualify for rebates (avoiding micro-accounting).

### Rule 3: Clearance Prerequisite
*   Before issuing a "Transfer Certificate (TC)" or "Graduation Certificate", the system strictly checks the Hostel Module for:
    1.  Zero pending rent/damage dues.
    2.  Full refund of closing Imprest Balance back to the parent's bank account via NEFT.
    3.  Return of the Security Deposit.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Parent Portal:** The parent dashboard gets a dedicated tab called "Hostel Wallet" showing a mini-statement of where their child is spending pocket money.
*   **To POS (Point of Sale):** API for the school canteen or stationary shop to verify `Imprest_Balance >= Bill_Amount` before allowing a purchase.

### Inbound Relationships
*   **From Hostel Management:** Listens to `Room_Allocation_Confirmed` events to trigger the billing cycles and lock in the applicable rates.

---

## USER WORKFLOWS

### Workflow 1: Mid-Term Room Upgrade (Non-AC to AC)
**Actor:** Hostel Warden / Accounts Clerk

1.  **Request:** Student requests a move from a standard 4-bed dorm to an AC Double room due to medical reasons in September.
2.  **Approval:** Warden approves the move in the Hostel Module.
3.  **Financial Check:** The system verifies the parent has approved the increased rate constraint.
4.  **Recalculation:** The system calculates:
    *   April-August: Billed at standard rate.
    *   Sept-March (7 months): Rate diff (Rs. 3000/mo * 7) = Rs. 21,000 extra due.
5.  **Billing:** An ad-hoc "Room Upgrade Invoice" is generated and sent to the parent instantly.

### Workflow 2: Discharging a Graduating Student
**Actor:** Accounts Officer

1.  **Context:** Grade 12 student is leaving school permanently.
2.  **Dashboard:** Officer opens "Full & Final Settlement" wizard.
3.  **Warden Clearance:** Checks if Warden has marked "Room Cleared, No Damage". (Status: YES).
4.  **Wallet Check:** System shows Imprest Balance: Rs. 1,450.
5.  **Deposit Check:** System shows Caution Deposit: Rs. 20,000.
6.  **Action:** Officer clicks "Process Refund".
7.  **Result:** System generates a Refund Voucher for Rs. 21,450, queues a NEFT instruction for the bank via API, and closes the student's financial ledger permanently marking `Settlement_Status = CLEARED`.

---

## REAL-WORLD SCENARIOS

### Scenario A: The Serial Defaulter & The Dining Hall
*   **Context:** Parent hasn't paid Hostel fees for 2 quarters. Dues > Rs. 1 Lakh.
*   **Rule Engine:** The system cannot legally or ethically starve the child. The "Mess Fee" module never imposes a physical block on the Dining Hall turnstiles.
*   **Enforcement:** Instead, the block is applied to the **Imprest Card** (stopping luxury purchases) and the **Exam Admit Card**. The biological necessities are ring-fenced from default penalties by system logic.

### Scenario B: Excursion / Trip Deductions
*   **Context:** The Geography class is going on a 3-day trek costing Rs. 5000 per head.
*   **Workflow:** Instead of collecting cash, the teacher circulates a "Digital Consent Form" on the parent portal.
*   **Action:** Parent clicks "Agree & Deduct from Wallet".
*   **Result:** The system debits Rs. 5000 from the student's Imprest Account, aggregates the funds for all 50 students, and creates a consolidated payable voucher for the trekking company.

---

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** March 2026

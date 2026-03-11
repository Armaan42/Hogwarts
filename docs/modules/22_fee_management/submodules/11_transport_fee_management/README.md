# TRANSPORT FEE MANAGEMENT

**Module:** Fee Management  
**Submodule Code:** FEE-TRANS-011  
**Category:** Optional Services  
**Priority:** High (P1)  
**Owners:** Transport Manager, Accounts Officer

---

## OVERVIEW

The Transport Fee Management submodule bridges the gap between the logistical operation of school buses and the financial collection of fees for those services. Because transport fees are often variable (based on distance, zone, or vehicle type) and highly dynamic (students changing routes mid-year or using one-way transport), this submodule handles the complex calculations before passing the finalized "Transport Due" to the main billing engine.

### Purpose

To ensure accurate, zone-based billing for school transportation services. It eliminates manual errors in calculating pro-rata transport charges when students join or leave the bus service mid-term, ensuring the transport logistical costs are adequately recovered.

### Scope

-   **Zone/Slab Pricing:** Defining cost per month based on distance slabs (e.g., 0-5 km, 5-10 km).
-   **One-Way vs. Two-Way:** Handling partial usage discounts (e.g., morning pickup only).
-   **Mid-Year Transitions:** Calculating pro-rata fees when a student changes their designated boarding stop in November.
-   **Vacation Logic:** Automatically excluding billing for the 2 months of summer vacation if the school policy dictates "10-month billing".
-   **Integration with Routing:** Linking the financial charge directly to the student's assignment on the "Route Manifest".

---

## KEY FEATURES

### 1. Dynamic Route-to-Fee Mapping

**Feature Description:**
Instead of manually typing "Rs. 2000" for a student, the admin assigns the student to "Stop: Green Park". The system inherently knows "Green Park" belongs to "Zone B", and "Zone B" costs Rs. 2000/month.

**Capabilities:**
*   If the school revises the price of "Zone B" to Rs. 2200, all 500 students assigned to stops in Zone B have their next term's invoice updated automatically.
*   Supports different fees for AC vs. Non-AC buses on the same route.

### 2. Pro-Rata & Mid-Term Adjustments

**Feature Description:**
The engine that handles changes without tearing up the accounting ledger.

**Logic:**
*   **Opt-In Late:** If Annual billing happens in April, but student opts for a bus in August, the system calculates `(Annual Fee / 10 months) * Remaining 7 months` and generates a supplementary invoice.
*   **Opt-Out:** If a student cancels transport in October after paying the Annual Fee, the system calculates the unused months and posts a Credit Note (Refund/Advance) to their core Fee Ledger.

### 3. Usage Type Modifiers

**Feature Description:**
Not all transport usage is identical.
*   **Full Usage:** Standard Two-Way charge.
*   **One-Way:** School may charge 60% of the flat rate (not exactly 50% due to seat blocking).
*   **Staff Usage:** Teachers using the bus might get a 100% waiver or a subsidized flat rate.
*   **Sibling Discount:** Some schools offer a 50% discount on the transport fee for the 3rd child.

### 4. Transport Defaulter Management

**Feature Description:**
Specific enforcement actions for unpaid transport fees.
*   While a school cannot legally stop a child from entering the classroom for unpaid fees, they CAN stop optional services.
*   If Transport Fee is overdue by 15 days, the student is automatically flagged in the Transport App used by the Bus Conductor/Attendant ("Do Not Board" list).

---

## DATABASE SCHEMA

To link the transport module's logistics with finance.

### 1. Transport Slabs (`fee_transport_slabs`)
The pricing tiers.

```sql
CREATE TABLE fee_transport_slabs (
    slab_id INT PRIMARY KEY AUTO_INCREMENT,
    academic_year_id INT NOT NULL,
    
    slab_name VARCHAR(100), -- 'Zone A (0-5km)', 'Zone B (5-10km)'
    monthly_rate DECIMAL(10,2),
    annual_rate DECIMAL(10,2),
    one_way_percentage DECIMAL(5,2) DEFAULT '60.00'
);
```

### 2. Stop Pricing Override (`fee_transport_stop_rates`)
Optional. If a specific stop has a custom price defying the slab (e.g., taking an expensive toll road).

```sql
CREATE TABLE fee_transport_stop_rates (
    rate_id INT PRIMARY KEY AUTO_INCREMENT,
    stop_id INT NOT NULL, -- FK to Transport Module
    slab_id INT, -- Inherits baseline from slab
    override_amount DECIMAL(10,2), -- Manual override
    
    effective_from DATE
);
```

### 3. Student Transport Ledger (`fee_student_transport_history`)
Tracks the exact usage period for calculation auditing.

```sql
CREATE TABLE fee_student_transport_history (
    record_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    route_stop_id INT NOT NULL,
    
    start_date DATE NOT NULL,
    end_date DATE, -- Null if currently using
    usage_type ENUM('TWO_WAY', 'PICKUP_ONLY', 'DROP_ONLY'),
    
    calculated_monthly_fee DECIMAL(10,2), -- The locked-in rate for this period
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## BUSINESS RULES

### Rule 1: Exclusion Months
*   Schools typically bill for either 10, 11, or 12 months.
*   System Configuration `excluded_billing_months: [5, 6]` (May and June).
*   If an invoice is generated for Q1 (April, May, June), the system will charge 3 months of Tuition, but only 1 month of Transport.

### Rule 2: Minimum Commitment
*   To prevent parents from using the bus for 1 week during rainy season and cancelling, the system can enforce a "Minimum Billing Period" of 1 Quarter. A cancellation in week 2 still bills for the full 3 months.

### Rule 3: Liability Transfer
*   The Transport Fee is classified as a separate Income Ledger (`revenue_transport`), which is essential because many schools outsource the transport fleet to a 3rd party vendor and must do a revenue-share payout.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Bus Conductor App:** Disables boarding pass (QR code) if dues are severely outstanding.
*   **To Core Invoice Engine:** Provides the `<amount>` and `<description>` (e.g., "Transport Fee - Route 4 Stop C") to be printed on the consolidated quarterly invoice.

### Inbound Relationships
*   **From Transport Module:** Triggers a webhook to Finance whenever a Route Allocation status changes to `APPROVED` or `REVOKED`.

---

## USER WORKFLOWS

### Workflow 1: Mid-Year Route Change Action
**Actor:** Transport Coordinator

1.  **Request:** Parent Submits "Address Change Request". Moves from 2km away (Zone A) to 15km away (Zone C).
2.  **Logistics:** Coordinator updates student route profile from Route 1 to Route 8, effective Nov 1st.
3.  **Finance Trigger:** System detects a change in the underlying `slab_id`.
4.  **Auto-Calculation:**
    *   Previous Zone A (Rs. 1000/mo) billed till Mar 31. Parent already paid.
    *   New Zone C (Rs. 3000/mo) active from Nov 1.
    *   Difference: Rs. 2000/mo * 5 remaining months = Rs. 10,000.
5.  **Billing:** System automatically generates a "Supplementary Transport Invoice" for Rs. 10,000 and emails the parent.

### Workflow 2: Vendor Revenue Share Reporting
**Actor:** Accounts Manager

1.  **Context:** The school fleet is run by "SafeTravel Travels". Contract says they get 80% of collected transport fee.
2.  **Navigation:** Reports -> Transport Analytics.
3.  **Filter:** Month = October. Status = "Collected".
4.  **Result:** Total Transport Fee Collected = Rs. 50,00,000.
5.  **Payout Generation:** The system highlights the 80% share (Rs. 40,00,000) and creates a standard "Purchase Invoice / Bill" against the Vendor Ledger to be paid out by the school.

---

## REAL-WORLD SCENARIOS

### Scenario A: The Sibling Rider
*   **Context:** Elder brother and younger sister ride the same bus.
*   **Rule Engine Eval:** System applies full transport fee to elder brother. For younger sister, the Concession Engine applies an "Ext-Transport Sibling 50%" rule.
*   **Output:** The generated invoice for the sister explicitly shows:
    Transport Zone B: Rs. 2000
    Sibling Discount: (Rs. 1000)
    Net Transport: Rs. 1000.

### Scenario B: Temporary Suspension
*   **Context:** Bus Route 12 is suspended for 2 weeks due to massive road construction. Parents ask for compensation.
*   **Admin Action:** Admin opens `fee_transport_slabs`, selects the Route, chooses "Bulk Credit Adjustment".
*   **Parameters:** "Credit 50% of 1 month's fee to all 40 students on Route 12".
*   **Result:** A credit note of Rs. 1000 is injected into the 40 student ledgers, which automatically reduces their Next Quarter's total bill by Rs. 1000.

---

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** March 2026

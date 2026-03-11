# FINANCIAL REPORTING & ANALYTICS

**Module:** Fee Management  
**Submodule Code:** FEE-RPT-009  
**Category:** Strategic Planning  
**Priority:** High (P1)  
**Owners:** CFO, Principal, Trust Board

---

## OVERVIEW

The Financial Reporting & Analytics submodule transforms raw transactional data into actionable financial intelligence. Unlike basic operational reports (e.g., "List of paid students"), this system provides deep insights into revenue trends, collection efficiency, future cash flow projections, and aging analysis. It is the cockpit for the CFO to ensure the financial health of the institution.

### Purpose

To provide accurate, real-time, and forecasted financial data to stakeholders. It answers critical questions like "Are we collecting faster than last year?", "What is our projected cash flow for March?", and "Which grade has the highest default rate?".

### Scope

-   **Daily Collection Reports (DCR):** Cashier-level closing summaries.
-   **Revenue Recognition:** Accrual vs. Cash basis reporting.
-   **Defaulter Aging:** 30/60/90 day buckets.
-   **Forecasting:** AI-driven prediction of next month's collection based on historical patterns.
-   **Head-wise Analysis:** Breaking down income by Tuition, Transport, Hostel, etc.
-   **Trust/Society Reporting:** Consolidated views for multi-branch schools.

---

## KEY FEATURES

### 1. Dynamic Dashboard Widgets

**Feature Description:**
Real-time visual indicators for quick decision-making.

**Widgets:**
*   **Today's Collection:** Live counter of money hit in bank + cash drawer.
*   **Collection Efficiency:** (Collected Amount / Total Demand) %. Green if > 95%.
*   **Top Defaulters:** List of top 10 students with highest dues.
*   **Mode Split:** Pie chart showing % of Online vs. Cash vs. Cheque.

### 2. Comprehensive Fee Registers

**Feature Description:**
Statutory registers required for audit and accounting.
*   **Fee Due Register:** List of all invoices raised (Accrual).
*   **Fee Collection Register:** List of all receipts generated (Cash).
*   **Discount Register:** Audit trail of all concessions given.
*   **Refund Register:** List of payouts.

### 3. Forecasting Engine

**Feature Description:**
Predictive analytics for cash flow management.
*   **Algorithm:** Uses historical payment dates of parents.
    *   "Parent X usually pays on the 7th of the month."
    *   "Parent Y usually pays only after the 2nd reminder."
*   **Output:** "Expected Inflow for Feb 1-10: ₹45 Lakhs (Confidence: 85%)".

### 4. Custom Report Builder

**Feature Description:**
Allowing users to slice and dice data without IT support.
*   **Dimensions:** Grade, Stream, Gender, Route, Fee Head, Payment Mode.
*   **Metrics:** Total Due, Received, Outstanding, Waived.
*   **Example Query:** "Show me Total Transport Fee collected from Grade 12 students in Q3, broken down by Bus Route."

---

## DATABASE SCHEMA

### 1. Daily Aggregates (`fee_daily_summaries`)
Pre-calculated values for fast dashboard loading.

```sql
CREATE TABLE fee_daily_summaries (
    summary_date DATE PRIMARY KEY,
    
    total_demand DECIMAL(15,2),
    total_collection DECIMAL(15,2),
    total_refunds DECIMAL(15,2),
    total_discounts DECIMAL(15,2),
    
    collection_online DECIMAL(15,2),
    collection_cash DECIMAL(15,2),
    collection_cheque DECIMAL(15,2),
    
    transaction_count INT
);
```

### 2. Report Templates (`fee_report_templates`)
Saved configurations for the report builder.

```sql
CREATE TABLE fee_report_templates (
    template_id INT PRIMARY KEY AUTO_INCREMENT,
    template_name VARCHAR(100),
    created_by INT,
    
    selected_columns JSON, -- ['student_name', 'sum(amount)']
    filters JSON, -- {'grade': '10', 'status': 'PAID'}
    group_by VARCHAR(50),
    sort_order VARCHAR(50),
    
    is_public BOOLEAN DEFAULT FALSE -- Shared with other admins
);
```

### 3. Forecast Data (`fee_cashflow_forecasts`)
Predicted values.

```sql
CREATE TABLE fee_cashflow_forecasts (
    forecast_month DATE, -- 2026-02-01
    
    predicted_inflow DECIMAL(15,2),
    optimistic_inflow DECIMAL(15,2),
    pessimistic_inflow DECIMAL(15,2),
    
    model_version VARCHAR(20),
    generated_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

---

## BUSINESS RULES

### Rule 1: Revenue Recognition
*   **Tuition Fee:** Recognized on a Pro-Rata basis (e.g., Annual Fee collected in April is recognized as 1/12th revenue every month).
*   **Admission Fee:** Recognized immediately (One-time).
*   **Caution Money:** Treated as Liability (Not Revenue).

### Rule 2: Access Control
*   **Principal:** Can see Totals and Defaulters.
*   **Class Teacher:** Can see only *their* class's fee status (Boolean: Paid/Not Paid). Cannot see Amount or financial details.
*   **Librarian:** Can see only "Library Fines".

### Rule 3: Data Immutability
*   Once a "Year End Report" is generated and signed off, the system "locks" the financial year.
*   No back-dated entries allowed in a locked period (requires "Period Re-opening" workflow with audit flags).

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Tally / SAP:** Exports XML/CSV vouchers for the main accounting software.
*   **To Trustee Mobile App:** Feeds the "Morning Coffee Report" (Key metrics delivered at 8 AM).

### Inbound Relationships
*   **From All Modules:** Aggregates data from Invoice, Payment, and Concession tables.

---

## USER WORKFLOWS

### Workflow 1: Trustee Review Meeting
**Actor:** CFO

1.  **Preparation:** Open "Executive Dashboard".
2.  **Snapshot:** Click "Quarterly Performance".
3.  **Insight:** Graph shows "Q3 Collection is 15% lower than Q2".
4.  **Drill Down:** Click on bar graph -> Reveals "Grade 9 and 10 have high outstanding".
5.  **Action:** Export "Grade 9 Defaulter List" -> Send to Principal for follow-up.

### Workflow 2: Statutory Audit
**Actor:** External Auditor

1.  **Request:** "Give me list of all Cancelled Receipts in FY 2025-26".
2.  **Dashboard:** Reports > Audit Trails > Cancelled Receipts.
3.  **Filter:** Date Range: 01-Apr-2025 to 31-Mar-2026.
4.  **Export:** Download Excel.
5.  **Verification:** Auditor checks "Reason for Cancellation" column for every row.

---

## REAL-WORLD SCENARIOS

### Scenario A: The "Caution Money" Refund
*   **Context:** 100 Grade 12 students graduate.
*   **Report:** "Liability Report" shows ₹50,00,000 in Caution Money ledger.
*   **Action:** Bulk Refund Process initiated.
*   **Impact:** Report updates to show reduced Liability. 

### Scenario B: Inflation Adjustment
*   **Context:** Planning fee hike for next year.
*   **Analysis:** CFO checks "Expense vs Revenue" trend for Transport.
*   **Result:** "Transport Expenses (Fuel) rose 20%, but Fee Revenue rose 0%".
*   **Decision:** "Increase Transport Fee by 15% next year".

---

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** January 20, 2026

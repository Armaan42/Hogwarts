# ACCOUNTS & FINANCE MODULE - COMPLETE DEPENDENCY ANALYSIS

## MODULE OVERVIEW

**Name:** Accounts & Finance Module  
**Role:** Financial Accounting & Reporting Hub - General Ledger & Compliance Engine  
**Type:** Core Financial Module  
**Dependencies:** 15+ modules feed financial data into this module  

**Primary Functions:**
- Chart of Accounts (COA) Management - Asset, Liability, Equity, Revenue, Expense accounts
- Journal Entry & General Ledger - Double-entry bookkeeping system
- Bank Reconciliation - Match bank statements with ledger entries
- Expense Management - Track operational expenses, reimbursements
- Budget Planning & Control - Annual budgets, variance analysis
- Financial Reporting - Balance Sheet, P&L, Cash Flow, Trial Balance
- Tax Management - GST compliance, TDS deduction, tax filing
- Audit Trail - Complete financial transaction history
- Fixed Asset Accounting - Depreciation, asset register
- Accounts Payable/Receivable - Vendor payments, customer collections
- Financial Year Management - Year-end closing, opening balances
- Multi-Entity Consolidation - For multi-campus schools

---

## OUTBOUND CONNECTIONS (Accounts & Finance → Other Modules)

### 1. TO FEE MANAGEMENT MODULE

**WHY This Connection Exists:**
Fee collection is the primary revenue source for schools. Every fee payment must be recorded as a journal entry in the general ledger. Accounts module receives fee transaction data and creates corresponding accounting entries.

**DATA FLOW:**
- **Fee Revenue Recognition:**
  - Fee payment amount
  - Payment date
  - Payment mode (cash, cheque, online, UPI)
  - Student ID and grade
  - Fee category (tuition, transport, hostel, exam, etc.)
  - GST amount (if applicable)
- **Accounting Entries:**
  - Debit: Bank/Cash account
  - Credit: Fee Revenue account (by category)
  - Credit: GST Payable account (if GST applicable)
- **Outstanding Dues:**
  - Total fees billed (Accounts Receivable)
  - Fees collected (Cash/Bank)
  - Outstanding balance (AR - Collections)

**TRIGGER EVENT:**
- **Fee Payment Received:** Create journal entry for revenue recognition
- **Fee Invoice Generated:** Create accounts receivable entry
- **Fee Refund Processed:** Create reversal journal entry
- **Month-End:** Reconcile fee collections with bank deposits

**IMPACT:**
- **Revenue Recognition:**
  - Fee payment of ₹50,000 → Journal Entry:
    - Dr. Bank A/c ₹50,000
    - Cr. Tuition Fee Revenue ₹50,000
- **GST Compliance:**
  - Fee ₹1,00,000 + GST 18% ₹18,000 = Total ₹1,18,000
  - Dr. Bank ₹1,18,000
  - Cr. Fee Revenue ₹1,00,000
  - Cr. GST Payable ₹18,000
- **Financial Reporting:**
  - Total fee revenue for year visible in P&L statement
  - Outstanding fees visible in Balance Sheet (Accounts Receivable)

**BUSINESS LOGIC:**
```
FUNCTION record_fee_payment(payment):
  // Create journal entry
  journal_entry = CREATE_JOURNAL_ENTRY
  journal_entry.date = payment.date
  journal_entry.reference = "FEE-" + payment.id
  journal_entry.description = "Fee payment from " + payment.student_name
  
  // Debit bank/cash account
  debit_line = CREATE_JOURNAL_LINE
  debit_line.account = GET_ACCOUNT("BANK_" + payment.bank_name)
  debit_line.debit = payment.amount
  debit_line.credit = 0
  journal_entry.lines.append(debit_line)
  
  // Credit fee revenue account (by category)
  credit_line = CREATE_JOURNAL_LINE
  credit_line.account = GET_ACCOUNT("REVENUE_FEE_" + payment.fee_category)
  credit_line.debit = 0
  credit_line.credit = payment.amount_excluding_gst
  journal_entry.lines.append(credit_line)
  
  // Credit GST payable (if applicable)
  IF payment.gst_amount > 0:
    gst_line = CREATE_JOURNAL_LINE
    gst_line.account = GET_ACCOUNT("LIABILITY_GST_PAYABLE")
    gst_line.debit = 0
    gst_line.credit = payment.gst_amount
    journal_entry.lines.append(gst_line)
  END IF
  
  // Post journal entry
  POST_JOURNAL_ENTRY(journal_entry)
  
  // Update accounts receivable
  UPDATE_AR(payment.student_id, -payment.amount)
  
  LOG_AUDIT("FEE_PAYMENT_RECORDED", payment.id, "Amount: ₹{payment.amount}")
  
  RETURN journal_entry
END FUNCTION
```

**EXAMPLE:**
- **Fee Payment:**
  - Student: Aarav Kumar (Grade 9)
  - Fee Category: Quarterly Tuition
  - Amount: ₹30,000 (excluding GST)
  - GST 18%: ₹5,400
  - Total: ₹35,400
  - Payment Mode: UPI
  - Date: 15-Jan-2026
- **Journal Entry:**
  ```
  Date: 15-Jan-2026
  Ref: FEE-2024-0001
  Description: Quarterly tuition fee - Aarav Kumar (2024/09/0045)
  
  Dr. Bank - HDFC Current A/c        ₹35,400
      Cr. Revenue - Tuition Fee                 ₹30,000
      Cr. GST Payable                           ₹5,400
  ```

---

### 2. TO HR & PAYROLL MODULE

**WHY This Connection Exists:**
Salary payments are the largest expense for schools. Every salary disbursement must be recorded in accounts with proper expense categorization, TDS deduction, and provident fund entries.

**DATA FLOW:**
- **Salary Components:**
  - Basic salary
  - Allowances (HRA, DA, TA, etc.)
  - Deductions (PF, ESI, TDS, loans, advances)
  - Net salary payable
- **Accounting Entries:**
  - Debit: Salary Expense accounts (by component)
  - Debit: Employer PF/ESI contribution
  - Credit: Bank (net salary)
  - Credit: TDS Payable
  - Credit: PF Payable
  - Credit: ESI Payable
- **Statutory Compliance:**
  - TDS deducted and deposited
  - PF deposited by 15th of next month
  - ESI deposited by 21st of next month

**TRIGGER EVENT:**
- **Salary Processing:** Monthly salary run completed
- **Salary Payment:** Net salary transferred to employee accounts
- **TDS Deposit:** Quarterly TDS payment to government
- **PF/ESI Deposit:** Monthly statutory deposit

**IMPACT:**
- **Expense Recognition:**
  - Total salary expense for month recorded in P&L
  - Breakdown by department (Teaching, Admin, Support)
- **Liability Management:**
  - TDS, PF, ESI liabilities tracked until payment
  - Ensures timely statutory compliance
- **Cash Flow:**
  - Salary payment is largest monthly cash outflow
  - Must be planned in cash flow forecast

**BUSINESS LOGIC:**
```
FUNCTION record_salary_payment(payroll_run):
  journal_entry = CREATE_JOURNAL_ENTRY
  journal_entry.date = payroll_run.payment_date
  journal_entry.reference = "SAL-" + payroll_run.month + "-" + payroll_run.year
  
  total_gross = 0
  total_deductions = 0
  
  FOR each employee IN payroll_run.employees:
    // Debit salary expense
    expense_line = CREATE_JOURNAL_LINE
    expense_line.account = GET_ACCOUNT("EXPENSE_SALARY_" + employee.department)
    expense_line.debit = employee.gross_salary
    expense_line.credit = 0
    journal_entry.lines.append(expense_line)
    
    total_gross += employee.gross_salary
    total_deductions += employee.total_deductions
  END FOR
  
  // Debit employer PF contribution
  employer_pf = payroll_run.total_employer_pf
  pf_expense_line = CREATE_JOURNAL_LINE
  pf_expense_line.account = GET_ACCOUNT("EXPENSE_PF_EMPLOYER")
  pf_expense_line.debit = employer_pf
  journal_entry.lines.append(pf_expense_line)
  
  // Credit bank (net salary)
  net_salary = total_gross - total_deductions
  bank_line = CREATE_JOURNAL_LINE
  bank_line.account = GET_ACCOUNT("BANK_SALARY")
  bank_line.credit = net_salary
  journal_entry.lines.append(bank_line)
  
  // Credit TDS payable
  tds_line = CREATE_JOURNAL_LINE
  tds_line.account = GET_ACCOUNT("LIABILITY_TDS_PAYABLE")
  tds_line.credit = payroll_run.total_tds
  journal_entry.lines.append(tds_line)
  
  // Credit PF payable (employee + employer)
  pf_line = CREATE_JOURNAL_LINE
  pf_line.account = GET_ACCOUNT("LIABILITY_PF_PAYABLE")
  pf_line.credit = payroll_run.total_employee_pf + employer_pf
  journal_entry.lines.append(pf_line)
  
  POST_JOURNAL_ENTRY(journal_entry)
  
  LOG_AUDIT("SALARY_RECORDED", payroll_run.id, "Employees: {payroll_run.employee_count}, Amount: ₹{total_gross}")
  
  RETURN journal_entry
END FUNCTION
```

**EXAMPLE:**
- **Monthly Salary (January 2026):**
  - Total Employees: 50
  - Gross Salary: ₹25,00,000
  - Employee PF (12%): ₹3,00,000
  - Employer PF (12%): ₹3,00,000
  - TDS: ₹2,50,000
  - Net Salary: ₹19,50,000
- **Journal Entry:**
  ```
  Date: 31-Jan-2026
  Ref: SAL-JAN-2026
  
  Dr. Salary Expense - Teaching        ₹15,00,000
  Dr. Salary Expense - Admin           ₹7,00,000
  Dr. Salary Expense - Support          ₹3,00,000
  Dr. PF Expense - Employer             ₹3,00,000
      Cr. Bank - Salary Account                    ₹19,50,000
      Cr. TDS Payable                              ₹2,50,000
      Cr. PF Payable                               ₹6,00,000
  ```

---

### 3. TO PROCUREMENT & VENDOR MODULE

**WHY This Connection Exists:**
All purchases (stationery, equipment, services) must be recorded as expenses with proper vendor liability tracking. Accounts module manages accounts payable and vendor payments.

**DATA FLOW:**
- **Purchase Invoice:**
  - Vendor name and GSTIN
  - Invoice number and date
  - Item description and quantity
  - Amount (excluding GST)
  - GST amount (CGST, SGST, IGST)
  - Total amount
- **Accounting Entries:**
  - Debit: Expense/Asset account (based on purchase type)
  - Debit: GST Input Credit
  - Credit: Accounts Payable (vendor liability)
- **Payment Entries:**
  - Debit: Accounts Payable
  - Credit: Bank

**TRIGGER EVENT:**
- **Purchase Invoice Received:** Create expense and liability entry
- **Payment Made:** Clear vendor liability
- **Month-End:** Age analysis of payables

**IMPACT:**
- **Expense Tracking:**
  - All operational expenses recorded by category
  - Budget vs actual comparison
- **Vendor Management:**
  - Outstanding payables tracked by vendor
  - Payment due dates monitored
- **GST Compliance:**
  - Input GST credit claimed
  - GST returns filed accurately

**BUSINESS LOGIC:**
```
FUNCTION record_purchase_invoice(invoice):
  journal_entry = CREATE_JOURNAL_ENTRY
  journal_entry.date = invoice.date
  journal_entry.reference = "PINV-" + invoice.vendor_invoice_number
  
  // Debit expense/asset account
  IF invoice.item_type == "CAPITAL_ASSET":
    account = GET_ACCOUNT("ASSET_" + invoice.asset_category)
  ELSE:
    account = GET_ACCOUNT("EXPENSE_" + invoice.expense_category)
  END IF
  
  debit_line = CREATE_JOURNAL_LINE
  debit_line.account = account
  debit_line.debit = invoice.amount_excluding_gst
  journal_entry.lines.append(debit_line)
  
  // Debit GST input credit
  gst_line = CREATE_JOURNAL_LINE
  gst_line.account = GET_ACCOUNT("ASSET_GST_INPUT_CREDIT")
  gst_line.debit = invoice.gst_amount
  journal_entry.lines.append(gst_line)
  
  // Credit accounts payable
  payable_line = CREATE_JOURNAL_LINE
  payable_line.account = GET_ACCOUNT("LIABILITY_ACCOUNTS_PAYABLE")
  payable_line.credit = invoice.total_amount
  journal_entry.lines.append(payable_line)
  
  POST_JOURNAL_ENTRY(journal_entry)
  
  // Update vendor ledger
  UPDATE_VENDOR_LEDGER(invoice.vendor_id, invoice.total_amount, "INVOICE")
  
  LOG_AUDIT("PURCHASE_RECORDED", invoice.id, "Vendor: {invoice.vendor_name}, Amount: ₹{invoice.total_amount}")
  
  RETURN journal_entry
END FUNCTION
```

**EXAMPLE:**
- **Purchase: Computer Lab Equipment**
  - Vendor: Tech Solutions Pvt Ltd
  - Invoice: TS/2024/0123
  - Date: 10-Jan-2026
  - Items: 30 computers @ ₹40,000 each
  - Amount: ₹12,00,000
  - GST 18%: ₹2,16,000
  - Total: ₹14,16,000
- **Journal Entry:**
  ```
  Date: 10-Jan-2026
  Ref: PINV-TS/2024/0123
  
  Dr. Fixed Assets - Computer Equipment   ₹12,00,000
  Dr. GST Input Credit                     ₹2,16,000
      Cr. Accounts Payable - Tech Solutions          ₹14,16,000
  ```

---

### 4. TO DONATIONS & FUNDRAISING MODULE

**WHY This Connection Exists:**
Donations are a significant revenue source for many schools. All donations must be recorded with proper categorization (corpus, specific purpose, general) and 80G certificate compliance.

**DATA FLOW:**
- **Donation Receipt:**
  - Donor name and PAN
  - Donation amount
  - Donation type (corpus, building fund, scholarship fund, general)
  - Payment mode
  - 80G certificate eligibility
- **Accounting Entries:**
  - Debit: Bank/Cash
  - Credit: Donation Revenue (by category)
- **Corpus Fund Management:**
  - Corpus donations cannot be used for operational expenses
  - Separate fund accounting required

**TRIGGER EVENT:**
- **Donation Received:** Record revenue
- **80G Certificate Issued:** Link to donation entry
- **Corpus Fund Utilization:** Requires board approval

**IMPACT:**
- **Revenue Recognition:**
  - Donation revenue shown separately in P&L
  - Corpus donations shown in Balance Sheet (restricted funds)
- **Tax Compliance:**
  - 80G certificates issued for eligible donations
  - Annual return filed with Income Tax department
- **Fund Accounting:**
  - Corpus funds tracked separately
  - Cannot be used for operational expenses

**EXAMPLE:**
- **Donation:**
  - Donor: Mr. Rajesh Sharma (Alumni)
  - Amount: ₹5,00,000
  - Purpose: Building Fund (corpus)
  - Date: 20-Jan-2026
  - 80G Eligible: Yes
- **Journal Entry:**
  ```
  Date: 20-Jan-2026
  Ref: DON-2024-0045
  
  Dr. Bank - Donation Account          ₹5,00,000
      Cr. Corpus Fund - Building                ₹5,00,000
  ```

---

### 5. TO TRANSPORT MANAGEMENT MODULE

**WHY This Connection Exists:**
Transport operations involve significant expenses (fuel, maintenance, driver salaries, insurance) and revenue (transport fees). Accounts module tracks all transport-related financial transactions.

**DATA FLOW:**
- **Transport Expenses:**
  - Fuel expenses (diesel, petrol)
  - Vehicle maintenance and repairs
  - Driver salaries
  - Insurance premiums
  - Road tax and permits
  - GPS tracking system fees
- **Transport Revenue:**
  - Transport fee collections
  - Route-wise revenue analysis
- **Asset Management:**
  - Bus purchase (capital expenditure)
  - Depreciation on vehicles

**TRIGGER EVENT:**
- **Fuel Purchase:** Record fuel expense
- **Maintenance Bill:** Record repair expense
- **Driver Salary:** Part of monthly payroll
- **Transport Fee Collection:** Record revenue
- **Bus Purchase:** Capitalize asset, start depreciation

**IMPACT:**
- **Cost per Student:** Transport expenses / students using transport
- **Route Profitability:** Revenue vs expenses per route
- **Budget Control:** Monitor transport expenses vs budget

**BUSINESS LOGIC:**
```
FUNCTION record_fuel_expense(fuel_purchase):
  journal_entry = CREATE_JOURNAL_ENTRY
  journal_entry.date = fuel_purchase.date
  journal_entry.reference = "FUEL-" + fuel_purchase.id
  
  // Debit fuel expense
  expense_line = CREATE_JOURNAL_LINE
  expense_line.account = GET_ACCOUNT("EXPENSE_TRANSPORT_FUEL")
  expense_line.debit = fuel_purchase.amount_excluding_gst
  journal_entry.lines.append(expense_line)
  
  // Debit GST input credit
  gst_line = CREATE_JOURNAL_LINE
  gst_line.account = GET_ACCOUNT("ASSET_GST_INPUT_CREDIT")
  gst_line.debit = fuel_purchase.gst_amount
  journal_entry.lines.append(gst_line)
  
  // Credit bank/cash
  payment_line = CREATE_JOURNAL_LINE
  payment_line.account = GET_ACCOUNT("BANK_CURRENT")
  payment_line.credit = fuel_purchase.total_amount
  journal_entry.lines.append(payment_line)
  
  POST_JOURNAL_ENTRY(journal_entry)
  
  // Update transport expense tracking
  UPDATE_TRANSPORT_EXPENSE_TRACKER(fuel_purchase.bus_number, fuel_purchase.amount)
  
  RETURN journal_entry
END FUNCTION
```

**EXAMPLE:**
- **Monthly Transport Expenses:**
  - Fuel: ₹2,50,000 (15 buses, avg 5,000 km/month)
  - Maintenance: ₹75,000
  - Driver Salaries: ₹3,00,000 (15 drivers @ ₹20,000/month)
  - Insurance: ₹25,000 (monthly amortization)
  - Total: ₹6,50,000
- **Transport Revenue:**
  - 450 students @ ₹1,500/month = ₹6,75,000
  - Profit: ₹25,000/month

---

### 6. TO FACILITIES & INFRASTRUCTURE MODULE

**WHY This Connection Exists:**
Facilities maintenance (electricity, water, repairs, housekeeping) represents significant operational expenses. Accounts module tracks all facility-related costs.

**DATA FLOW:**
- **Utility Expenses:**
  - Electricity bills
  - Water bills
  - Internet and telephone
  - Gas (for labs, canteen)
- **Maintenance Expenses:**
  - Building repairs
  - Plumbing and electrical work
  - Painting and renovation
  - Pest control
- **Housekeeping Expenses:**
  - Cleaning staff salaries
  - Cleaning supplies
  - Waste management
- **Security Expenses:**
  - Security guard salaries
  - CCTV maintenance
  - Access control systems

**TRIGGER EVENT:**
- **Utility Bill Received:** Record expense
- **Maintenance Work Completed:** Record expense
- **Monthly Housekeeping:** Part of payroll
- **Capital Improvement:** Capitalize asset

**IMPACT:**
- **Cost per Square Foot:** Total facility expenses / total area
- **Budget Monitoring:** Track against annual facility budget
- **Energy Efficiency:** Monitor electricity consumption trends

**BUSINESS LOGIC:**
```
FUNCTION record_utility_bill(bill):
  journal_entry = CREATE_JOURNAL_ENTRY
  journal_entry.date = bill.date
  journal_entry.reference = "UTIL-" + bill.bill_number
  
  // Debit utility expense
  expense_account = GET_ACCOUNT("EXPENSE_UTILITIES_" + bill.utility_type)
  expense_line = CREATE_JOURNAL_LINE
  expense_line.account = expense_account
  expense_line.debit = bill.amount
  journal_entry.lines.append(expense_line)
  
  // Credit accounts payable or bank
  IF bill.paid:
    credit_account = GET_ACCOUNT("BANK_CURRENT")
  ELSE:
    credit_account = GET_ACCOUNT("LIABILITY_ACCOUNTS_PAYABLE")
  END IF
  
  credit_line = CREATE_JOURNAL_LINE
  credit_line.account = credit_account
  credit_line.credit = bill.amount
  journal_entry.lines.append(credit_line)
  
  POST_JOURNAL_ENTRY(journal_entry)
  
  RETURN journal_entry
END FUNCTION
```

**EXAMPLE:**
- **Monthly Utility Bills:**
  - Electricity: ₹3,50,000 (high consumption due to AC, labs, computers)
  - Water: ₹25,000
  - Internet: ₹15,000
  - Telephone: ₹10,000
  - Total: ₹4,00,000/month

---

### 7. TO INVENTORY & ASSET MANAGEMENT MODULE

**WHY This Connection Exists:**
Fixed assets (buildings, equipment, furniture) and inventory (stationery, lab supplies) have financial implications. Accounts module manages asset capitalization, depreciation, and inventory valuation.

**DATA FLOW:**
- **Fixed Asset Register:**
  - Asset description and location
  - Purchase date and cost
  - Depreciation method and rate
  - Accumulated depreciation
  - Net book value
- **Inventory Valuation:**
  - Opening stock
  - Purchases during period
  - Closing stock
  - Cost of goods consumed
- **Depreciation:**
  - Monthly depreciation expense
  - Accumulated depreciation

**TRIGGER EVENT:**
- **Asset Purchase:** Capitalize asset
- **Month-End:** Calculate depreciation
- **Asset Disposal:** Remove from books
- **Inventory Count:** Adjust stock valuation

**IMPACT:**
- **Balance Sheet:** Fixed assets shown at net book value
- **P&L Statement:** Depreciation shown as expense
- **Asset Tracking:** Complete asset register maintained

**BUSINESS LOGIC:**
```
FUNCTION calculate_monthly_depreciation():
  assets = GET_ALL_FIXED_ASSETS(status="ACTIVE")
  total_depreciation = 0
  
  FOR each asset IN assets:
    IF asset.depreciation_method == "STRAIGHT_LINE":
      annual_depreciation = (asset.cost - asset.salvage_value) / asset.useful_life_years
      monthly_depreciation = annual_depreciation / 12
    ELSE IF asset.depreciation_method == "WDV":
      // Written Down Value method
      monthly_depreciation = (asset.net_book_value * asset.depreciation_rate) / 12
    END IF
    
    asset.accumulated_depreciation += monthly_depreciation
    asset.net_book_value = asset.cost - asset.accumulated_depreciation
    total_depreciation += monthly_depreciation
  END FOR
  
  // Create depreciation journal entry
  journal_entry = CREATE_JOURNAL_ENTRY
  journal_entry.date = LAST_DAY_OF_MONTH
  journal_entry.reference = "DEP-" + CURRENT_MONTH
  
  // Debit depreciation expense
  debit_line = CREATE_JOURNAL_LINE
  debit_line.account = GET_ACCOUNT("EXPENSE_DEPRECIATION")
  debit_line.debit = total_depreciation
  journal_entry.lines.append(debit_line)
  
  // Credit accumulated depreciation
  credit_line = CREATE_JOURNAL_LINE
  credit_line.account = GET_ACCOUNT("ACCUMULATED_DEPRECIATION")
  credit_line.credit = total_depreciation
  journal_entry.lines.append(credit_line)
  
  POST_JOURNAL_ENTRY(journal_entry)
  
  RETURN total_depreciation
END FUNCTION
```

**EXAMPLE:**
- **Fixed Assets:**
  - Buildings: ₹5,00,00,000 (depreciation 5% p.a. = ₹2,08,333/month)
  - Computers: ₹50,00,000 (depreciation 33.33% p.a. = ₹1,38,875/month)
  - Furniture: ₹25,00,000 (depreciation 10% p.a. = ₹20,833/month)
  - Buses: ₹1,50,00,000 (depreciation 20% p.a. = ₹2,50,000/month)
  - **Total Monthly Depreciation:** ₹6,18,041

---

### 8. TO LIBRARY MANAGEMENT MODULE

**WHY This Connection Exists:**
Library operations involve book purchases (capital/expense), subscription fees, and fine collections. Accounts module tracks library-related financial transactions.

**DATA FLOW:**
- **Book Purchases:**
  - New book acquisitions
  - Capitalized or expensed based on value
- **Subscription Fees:**
  - Magazine and journal subscriptions
  - Online database subscriptions
- **Fine Collections:**
  - Overdue book fines
  - Lost book charges
- **Library Expenses:**
  - Librarian salary
  - Library maintenance

**TRIGGER EVENT:**
- **Book Purchase:** Record expense/asset
- **Subscription Renewal:** Record expense
- **Fine Collection:** Record revenue
- **Lost Book Charge:** Record revenue

**IMPACT:**
- **Library Budget:** Monitor expenses vs budget
- **Fine Revenue:** Track fine collections
- **Book Valuation:** Maintain library asset value

**EXAMPLE:**
- **Monthly Library Transactions:**
  - Book Purchases: ₹25,000
  - Subscriptions: ₹10,000
  - Fine Collections: ₹5,000 (revenue)
  - Librarian Salary: ₹30,000

---

### 9. TO CANTEEN & NUTRITION MODULE

**WHY This Connection Exists:**
Canteen operations involve food purchases, staff salaries, and meal sales. If canteen is run by school (not outsourced), all financial transactions must be recorded.

**DATA FLOW:**
- **Canteen Expenses:**
  - Food and beverage purchases
  - Canteen staff salaries
  - Kitchen equipment and utensils
  - Gas and electricity (canteen portion)
- **Canteen Revenue:**
  - Meal sales to students
  - Meal sales to staff
  - Catering services
- **Inventory:**
  - Opening stock of food items
  - Purchases
  - Closing stock
  - Cost of goods sold

**TRIGGER EVENT:**
- **Food Purchase:** Record expense
- **Meal Sale:** Record revenue
- **Month-End:** Calculate profit/loss

**IMPACT:**
- **Canteen Profitability:** Revenue vs expenses
- **Subsidy Calculation:** If school subsidizes meals
- **Cost per Meal:** Total expenses / meals served

**EXAMPLE:**
- **Monthly Canteen Operations:**
  - Food Purchases: ₹2,00,000
  - Staff Salaries: ₹1,00,000
  - Utilities: ₹20,000
  - Total Expenses: ₹3,20,000
  - Meal Sales: ₹3,50,000
  - Profit: ₹30,000

---

### 10. TO EVENTS & ACTIVITIES MODULE

**WHY This Connection Exists:**
School events (annual day, sports day, cultural fest) involve expenses (venue, decorations, prizes) and sometimes revenue (ticket sales, sponsorships). Accounts module tracks event-related finances.

**DATA FLOW:**
- **Event Expenses:**
  - Venue rental
  - Decorations and props
  - Prizes and trophies
  - Catering
  - Sound and lighting
  - Printing (invitations, banners)
- **Event Revenue:**
  - Ticket sales
  - Sponsorships
  - Stall rentals
- **Event Budget:**
  - Budget allocation per event
  - Actual expenses vs budget

**TRIGGER EVENT:**
- **Event Expense:** Record against event budget
- **Event Revenue:** Record income
- **Event Completion:** Close event budget

**IMPACT:**
- **Event Profitability:** Revenue vs expenses per event
- **Budget Control:** Monitor event spending
- **Sponsorship Tracking:** Track sponsor contributions

**EXAMPLE:**
- **Annual Day Event:**
  - Budget: ₹5,00,000
  - Expenses: Venue ₹1,50,000, Decorations ₹75,000, Catering ₹1,25,000, Prizes ₹50,000, Others ₹75,000 = ₹4,75,000
  - Revenue: Ticket Sales ₹2,00,000, Sponsorships ₹3,00,000 = ₹5,00,000
  - Net: ₹25,000 surplus

---

## INBOUND CONNECTIONS (Other Modules → Accounts & Finance)

### 1. FROM ALL REVENUE MODULES - REVENUE DATA

**WHY This Connection Exists:**
All revenue-generating modules (Fee, Donations, Events, Canteen, Library Fines) send transaction data to Accounts for revenue recognition and financial reporting.

**DATA FLOW:**
- Revenue amount
- Revenue category
- Payment date
- Payment mode
- GST amount (if applicable)
- Customer/student details

**TRIGGER EVENT:**
- Any revenue transaction in any module

**IMPACT:**
- Complete revenue tracking across all sources
- Accurate financial statements
- GST compliance
- Revenue analysis by category

**BUSINESS LOGIC:**
```
FUNCTION receive_revenue_data(revenue_transaction):
  // Validate transaction
  IF NOT VALIDATE_REVENUE_TRANSACTION(revenue_transaction):
    RETURN {success: FALSE, error: "Invalid transaction data"}
  END IF
  
  // Create journal entry
  journal_entry = CREATE_JOURNAL_ENTRY
  journal_entry.date = revenue_transaction.date
  journal_entry.reference = revenue_transaction.module + "-" + revenue_transaction.id
  journal_entry.description = revenue_transaction.description
  
  // Debit bank/cash
  debit_line = CREATE_JOURNAL_LINE
  debit_line.account = GET_ACCOUNT("BANK_" + revenue_transaction.payment_mode)
  debit_line.debit = revenue_transaction.total_amount
  journal_entry.lines.append(debit_line)
  
  // Credit revenue account
  credit_line = CREATE_JOURNAL_LINE
  credit_line.account = GET_ACCOUNT("REVENUE_" + revenue_transaction.category)
  credit_line.credit = revenue_transaction.amount_excluding_gst
  journal_entry.lines.append(credit_line)
  
  // Credit GST payable (if applicable)
  IF revenue_transaction.gst_amount > 0:
    gst_line = CREATE_JOURNAL_LINE
    gst_line.account = GET_ACCOUNT("LIABILITY_GST_PAYABLE")
    gst_line.credit = revenue_transaction.gst_amount
    journal_entry.lines.append(gst_line)
  END IF
  
  POST_JOURNAL_ENTRY(journal_entry)
  
  LOG_AUDIT("REVENUE_RECORDED", revenue_transaction.id, "Amount: ₹{revenue_transaction.total_amount}")
  
  RETURN {success: TRUE, journal_entry_id: journal_entry.id}
END FUNCTION
```

**EXAMPLE:**
- **Daily Revenue Summary:**
  - Fee Collections: ₹5,00,000
  - Canteen Sales: ₹25,000
  - Library Fines: ₹2,000
  - Event Tickets: ₹15,000
  - **Total Daily Revenue:** ₹5,42,000

---

### 2. FROM ALL EXPENSE MODULES - EXPENSE DATA

**WHY This Connection Exists:**
All expense-generating modules (HR, Procurement, Facilities, Transport, Library, Canteen) send expense data to Accounts for expense recognition and budget tracking.

**DATA FLOW:**
- Expense amount
- Expense category
- Expense date
- Vendor/payee
- GST amount (if applicable)
- Department/cost center
- Budget code

**TRIGGER EVENT:**
- Any expense transaction in any module

**IMPACT:**
- Complete expense tracking across all categories
- Budget vs actual analysis
- Cost control and variance analysis
- Department-wise expense reports

**BUSINESS LOGIC:**
```
FUNCTION receive_expense_data(expense_transaction):
  // Validate transaction
  IF NOT VALIDATE_EXPENSE_TRANSACTION(expense_transaction):
    RETURN {success: FALSE, error: "Invalid transaction data"}
  END IF
  
  // Check budget availability
  budget_check = CHECK_BUDGET_AVAILABILITY(
    expense_transaction.budget_code,
    expense_transaction.amount
  )
  
  IF NOT budget_check.available:
    // Flag for approval if over budget
    CREATE_APPROVAL_REQUEST(expense_transaction, "OVER_BUDGET")
    RETURN {success: FALSE, requires_approval: TRUE}
  END IF
  
  // Create journal entry
  journal_entry = CREATE_JOURNAL_ENTRY
  journal_entry.date = expense_transaction.date
  journal_entry.reference = expense_transaction.module + "-" + expense_transaction.id
  
  // Debit expense account
  debit_line = CREATE_JOURNAL_LINE
  debit_line.account = GET_ACCOUNT("EXPENSE_" + expense_transaction.category)
  debit_line.debit = expense_transaction.amount_excluding_gst
  debit_line.cost_center = expense_transaction.department
  journal_entry.lines.append(debit_line)
  
  // Debit GST input credit (if applicable)
  IF expense_transaction.gst_amount > 0:
    gst_line = CREATE_JOURNAL_LINE
    gst_line.account = GET_ACCOUNT("ASSET_GST_INPUT_CREDIT")
    gst_line.debit = expense_transaction.gst_amount
    journal_entry.lines.append(gst_line)
  END IF
  
  // Credit bank/accounts payable
  credit_line = CREATE_JOURNAL_LINE
  IF expense_transaction.paid:
    credit_line.account = GET_ACCOUNT("BANK_CURRENT")
  ELSE:
    credit_line.account = GET_ACCOUNT("LIABILITY_ACCOUNTS_PAYABLE")
  END IF
  credit_line.credit = expense_transaction.total_amount
  journal_entry.lines.append(credit_line)
  
  POST_JOURNAL_ENTRY(journal_entry)
  
  // Update budget utilization
  UPDATE_BUDGET_UTILIZATION(expense_transaction.budget_code, expense_transaction.amount)
  
  LOG_AUDIT("EXPENSE_RECORDED", expense_transaction.id, "Amount: ₹{expense_transaction.total_amount}")
  
  RETURN {success: TRUE, journal_entry_id: journal_entry.id}
END FUNCTION
```

**EXAMPLE:**
- **Monthly Expense Summary by Department:**
  - Teaching: ₹18,00,000 (salaries, training)
  - Administration: ₹8,00,000 (salaries, office expenses)
  - Facilities: ₹5,00,000 (utilities, maintenance)
  - Transport: ₹6,50,000 (fuel, maintenance, salaries)
  - Library: ₹65,000 (books, subscriptions)
  - Canteen: ₹3,20,000 (food, salaries)
  - **Total Monthly Expenses:** ₹41,35,000

---

### 3. FROM AUDIT & COMPLIANCE MODULE - AUDIT REQUESTS

**WHY This Connection Exists:**
External auditors and internal audit teams need access to financial records for audit purposes. Accounts module must provide complete, accurate financial data.

**DATA FLOW:**
- **Audit Request:**
  - Audit period (start date, end date)
  - Account codes (specific accounts or all)
  - Transaction types (revenue, expense, asset, liability)
  - Supporting documents required
- **Audit Response:**
  - Trial balance
  - General ledger (account-wise)
  - Journal entries with supporting documents
  - Bank reconciliation statements
  - Fixed asset register
  - Accounts payable/receivable aging
  - GST returns and reconciliation
  - TDS returns and reconciliation

**TRIGGER EVENT:**
- Annual audit (June-July for March year-end)
- Internal audit (quarterly)
- Tax audit (September for previous year)
- Regulatory inspection (ad-hoc)

**IMPACT:**
- Audit compliance ensures financial transparency
- Clean audit report builds stakeholder confidence
- Identifies control weaknesses for improvement
- Regulatory compliance (Income Tax, GST, Companies Act)

**BUSINESS LOGIC:**
```
FUNCTION generate_audit_report(audit_request):
  audit_report = CREATE_AUDIT_REPORT
  audit_report.period_start = audit_request.start_date
  audit_report.period_end = audit_request.end_date
  audit_report.generated_at = NOW
  
  // Trial Balance
  audit_report.trial_balance = GENERATE_TRIAL_BALANCE(
    audit_request.start_date,
    audit_request.end_date
  )
  
  // General Ledger
  IF audit_request.include_general_ledger:
    audit_report.general_ledger = GENERATE_GENERAL_LEDGER(
      audit_request.start_date,
      audit_request.end_date,
      audit_request.account_codes
    )
  END IF
  
  // Journal Entries
  audit_report.journal_entries = GET_JOURNAL_ENTRIES(
    audit_request.start_date,
    audit_request.end_date
  )
  
  // Supporting Documents
  IF audit_request.include_documents:
    FOR each journal_entry IN audit_report.journal_entries:
      journal_entry.documents = GET_SUPPORTING_DOCUMENTS(journal_entry.id)
    END FOR
  END IF
  
  // Bank Reconciliation
  audit_report.bank_reconciliation = GENERATE_BANK_RECONCILIATION(
    audit_request.end_date
  )
  
  // Fixed Asset Register
  audit_report.fixed_assets = GET_FIXED_ASSET_REGISTER(
    audit_request.end_date
  )
  
  // Payables/Receivables Aging
  audit_report.payables_aging = GENERATE_PAYABLES_AGING(audit_request.end_date)
  audit_report.receivables_aging = GENERATE_RECEIVABLES_AGING(audit_request.end_date)
  
  // GST Reconciliation
  audit_report.gst_reconciliation = GENERATE_GST_RECONCILIATION(
    audit_request.start_date,
    audit_request.end_date
  )
  
  // TDS Reconciliation
  audit_report.tds_reconciliation = GENERATE_TDS_RECONCILIATION(
    audit_request.start_date,
    audit_request.end_date
  )
  
  // Export to PDF
  pdf_report = EXPORT_TO_PDF(audit_report)
  
  LOG_AUDIT("AUDIT_REPORT_GENERATED", audit_request.id, "Period: {audit_request.start_date} to {audit_request.end_date}")
  
  RETURN pdf_report
END FUNCTION
```

**EXAMPLE:**
- **Annual Audit (FY 2024-25):**
  - Period: 01-Apr-2024 to 31-Mar-2025
  - Auditor: M/s ABC & Associates, Chartered Accountants
  - Documents Provided:
    - Trial Balance (as on 31-Mar-2025)
    - General Ledger (all accounts, 12 months)
    - Journal Entries (5,234 entries)
    - Bank Reconciliation (all 3 bank accounts)
    - Fixed Asset Register (₹7,25,00,000 gross value)
    - Payables Aging (₹15,00,000 outstanding)
    - Receivables Aging (₹45,00,000 fee dues)
    - GST Returns (12 monthly + 4 quarterly + 1 annual)
    - TDS Returns (4 quarterly)
  - Audit Completion: 15-Jul-2025
  - Audit Opinion: Unqualified (clean report)

---

### 4. FROM BUDGET PLANNING MODULE - BUDGET DATA

**WHY This Connection Exists:**
Annual budgets are prepared and approved at the start of the financial year. Accounts module receives budget data and tracks actual performance against budget.

**DATA FLOW:**
- **Budget Data:**
  - Budget year
  - Revenue budgets (by category)
  - Expense budgets (by department, by category)
  - Capital expenditure budget
  - Cash flow budget
- **Budget Tracking:**
  - Actual vs budget comparison
  - Variance analysis (favorable/unfavorable)
  - Budget utilization percentage
  - Alerts for budget overruns

**TRIGGER EVENT:**
- **Budget Approval:** Load budget into accounts system
- **Month-End:** Generate budget vs actual report
- **Quarterly Review:** Comprehensive variance analysis
- **Budget Revision:** Update budget mid-year (if approved)

**IMPACT:**
- **Financial Control:** Prevents overspending
- **Performance Monitoring:** Track against targets
- **Decision Making:** Budget variance informs management decisions
- **Accountability:** Department heads accountable for budgets

**EXAMPLE:**
- **Annual Budget (FY 2025-26):**
  - **Revenue Budget:**
    - Fee Revenue: ₹10,00,00,000
    - Donation Revenue: ₹50,00,000
    - Other Revenue: ₹10,00,000
    - **Total Revenue:** ₹10,60,00,000
  - **Expense Budget:**
    - Salaries: ₹6,00,00,000
    - Utilities: ₹48,00,000
    - Maintenance: ₹36,00,000
    - Transport: ₹78,00,000
    - Library: ₹12,00,000
    - Others: ₹1,86,00,000
    - **Total Expenses:** ₹9,60,00,000
  - **Surplus Budget:** ₹1,00,00,000
  - **Capital Expenditure:** ₹2,00,00,000 (new building wing)

---

## SUMMARY

**Total Connections:** 20+ modules depend on Accounts & Finance

**Critical Dependencies:**
- **Revenue Modules:** Fee Management (primary), Donations, Events, Canteen, Library
- **Expense Modules:** HR & Payroll (largest), Procurement, Facilities, Transport
- **Compliance Modules:** Audit, Tax, Budget

**Data Flow Metrics:**
- **Daily Transactions:** 100-500 (depending on school size)
- **Monthly Journal Entries:** 1,000-5,000
- **Annual Financial Statements:** 4 quarterly + 1 annual
- **GST Returns:** 12 monthly + 4 quarterly + 1 annual = 17/year
- **TDS Returns:** 4 quarterly/year
- **Audit Reports:** 4 internal + 1 external = 5/year

**Integration Complexity:** VERY HIGH
- Double-entry bookkeeping requires balanced entries
- GST compliance complex (multiple tax rates, input credit, reverse charge)
- Statutory compliance critical (TDS, PF, ESI, audit)
- Multi-dimensional reporting (by department, by category, by project)

**Best Practices:**
1. **Daily Reconciliation:** Bank accounts reconciled daily
2. **Monthly Closing:** Books closed by 5th of next month
3. **Budget Monitoring:** Monthly budget vs actual reports
4. **GST Compliance:** Timely filing of returns (by 20th of next month)
5. **Audit Trail:** Complete transaction history maintained (7 years)
6. **Segregation of Duties:** Different users for entry, approval, payment
7. **Backup:** Daily backup of financial data (encrypted)
8. **Access Control:** Restricted access to financial modules (role-based)
9. **Documentation:** All entries supported by documents (invoices, bills, receipts)
10. **Professional Accountant:** Qualified CA/CMA managing finances
11. **Internal Controls:** Approval workflows, maker-checker, limits
12. **Regular Audits:** Quarterly internal audit, annual external audit

**Financial Statements Generated:**
1. **Balance Sheet:** Assets, Liabilities, Equity (monthly, quarterly, annual)
2. **Profit & Loss Statement:** Revenue, Expenses, Net Profit/Loss (monthly, quarterly, annual)
3. **Cash Flow Statement:** Operating, Investing, Financing activities (quarterly, annual)
4. **Trial Balance:** All account balances (monthly)
5. **Budget vs Actual:** Variance analysis (monthly)
6. **Department-wise Expenses:** Cost center reports (monthly)
7. **GST Returns:** GSTR-1 (sales), GSTR-3B (summary), GSTR-9 (annual)
8. **TDS Returns:** 24Q (salary), 26Q (non-salary)
9. **Audit Reports:** Internal audit, external audit, tax audit
10. **Management Reports:** Revenue analysis, expense analysis, profitability analysis

**Compliance Requirements:**
- **Income Tax Act:** 80G certificates (donations), TDS deduction and deposit, tax audit (if turnover >₹1 crore)
- **GST Act:** GST registration, monthly/quarterly returns, annual return, payment by 20th
- **Companies Act:** (if school is a company) Annual filing, board meetings, AGM
- **Society/Trust Act:** Annual returns, audit, compliance certificate
- **PF Act:** PF registration, monthly deposits by 15th, annual return
- **ESI Act:** ESI registration, monthly deposits by 21st, half-yearly return
- **Professional Tax:** (state-specific) Monthly deposit
- **Shops & Establishment Act:** Registration, compliance

**Chart of Accounts Structure:**
```
1000-1999: ASSETS
  1100-1199: Current Assets
    1110: Cash on Hand
    1120: Bank - Current Account
    1130: Bank - Savings Account
    1140: Bank - Fixed Deposit
    1150: Accounts Receivable - Fee Dues
    1160: GST Input Credit
    1170: Prepaid Expenses
    1180: Other Current Assets
  1200-1299: Fixed Assets
    1210: Land
    1220: Buildings
    1230: Furniture & Fixtures
    1240: Computers & Equipment
    1250: Vehicles (Buses)
    1260: Library Books
    1270: Lab Equipment
  1300-1399: Accumulated Depreciation
    1310: Accumulated Depreciation - Buildings
    1320: Accumulated Depreciation - Furniture
    1330: Accumulated Depreciation - Computers
    1340: Accumulated Depreciation - Vehicles

2000-2999: LIABILITIES
  2100-2199: Current Liabilities
    2110: Accounts Payable - Vendors
    2120: GST Payable
    2130: TDS Payable
    2140: PF Payable
    2150: ESI Payable
    2160: Salary Payable
    2170: Other Current Liabilities
  2200-2299: Long-term Liabilities
    2210: Bank Loan - Building
    2220: Bank Loan - Vehicles
    2230: Other Long-term Liabilities

3000-3999: EQUITY
  3100: Capital/Corpus Fund
  3200: Reserves & Surplus
  3300: Retained Earnings
  3400: Current Year Profit/Loss

4000-4999: REVENUE
  4100-4199: Fee Revenue
    4110: Tuition Fee
    4120: Transport Fee
    4130: Hostel Fee
    4140: Exam Fee
    4150: Activity Fee
  4200-4299: Other Revenue
    4210: Donation Revenue
    4220: Canteen Revenue
    4230: Library Fine Revenue
    4240: Event Revenue
    4250: Interest Income

5000-5999: EXPENSES
  5100-5199: Salary & Benefits
    5110: Teaching Staff Salary
    5120: Administrative Staff Salary
    5130: Support Staff Salary
    5140: PF Employer Contribution
    5150: ESI Employer Contribution
  5200-5299: Utilities
    5210: Electricity
    5220: Water
    5230: Internet & Telephone
    5240: Gas
  5300-5399: Maintenance
    5310: Building Maintenance
    5320: Equipment Maintenance
    5330: Vehicle Maintenance
  5400-5499: Transport
    5410: Fuel
    5420: Vehicle Insurance
    5430: Road Tax & Permits
  5500-5599: Library
    5510: Book Purchases
    5520: Subscriptions
  5600-5699: Canteen
    5610: Food Purchases
    5620: Canteen Staff Salary
  5700-5799: Administrative
    5710: Stationery & Printing
    5720: Postage & Courier
    5730: Bank Charges
    5740: Professional Fees (CA, Legal)
  5800-5899: Depreciation
    5810: Depreciation Expense
```

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 16, 2026  
**Version:** 1.0  
**Compliance:** Indian GAAP, GST Act, Income Tax Act, Companies Act, Society/Trust Act


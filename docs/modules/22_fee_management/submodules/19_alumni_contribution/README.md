# ALUMNI CONTRIBUTION & DONATIONS

**Module:** Fee Management  
**Submodule Code:** FEE-ALUMNI-019  
**Category:** Strategic Financials  
**Priority:** Medium (P2)  
**Owners:** Alumni Relations Officer, Principal, Accounts Manager

---

## OVERVIEW

The Alumni Contribution & Donations submodule bridges the gap between active student billing and post-graduation financial engagement. While standard fee management focuses on mandatory demands (Invoices), this submodule handles voluntary inflows (Pledges and Donations). It equips the school with professional fundraising tools, allowing alumni, philanthropists, and corporate CSR (Corporate Social Responsibility) programs to financially support the institution with total transparency.

### Purpose

To diversify the school's revenue streams beyond tuition fees. It provides a structured, legally compliant mechanism for receiving donations, issuing tax-exemption certificates (like 80G in India or 501c3 equivalent receipts), and tracking the deployment of restricted funds (e.g., money donated specifically for building a new science lab).

### Scope

-   **Campaign Management:** Creating targeted fundraising goals (e.g., "Library Expansion 2026 - Target 50 Lakhs").
-   **Pledge Tracking:** Recording promises to pay over time (e.g., "Will donate Rs. 1 Lakh/year for 5 years").
-   **Online Donation Portal:** A public or semi-private web interface for alumni to swipe a card and donate instantly.
-   **Tax Receipt Generation:** Auto-generating strictly formatted receipts required by tax authorities for the donor to claim deductions.
-   **Restricted Fund Accounting:** Ensuring money donated for "Sports" isn't accidentally spent on "Electricity Bills".

---

## KEY FEATURES

### 1. Flexible Campaign Engine

**Feature Description:**
The ability to spin up different "buckets" for incoming money.
*   **Unrestricted Fund:** General donation for school development.
*   **Endowment Fund:** Principal amount is locked; school only spends the interest earned to fund perpetual scholarships (e.g., "The Arya Memorial Trophy Fund").
*   **Capital Project:** Specific goal like "New Swimming Pool". Shows a live progress bar on the alumni portal.

### 2. Donor Profile & Engagement Tracking

**Feature Description:**
A mini-CRM tailored for fundraising.
*   **History:** Tracks an alumnus's lifetime giving.
*   **Tiering:** Automatically classifies donors (e.g., "Silver", "Gold", "Platinum Circle") based on contribution volume.
*   **Communication:** Integrates with the email engine to send personalized "Thank You" notes and annual "Impact Reports" showing how their money was spent.

### 3. Automated Tax Compliance

**Feature Description:**
The most critical feature for encouraging large donations.
*   **Format:** Generates a specific format receipt (e.g., Form 10BE in India) bearing the school's strictly validated registration numbers.
*   **Year-End Reporting:** Generates the bulk XML file required by the government outlining all donations received in that financial year for cross-matching against donor tax returns.

### 4. Sponsor-A-Student (Micro-Philanthropy)

**Feature Description:**
Linking the Donation module directly to the Scholarship module (FEE-SCHOL-008).
*   Alumnus logs in, sees a list of anonymized profiles: "Grade 8 Girl, excellent academic record, father lost job, needs Rs. 40,000 for tuition."
*   Alumnus clicks "Fund this Student".
*   The system takes the Rs. 40,000 donation and immediately generates a "Credit Note" against that specific student's outstanding tuition invoice.

---

## DATABASE SCHEMA

Note: This system works in parallel to `fee_invoices` (which represent mandatory debt) using `fee_pledges` (voluntary intent).

### 1. Fundraising Campaigns (`fee_donation_campaigns`)
The buckets collecting money.

```sql
CREATE TABLE fee_donation_campaigns (
    campaign_id INT PRIMARY KEY AUTO_INCREMENT,
    campaign_name VARCHAR(150),
    description TEXT,
    
    fund_type ENUM('UNRESTRICTED', 'RESTRICTED_CAPITAL', 'ENDOWMENT', 'SCHOLARSHIP'),
    target_amount DECIMAL(15,2),
    collected_amount DECIMAL(15,2) DEFAULT 0,
    
    start_date DATE,
    end_date DATE,
    is_active BOOLEAN DEFAULT TRUE
);
```

### 2. Donor Profiles (`fee_donors`)
The entities giving money (Alumni or Corporate).

```sql
CREATE TABLE fee_donors (
    donor_id INT PRIMARY KEY AUTO_INCREMENT,
    donor_type ENUM('INDIVIDUAL_ALUMNI', 'CORPORATE_CSR', 'PARENT', 'PHILANTHROPIST'),
    
    name VARCHAR(255),
    tax_id_number VARCHAR(50), -- PAN/SSN (Critical for tax receipts)
    email VARCHAR(100),
    phone VARCHAR(20),
    
    graduation_year INT, -- For alumni correlation
    lifetime_giving DECIMAL(15,2) DEFAULT 0
);
```

### 3. Donations & Pledges (`fee_donations`)
The actual financial transactions.

```sql
CREATE TABLE fee_donations (
    donation_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    donor_id INT NOT NULL,
    campaign_id INT NOT NULL,
    
    pledge_date DATE,
    pledged_amount DECIMAL(15,2),
    
    realized_amount DECIMAL(15,2) DEFAULT 0,
    payment_status ENUM('PLEDGED', 'PARTIALLY_PAID', 'FULLY_PAID', 'CANCELLED'),
    
    tax_receipt_generated BOOLEAN DEFAULT FALSE,
    tax_receipt_number VARCHAR(100),
    
    FOREIGN KEY (donor_id) REFERENCES fee_donors(donor_id),
    FOREIGN KEY (campaign_id) REFERENCES fee_donation_campaigns(campaign_id)
);
```

---

## BUSINESS RULES

### Rule 1: Separation of Bank Accounts
*   Due to anti-money laundering and strictly regulated charity laws, the system strictly enforces that PG routing for "Donations" goes to a separate Merchant ID / Bank Account than "Tuition Fees".
*   If a foreign alumni donates in USD, it MUST route to the school's specialized Foreign Currency (FCRA-approved) bank account.

### Rule 2: Refund Prohibition
*   Unlike tuition fees which can be refunded if a student leaves, a verified tax-exempt donation is **unrefundable** under standard compliance rules once the Tax Receipt has been issued. The UI disables the "Refund" button for the accounts team.

### Rule 3: Anonymous Donations
*   If a person donates Rs. 1 Lakh "Anonymously", the system generates a standard receipt, but masks the name on all public donor walls and reports. However, the system strictly requires the PAN/Tax ID secretly in the backend to satisfy government audit rules regarding "unidentified cash deposits".

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Accounting System (Submodule 15):** Passes vouchers. `Debit: Bank` | `Credit: Donation Revenue (Corpus Fund)`.
*   **To Alumni Management Module:** Updates the alumnus's engagement score. Highly engaged financial donors are flagged for "VIP Invites" to the Annual Day.

### Inbound Relationships
*   **From Scholarship Submodule (08):** Sends "Fund Utilization Reports" back to the donor (e.g., "Your Rs. 50k helped Student X pass Grade 10").

---

## USER WORKFLOWS

### Workflow 1: Launching a Capital Campaign
**Actor:** Alumni Relations Officer

1.  **Creation:** Officer logs in, creates campaign: "Project 2030: Indoor Basketball Court". Target: Rs. 1 Crore.
2.  **Marketing:** Generates a unique Campaign Donation URL from the ERP.
3.  **Broadcast:** Emails URL to the alumni database.
4.  **Action:** Alumnus 'Rahul (Class of 1995)' clicks link, enters Rs. 5 Lakhs, pays via Corporate Credit Card.
5.  **Processing:** System realizes payment -> Increments `collected_amount` in `fee_donation_campaigns` -> Instantly emails 80G Tax Receipt PDF to Rahul.
6.  **Dashboard:** Progress bar on the Alumni Portal jumps to 5%.

### Workflow 2: Reconciling a Corporate CSR Grant
**Actor:** Accounts Manager

1.  **Context:** Microsoft CSR promises Rs. 20 Lakhs for STEM equipment.
2.  **Pledge:** Admin creates Donor Profile "Microsoft India" -> Logs a `PLEDGE` of Rs. 20 Lakhs. Status is "Unpaid".
3.  **Receipt:** 2 months later, a bank transfer arrives.
4.  **Reconciliation:** Admin goes to Bank Recon (Submodule 06) -> Maps the Rs. 20 Lakh credit to the Microsoft Pledge.
5.  **Status Change:** Pledge converts to `FULLY_PAID`. Tax receipt generated and handed to the Microsoft compliance team.

---

## REAL-WORLD SCENARIOS

### Scenario A: The Graduating Class Gift
*   **Context:** The outgoing Grade 12 batch decides to pool money to buy 10 benches for the courtyard.
*   **Workflow:** Instead of collecting cash chaotically, the school creates a micro-campaign.
*   **Execution:** 150 students log into their Parent/Student portals. A new widget appears: "Class of 2026 Gift". They each click and pay Rs. 500 from their mobile app.
*   **Result:** The system aggregates Rs. 75,000 perfectly with individual receipts for every parent, maintaining 100% audit compliance.

### Scenario B: Restrictive Spending Block
*   **Context:** An alumnus gave Rs. 5 Lakhs specifically labeled "Restricted: For Girls' Sports Equipment Only".
*   **ERP Control:** The Accounts module tags this ledger balance.
*   **Event:** The IT department tries to raise a Purchase Order for iPads and selects the "Donation Fund" ledger as the funding source.
*   **Block:** The Purchase Module API queries the Fee/Accounting module and rejects the transaction: "Error: Attempting to use Restricted Funds (Girls Sports) for an unauthorized expense category (IT Assets)."

---

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** March 2026

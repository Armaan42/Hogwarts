# STUDENT FEE ASSIGNMENT

**Module:** Fee Management  
**Submodule Code:** FEE-ASSIGN-002  
**Category:** Core Processing  
**Priority:** Critical (P0)  
**Owners:** Accounts Officer, Registrar

---

## OVERVIEW

The Student Fee Assignment submodule acts as the bridge between generic **Fee Structures** and individual **Students**. It is the engine that personalizes the fee demands. While a "Grade 5 Fee Structure" is a template, the "Student Fee Assignment" is the concrete instantiation of that structure for a specific student, accounting for their unique attributes like admission date, chosen optional subjects, transport zone, stream, and applicable concessions.

### Purpose

To accurately calculate and map the receivables for every student by combining the baseline academic fees with student-specific choices and rules. It handles the "Who pays What" logic, ensuring that no student is under-charged or over-charged.

### Scope

-   **Structure Mapping:** Linking active students to the correct Grade/Stream fee structure.
-   **Optional Selection:** Assigning transport routes, hostel rooms, and activity clubs to student profiles.
-   **Pro-Rata Calculation:** Handling mid-session admissions and withdrawals.
-   **Concession Application:** Linking scholarships and discounts to specific students.
-   **Fee Adjustments:** Manual additions or waivers for specific cases.
-   **Audit Trail:** Tracking who assigned/changed fees and when.

---

## KEY FEATURES

### 1. Dynamic Fee Calculation Engine

**Feature Description:**
The core algorithm that runs whenever a fee cycle is triggered or a student's profile changes. It iterates through all assigned fee heads and calculates the net payable amount.

**Algorithm Logic:**
```python
def calculate_net_fees(student, fee_heads):
    total_payable = 0
    line_items = []

    for head in fee_heads:
        base_amount = head.default_amount
        
        # 1. Apply Proration (for mid-year entry)
        if student.admission_date > academic_year.start_date and head.is_proratable:
            base_amount = calculate_prorata(base_amount, student.admission_date)

        # 2. Apply Zone/Type Multipliers (e.g. Transport Distance)
        if head.type == 'TRANSPORT':
            base_amount = get_zone_rate(student.transport_zone)

        # 3. Calculate Concessions
        discount = 0
        for concession in student.concessions:
            if concession.applies_to(head):
                discount += concession.calculate_discount(base_amount)

        net_amount = base_amount - discount
        
        # 4. Tax Calculation
        tax = 0
        if head.is_taxable:
            tax = net_amount * (head.tax_rate / 100)

        total_payable += (net_amount + tax)
        line_items.append({
            'head': head.name,
            'base': base_amount,
            'discount': discount,
            'tax': tax,
            'net': net_amount + tax
        })

    return line_items, total_payable
```

### 2. Transport & Hostel Fee Mapping

**Feature Description:**
Since Transport and Hostel fees vary significantly by usage (Distance/Room Type), this submodule integrates directly with those modules to fetch the correct variables.
*   **Transport:** Fee is determined by the `RoutePointID` or `ZoneID` assigned to the student.
*   **Hostel:** Fee is determined by `HostelBlockID` and `RoomType` (AC/Non-AC, Single/Double).

**Dynamic Updates:**
If a student changes their bus stop in the middle of the term, the system automatically:
1.  Calculates the fee for the old stop up to the change date.
2.  Calculates the fee for the new stop from the change date.
3.  Posts an adjustment (Debit/Credit) to the student's ledger.

### 3. Sibling Logic & Grouping

**Feature Description:**
Automatically identifies siblings to apply "Sibling Discounts".
*   **Grouping:** Uses `FatherName`, `MotherName`, and `ContactNumber` logic (or a specific `FamilyID`) to group students.
*   **Priority:** Identifies the "Eldest" (pays full fee) vs. "Younger" (gets discount) based on Grade or Date of Birth.

### 4. Scholarship & Concession Management

**Feature Description:**
Manages the lifecycle of concessions.
*   **Assignment:** Linking a "Merit Scholarship (25%)" to a student.
*   **Validity:** Scholarships can be valid for 1 Year, 1 Term, or Permanent.
*   **Renewal:** Rules for auto-renewal (e.g., "Must maintain GPA > 3.5").
*   **Revocation:** Manual or automatic removal if conditions aren't met.

---

## DATABASE SCHEMA

### 1. Student Fee Maps (`student_fee_maps`)
The master link between a student and a generic fee structure.

```sql
CREATE TABLE student_fee_maps (
    map_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    fee_structure_id INT NOT NULL,
    academic_year_id INT NOT NULL,
    
    -- Status
    is_active BOOLEAN DEFAULT TRUE,
    activation_date DATE,
    deactivation_date DATE, -- For mid-year withdrawal
    
    assigned_by INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (fee_structure_id) REFERENCES fee_structures(structure_id)
);
```

### 2. Student Optional Services (`student_optional_services`)
Tracks subscriptions to optional fees.

```sql
CREATE TABLE student_optional_services (
    service_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    fee_head_id INT NOT NULL, -- e.g., Transport, Karate
    
    -- Configuration
    service_variant_id INT, -- e.g., usage of 'Zone A' or 'Black Belt Class'
    custom_rate DECIMAL(10,2), -- Override rate if applicable
    
    start_date DATE NOT NULL,
    end_date DATE,
    status ENUM('ACTIVE', 'SUSPENDED', 'CANCELLED'),
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 3. Student Concessions (`student_concessions`)
Discounts assigned to specific students.

```sql
CREATE TABLE student_concessions (
    grant_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    concession_rule_id INT NOT NULL,
    
    -- Validity
    effective_from DATE NOT NULL,
    valid_until DATE,
    
    -- Status
    status ENUM('ACTIVE', 'REVOKED', 'EXPIRED'),
    revocation_reason TEXT,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (concession_rule_id) REFERENCES fee_concession_rules(rule_id)
);
```

### 4. Fee Adjustments (`fee_adjustments`)
One-off charges or waivers.

```sql
CREATE TABLE fee_adjustments (
    adj_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    fee_head_id INT, -- Optional, if linked to a specific head
    
    adjustment_type ENUM('CHARGE', 'WAIVER', 'REFUND', 'SCHOLARSHIP_CREDIT'),
    amount DECIMAL(10,2) NOT NULL,
    reason TEXT,
    
    approved_by INT,
    transaction_date DATE DEFAULT CURRENT_DATE
);
```

---

## BUSINESS RULES

### Rule 1: One Base Structure
A student can map to only **one** Base Tuition Structure at a time for a given academic term. If they move grades, the old mapping is deactivated and a new one created.

### Rule 2: Proration Logic
*   **Tuition:** Usually prorated by Month (Start Month to End of Year).
*   **Annual Charges:** Often **not** prorated (charged 100% regardless of entry date).
*   **Transport:** Prorated by Month.
*   **Logic:** `Chargeable Months = (EndMonth - JoinMonth) + 1`.

### Rule 3: Concession Caps
*   **Upper Limit:** A student cannot receive total concessions > 100% of the Fee.
*   **Stacking:** Rules define if concessions stack. E.g., "Sibling Discount" might **not** apply if "Staff Ward Waiver" is active. The logic usually picks the *higher* of the applicable discounts.

### Rule 4: Arrears Carry Forward
When assigning fees for a new year, any "Outstanding Balance" (`Due - Paid`) from the previous year is automatically added as an "Arrears" opening balance in the new ledger.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Invoice Generation:** Provides the calculated line items to generate the bill.
*   **To Parent Portal:** Displays the "Fee Breakdown" so parents understand *why* the total is what it is.

### Inbound Relationships
*   **From Admissions:** Triggered when `AdmissionStatus` becomes `CONFIRMED`.
*   **From Student Module:** Listens for `ClassChange`, `SectionChange`, or `Withdrawal` events to trigger fee recalculations.
*   **From Scholarship Module:** Receives approved scholarship grants to map to students.

---

## USER WORKFLOWS

### Workflow 1: New Admission Fee Assignment
**Actor:** Registrar / Admissions Officer

1.  **Event:** Student profile created, Grade 5 selected.
2.  **Verification:** User checks "Apply Transport?" (Yes, Zone 2). User checks "Sibling?" (Yes, found Sister in Grade 9).
3.  **Action:** Click "Assign Fees".
4.  **System Process:**
    *   Fetches Grade 5 Base Structure.
    *   Adds Zone 2 Transport Fee.
    *   Detects Sibling -> Applies 10% Discount to Tuition.
    *   Generates "Admission Invoice".
5.  **Output:** Fee breakdown shown on screen. User confirms. Invoice sent to parent.

### Workflow 2: Assigning a Scholarship
**Actor:** Principal / Accounts Officer

1.  **Search:** Find Student "Aryan Sharma".
2.  **Action:** Go to "Concessions" tab -> "Add".
3.  **Selection:** Choose "Sports Excellence Scholarship (50%)".
4.  **Date:** Effective from "1st October".
5.  **Reason:** "Gold Medal in State Swimming".
6.  **Save:** System recalculates pending future invoices. The next invoice will reflect the 50% waiver.

---

## REAL-WORLD SCENARIOS

### Scenario A: Transport Withdrawal
**Context:** Student stops using value bus service in November.
**Outcome:**
1.  Admin sets `Transport End Date = 31st Oct`.
2.  System sets Transport Fee to `Inactive` for Nov-March.
3.  Future invoices (Dec/March quarters) will auto-exclude Transport Fee.
4.  No manual intervention needed for future bills.

### Scenario B: Staff Ward Benefit
**Context:** A teacher joins the school. Her child is admitted.
**Outcome:**
1.  Child marked as Category `Staff Ward`.
2.  Fee Assignment engine detects category.
3.  Applies `Staff Waiver Rule` (e.g., 100% Tuition Waiver).
4.  Invoice generated only for "Annual Charges" and "Books".

---

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** January 20, 2026

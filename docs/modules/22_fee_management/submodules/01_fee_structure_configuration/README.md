# FEE STRUCTURE & CONFIGURATION

**Module:** Fee Management  
**Submodule Code:** FEE-STRUCT-001  
**Category:** Financial Core  
**Priority:** Critical (P0)  
**Owners:** Finance Manager, System Administrator

---

## OVERVIEW

This submodule handles the definition and management of all fee types, structures, and configurations for different grades, categories, and academic years. It serves as the foundation for the entire fee management system, ensuring that every financial charge is properly categorized, tracked, and compliant with accounting standards.

### Purpose

To provide a flexible and robust framework for defining complex fee structures, managing grade-wise and category-wise variations, handling optional fee components, and configuring academic year financial settings. It ensures accuracy in fee billing and seamless integration with the accounting system.

### Scope

- **Fee Head Management:** Creation and configuration of tuition, transport, hostel, and miscellaneous fee heads.
- **Tuition Structure:** Grade-wise and stream-wise tuition fee definition.
- **Category Management:** Different fee rules for detailed categories (e.g., Staff Ward, EWS, Scholarship).
- **Academic Year Config:** Managing financial year boundaries, terms, and fee cycles.
- **Tax & Ledger Mapping:** Mapping fee heads to GL accounts and tax rules (GST).
- **Revision History:** Tracking changes to fee structures over time (Audit Trail).
- **Approval Workflows:** Management approval for fee hikes or structural changes.

---

## KEY FEATURES

### 1. Advanced Fee Head Configuration

**Feature Description:**
The system supports the creation of unlimited fee heads with granular configuration options. Each fee head acts as a distinct line item in invoices and accounting ledgers.

**Fee Head Types:**
1.  **Tuition Fee:** Recurring, mandatory, grade-specific.
2.  **Admission Fee:** One-time, new students only.
3.  **Annual Charges:** Yearly, covering maintenance, library, etc.
4.  **Transport Fee:** Distance/Zone based, optional.
5.  **Hostel & Mess Fee:** Boarding specific, optional.
6.  **Lab & Exam Fees:** Usage-based, specific to streams/grades.
7.  **Fines & Penalties:** System-calculated based on rules.
8.  **Miscellaneous:** Ad-hoc charges (e.g., ID card replacement).

**Configuration parameters:**
-   **Taxability:** GST Applicable (Yes/No), Rate (0%, 5%, 12%, 18%).
-   **Refundable:** Whether the fee is refundable upon withdrawal (e.g., Security Deposit).
-   **Frequency:** Monthly, Quarterly, Semi-Annual, Annual, One-time.
-   **GL Code:** Link to specific General Ledger account (e.g., 4001-Tuition Income).
-   **Priority:** Order of payment allocation (e.g., pay Penalties first, then Tuition).

### 2. Hierarchical Fee Structures

**Feature Description:**
Allows defining fee structures at multiple levels of granularity to handle the complex pricing models of large institutions.

**Hierarchy Levels:**
1.  **Global Base Rate:** Base fee applicable to the entire school (rare).
2.  **School Level:** Primary, Middle, Senior School distinct rates.
3.  **Grade Level:** Specific rates for Grade 1, Grade 10, etc.
4.  **Stream Level:** Science vs. Commerce streams (Grades 11-12).
5.  **Student Category Level:** Overrides for NRI, EWS, Staff Kids.

**Algorithm: Fee Structure Resolution**
When calculating fees for a student, the system resolves the applicable structure based on specificity.

```python
def resolve_fee_structure(student):
    # Start with Grade-level structure
    base_structure = get_structure_for_grade(student.grade)
    
    # Check for Stream-specific overrides (for Grade 11-12)
    if student.stream:
        stream_override = get_stream_override(student.grade, student.stream)
        base_structure.merge(stream_override)
        
    # Check for Category-specific rules (e.g., NRI pays in USD or higher rate)
    if student.category == 'NRI':
         nri_rules = get_category_rules('NRI')
         base_structure.apply_multiplier(nri_rules.multiplier)
    
    # Add Optional Components based on enrollment
    if student.transport_enrolled:
        base_structure.add_component(get_transport_fee(student.transport_zone))
        
    return base_structure
```

### 3. Usage-Based & Optional Fees

**Feature Description:**
Not all fees apply to all students. The module supports:
-   **Opt-in Fees:** Clubs, Annual Day participation, excursons.
-   **Consumption Fees:** Lab breakage, lost library books (integrated with respective modules).
-   **Late Fees:** Automatically calculated based on overdue duration.

**Configuration Example:**
*   **Swimming Class:** Optional, ₹2,000/term. Added only if `student.activities.includes('swimming')`.
*   **Computer Lab:** Mandatory for 'Computer Science' students, Optional for others.

### 4. Fee Revision & Versioning

**Feature Description:**
Maintains a complete history of fee structures. When fees are revised for a new academic year:
1.  **Draft Mode:** Finance team creates proposed structure.
2.  **Approval Workflow:** Principal/Board reviews and approves.
3.  **Publishing:** New structure becomes active for the target academic year.
4.  **Grandfathering:** Option to keep existing students on old structure while applying new structure to new admissions.

---

## DATABASE SCHEMA

### 1. Fee Head Master (`fee_heads`)
Defines the types of fees collected.

```sql
CREATE TABLE fee_heads (
    head_id INT PRIMARY KEY AUTO_INCREMENT,
    head_name VARCHAR(100) NOT NULL,
    short_code VARCHAR(20) UNIQUE NOT NULL,
    description TEXT,
    
    -- Financial Config
    gl_account_code VARCHAR(50) NOT NULL, -- Integration with Accounting
    is_taxable BOOLEAN DEFAULT FALSE,
    tax_rate DECIMAL(5,2) DEFAULT 0.00,
    currency_code VARCHAR(3) DEFAULT 'INR',
    
    -- Behavior Config
    is_refundable BOOLEAN DEFAULT FALSE,
    is_optional BOOLEAN DEFAULT FALSE,
    payment_priority INT DEFAULT 5, -- 1 = Highest (pay first)
    
    -- Frequency
    frequency_type ENUM('ONE_TIME', 'MONTHLY', 'QUARTERLY', 'ANNUAL', 'AD_HOC') NOT NULL,
    
    -- Metadata
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### 2. Fee Structures (`fee_structures`)
Links fee heads to specific academic contexts (Grade/Year).

```sql
CREATE TABLE fee_structures (
    structure_id INT PRIMARY KEY AUTO_INCREMENT,
    structure_name VARCHAR(100),
    academic_year_id INT NOT NULL,
    
    -- Applicability Scope
    grade_level_id INT, -- Null means applicable to all grades usually not the case
    stream_id INT,      -- Null means applicable to all streams
    student_category_id INT, -- Null means General category
    
    -- Status
    status ENUM('DRAFT', 'PENDING_APPROVAL', 'ACTIVE', 'ARCHIVED') DEFAULT 'DRAFT',
    approved_by INT REFERENCES users(user_id),
    approval_date DATETIME,
    
    FOREIGN KEY (academic_year_id) REFERENCES academic_years(year_id)
);
```

### 3. Fee Structure Items (`fee_structure_items`)
The line items within a structure.

```sql
CREATE TABLE fee_structure_items (
    item_id INT PRIMARY KEY AUTO_INCREMENT,
    structure_id INT NOT NULL,
    fee_head_id INT NOT NULL,
    
    -- Amount Configuration
    base_amount DECIMAL(12,2) NOT NULL,
    
    -- Scheduling
    due_day INT, -- Day of the month/period it is due
    due_month INT, -- Specific month if annual/one-time
    
    FOREIGN KEY (structure_id) REFERENCES fee_structures(structure_id),
    FOREIGN KEY (fee_head_id) REFERENCES fee_heads(head_id)
);
```

### 4. Fee Concession Rules (`fee_concession_rules`)
Rules for discounts (Scholarships, Sibling, Staff).

```sql
CREATE TABLE fee_concession_rules (
    rule_id INT PRIMARY KEY AUTO_INCREMENT,
    rule_name VARCHAR(100), -- e.g., "Sibling Discount", "Merit Scholarship"
    concession_type ENUM('PERCENTAGE', 'FLAT_AMOUNT'),
    value DECIMAL(10,2),
    
    -- Applicability
    applicable_fee_head_id INT, -- Usually applies only to Tuition Fee
    criteria_json JSON, -- Logic for application (e.g., {"sibling_index": ">1"})
    
    is_active BOOLEAN DEFAULT TRUE
);
```

---

## BUSINESS RULES

### Rule 1: Mandatory vs. Optional Fees
*   **Mandatory Fees** (Tuition, Annual Charges) are automatically assigned to all students in the mapped Grade/Stream.
*   **Optional Fees** (Transport, Hostel) are assigned only upon subscription/enrollment in the respective module.

### Rule 2: Fee Revision Locking
*   Once a Fee Structure is marked `ACTIVE` and invoices have been generated against it, it **cannot** be modified.
*   Corrections require creating a new version or passing adjustment entries (Credit/Debit Notes).
*   *Exception:* Ad-hoc fees can be added to individual students at any time.

### Rule 3: Tax Compliance (GST)
*   If `is_taxable` is TRUE, GST must be calculated on top of the `base_amount`.
*   Educational services (Tuition) are often exempt, but ancillary services (AC Transport, Uniforms sold by school) might be taxable depending on local laws.
*   The system separates the Tax Component for reporting.

### Rule 4: Priority of Payment
*   Partial payments are allocated based on `payment_priority`.
*   Standard Priority:
    1.  **Fines/Penalties** (Must clear first)
    2.  **Previous Year Dues**
    3.  **Tuition Fee**
    4.  **Ancillary Fees** (Transport, etc.)

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Student Management:** Defines the financial profile for every student.
*   **To Finance/Accounting:** Exports Journal Entries (Accrual of Fees) based on the mapped GL Codes.
*   **To Admissions Code:** Provides the "Admission Fee" and "Security Deposit" amounts for new applicants.

### Inbound Relationships
*   **From Academic Structure:** Dependencies on Grades, Streams, and Academic Years.
*   **From Transport Module:** Transport Zone definitions (used to set Transport Fee rates).
*   **From Hostel Module:** Room Type definitions (used to set Hostel Fee rates).

---

## USER WORKFLOWS

### Workflow 1: Configuring a New Academic Year Fee
**Actor:** Finance Manager

1.  **Initialization:** Select "Create New Fee Structure" from the Dashboard.
2.  **Clone:** Choose "Clone from Previous Year" to pre-fill data.
3.  **Adjustment:**
    *   Apply a bulk increase (e.g., +5%) to Tuition Fees.
    *   Update specific heads (e.g., increase Exam Fee by ₹500).
4.  **Review:** Check the projected revenue Summary based on current student counts.
5.  **Submission:** Click "Submit for Approval".
6.  **Approval:** Principal receives notification, reviews impact analysis, and approves.
7.  **Activation:** Structure becomes "Active" for the upcoming dates.

### Workflow 2: Defining a New Fee Head (e.g., Robotics Club)
**Actor:** Admin

1.  **Create Head:** Go to Fee Heads > Add New.
2.  **Details:**
    *   Name: "Robotics Club Fee"
    *   Type: Optional / Activity
    *   Frequency: Quarterly
    *   GL Code: 4055 (Clubs Income)
3.  **Rules:**
    *   Refundable: No
    *   Taxable: No
4.  **Save:** Fee Head acts as a template.
5.  **Assign:** Add this head to the Grade 6-10 structure with amount ₹1,500.

---

## REAL-WORLD SCENARIOS

### Scenario A: Sibling Discount Application
*   **Policy:** 2nd child gets 10% off Tuition; 3rd child gets 20%.
*   **System Setup:**
    *   Fee Concession Rule configured with logic `if sibling_count == 2 then discount = 10% on Tuition`.
*   **Runtime:** When generating the fee demand for the younger sibling, the system checks the `family_id`, counts active students, and applies the concession line item automatically.

### Scenario B: Mid-Year Admission
*   **Constraint:** Student joins in September (Academic year started in April).
*   **Logic:**
    *   **One-time fees** (Admission, Security): Charged 100%.
    *   **Annual charges:** Charged Pro-rata (optional policy) or 100%.
    *   **Tuition/Transport:** Charged only from September onwards.
*   **Outcome:** The system generates a custom "Initial Invoice" dynamically calculating these components.

---

**Status:** Production-Ready Documentation  
**Version:** 2.0  
**Last Updated:** January 20, 2026

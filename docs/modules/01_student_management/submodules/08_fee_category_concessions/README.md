# FEE CATEGORY & CONCESSIONS - COMPLETE DOCUMENTATION

**Module:** Student Management  
**Submodule Code:** STD-FEE-008  
**Category:** Core Foundation  
**Priority:** Critical (P0)  
**Owner:** Accounts Department & Admissions

---

## 📋 OVERVIEW

The Fee Category & Concessions submodule manages student fee categorization, scholarship allocation, sibling discounts, staff ward concessions, EWS (Economically Weaker Section) quotas, merit-based scholarships, and special fee waivers. It integrates with the Fee Management module to ensure accurate fee calculation and collection while maintaining transparency and compliance with government regulations.

### Purpose

Categorize students into appropriate fee structures, apply eligible concessions and scholarships, manage sibling discounts, handle EWS quota compliance, process merit scholarships, track fee waiver applications, and ensure equitable fee distribution while maintaining financial sustainability.

### Scope

- **Fee Categories**: Regular, EWS, Staff Ward, Transport, Hostel
- **Sibling Discounts**: Auto-calculated based on number of children
- **Scholarships**: Merit-based, need-based, sports, academic
- **EWS Quota**: 25% reservation with fee concessions
- **Staff Ward**: Employee children benefits
- **Special Concessions**: Case-by-case approvals
- **Fee Waivers**: Partial/full fee exemptions

---

## 🎯 KEY FEATURES

### 1. Fee Categories

#### 1.1 Regular Fee Category

**Standard Fee Structure:**
```yaml
Category: REGULAR
Description: Standard fee-paying students
Eligibility: All students not in special categories

Grade 6 Fee Structure (2024-25):

Tuition Fee:
  Annual: ₹1,20,000
  Per Term: ₹40,000 (3 terms)
  Per Month: ₹10,000 (12 months)

Development Fee:
  Annual: ₹15,000 (one-time at admission)
  
Activity Fee:
  Annual: ₹12,000
  Includes: Sports, Arts, Music, Dance, Clubs
  
Examination Fee:
  Per Term: ₹2,000
  Total Annual: ₹6,000
  
Library Fee:
  Annual: ₹3,000
  
Computer Lab Fee:
  Annual: ₹5,000
  
Total Annual Fee: ₹1,61,000

Payment Options:
  - Annual (5% discount): ₹1,52,950
  - Term-wise (3 installments): ₹53,667 each
  - Monthly (12 installments): ₹13,417 each

Example Student:
  Name: Aarav Patel (2024/06/0456)
  Category: REGULAR
  Grade: 6-A
  Annual Fee: ₹1,61,000
  Payment Mode: Term-wise
  Discount: None
  Final Amount: ₹1,61,000
```

#### 1.2 EWS (Economically Weaker Section) Category

**EWS Quota Fee Structure:**
```yaml
Category: EWS
Description: 25% reservation for economically weaker sections
Eligibility: Family income < ₹5,00,000 per annum
Government Mandate: RTE Act 2009

Fee Concession: 100% tuition fee waiver

Grade 6 Fee Structure (EWS):

Tuition Fee:
  Annual: ₹1,20,000
  Waiver: ₹1,20,000 (100%)
  Payable: ₹0 ✓

Development Fee:
  Annual: ₹15,000
  Waiver: ₹15,000 (100%)
  Payable: ₹0 ✓

Activity Fee:
  Annual: ₹12,000
  Waiver: ₹6,000 (50%)
  Payable: ₹6,000

Examination Fee:
  Annual: ₹6,000
  Waiver: ₹3,000 (50%)
  Payable: ₹3,000

Library Fee:
  Annual: ₹3,000
  Waiver: ₹1,500 (50%)
  Payable: ₹1,500

Computer Lab Fee:
  Annual: ₹5,000
  Waiver: ₹2,500 (50%)
  Payable: ₹2,500

Total Annual Fee: ₹1,61,000
Total Waiver: ₹1,48,000 (92%)
Total Payable: ₹13,000 (8%)

Eligibility Criteria:
  - Family income < ₹5,00,000/year
  - Income certificate from government
  - Aadhaar card mandatory
  - Residence proof required
  - Annual renewal of documents

Verification Process:
  - Income certificate verification
  - Home visit (if required)
  - Document authentication
  - Annual re-verification

Example Student:
  Name: Rahul Singh (2024/06/0460)
  Category: EWS
  Family Income: ₹3,50,000/year
  Income Certificate: Valid till 31/03/2025
  Annual Fee: ₹13,000 (92% waiver)
  Status: VERIFIED ✓
```

#### 1.3 Staff Ward Category

**Employee Children Benefits:**
```yaml
Category: STAFF_WARD
Description: Children of school employees
Eligibility: Parent employed by school (teaching/non-teaching)

Fee Concession: 50% on tuition + development fee

Grade 6 Fee Structure (Staff Ward):

Tuition Fee:
  Annual: ₹1,20,000
  Waiver: ₹60,000 (50%)
  Payable: ₹60,000

Development Fee:
  Annual: ₹15,000
  Waiver: ₹7,500 (50%)
  Payable: ₹7,500

Activity Fee:
  Annual: ₹12,000
  Waiver: ₹0
  Payable: ₹12,000

Examination Fee:
  Annual: ₹6,000
  Waiver: ₹0
  Payable: ₹6,000

Library Fee:
  Annual: ₹3,000
  Waiver: ₹0
  Payable: ₹3,000

Computer Lab Fee:
  Annual: ₹5,000
  Waiver: ₹0
  Payable: ₹5,000

Total Annual Fee: ₹1,61,000
Total Waiver: ₹67,500 (42%)
Total Payable: ₹93,500 (58%)

Eligibility Criteria:
  - Parent must be permanent employee
  - Minimum 2 years of service
  - Employee in good standing
  - Maximum 2 children per employee
  - Benefit continues even if parent retires (till Grade 12)

Employee Categories:
  Teaching Staff: 50% concession
  Non-Teaching Staff: 50% concession
  Contract Staff: 25% concession (after 3 years)

Example Student:
  Name: Priya Sharma (2024/06/0458)
  Category: STAFF_WARD
  Parent: Mrs. Anjali Gupta (Class Teacher)
  Service: 12 years
  Annual Fee: ₹93,500 (42% waiver)
  Status: APPROVED ✓
```

---

### 2. Sibling Discounts

#### 2.1 Automatic Sibling Discount Calculation

**Discount Structure:**
```yaml
Sibling Discount Policy:

1st Child:
  Discount: 0%
  Reason: First child in school
  
2nd Child:
  Discount: 10% on tuition fee
  Applies to: Younger sibling
  
3rd Child:
  Discount: 15% on tuition fee
  Applies to: Youngest sibling
  
4th+ Child:
  Discount: 20% on tuition fee
  Applies to: Youngest sibling

Discount Application:
  - Applied only on tuition fee
  - Other fees (activity, exam, etc.) not discounted
  - Discount given to younger siblings
  - Eldest child pays full fee
  - Auto-calculated by system

Example Family:
  Family ID: FAM-2024-1234
  Father: Mr. Rajesh Patel
  Mother: Mrs. Priya Patel

  Child 1: Diya Patel (2018/08/0123)
    Grade: 8-A
    Tuition Fee: ₹1,40,000
    Sibling Discount: 0% (eldest)
    Payable: ₹1,40,000
    
  Child 2: Aarav Patel (2024/06/0456)
    Grade: 6-A
    Tuition Fee: ₹1,20,000
    Sibling Discount: 10% (2nd child)
    Discount Amount: ₹12,000
    Payable: ₹1,08,000
    
  Child 3: Arjun Patel (2025/01/0089)
    Grade: 1-B
    Tuition Fee: ₹80,000
    Sibling Discount: 15% (3rd child)
    Discount Amount: ₹12,000
    Payable: ₹68,000

Total Family Tuition: ₹3,40,000
Total Discount: ₹24,000
Total Payable: ₹3,16,000
Family Savings: 7% overall
```

#### 2.2 Sibling Discount Edge Cases

**Special Scenarios:**
```yaml
Scenario 1: Sibling Graduates Mid-Year
  Situation: Eldest sibling completes Grade 12 in March
  Impact: 2nd child becomes eldest from next academic year
  Action: Discount recalculated from April
  
  Example:
    Before (2024-25):
      Child 1 (Grade 12): 0% discount
      Child 2 (Grade 9): 10% discount
      
    After (2025-26):
      Child 1: Graduated (Alumni)
      Child 2 (Grade 10): 0% discount (now eldest)
      
Scenario 2: New Sibling Joins Mid-Year
  Situation: 3rd child admitted in July
  Impact: Existing 2nd child's discount unchanged
  Action: New child gets 3rd child discount
  
  Example:
    Before:
      Child 1: 0% discount
      Child 2: 10% discount
      
    After new admission:
      Child 1: 0% discount
      Child 2: 10% discount (unchanged)
      Child 3: 15% discount (new)
      
Scenario 3: Sibling Withdrawn
  Situation: Middle child withdrawn from school
  Impact: Younger sibling's discount adjusted
  Action: Recalculate from next term
  
  Example:
    Before:
      Child 1: 0%
      Child 2: 10%
      Child 3: 15%
      
    After Child 2 withdrawal:
      Child 1: 0%
      Child 3: 10% (now 2nd child)
      
Scenario 4: Twins/Triplets
  Situation: Multiple children in same grade
  Impact: Age-based discount application
  Action: Elder twin = 0%, Younger twin = 10%
  
  Example (Twins):
    Twin A (elder by 5 minutes): 0% discount
    Twin B (younger): 10% discount
```

---

### 3. Merit-Based Scholarships

#### 3.1 Academic Excellence Scholarship

**Scholarship Criteria:**
```yaml
Scholarship: Academic Excellence Award
Eligibility: Based on previous year performance

Scholarship Tiers:

Tier 1 - Gold (95%+ marks):
  Scholarship: 50% tuition fee waiver
  Eligibility: 95% or above in previous grade
  Duration: 1 academic year
  Renewal: Based on maintaining 90%+
  
Tier 2 - Silver (90-95% marks):
  Scholarship: 30% tuition fee waiver
  Eligibility: 90-95% in previous grade
  Duration: 1 academic year
  Renewal: Based on maintaining 85%+
  
Tier 3 - Bronze (85-90% marks):
  Scholarship: 20% tuition fee waiver
  Eligibility: 85-90% in previous grade
  Duration: 1 academic year
  Renewal: Based on maintaining 80%+

Application Process:
  - Automatic consideration (no application needed)
  - Based on Grade 10 board results (for Grade 11)
  - Based on annual exam results (for other grades)
  - Scholarship awarded at admission/promotion
  
Renewal Criteria:
  - Maintain minimum percentage
  - No disciplinary issues
  - 90%+ attendance
  - Annual review

Example Student:
  Name: Diya Patel (2018/08/0123)
  Grade: 11 (Science - PCM)
  Grade 10 Result: 96.5%
  Scholarship: Gold (50% waiver)
  
  Tuition Fee: ₹1,50,000
  Scholarship: ₹75,000 (50%)
  Payable: ₹75,000
  
  Renewal Condition: Maintain 90%+ in Grade 11
```

#### 3.2 Sports Excellence Scholarship

**Sports Scholarship:**
```yaml
Scholarship: Sports Excellence Award
Eligibility: State/National level sports achievements

Scholarship Levels:

National Level:
  Achievement: Represented India
  Scholarship: 75% tuition fee waiver
  Additional: Free sports equipment
  
State Level:
  Achievement: Represented state
  Scholarship: 50% tuition fee waiver
  Additional: Sports kit subsidy
  
District Level:
  Achievement: District champion
  Scholarship: 25% tuition fee waiver
  
School Level:
  Achievement: School team captain
  Scholarship: 10% tuition fee waiver

Eligibility Criteria:
  - Valid sports certificate
  - Active participation in school sports
  - Represent school in competitions
  - Maintain 60%+ academic performance
  - No disciplinary issues

Example Student:
  Name: Rohan Kumar (2024/06/0457)
  Sport: Cricket
  Achievement: State U-14 team member
  Certificate: Maharashtra Cricket Association
  
  Scholarship: 50% tuition fee waiver
  Tuition Fee: ₹1,20,000
  Waiver: ₹60,000
  Payable: ₹60,000
  
  Conditions:
    - Continue playing for school team
    - Maintain 60%+ marks
    - Represent school in tournaments
```

---

### 4. Need-Based Financial Aid

#### 4.1 Financial Hardship Concession

**Hardship Concession Process:**
```yaml
Concession Type: Financial Hardship
Eligibility: Sudden financial crisis in family
Maximum Concession: 50% total fee

Qualifying Situations:
  - Parent job loss
  - Medical emergency in family
  - Natural disaster impact
  - Business failure
  - Death of earning member

Application Process:
  Step 1: Submit application with supporting documents
  Step 2: Financial assessment by committee
  Step 3: Home visit (if required)
  Step 4: Committee review
  Step 5: Principal approval
  Step 6: Concession granted

Required Documents:
  - Income proof (latest 3 months)
  - Bank statements
  - Supporting documents (medical bills, etc.)
  - Employer letter (if job loss)
  - Affidavit

Example Case:
  Student: Amit Kumar (2024/06/0461)
  Grade: 7-B
  Situation: Father lost job due to company closure
  
  Application Details:
    Applied: 15/08/2024
    Reason: Father unemployed since July 2024
    Previous Income: ₹8,00,000/year
    Current Income: ₹0 (Mother: ₹3,00,000/year)
    
  Documents Submitted:
    - Termination letter from employer
    - Last 3 months salary slips
    - Bank statements
    - Mother's income proof
    
  Committee Decision:
    Concession: 40% on total fee
    Duration: 1 academic year
    Review: After 6 months
    
  Fee Impact:
    Total Fee: ₹1,35,000
    Concession: ₹54,000 (40%)
    Payable: ₹81,000
    
  Conditions:
    - Father to submit job search proof monthly
    - Review after 6 months
    - If situation improves, concession may be reduced
```

---

### 5. Combined Concessions & Limits

#### 5.1 Multiple Concession Stacking

**Concession Combination Rules:**
```yaml
Rule 1: Maximum Total Concession
  Limit: 75% of total fee
  Reason: School financial sustainability
  
Rule 2: Concession Priority Order
  1. EWS Quota (100% - standalone, cannot combine)
  2. Staff Ward (50% - can combine with sibling)
  3. Merit Scholarship (up to 50% - can combine)
  4. Sibling Discount (up to 20% - can combine)
  5. Sports Scholarship (up to 75% - limited combination)
  6. Financial Hardship (up to 50% - case by case)

Allowed Combinations:

Combination 1: Staff Ward + Sibling
  Staff Ward: 50% on tuition
  Sibling (2nd child): 10% on tuition
  Total: 60% on tuition ✓ Allowed
  
Combination 2: Merit + Sibling
  Merit (Gold): 50% on tuition
  Sibling (2nd child): 10% on tuition
  Total: 60% on tuition ✓ Allowed
  
Combination 3: Sports + Sibling
  Sports (State): 50% on tuition
  Sibling (2nd child): 10% on tuition
  Total: 60% on tuition ✓ Allowed
  
Combination 4: Staff Ward + Merit
  Staff Ward: 50% on tuition
  Merit (Gold): 50% on tuition
  Total: 100% on tuition ❌ Not Allowed
  Maximum: 75% (Staff 50% + Merit 25%)

Not Allowed Combinations:

EWS + Any Other:
  EWS is standalone (100% waiver)
  Cannot combine with other concessions
  
Sports (National 75%) + Merit (50%):
  Total would be 125% ❌
  Maximum allowed: 75%
  Apply higher concession only

Example Complex Case:
  Student: Priya Sharma (Staff Ward + Sibling + Merit)
  
  Base Tuition Fee: ₹1,20,000
  
  Concession 1: Staff Ward (50%)
    Discount: ₹60,000
    
  Concession 2: Sibling (2nd child - 10%)
    Discount: ₹12,000
    
  Concession 3: Merit Silver (30%)
    Discount: ₹36,000
    
  Total Discount: ₹1,08,000 (90%)
  Maximum Allowed: ₹90,000 (75%)
  
  Final Calculation:
    Tuition Fee: ₹1,20,000
    Discount Applied: ₹90,000 (75% cap)
    Payable: ₹30,000
    
  Concession Breakdown (capped):
    Staff Ward: ₹60,000 (50%)
    Sibling: ₹12,000 (10%)
    Merit: ₹18,000 (15% - adjusted)
    Total: ₹90,000 (75%)
```

---

## 📊 DATABASE SCHEMA

### Student Fee Category Table

```sql
CREATE TABLE student_fee_categories (
  fee_category_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Primary Category
  primary_category ENUM(
    'REGULAR', 'EWS', 'STAFF_WARD', 'SCHOLARSHIP', 'SPECIAL'
  ) NOT NULL DEFAULT 'REGULAR',
  
  -- Fee Structure
  base_tuition_fee DECIMAL(10,2) NOT NULL,
  development_fee DECIMAL(10,2) DEFAULT 0,
  activity_fee DECIMAL(10,2) DEFAULT 0,
  examination_fee DECIMAL(10,2) DEFAULT 0,
  library_fee DECIMAL(10,2) DEFAULT 0,
  computer_lab_fee DECIMAL(10,2) DEFAULT 0,
  transport_fee DECIMAL(10,2) DEFAULT 0,
  hostel_fee DECIMAL(10,2) DEFAULT 0,
  
  -- Total
  total_annual_fee DECIMAL(10,2) NOT NULL,
  
  -- Category Details
  ews_eligible BOOLEAN DEFAULT FALSE,
  ews_certificate_number VARCHAR(50),
  ews_certificate_valid_till DATE,
  
  staff_ward BOOLEAN DEFAULT FALSE,
  parent_employee_id INT,
  
  -- Metadata
  assigned_date DATE NOT NULL,
  assigned_by INT,
  effective_from DATE NOT NULL,
  effective_to DATE,
  
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  notes TEXT,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_year (student_id, academic_year),
  INDEX idx_category (primary_category),
  UNIQUE INDEX idx_student_active (student_id, academic_year)
);
```

### Fee Concessions Table

```sql
CREATE TABLE fee_concessions (
  concession_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Concession Type
  concession_type ENUM(
    'SIBLING_DISCOUNT', 'MERIT_SCHOLARSHIP', 'SPORTS_SCHOLARSHIP',
    'NEED_BASED', 'FINANCIAL_HARDSHIP', 'SPECIAL_CASE', 'OTHER'
  ) NOT NULL,
  
  -- Concession Details
  concession_name VARCHAR(200) NOT NULL,
  concession_percentage DECIMAL(5,2) NOT NULL,
  concession_amount DECIMAL(10,2) NOT NULL,
  
  -- Applicable On
  applicable_on ENUM('TUITION', 'TOTAL_FEE', 'SPECIFIC_COMPONENTS') NOT NULL,
  specific_components TEXT,
  
  -- Eligibility
  eligibility_criteria TEXT,
  supporting_documents TEXT,
  
  -- Approval
  application_date DATE,
  approval_status ENUM('PENDING', 'APPROVED', 'REJECTED', 'CANCELLED') DEFAULT 'PENDING',
  approved_by INT,
  approval_date DATE,
  rejection_reason TEXT,
  
  -- Duration
  effective_from DATE NOT NULL,
  effective_to DATE NOT NULL,
  is_renewable BOOLEAN DEFAULT FALSE,
  renewal_criteria TEXT,
  
  -- Auto-calculated (for sibling)
  auto_calculated BOOLEAN DEFAULT FALSE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  created_by INT,
  notes TEXT,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_year (student_id, academic_year),
  INDEX idx_type (concession_type),
  INDEX idx_status (approval_status)
);
```

### Scholarship Applications Table

```sql
CREATE TABLE scholarship_applications (
  application_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Scholarship Details
  scholarship_type ENUM('MERIT', 'SPORTS', 'NEED_BASED', 'SPECIAL_TALENT') NOT NULL,
  scholarship_name VARCHAR(200) NOT NULL,
  academic_year VARCHAR(9) NOT NULL,
  
  -- Application
  application_date DATE NOT NULL,
  applied_by INT NOT NULL,
  
  -- Eligibility
  eligibility_score DECIMAL(5,2),
  previous_grade_percentage DECIMAL(5,2),
  sports_achievement VARCHAR(500),
  family_income DECIMAL(12,2),
  
  -- Documents
  marksheet_uploaded BOOLEAN DEFAULT FALSE,
  income_certificate_uploaded BOOLEAN DEFAULT FALSE,
  sports_certificate_uploaded BOOLEAN DEFAULT FALSE,
  other_documents TEXT,
  
  -- Assessment
  committee_review_date DATE,
  committee_decision TEXT,
  
  -- Approval
  approval_status ENUM('PENDING', 'UNDER_REVIEW', 'APPROVED', 'REJECTED') DEFAULT 'PENDING',
  approved_by INT,
  approval_date DATE,
  rejection_reason TEXT,
  
  -- Scholarship Amount
  scholarship_percentage DECIMAL(5,2),
  scholarship_amount DECIMAL(10,2),
  
  -- Duration
  valid_from DATE,
  valid_to DATE,
  
  -- Renewal
  renewal_required BOOLEAN DEFAULT TRUE,
  renewal_criteria TEXT,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  notes TEXT,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_type (scholarship_type),
  INDEX idx_status (approval_status),
  INDEX idx_year (academic_year)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Automatic Sibling Discount Calculation

```javascript
FUNCTION calculate_sibling_discount(student_id, academic_year) {
  // Get student details
  student = GET_STUDENT(student_id)
  
  // Find family
  family_id = student.family_id
  IF NOT family_id {
    RETURN {discount: 0, reason: "No family linkage"}
  }
  
  // Get all active siblings in school
  siblings = QUERY("
    SELECT s.student_id, s.date_of_birth, s.admission_number,
           s.current_grade, fc.base_tuition_fee
    FROM students s
    JOIN student_fee_categories fc ON s.student_id = fc.student_id
    WHERE s.family_id = ?
    AND s.student_status = 'ACTIVE'
    AND fc.academic_year = ?
    ORDER BY s.date_of_birth ASC
  ", [family_id, academic_year])
  
  IF siblings.length == 0 {
    RETURN {discount: 0, reason: "No siblings found"}
  }
  
  // Determine child position (by age)
  child_position = 0
  FOR i = 0 TO siblings.length - 1 {
    IF siblings[i].student_id == student_id {
      child_position = i + 1
      BREAK
    }
  }
  
  // Calculate discount based on position
  discount_percentage = 0
  
  IF child_position == 1 {
    discount_percentage = 0  // Eldest child, no discount
  } ELSE IF child_position == 2 {
    discount_percentage = 10  // 2nd child, 10% discount
  } ELSE IF child_position == 3 {
    discount_percentage = 15  // 3rd child, 15% discount
  } ELSE {
    discount_percentage = 20  // 4th+ child, 20% discount
  }
  
  // Get tuition fee
  fee_category = GET_FEE_CATEGORY(student_id, academic_year)
  tuition_fee = fee_category.base_tuition_fee
  
  // Calculate discount amount
  discount_amount = (tuition_fee * discount_percentage) / 100
  
  // Check if concession already exists
  existing_concession = QUERY("
    SELECT * FROM fee_concessions
    WHERE student_id = ?
    AND academic_year = ?
    AND concession_type = 'SIBLING_DISCOUNT'
  ", [student_id, academic_year])
  
  IF existing_concession.exists() {
    // Update existing
    UPDATE fee_concessions SET
      concession_percentage = discount_percentage,
      concession_amount = discount_amount,
      modified_date = NOW()
    WHERE concession_id = existing_concession.concession_id
  } ELSE {
    // Create new
    INSERT INTO fee_concessions (
      student_id,
      academic_year,
      concession_type,
      concession_name,
      concession_percentage,
      concession_amount,
      applicable_on,
      effective_from,
      effective_to,
      auto_calculated,
      approval_status
    ) VALUES (
      student_id,
      academic_year,
      'SIBLING_DISCOUNT',
      `Sibling Discount (${child_position}${GET_ORDINAL_SUFFIX(child_position)} child)`,
      discount_percentage,
      discount_amount,
      'TUITION',
      GET_ACADEMIC_YEAR_START(academic_year),
      GET_ACADEMIC_YEAR_END(academic_year),
      TRUE,
      'APPROVED'
    )
  }
  
  // Log activity
  LOG_AUDIT("Sibling discount calculated", {
    student_id: student_id,
    family_id: family_id,
    child_position: child_position,
    siblings_count: siblings.length,
    discount_percentage: discount_percentage,
    discount_amount: discount_amount
  })
  
  RETURN {
    discount_percentage: discount_percentage,
    discount_amount: discount_amount,
    child_position: child_position,
    total_siblings: siblings.length,
    applied: TRUE
  }
}
```

### Rule 2: Total Concession Cap Validation

```javascript
FUNCTION validate_total_concessions(student_id, academic_year) {
  // Get all approved concessions
  concessions = QUERY("
    SELECT * FROM fee_concessions
    WHERE student_id = ?
    AND academic_year = ?
    AND approval_status = 'APPROVED'
  ", [student_id, academic_year])
  
  // Get fee category
  fee_category = GET_FEE_CATEGORY(student_id, academic_year)
  
  // Check if EWS (standalone, cannot combine)
  IF fee_category.primary_category == 'EWS' {
    // EWS students cannot have additional concessions
    non_ews_concessions = concessions.filter(c => c.concession_type != 'EWS_QUOTA')
    
    IF non_ews_concessions.length > 0 {
      RETURN {
        valid: FALSE,
        error: "EWS students cannot have additional concessions",
        action: "Remove other concessions"
      }
    }
    
    RETURN {valid: TRUE, total_percentage: 100, capped: FALSE}
  }
  
  // Calculate total concession
  total_concession_amount = 0
  total_percentage = 0
  
  FOR each concession IN concessions {
    IF concession.applicable_on == 'TUITION' {
      total_concession_amount += concession.concession_amount
    } ELSE IF concession.applicable_on == 'TOTAL_FEE' {
      total_concession_amount += concession.concession_amount
    }
  }
  
  // Calculate percentage
  total_percentage = (total_concession_amount / fee_category.base_tuition_fee) * 100
  
  // Check if exceeds 75% cap
  MAX_CONCESSION_PERCENTAGE = 75
  
  IF total_percentage > MAX_CONCESSION_PERCENTAGE {
    // Cap the concessions
    allowed_amount = (fee_category.base_tuition_fee * MAX_CONCESSION_PERCENTAGE) / 100
    excess_amount = total_concession_amount - allowed_amount
    
    // Adjust concessions (priority-based)
    // Priority: Staff Ward > Merit > Sports > Sibling > Others
    
    adjusted_concessions = ADJUST_CONCESSIONS_TO_CAP(
      concessions,
      allowed_amount,
      fee_category.base_tuition_fee
    )
    
    // Update concessions in database
    FOR each adjusted IN adjusted_concessions {
      UPDATE fee_concessions SET
        concession_amount = adjusted.new_amount,
        concession_percentage = adjusted.new_percentage,
        notes = CONCAT(notes, ' | Capped at 75% total concession limit')
      WHERE concession_id = adjusted.concession_id
    }
    
    // Notify accounts department
    SEND_NOTIFICATION("accounts_team", {
      title: "Concession Capped",
      message: `Student ${student_id} concessions capped at 75%`,
      priority: "NORMAL"
    })
    
    RETURN {
      valid: TRUE,
      total_percentage: MAX_CONCESSION_PERCENTAGE,
      capped: TRUE,
      original_percentage: total_percentage,
      excess_amount: excess_amount,
      adjusted_concessions: adjusted_concessions
    }
  }
  
  RETURN {
    valid: TRUE,
    total_percentage: total_percentage,
    capped: FALSE,
    total_amount: total_concession_amount
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Fee Management Module**: Fee structure and concession amounts
2. **Accounts Module**: Financial reporting and tracking
3. **Scholarship Module**: Scholarship allocation and tracking
4. **Communication Module**: Concession approval notifications

### Inbound Integrations

1. **Student Registration**: Initial fee category assignment
2. **Academic Module**: Merit-based scholarship eligibility
3. **Sports Module**: Sports scholarship eligibility
4. **HR Module**: Staff ward verification

---

## 👥 USER WORKFLOWS

### Workflow: Scholarship Application by Student

**Actor:** Student/Parent  
**Duration:** 20-30 minutes  
**Trigger:** Eligible for merit scholarship

**Steps:**

1. Login to Student/Parent Portal
2. Navigate to: Finance → Scholarships
3. View eligible scholarships
4. Select: Merit Scholarship (Gold - 50%)
5. Fill application form
6. Upload Grade 10 marksheet (96.5%)
7. Submit application
8. Committee reviews application
9. Principal approves
10. Scholarship activated
11. Fee structure updated
12. Confirmation email sent

**Expected Outcome:** Scholarship approved, 50% tuition fee waiver applied

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 17, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade  
**Compliance:** RTE Act 2009, EWS Guidelines, School Fee Regulations

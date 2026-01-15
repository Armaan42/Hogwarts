# FEE STRUCTURE & CONFIGURATION

**Module:** Fee Management  
**Submodule Code:** FEE-STRUCT-001  
**Category:** Financial Core  
**Priority:** Critical (P0)

## Overview

This submodule handles the definition and management of all fee types, structures, and configurations for different grades, categories, and academic years. It serves as the foundation for the entire fee management system.

## Purpose

Define and manage all fee heads, grade-wise fee structures, optional components, academic year configurations, fee revisions, and concession rules to ensure accurate and consistent fee calculation across the institution.

## Key Features

### 1.1 Fee Head Master

**Fee Head Types:**
- Tuition Fee (Main academic fee)
- Admission Fee (One-time, at admission)
- Annual Charges (Yearly, non-recurring)
- Development Fee (Infrastructure)
- Examination Fee (Per term/annual)
- Laboratory Fee (Science, Computer labs)
- Library Fee (Books, digital resources)
- Activity Fee (Sports, cultural activities)
- Transport Fee (Bus service)
- Hostel Fee (Boarding charges)
- Mess Fee (Meals for boarders)
- Uniform Fee (Prescribed uniform)
- Textbook Fee (Academic books)
- Stationery Fee (Notebooks, supplies)
- Security Deposit (Refundable at exit)
- Caution Money (Refundable)
- Late Fee/Fine (Payment delays)
- Miscellaneous Fee (Ad-hoc charges)

**Fee Head Configuration:**
- Name, Short Code (TF, AF, LF...)
- Description
- Tax Applicable (GST 0%, 12%, 18%)
- Refundable/Non-refundable
- Mandatory/Optional
- Frequency (One-time/Annual/Quarterly/Monthly)
- GL Account Code (for accounting integration)

### 1.2 Grade-wise Fee Structure

**Primary School (Grades 1-5):**
- Tuition: ₹60,000/year
- Annual Charges: ₹5,000
- Exam Fee: ₹2,000
- Activity Fee: ₹3,000
- **Total: ₹70,000/year**

**Middle School (Grades 6-8):**
- Tuition: ₹80,000/year
- Annual Charges: ₹6,000
- Exam Fee: ₹3,000
- Lab Fee: ₹4,000
- Activity Fee: ₹4,000
- **Total: ₹97,000/year**

**Secondary (Grades 9-10):**
- Tuition: ₹1,00,000/year
- Annual Charges: ₹7,000
- Exam Fee: ₹5,000 (Board exam preparation)
- Lab Fee: ₹5,000
- Activity Fee: ₹5,000
- **Total: ₹1,22,000/year**

**Senior Secondary (Grades 11-12):**

*Science Stream:*
- Tuition: ₹1,30,000/year
- Lab Fee: ₹8,000 (Physics, Chemistry, Biology)
- Exam Fee: ₹6,000
- **Total: ₹1,50,000/year (approx)**

*Commerce Stream:*
- Tuition: ₹1,10,000/year
- Computer Lab: ₹4,000
- Exam Fee: ₹6,000
- **Total: ₹1,25,000/year (approx)**

*Arts/Humanities:*
- Tuition: ₹1,00,000/year
- Exam Fee: ₹6,000
- **Total: ₹1,10,000/year (approx)**

### 1.3 Optional/Add-on Fee Components

**Transport Fee (Distance-based):**
- 0-5 km: ₹12,000/year
- 5-10 km: ₹18,000/year
- 10-15 km: ₹24,000/year
- 15-20 km: ₹30,000/year
- 20+ km: ₹36,000/year
- One-way vs Two-way options
- Pickup + Drop vs Drop only

**Hostel Fee:**
- Boarding charges: ₹80,000/year
- Mess charges: ₹50,000/year (3 meals/day)
- Room type variations:
  - Shared room (4 students): Standard rate
  - Shared room (2 students): +₹15,000/year
  - Single room: +₹30,000/year

**Extra-curricular Add-ons:**
- Music lessons (Instrumental): ₹12,000/year
- Dance classes: ₹10,000/year
- Swimming coaching: ₹15,000/year
- Martial arts: ₹12,000/year
- Coding classes: ₹18,000/year

### 1.4 Academic Year Configuration

**Financial Year:**
- Start Date: April 1
- End Date: March 31

**Fee Payment Terms:**
- Term 1: April-June
- Term 2: July-September
- Term 3: October-December
- Term 4: January-March

**Fee Freezing:**
- Fee structure locked before academic year starts
- Changes require board approval
- Mid-year changes applied pro-rata

### 1.5 Fee Revision & Escalation

**Annual Fee Increase:**
- Board approval required
- Typical increase: 8-12% year-over-year
- Inflation adjustment

**Revision Process:**
1. Proposal in January
2. Board meeting in February
3. Approval + Parent notification in March
4. New fees effective from April (new academic year)

**Grandfather Clause:**
- Existing students: Gradual increase (max 10%/year)
- New admissions: Full new rate applicable

### 1.6 Concession & Discount Rules

**Sibling Discount (Automatic):**
- 1st child: 0% discount
- 2nd child: 10% discount on tuition
- 3rd child: 15% discount on tuition
- 4th+ child: 20% discount on tuition
- Calculated on tuition fee only (not on transport, hostel)

**Scholarship Discount:**
- Merit-based: 25%, 50%, 75%, 100%
- Sports quota: 25%, 50%
- Need-based: Case-by-case (up to 100%)
- Applies to: Tuition + Annual charges (not optional fees)

**Staff Ward Discount:**
- Teaching staff: 50-100% waiver
- Non-teaching staff: 25-50% waiver
- Based on years of service

**Alumni Discount:**
- Parent is alumnus: 5% discount
- Both parents alumni: 10% discount

**Early Bird Discount:**
- Pay full year in April: 5% discount
- Pay term fee 15 days in advance: 2% discount

**Bulk Payment Discount:**
- Multiple children + full annual payment: Additional 2%

## Data Fields

### Fee Head Master Table
```
fee_head_id (PK)
fee_head_name
short_code
description
tax_rate (GST %)
is_refundable (boolean)
is_mandatory (boolean)
frequency (ENUM: ONE_TIME, ANNUAL, QUARTERLY, MONTHLY)
gl_account_code
created_date
modified_date
status (ACTIVE/INACTIVE)
```

### Grade Fee Structure Table
```
structure_id (PK)
academic_year
grade_id (FK)
stream_id (FK - for Grades 11-12)
fee_head_id (FK)
amount
effective_from_date
effective_to_date
approved_by
approval_date
status
```

### Discount Rules Table
```
discount_id (PK)
discount_type (ENUM: SIBLING, SCHOLARSHIP, STAFF_WARD, ALUMNI, EARLY_BIRD)
discount_percentage
eligibility_criteria
applicable_fee_heads
auto_apply (boolean)
requires_approval (boolean)
created_date
status
```

## Business Rules

### Rule 1: Fee Structure Validation
```
FUNCTION validate_fee_structure(grade, academic_year):
  // Ensure all mandatory fee heads are defined
  mandatory_heads = GET mandatory_fee_heads()
  
  FOR each head IN mandatory_heads:
    IF NOT EXISTS fee_structure(grade, academic_year, head):
      RETURN ERROR "Missing fee head: {head.name} for Grade {grade}"
    END IF
  END FOR
  
  // Validate total fee is within acceptable range
  total_fee = SUM(fee_structure.amount WHERE grade AND academic_year)
  
  IF total_fee < minimum_fee_threshold:
    RETURN ERROR "Total fee too low"
  END IF
  
  IF total_fee > maximum_fee_threshold:
    RETURN WARNING "Total fee exceeds typical range, requires approval"
  END IF
  
  RETURN SUCCESS
END FUNCTION
```

### Rule 2: Discount Calculation Priority
```
FUNCTION calculate_total_discount(student):
  base_tuition = GET tuition_fee(student.grade)
  total_discount = 0
  
  // Priority 1: Scholarship (highest priority)
  IF student.scholarship_percentage > 0:
    scholarship_discount = base_tuition * (student.scholarship_percentage / 100)
    total_discount += scholarship_discount
    base_tuition -= scholarship_discount
  END IF
  
  // Priority 2: Staff Ward Discount
  IF student.category = "STAFF_WARD":
    staff_discount = base_tuition * (staff_discount_percentage / 100)
    total_discount += staff_discount
    base_tuition -= staff_discount
  END IF
  
  // Priority 3: Sibling Discount (on remaining amount)
  sibling_count = COUNT active_siblings(student.family_id)
  IF sibling_count >= 1:
    sibling_discount_pct = GET sibling_discount_percentage(sibling_count)
    sibling_discount = base_tuition * (sibling_discount_pct / 100)
    total_discount += sibling_discount
  END IF
  
  // Priority 4: Alumni Discount (on remaining amount)
  IF student.parent_is_alumni:
    alumni_discount = base_tuition * 0.05  // 5%
    total_discount += alumni_discount
  END IF
  
  RETURN total_discount
END FUNCTION
```

### Rule 3: Fee Revision Approval Workflow
```
FUNCTION approve_fee_revision(revision_proposal):
  // Validate revision percentage
  IF revision_proposal.increase_percentage > 15:
    RETURN ERROR "Fee increase >15% requires special board approval"
  END IF
  
  // Check existing student impact
  existing_students = COUNT students WHERE status = "ACTIVE"
  grandfather_clause_applicable = TRUE
  
  IF grandfather_clause_applicable:
    max_increase_existing = 10  // 10% max for existing students
    revision_proposal.existing_student_increase = MIN(revision_proposal.increase_percentage, max_increase_existing)
  END IF
  
  // Approval workflow
  revision_proposal.status = "PENDING_APPROVAL"
  NOTIFY finance_committee(revision_proposal)
  
  // Wait for approval
  IF approved_by_committee:
    revision_proposal.status = "APPROVED"
    revision_proposal.effective_from = NEXT_ACADEMIC_YEAR_START
    
    // Notify parents
    SEND_MASS_NOTIFICATION(parents, "Fee revision for next academic year")
  ELSE:
    revision_proposal.status = "REJECTED"
  END IF
  
  RETURN revision_proposal
END FUNCTION
```

## Integration Points

### Connects TO:
1. **Student Management Module**
   - Retrieves: Student grade, category, sibling information
   - Purpose: Determine applicable fee structure

2. **Accounts & Finance Module**
   - Sends: Fee head definitions, GL account codes
   - Purpose: Chart of accounts integration

3. **Transport Management Module**
   - Receives: Student distance from school
   - Purpose: Calculate distance-based transport fee

4. **Hostel Management Module**
   - Receives: Room type, boarding status
   - Purpose: Calculate hostel and mess charges

5. **Scholarship Management Module**
   - Receives: Scholarship awards, percentages
   - Purpose: Apply scholarship discounts

### Receives FROM:
1. **Admissions Module**
   - Data: New student admission details
   - Trigger: Assign initial fee structure

2. **Board/Management Module**
   - Data: Fee revision approvals
   - Trigger: Update fee structures for new academic year

## User Workflows

### Workflow 1: Define New Fee Structure for Academic Year

**Actor:** Finance Manager

**Steps:**
1. Navigate to Fee Management > Fee Structure Configuration
2. Select Academic Year: 2025-26
3. Click "Create New Structure"
4. Select Grade: Grade 6
5. Add Fee Heads:
   - Tuition: ₹85,000 (8% increase from previous year)
   - Annual Charges: ₹6,500
   - Exam Fee: ₹3,200
   - Lab Fee: ₹4,500
   - Activity Fee: ₹4,200
6. Set Effective Dates: April 1, 2025 - March 31, 2026
7. Submit for Approval
8. **System validates** total fee, checks for missing mandatory heads
9. **System routes** to Finance Committee for approval
10. Upon approval, **system locks** fee structure
11. **System notifies** admissions team: "Fee structure ready for 2025-26"

**Expected Outcome:** Fee structure defined and approved for Grade 6, ready for student assignment.

### Workflow 2: Apply Sibling Discount

**Actor:** System (Automated)

**Steps:**
1. **Trigger:** New student admission (Student B from Family X)
2. **System checks:** Existing students from Family X
3. **System finds:** Student A (Grade 8) already enrolled
4. **System determines:** Student B is 2nd child → 10% sibling discount applicable
5. **System calculates:**
   - Base tuition for Grade 6: ₹85,000
   - Sibling discount (10%): ₹8,500
   - Adjusted tuition: ₹76,500
6. **System applies** discount to Student B's fee assignment
7. **System logs** discount application with reason
8. **System notifies** parent: "Sibling discount of ₹8,500 applied"

**Expected Outcome:** Sibling discount automatically applied, parent informed.

### Workflow 3: Revise Fee Structure Mid-Year (Exception)

**Actor:** Principal

**Steps:**
1. **Scenario:** Government mandates new lab safety equipment fee (₹2,000)
2. Principal logs into system
3. Navigate to Fee Management > Fee Revision
4. Select: Current Academic Year (2024-25)
5. Select: Grades 9-12 (affected grades)
6. Add New Fee Head: "Lab Safety Equipment Fee" - ₹2,000
7. Set Effective Date: October 1, 2024 (mid-year)
8. Provide Justification: "Government mandate for lab safety compliance"
9. Submit for Board Approval
10. **System calculates** pro-rata amount (6 months): ₹1,000
11. Board approves
12. **System generates** supplementary invoices for affected students
13. **System sends** notification to parents with explanation

**Expected Outcome:** Mid-year fee addition processed, parents notified with clear justification.

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026  
**Documentation Standard:** Production-Ready

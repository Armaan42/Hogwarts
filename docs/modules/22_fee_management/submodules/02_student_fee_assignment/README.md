# STUDENT FEE ASSIGNMENT

**Module:** Fee Management  
**Submodule Code:** FEE-ASSIGN-002  
**Category:** Financial Core  
**Priority:** Critical (P0)

## Overview

This submodule applies fee structures to individual students based on their profile, grade, category, and entitlements. It handles automatic fee assignment, manual adjustments, mid-year changes, and withdrawal calculations.

## Purpose

Apply appropriate fee structure to each student considering their grade, category (regular/scholarship/staff ward), siblings, transport enrollment, hostel status, and other factors to ensure accurate billing.

## Key Features

### 2.1 Automatic Fee Assignment

**At Admission:**
- Student admitted to Grade 6
- System picks Grade 6 base fee structure
- Student category: Regular
- Siblings: Yes (1 sibling already enrolled)
- System auto-applies 10% sibling discount
- Transport: Yes, distance 12 km → ₹24,000/year added
- **Final Fee:**
  - Base (Grade 6): ₹97,000
  - Sibling discount on tuition (₹80,000): -₹8,000
  - Transport: +₹24,000
  - **Total: ₹1,13,000/year**

### 2.2 Manual Fee Adjustments

**Special Concessions:**
- Principal approves case-by-case
- Example: Parent job loss, family emergency
- Temporary discount (50% for 6 months)
- Requires justification document
- Board approval for >25% concession

**Fee Waiver:**
- 100% scholarship (merit or need-based)
- Documented in fee assignment
- Reviewed annually

**Ad-hoc Charges:**
- Damage to school property: ₹5,000
- Lost textbook: ₹800
- Replacement ID card: ₹200
- Added to student fee account

### 2.3 Mid-Year Fee Changes

**Grade Promotion (Mid-year):**
- Exceptional student promoted from Grade 7 to Grade 8 in October
- Fee adjustment:
  - Grade 7 fee: April-Sept (6 months)
  - Grade 8 fee: Oct-March (6 months)
  - Pro-rata calculation

**Transport Enrollment Change:**
- Student starts using transport from July
- Transport fee: 9 months (July-March)
- Calculated: ₹24,000 × (9/12) = ₹18,000

**Hostel Enrollment:**
- Day scholar converts to boarder in August
- Hostel fee: 8 months (Aug-March)

### 2.4 Withdrawal & Refund Calculation

**Student Withdraws:**
- Withdrawal date: September 15
- Fees paid: April-September (6 months)
- Refund policy:
  - Tuition: Pro-rata refund for unused months (Oct-March)
  - Admission fee: Non-refundable
  - Security deposit: Refundable after 30 days
  - Transport: If paid in advance, refund for unused months

**Refund Calculation:**
```
Annual tuition: ₹90,000
Paid for: 6 months (Apr-Sep)
Withdrawal: Sep 15
Refundable: 6 months (Oct-Mar) = ₹45,000

Processing:
- Security deposit: ₹10,000 (refunded)
- Tuition refund: ₹45,000
- Total refund: ₹55,000
```

## Data Fields

```
student_fee_assignment_id (PK)
student_id (FK)
academic_year
grade_id (FK)
base_fee_structure_id (FK)
total_annual_fee
tuition_fee
other_fees_breakdown (JSON)
discounts_applied (JSON)
scholarship_percentage
sibling_discount_amount
staff_ward_discount
transport_fee
hostel_fee
mess_fee
extracurricular_fees
total_discount
net_payable
assignment_date
assigned_by
status (ACTIVE/WITHDRAWN/SUSPENDED)
```

## Business Rules

### Fee Assignment Logic
```
FUNCTION assign_student_fee(student):
  base_fee = GET grade_fee_structure(student.grade)
  
  // Apply category discounts
  IF student.scholarship_percentage > 0:
    tuition_discount = base_fee.tuition * (student.scholarship_percentage / 100)
    base_fee.tuition -= tuition_discount
  END IF
  
  // Apply sibling discount
  sibling_count = COUNT(siblings WHERE status = "ACTIVE")
  IF sibling_count = 1:  // Student is 2nd child
    base_fee.tuition *= 0.90  // 10% off
  ELSE IF sibling_count = 2:  // Student is 3rd child
    base_fee.tuition *= 0.85  // 15% off
  END IF
  
  // Apply staff discount
  IF student.category = "STAFF_WARD":
    base_fee.tuition *= 0.50  // 50% off
  END IF
  
  // Add optional components
  IF student.transport_enrolled:
    transport_fee = CALCULATE_TRANSPORT_FEE(student.distance)
    base_fee.add(transport_fee)
  END IF
  
  IF student.hostel_enrolled:
    hostel_fee = GET hostel_fee_structure(student.room_type)
    base_fee.add(hostel_fee)
  END IF
  
  // Store final fee breakdown
  SAVE student_fee_assignment(student, base_fee)
  
  RETURN base_fee
END FUNCTION
```

## Integration Points

### Connects TO:
1. **Student Management** - Get student profile, grade, category
2. **Transport Management** - Get distance for transport fee calculation
3. **Hostel Management** - Get room type for hostel fee
4. **Scholarship Module** - Get scholarship awards
5. **Invoice Generation** - Provide fee breakdown for invoicing

### Receives FROM:
1. **Admissions Module** - New student admission trigger
2. **Fee Structure Module** - Base fee structures
3. **Discount Rules Module** - Applicable discounts

## User Workflows

### Workflow: Assign Fee to New Student

**Actor:** Admissions Officer

**Steps:**
1. Student admission completed
2. System triggers automatic fee assignment
3. System retrieves Grade 6 fee structure
4. System checks for siblings → Found 1 sibling
5. System applies 10% sibling discount
6. Parent opts for transport (12 km distance)
7. System adds transport fee ₹24,000
8. Final fee calculated: ₹1,13,000/year
9. Fee assignment saved
10. Parent notified with fee breakdown

**Expected Outcome:** Student fee accurately assigned with all applicable discounts and add-ons.

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026  
**Documentation Standard:** Production-Ready

# SIBLING DISCOUNT CALCULATOR

**Module:** Parent Financial Planning  
**Submodule Code:** FINPLAN-007  
**Category:** Fee Optimization  
**Priority:** Medium (P2)  
**Owner:** Finance & Admissions Department

---

## 📋 OVERVIEW

The Sibling Discount Calculator submodule automates the calculation, application, and tracking of fee concessions for families with multiple children enrolled in the institution. It handles various discount tiers based on number of siblings, grade levels, enrollment dates, and institutional policies — ensuring accurate billing and transparent communication with parents.

### Purpose

Reduce the financial burden on multi-child families through automated sibling discount computation, provide transparent breakdowns of savings, handle complex scenarios like mid-year enrollments and withdrawals, and incentivize multi-child enrollment for improved retention rates.

### Scope

- **Discount Calculation**: Automated tiered discount engine based on sibling count
- **Enrollment Verification**: Real-time sibling verification through family records
- **Fee Adjustment**: Direct integration with Fee Management for billing updates
- **Scenario Modeling**: "What-if" calculator for prospective parents during admissions
- **Reporting**: Discount utilization analytics and revenue impact assessment

---

## 🎯 KEY FEATURES

### 7.1 Sibling Discount Structure

**Standard Discount Tiers:**
```
╔══════════════════════════════════════════════════════════════╗
║               SIBLING DISCOUNT SCHEDULE 2024-25              ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  1st Child:  No discount (standard fees)                     ║
║  2nd Child:  10% discount on tuition fee                     ║
║  3rd Child:  15% discount on tuition fee                     ║
║  4th+ Child: 20% discount on tuition fee                     ║
║                                                              ║
║  APPLICABLE ON: Tuition Fee ONLY                             ║
║  NOT ON: Transport, Books, Uniform, Activity Fee             ║
║                                                              ║
║  ELIGIBILITY: All siblings must be currently enrolled        ║
║  EFFECTIVE: From the month of 2nd child's enrollment         ║
║                                                              ║
╚══════════════════════════════════════════════════════════════╝
```

### 7.2 Discount Calculation Examples

**Example 1: Two Siblings**
```
Family: Kumar Family
  Child 1: Aarav Kumar (Grade 8) - Enrolled since Grade 1
    Annual Tuition: ₹1,20,000
    Discount: 0% (1st child)
    Payable Tuition: ₹1,20,000

  Child 2: Ananya Kumar (Grade 5) - Enrolled since Grade 3
    Annual Tuition: ₹95,000
    Discount: 10% = ₹9,500
    Payable Tuition: ₹85,500

  Total Tuition: ₹2,15,000  →  After Discount: ₹2,05,500
  Annual Savings: ₹9,500
  K-12 Savings (projected): ₹1,23,500 (13 years)
```

**Example 2: Three Siblings**
```
Family: Sharma Family
  Child 1: Rohan Sharma (Grade 10)
    Annual Tuition: ₹1,40,000
    Discount: 0%
    Payable: ₹1,40,000

  Child 2: Priya Sharma (Grade 7)
    Annual Tuition: ₹1,10,000
    Discount: 10% = ₹11,000
    Payable: ₹99,000

  Child 3: Meera Sharma (Grade 3)
    Annual Tuition: ₹85,000
    Discount: 15% = ₹12,750
    Payable: ₹72,250

  Total Tuition: ₹3,35,000  →  After Discount: ₹3,11,250
  Annual Savings: ₹23,750
  Combined K-12 Savings: ₹3,08,750 (projected)
```

**Example 3: Prospective Parent "What-If" Calculator**
```
Scenario: Mr. Vikram Patel is considering enrolling his 3rd child.

Current Enrollment:
  Child 1 (Grade 9): Tuition ₹1,30,000 → No discount
  Child 2 (Grade 6): Tuition ₹1,05,000 → 10% = ₹10,500 discount

If 3rd child enrolled (Grade 1):
  Child 3: Tuition ₹80,000 → 15% = ₹12,000 discount
  
  Total New Savings: ₹10,500 + ₹12,000 = ₹22,500/year
  13-Year Projected Savings for Child 3: ₹1,56,000
  
  System Message: "Enrolling your 3rd child saves ₹22,500/year. 
  Over 13 years, you save approximately ₹2,92,500 across all children."
```

### 7.3 Sibling Identification Engine

**Family Mapping Logic:**
```
FUNCTION identify_siblings(student_id):
  student = GET_STUDENT(student_id)
  
  // Method 1: Parent ID matching
  siblings_by_parent = FIND_STUDENTS(
    WHERE father_id = student.father_id 
    OR mother_id = student.mother_id
    AND student_id != student.student_id
    AND enrollment_status = 'ACTIVE'
  )
  
  // Method 2: Family tree cross-reference
  siblings_by_family = FAMILY_TREE.get_siblings(student_id)
  
  // Method 3: Address matching (secondary verification)
  siblings_by_address = FIND_STUDENTS(
    WHERE residential_address MATCHES student.residential_address
    AND last_name = student.last_name
    AND student_id != student.student_id
  )
  
  // Merge and deduplicate
  all_siblings = UNION(siblings_by_parent, siblings_by_family, siblings_by_address)
  verified_siblings = VERIFY_EACH(all_siblings)
  
  // Order by enrollment date (oldest first = Child 1)
  SORT verified_siblings BY enrollment_date ASC
  
  RETURN {
    total_siblings: verified_siblings.COUNT + 1,
    sibling_order: DETERMINE_ORDER(student, verified_siblings),
    discount_applicable: CALCULATE_DISCOUNT(sibling_order)
  }
END FUNCTION
```

### 7.4 Dynamic Discount Adjustment

**Scenarios That Trigger Recalculation:**
```
Trigger 1: New sibling enrolled
  → All existing siblings rechecked
  → Discount tier may change for youngest child
  
Trigger 2: Sibling withdrawn/transferred
  → Remaining siblings re-evaluated
  → If only 1 child remains → discount removed
  → If 3 children reduced to 2 → 3rd child discount drops from 15% to 10%
  
Trigger 3: Sibling completes school (Grade 12 → Alumni)
  → Sibling count reduced for remaining children
  → Discount adjustment effective from next quarter
  
Trigger 4: Academic year rollover
  → All sibling discounts re-verified
  → Fee structure updated for new year
  
Trigger 5: Fee structure revision
  → Discounts recalculated on new base tuition
  → Parents notified of updated discount amounts
```

---

## 📊 DATA FIELDS

### Sibling Discount Record

```sql
CREATE TABLE finplan_sibling_discount (
    discount_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    family_id INT NOT NULL,
    academic_year VARCHAR(9) NOT NULL,
    
    -- Child Details
    student_id INT NOT NULL,
    student_name VARCHAR(100),
    grade VARCHAR(10),
    sibling_order INT,                          -- 1, 2, 3, 4...
    
    -- Sibling Mapping
    total_siblings_enrolled INT,
    sibling_ids JSON,                           -- [101, 102, 103]
    verification_method ENUM('PARENT_ID', 'FAMILY_TREE', 'MANUAL'),
    
    -- Discount Calculation
    base_tuition_fee DECIMAL(10,2),
    discount_percentage DECIMAL(5,2),
    discount_amount DECIMAL(10,2),
    net_tuition_fee DECIMAL(10,2),
    
    -- Non-Discounted Components
    transport_fee DECIMAL(10,2) DEFAULT 0,
    activity_fee DECIMAL(10,2) DEFAULT 0,
    other_fees DECIMAL(10,2) DEFAULT 0,
    total_annual_fee DECIMAL(10,2),
    
    -- Status
    status ENUM('ACTIVE', 'RECALCULATED', 'EXPIRED', 'REVOKED'),
    effective_from DATE,
    effective_until DATE,
    
    -- Audit
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    last_recalculated DATETIME,
    recalculation_reason VARCHAR(200)
);
```

---

## 📋 BUSINESS RULES

### Rule 1: Child Ordering
```
FUNCTION determine_child_order(siblings):
  // Order by enrollment date, not by age
  SORT siblings BY enrollment_date ASC
  FOR i = 1 TO siblings.LENGTH:
    siblings[i].sibling_order = i
    siblings[i].discount_percentage = GET_DISCOUNT_TIER(i)
  END FOR
  
  // Discount tiers
  FUNCTION GET_DISCOUNT_TIER(order):
    SWITCH order:
      CASE 1: RETURN 0       // No discount
      CASE 2: RETURN 10      // 10%
      CASE 3: RETURN 15      // 15%
      DEFAULT: RETURN 20     // 20% for 4th+
    END SWITCH
  END FUNCTION
END FUNCTION
```

### Rule 2: Mid-Year Enrollment
```
FUNCTION calculate_mid_year_discount(student, enrollment_month):
  remaining_months = 12 - enrollment_month + 4  // Apr=4 to Mar=3
  IF remaining_months <= 0: remaining_months += 12
  
  annual_discount = base_tuition * discount_percentage / 100
  prorated_discount = annual_discount * remaining_months / 12
  
  RETURN {
    annual_discount: annual_discount,
    prorated_discount: prorated_discount,
    effective_from: enrollment_month,
    months_applicable: remaining_months
  }
END FUNCTION
```

### Rule 3: Withdrawal Impact
```
FUNCTION handle_sibling_withdrawal(withdrawn_student_id):
  family = GET_FAMILY(withdrawn_student_id)
  remaining = GET_ACTIVE_SIBLINGS(family.family_id)
  
  IF remaining.COUNT == 1:
    // Only one child left → remove all discounts
    REMOVE_DISCOUNT(remaining[0])
    NOTIFY parent: "Sibling discount removed. Only 1 child enrolled."
  ELSE:
    // Re-order remaining siblings
    RECALCULATE_ORDER(remaining)
    FOR EACH child IN remaining:
      old_discount = child.discount_percentage
      new_discount = GET_DISCOUNT_TIER(child.new_order)
      IF old_discount != new_discount:
        UPDATE_DISCOUNT(child, new_discount)
        NOTIFY parent: "Discount updated for {child.name}: {old_discount}% → {new_discount}%"
      END IF
    END FOR
  END IF
  
  // Effective from next billing cycle
  UPDATE fee_management SET effective_date = NEXT_QUARTER_START()
END FUNCTION
```

---

## 🔄 INTEGRATION POINTS

| Connected Module | Data Exchanged | Direction |
|---|---|---|
| Student Management (01) | Family tree, sibling mapping, enrollment status | Inbound |
| Fee Management (22) | Discount amounts, adjusted fee structure, billing updates | Outbound |
| Admissions & CRM (15) | Prospective sibling info, "what-if" calculations | Outbound |
| Communication (30) | Discount notifications, renewal alerts | Outbound |
| Accounts & Finance (23) | Revenue impact reporting, discount reconciliation | Outbound |

---

## 👤 USER WORKFLOWS

### Workflow 1: New Sibling Enrollment

```
Context: Mrs. Kavita Patel enrolls her 3rd child (Arjun, Grade 1).
Existing: Riya (Grade 8), Sahil (Grade 5)

Step 1: Admissions module confirms Arjun's enrollment
Step 2: System detects family_id match → identifies 2 existing siblings
Step 3: Sibling Discount Engine activates:
        - Riya (Child 1): 0% discount (unchanged)
        - Sahil (Child 2): 10% discount (unchanged)
        - Arjun (Child 3): 15% discount (NEW)
Step 4: System calculates: Arjun's tuition ₹80,000 × 15% = ₹12,000 discount
Step 5: Fee Management updated: Arjun's tuition = ₹68,000
Step 6: Parent notified: "Sibling discount of ₹12,000 (15%) applied for Arjun."
Step 7: Consolidated family fee statement generated
```

### Workflow 2: Sibling Graduates (Withdrawal Impact)

```
Context: Riya Patel (Child 1) completes Grade 12 in March 2025.
Remaining: Sahil (Child 2, now Grade 6), Arjun (Child 3, now Grade 2)

Step 1: System detects Riya's status changed to "ALUMNI" (April 2025)
Step 2: Sibling count reduced: 3 → 2
Step 3: Recalculation triggered:
        - Sahil: Re-ordered as Child 1 → discount 10% → 0% (₹11,000 discount removed)
        - Arjun: Re-ordered as Child 2 → discount 15% → 10% (from ₹12,750 to ₹8,500)
Step 4: Parent notified 30 days in advance:
        "With Riya's graduation, sibling discounts will be adjusted from April 2025.
        Sahil: ₹11,000 discount removed. Arjun: discount reduced to 10% (₹8,500)."
Step 5: Fee Management updated for April 2025 billing cycle
```

---

## ⚠️ EDGE CASES

### Edge Case 1: Twins Enrolled Same Day
- **Scenario:** Twins Aditya and Aditi Gupta both enrolled in Grade 1 on the same day.
- **Resolution:** When enrollment dates are identical, system uses alphabetical order of first name. Aditya = Child 1 (0%), Aditi = Child 2 (10%). Parent can request manual override to designate which child gets the discount.

### Edge Case 2: Step-Siblings from Blended Family
- **Scenario:** Mr. Verma remarries. His daughter (Priya) and step-daughter (Neha) are both enrolled.
- **Resolution:** If both children are linked to the same parent_id in the system (father_id or mother_id), sibling discount applies. If different parent records → admin must manually link families. System flag: "Potential sibling – manual verification required."

### Edge Case 3: Sibling in Different Branch (Multi-Campus)
- **Scenario:** Child 1 enrolled in City Branch, Child 2 enrolled in Highway Branch.
- **Resolution:** Cross-campus sibling discount applies only if enabled in config (`cross_campus_sibling_discount = true`). Default: same campus only. When enabled, Multi-Campus Management module verifies sibling enrollment across branches.

---

## ⚙️ CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `sibling_discount_tier_2` | 10 | Discount % for 2nd child |
| `sibling_discount_tier_3` | 15 | Discount % for 3rd child |
| `sibling_discount_tier_4_plus` | 20 | Discount % for 4th+ child |
| `discount_applicable_on` | `['TUITION_FEE']` | Fee components eligible for sibling discount |
| `sibling_verification_method` | `PARENT_ID` | Primary method: PARENT_ID, FAMILY_TREE, MANUAL |
| `cross_campus_sibling_discount` | `false` | Allow sibling discount across campuses |
| `prorate_mid_year` | `true` | Pro-rate discount for mid-year enrollments |
| `graduation_notice_days` | 30 | Days before graduation to notify of discount change |
| `max_children_discount` | 5 | Maximum children eligible for discount |
| `what_if_calculator_enabled` | `true` | Show prospective parents the savings calculator |

---

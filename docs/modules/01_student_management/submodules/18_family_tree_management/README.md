# FAMILY TREE MANAGEMENT - COMPLETE DOCUMENTATION

**Module:** Student Management  
**Submodule Code:** STD-FAMILY-018  
**Category:** Family Relations  
**Priority:** Medium (P2)  
**Owner:** Student Records Department

---

## 📋 OVERVIEW

The Family Tree Management submodule maintains comprehensive family relationships, tracks siblings across different schools, manages extended family connections, identifies legacy students, facilitates sibling discounts, and provides genealogical insights for multi-generational school families.

### Purpose

Map family relationships, track siblings, identify alumni parents, manage legacy admissions, automate sibling discounts, facilitate family communications, and maintain multi-generational school connections.

### Scope

- **Family Mapping**: Parent-child relationships
- **Sibling Tracking**: Brothers/sisters in school
- **Extended Family**: Grandparents, guardians
- **Legacy Students**: Alumni children
- **Multi-generational**: Family school history
- **Sibling Discounts**: Automated calculation

---

## 🎯 KEY FEATURES

### 1. Family Relationship Mapping

#### 1.1 Family Structure

**Family Tree:**
```yaml
Family ID: FAM-2024-1234
Family Name: Patel Family

Generation 1 (Grandparents):
  Grandfather: Mr. Suresh Patel
    Relation: Grandfather
    Alumni: Yes (Class of 1975)
    
  Grandmother: Mrs. Anjali Patel
    Relation: Grandmother

Generation 2 (Parents):
  Father: Mr. Rajesh Patel
    Relation: Father
    Alumni: Yes (Class of 2000)
    Occupation: Software Engineer
    
  Mother: Mrs. Priya Patel
    Relation: Mother
    Alumni: No
    Occupation: Teacher

Generation 3 (Current Students):
  Child 1: Diya Patel (2018/08/0123)
    Grade: 11-A
    Status: Active
    Sibling Position: 1st (Eldest)
    
  Child 2: Aarav Patel (2024/06/0456)
    Grade: 6-A
    Status: Active
    Sibling Position: 2nd
    
  Child 3: Arjun Patel (2025/01/0089)
    Grade: 1-B
    Status: Active
    Sibling Position: 3rd (Youngest)

Extended Family:
  Uncle: Mr. Suresh Patel Jr.
    Relation: Uncle (Father's brother)
    Alumni: Yes (Class of 2005)
    Children in School: 2
    
  Aunt: Mrs. Meera Sharma
    Relation: Aunt (Mother's sister)
    Alumni: No

Family Legacy:
  Generations in School: 3
  Total Alumni: 3
  Current Students: 3
  Legacy Status: Multi-generational Family
  
Family Benefits:
  Sibling Discount: Applied (2nd child: 10%, 3rd child: 15%)
  Alumni Discount: Not applicable
  Legacy Priority: Yes (for future admissions)
```

---

### 2. Sibling Management

#### 2.1 Sibling Tracking

**Sibling Group:**
```yaml
Family: Patel Family (FAM-2024-1234)
Total Siblings in School: 3

Sibling 1:
  Name: Diya Patel
  Admission Number: 2018/08/0123
  Grade: 11-A
  Age: 16 years
  DOB: 15/03/2008
  Position: Eldest
  Sibling Discount: 0%
  
Sibling 2:
  Name: Aarav Patel
  Admission Number: 2024/06/0456
  Grade: 6-A
  Age: 13 years
  DOB: 15/05/2012
  Position: Middle
  Sibling Discount: 10%
  
Sibling 3:
  Name: Arjun Patel
  Admission Number: 2025/01/0089
  Grade: 1-B
  Age: 6 years
  DOB: 20/08/2019
  Position: Youngest
  Sibling Discount: 15%

Sibling Interactions:
  - Same transport route
  - Emergency contact sharing
  - Parent meeting coordination
  - Combined fee payment option
```

---

## 📊 DATABASE SCHEMA

### Family Tree Table

```sql
CREATE TABLE family_tree (
  family_tree_id INT PRIMARY KEY AUTO_INCREMENT,
  family_id INT NOT NULL,
  
  -- Person Details
  person_id INT,
  person_type ENUM('STUDENT', 'PARENT', 'GRANDPARENT', 'GUARDIAN', 'SIBLING', 'OTHER') NOT NULL,
  
  -- Relationship
  relation_to_student VARCHAR(100),
  
  -- Alumni Status
  is_alumni BOOLEAN DEFAULT FALSE,
  alumni_id INT,
  batch_year INT,
  
  -- Generation
  generation_level INT,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (family_id) REFERENCES families(family_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_family_id (family_id),
  INDEX idx_person_type (person_type),
  INDEX idx_alumni (is_alumni)
);
```

### Sibling Groups Table

```sql
CREATE TABLE sibling_groups (
  sibling_group_id INT PRIMARY KEY AUTO_INCREMENT,
  family_id INT NOT NULL,
  
  -- Siblings
  student_id INT NOT NULL,
  sibling_position INT NOT NULL,
  
  -- Discount
  sibling_discount_percentage DECIMAL(5,2) DEFAULT 0,
  
  -- Status
  is_active BOOLEAN DEFAULT TRUE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (family_id) REFERENCES families(family_id) ON DELETE CASCADE,
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_family_id (family_id),
  INDEX idx_student_id (student_id)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Automatic Sibling Position Calculation

```javascript
FUNCTION calculate_sibling_positions(family_id) {
  // Get all active students in family
  siblings = QUERY("
    SELECT student_id, date_of_birth
    FROM students
    WHERE family_id = ?
    AND student_status = 'ACTIVE'
    ORDER BY date_of_birth ASC
  ", [family_id])
  
  // Assign positions
  FOR i = 0 TO siblings.length - 1 {
    position = i + 1
    
    // Calculate discount
    discount = 0
    IF position == 2 {
      discount = 10
    } ELSE IF position == 3 {
      discount = 15
    } ELSE IF position >= 4 {
      discount = 20
    }
    
    // Update sibling group
    UPDATE sibling_groups SET
      sibling_position = position,
      sibling_discount_percentage = discount
    WHERE student_id = siblings[i].student_id
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Fee Management**: Sibling discount application
2. **Admissions**: Legacy priority
3. **Alumni Module**: Alumni parent tracking

### Inbound Integrations

1. **Student Registration**: Family linkage
2. **Parent Portal**: Family tree view

---

## 👥 USER WORKFLOWS

### Workflow: Link Siblings

**Actor:** Admissions Officer  
**Duration:** 10 minutes

**Steps:**

1. Identify family during admission
2. Search for existing family record
3. Link new student to family
4. Update sibling positions
5. Calculate sibling discounts
6. Apply to fee structure
7. Notify parents
8. Update family tree

**Expected Outcome:** Siblings linked, discounts applied

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

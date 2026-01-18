# SECTION & CLASS ALLOCATION - COMPLETE DOCUMENTATION

**Module:** Student Management  
**Submodule Code:** STD-SECTION-004  
**Category:** Core Foundation  
**Priority:** High (P1)  
**Owner:** Academic Administration

---

## 📋 OVERVIEW

The Section & Class Allocation submodule manages the assignment of students to specific grades, sections, and classrooms. It handles section balancing, teacher allocation, classroom capacity management, section changes, and ensures optimal distribution of students based on various criteria including academic performance, gender ratio, and special needs.

### Purpose

Systematically organize students into balanced sections, assign appropriate classrooms and teachers, manage section changes throughout the academic year, maintain optimal class sizes, and ensure fair distribution based on academic and demographic factors.

### Scope

- **Initial Allocation**: Assign new students to sections
- **Section Balancing**: Distribute students evenly across sections
- **Classroom Assignment**: Allocate physical classrooms
- **Teacher Assignment**: Assign class teachers and subject teachers
- **Section Changes**: Handle mid-year section transfers
- **Capacity Management**: Monitor and enforce class size limits

---

## 🎯 KEY FEATURES

### 1. Section Allocation System

#### 1.1 Automatic Section Assignment

**Allocation Criteria:**
```yaml
Grade: 6
Total Students: 180
Sections Available: A, B, C, D, E, F (6 sections)
Target Section Size: 30 students per section

Allocation Rules:
  1. Gender Balance: 50-50 ratio (±5%)
  2. Academic Performance: Mixed ability grouping
  3. Special Needs: Distributed evenly
  4. Sibling Preference: Same section if requested
  5. Behavioral History: Separate problem students
  6. Language Preference: Consider mother tongue

Allocation Algorithm:
  Step 1: Sort students by admission number
  Step 2: Apply sibling preference
  Step 3: Distribute special needs students
  Step 4: Balance gender ratio
  Step 5: Mix academic performance levels
  Step 6: Assign remaining students sequentially
```

**Example Allocation:**
```yaml
Section 6-A:
  Total Students: 30
  Boys: 16 (53%)
  Girls: 14 (47%)
  Academic Mix:
    High Performers: 10 (33%)
    Average: 15 (50%)
    Need Support: 5 (17%)
  Special Needs: 2 students
  Class Teacher: Mrs. Anjali Gupta
  Classroom: Room 201 (Building A)

Section 6-B:
  Total Students: 30
  Boys: 15 (50%)
  Girls: 15 (50%)
  Academic Mix:
    High Performers: 9 (30%)
    Average: 16 (53%)
    Need Support: 5 (17%)
  Special Needs: 2 students
  Class Teacher: Mr. Suresh Kumar
  Classroom: Room 202 (Building A)

[Sections C, D, E, F follow similar pattern]
```

#### 1.2 Manual Section Assignment

**Override Allocation:**
```yaml
Student: Aarav Patel (2024/06/0456)
Auto-Assigned Section: 6-C

Manual Override Request:
  Requested By: Principal
  Reason: Sibling in 6-A (Diya Patel)
  New Section: 6-A
  Approval: Required
  Approved By: Academic Coordinator
  Date: 05/04/2024

Impact Analysis:
  Section 6-A:
    Current: 30 students
    After: 31 students (Over capacity by 1)
    Gender Ratio: 17 boys, 14 girls (54% boys)
    
  Section 6-C:
    Current: 30 students
    After: 29 students (Under capacity by 1)
    Gender Ratio: 14 boys, 15 girls (48% boys)

Approval Status: APPROVED
Reason: Sibling preference, minimal impact on balance
```

---

### 2. Section Balancing

#### 2.1 Gender Balance

**Gender Distribution:**
```yaml
Grade 6 - Gender Analysis:

Section A:
  Boys: 16 (53%)
  Girls: 14 (47%)
  Ratio: 1.14:1
  Status: BALANCED ✓

Section B:
  Boys: 15 (50%)
  Girls: 15 (50%)
  Ratio: 1:1
  Status: PERFECTLY BALANCED ✓

Section C:
  Boys: 18 (60%)
  Girls: 12 (40%)
  Ratio: 1.5:1
  Status: IMBALANCED ❌
  Action Required: Move 2 boys to other sections

Balancing Action:
  Move: 2 boys from Section C
  To: Section D (has 13 boys, 17 girls)
  Result: Both sections become balanced
```

#### 2.2 Academic Performance Balance

**Performance Distribution:**
```yaml
Section 6-A - Academic Mix:

High Performers (80%+): 10 students (33%)
  - Ensures peer learning
  - Leadership opportunities
  
Average (60-80%): 15 students (50%)
  - Majority of class
  - Standard curriculum pace
  
Need Support (<60%): 5 students (17%)
  - Extra attention required
  - Remedial support

Teacher Workload: BALANCED
Class Average: 72% (Target: 70-75%)
Status: OPTIMAL ✓
```

#### 2.3 Special Needs Distribution

**Inclusive Education:**
```yaml
Grade 6 - Special Needs Students: 12 total

Distribution:
  Section A: 2 students (ADHD, Dyslexia)
  Section B: 2 students (Autism, Visual impairment)
  Section C: 2 students (Hearing impairment, Physical disability)
  Section D: 2 students (Learning disability, Speech disorder)
  Section E: 2 students (Dysgraphia, Dyscalculia)
  Section F: 2 students (Emotional disorder, Behavioral issues)

Support Allocation:
  - Each section has 1 shadow teacher
  - Special education coordinator assigned
  - IEP plans in place
  - Classroom modifications done

Status: EVENLY DISTRIBUTED ✓
```

---

### 3. Classroom Assignment

#### 3.1 Physical Classroom Allocation

**Classroom Details:**
```yaml
Section 6-A:
  Classroom: Room 201
  Building: A (Main Building)
  Floor: 2nd Floor
  Capacity: 35 students
  Current Strength: 30 students
  Utilization: 86%
  
Facilities:
  - Smart Board: Yes
  - Projector: Yes
  - AC: Yes
  - Fans: 4
  - Lights: 8 LED panels
  - Benches: 15 (dual seating)
  - Teacher Desk: 1
  - Whiteboard: 1 (6x4 ft)
  - Notice Board: 1
  - First Aid Kit: Yes
  - Fire Extinguisher: Yes

Accessibility:
  - Wheelchair Accessible: Yes
  - Ramp Available: Yes
  - Elevator Access: Yes
  - Accessible Washroom: Nearby

Special Features:
  - Natural Lighting: Excellent
  - Ventilation: Good
  - Noise Level: Low
  - Distance from Canteen: 50m
  - Distance from Library: 30m
```

#### 3.2 Lab & Specialized Room Allocation

**Subject-wise Room Assignment:**
```yaml
Grade 6 - Specialized Rooms:

Science Lab:
  Sections: All (A-F)
  Schedule: 2 periods/week per section
  Capacity: 30 students
  Equipment: Microscopes, specimens, chemicals
  Lab Assistant: Mr. Ramesh

Computer Lab:
  Sections: All (A-F)
  Schedule: 2 periods/week per section
  Capacity: 30 workstations
  Software: Windows 11, Office, Coding tools
  Lab Incharge: Ms. Priya

Art Room:
  Sections: All (A-F)
  Schedule: 1 period/week per section
  Capacity: 35 students
  Supplies: Paints, brushes, canvas
  Art Teacher: Mrs. Meera

Music Room:
  Sections: All (A-F)
  Schedule: 1 period/week per section
  Capacity: 30 students
  Instruments: Keyboards, guitars, drums
  Music Teacher: Mr. Vikram
```

---

### 4. Teacher Assignment

#### 4.1 Class Teacher Allocation

**Class Teacher Details:**
```yaml
Section 6-A:
  Class Teacher: Mrs. Anjali Gupta
  Qualification: M.A. (English), B.Ed
  Experience: 12 years
  Subject Expertise: English, Social Studies
  
Responsibilities:
  - Daily attendance marking
  - Student welfare monitoring
  - Parent communication
  - Discipline management
  - Academic progress tracking
  - Report card preparation
  - Class assemblies
  - Field trip supervision

Contact:
  Mobile: +91-9876543210
  Email: anjali.gupta@hogwarts.edu
  Cabin: Room 205

Workload:
  Class Teacher: 6-A (30 students)
  Subject Teaching: English (6-A, 6-B, 6-C)
  Total Periods: 24/week
  Status: OPTIMAL
```

#### 4.2 Subject Teacher Allocation

**Subject-wise Teachers:**
```yaml
Grade 6-A Subject Teachers:

English:
  Teacher: Mrs. Anjali Gupta (Class Teacher)
  Periods/Week: 6
  
Mathematics:
  Teacher: Mr. Suresh Kumar
  Periods/Week: 6
  
Science:
  Teacher: Dr. Ramesh Mehta
  Periods/Week: 5
  
Social Studies:
  Teacher: Mrs. Priya Singh
  Periods/Week: 5
  
Hindi:
  Teacher: Mrs. Meera Sharma
  Periods/Week: 4
  
Computer Science:
  Teacher: Mr. Vikram Patel
  Periods/Week: 2
  
Physical Education:
  Teacher: Mr. Rajesh Kumar
  Periods/Week: 2
  
Art:
  Teacher: Mrs. Kavita Reddy
  Periods/Week: 1
  
Music:
  Teacher: Mr. Amit Joshi
  Periods/Week: 1

Total Periods: 32/week
```

---

### 5. Section Change Management

#### 5.1 Mid-Year Section Transfer

**Section Change Request:**
```yaml
Student: Rohan Sharma (2024/06/0457)
Current Section: 6-C
Requested Section: 6-A

Request Details:
  Requested By: Parent (Mr. Sharma)
  Request Date: 15/07/2024
  Reason: Difficulty adjusting with classmates
  Supporting Documents: Counselor recommendation
  
Current Section Analysis:
  Section 6-C:
    Strength: 30 students
    After Transfer: 29 students
    Impact: Minimal
    
Target Section Analysis:
  Section 6-A:
    Strength: 30 students
    After Transfer: 31 students
    Capacity: 35 students
    Impact: Acceptable

Academic Impact:
  Current Performance: 75% (Good)
  Section 6-A Average: 72%
  Expected Impact: Positive (student above average)

Approval Workflow:
  Step 1: Class Teacher (6-C) - APPROVED
  Step 2: Class Teacher (6-A) - APPROVED
  Step 3: Academic Coordinator - APPROVED
  Step 4: Principal - APPROVED

Transfer Date: 20/07/2024
Status: APPROVED
Effective From: 22/07/2024 (Monday)

Post-Transfer Actions:
  - Update attendance register
  - Inform all subject teachers
  - Update timetable access
  - Notify transport (if applicable)
  - Update parent portal
  - Inform canteen (meal preferences)
```

#### 5.2 Disciplinary Section Transfer

**Forced Section Change:**
```yaml
Student: Arjun Verma (2024/06/0458)
Current Section: 6-B
New Section: 6-E

Reason: Disciplinary Action
Issue: Repeated behavioral problems, peer conflicts
Recommended By: School Counselor + Discipline Committee

Transfer Type: DISCIPLINARY
Approval: Principal
Date: 10/08/2024

Conditions:
  - Behavioral counseling mandatory
  - Weekly progress reports
  - Parent meeting scheduled
  - Probation period: 3 months
  - Review: 10/11/2024

Monitoring:
  - Class teacher daily reports
  - Counselor weekly sessions
  - Parent communication (weekly)
```

---

## 📊 DATABASE SCHEMA

### Section Allocation Table

```sql
CREATE TABLE section_allocations (
  allocation_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Academic Details
  academic_year VARCHAR(9) NOT NULL,
  grade VARCHAR(10) NOT NULL,
  section VARCHAR(10) NOT NULL,
  roll_number INT,
  
  -- Classroom
  classroom_id INT,
  classroom_number VARCHAR(20),
  building VARCHAR(50),
  floor VARCHAR(20),
  
  -- Teacher Assignment
  class_teacher_id INT,
  
  -- Allocation Details
  allocation_type ENUM('AUTO', 'MANUAL', 'TRANSFER') DEFAULT 'AUTO',
  allocation_date DATE NOT NULL,
  allocated_by INT,
  
  -- Status
  status ENUM('ACTIVE', 'TRANSFERRED', 'PROMOTED', 'INACTIVE') DEFAULT 'ACTIVE',
  effective_from DATE NOT NULL,
  effective_to DATE,
  
  -- Transfer Details (if applicable)
  previous_section VARCHAR(10),
  transfer_reason TEXT,
  transfer_approved_by INT,
  transfer_date DATE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  created_by INT,
  modified_by INT,
  notes TEXT,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  FOREIGN KEY (classroom_id) REFERENCES classrooms(classroom_id),
  FOREIGN KEY (class_teacher_id) REFERENCES teachers(teacher_id),
  
  -- Indexes
  INDEX idx_student_year (student_id, academic_year),
  INDEX idx_grade_section (grade, section, academic_year),
  INDEX idx_status (status),
  UNIQUE INDEX idx_student_active (student_id, academic_year, status)
);
```

### Classroom Table

```sql
CREATE TABLE classrooms (
  classroom_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Classroom Details
  classroom_number VARCHAR(20) NOT NULL,
  classroom_name VARCHAR(100),
  building VARCHAR(50),
  floor VARCHAR(20),
  
  -- Capacity
  seating_capacity INT NOT NULL,
  current_occupancy INT DEFAULT 0,
  
  -- Facilities
  has_smartboard BOOLEAN DEFAULT FALSE,
  has_projector BOOLEAN DEFAULT FALSE,
  has_ac BOOLEAN DEFAULT FALSE,
  has_computer BOOLEAN DEFAULT FALSE,
  number_of_fans INT,
  number_of_lights INT,
  number_of_benches INT,
  
  -- Accessibility
  wheelchair_accessible BOOLEAN DEFAULT FALSE,
  has_ramp BOOLEAN DEFAULT FALSE,
  elevator_access BOOLEAN DEFAULT FALSE,
  
  -- Type
  room_type ENUM('REGULAR', 'LAB', 'LIBRARY', 'AUDITORIUM', 'SPORTS', 'OTHER') DEFAULT 'REGULAR',
  
  -- Status
  status ENUM('ACTIVE', 'UNDER_MAINTENANCE', 'INACTIVE') DEFAULT 'ACTIVE',
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Indexes
  INDEX idx_building_floor (building, floor),
  INDEX idx_capacity (seating_capacity),
  INDEX idx_status (status)
);
```

### Section Change History Table

```sql
CREATE TABLE section_change_history (
  change_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Change Details
  academic_year VARCHAR(9) NOT NULL,
  from_grade VARCHAR(10),
  from_section VARCHAR(10),
  to_grade VARCHAR(10),
  to_section VARCHAR(10),
  
  -- Request
  change_type ENUM('SECTION_TRANSFER', 'GRADE_PROMOTION', 'DISCIPLINARY', 'PARENT_REQUEST') NOT NULL,
  change_reason TEXT,
  requested_by INT,
  request_date DATE,
  
  -- Approval
  approved_by INT,
  approval_date DATE,
  approval_status ENUM('PENDING', 'APPROVED', 'REJECTED') DEFAULT 'PENDING',
  rejection_reason TEXT,
  
  -- Effective Date
  effective_date DATE,
  
  -- Impact
  impact_analysis TEXT,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_approval_status (approval_status),
  INDEX idx_effective_date (effective_date)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Automatic Section Allocation

```javascript
FUNCTION allocate_students_to_sections(grade, academic_year) {
  // Get all students for this grade
  students = QUERY("
    SELECT s.*, hp.gender, 
           COALESCE(prev.average_percentage, 0) as prev_performance
    FROM students s
    LEFT JOIN student_health_profiles hp ON s.student_id = hp.student_id
    LEFT JOIN academic_records prev ON s.student_id = prev.student_id
    WHERE s.current_grade = ?
    AND s.academic_year = ?
    AND s.student_status = 'ACTIVE'
    ORDER BY s.admission_number
  ", [grade, academic_year])
  
  // Get available sections
  sections = GET_SECTIONS_FOR_GRADE(grade)
  target_size = CEIL(students.length / sections.length)
  
  // Initialize section arrays
  section_assignments = {}
  FOR each section IN sections {
    section_assignments[section] = {
      students: [],
      boys: 0,
      girls: 0,
      high_performers: 0,
      average: 0,
      need_support: 0,
      special_needs: 0
    }
  }
  
  // Step 1: Handle sibling preferences
  FOR each student IN students {
    siblings = GET_SIBLINGS_IN_SCHOOL(student.family_id, grade)
    IF siblings.length > 0 AND student.sibling_same_section_preference {
      sibling_section = siblings[0].section
      IF section_assignments[sibling_section].students.length < target_size + 2 {
        ASSIGN_TO_SECTION(student, sibling_section, section_assignments)
        REMOVE_FROM_POOL(student, students)
      }
    }
  }
  
  // Step 2: Distribute special needs students evenly
  special_needs_students = FILTER(students, s => s.has_special_needs)
  special_needs_per_section = CEIL(special_needs_students.length / sections.length)
  
  FOR each section IN sections {
    count = 0
    FOR each student IN special_needs_students {
      IF count < special_needs_per_section {
        ASSIGN_TO_SECTION(student, section, section_assignments)
        REMOVE_FROM_POOL(student, students)
        count++
      }
    }
  }
  
  // Step 3: Balance gender ratio
  boys = FILTER(students, s => s.gender == 'Male')
  girls = FILTER(students, s => s.gender == 'Female')
  
  boys_per_section = CEIL(boys.length / sections.length)
  girls_per_section = CEIL(girls.length / sections.length)
  
  FOR each section IN sections {
    // Assign boys
    FOR i = 0 TO boys_per_section {
      IF boys.length > 0 {
        student = boys.shift()
        ASSIGN_TO_SECTION(student, section, section_assignments)
      }
    }
    
    // Assign girls
    FOR i = 0 TO girls_per_section {
      IF girls.length > 0 {
        student = girls.shift()
        ASSIGN_TO_SECTION(student, section, section_assignments)
      }
    }
  }
  
  // Step 4: Mix academic performance
  // Categorize remaining students
  high_performers = FILTER(students, s => s.prev_performance >= 80)
  average_students = FILTER(students, s => s.prev_performance >= 60 AND s.prev_performance < 80)
  need_support = FILTER(students, s => s.prev_performance < 60)
  
  // Distribute evenly
  FOR each section IN sections {
    WHILE section_assignments[section].students.length < target_size {
      // Add one from each category
      IF high_performers.length > 0 {
        ASSIGN_TO_SECTION(high_performers.shift(), section, section_assignments)
      }
      IF average_students.length > 0 {
        ASSIGN_TO_SECTION(average_students.shift(), section, section_assignments)
      }
      IF need_support.length > 0 {
        ASSIGN_TO_SECTION(need_support.shift(), section, section_assignments)
      }
    }
  }
  
  // Step 5: Save allocations to database
  FOR each section IN sections {
    FOR each student IN section_assignments[section].students {
      INSERT INTO section_allocations (
        student_id,
        academic_year,
        grade,
        section,
        allocation_type,
        allocation_date,
        status,
        effective_from
      ) VALUES (
        student.student_id,
        academic_year,
        grade,
        section,
        'AUTO',
        CURRENT_DATE(),
        'ACTIVE',
        GET_ACADEMIC_YEAR_START_DATE(academic_year)
      )
    }
  }
  
  // Step 6: Assign classrooms and teachers
  ASSIGN_CLASSROOMS_TO_SECTIONS(grade, sections)
  ASSIGN_CLASS_TEACHERS(grade, sections)
  
  // Step 7: Generate report
  GENERATE_ALLOCATION_REPORT(grade, academic_year, section_assignments)
  
  RETURN section_assignments
}
```

### Rule 2: Section Change Validation

```javascript
FUNCTION validate_section_change(student_id, from_section, to_section, reason) {
  // Get current allocation
  current = QUERY("
    SELECT * FROM section_allocations
    WHERE student_id = ?
    AND status = 'ACTIVE'
  ", [student_id])
  
  IF NOT current {
    RETURN {valid: FALSE, error: "No active allocation found"}
  }
  
  // Check if sections are different
  IF current.section == to_section {
    RETURN {valid: FALSE, error: "Student already in target section"}
  }
  
  // Check target section capacity
  target_section = QUERY("
    SELECT COUNT(*) as strength, c.seating_capacity
    FROM section_allocations sa
    JOIN classrooms c ON sa.classroom_id = c.classroom_id
    WHERE sa.grade = ?
    AND sa.section = ?
    AND sa.status = 'ACTIVE'
    GROUP BY c.seating_capacity
  ", [current.grade, to_section])
  
  IF target_section.strength >= target_section.seating_capacity {
    RETURN {
      valid: FALSE,
      error: "Target section at full capacity",
      current_strength: target_section.strength,
      capacity: target_section.seating_capacity
    }
  }
  
  // Check if student has pending section change request
  pending = QUERY("
    SELECT * FROM section_change_history
    WHERE student_id = ?
    AND approval_status = 'PENDING'
  ", [student_id])
  
  IF pending {
    RETURN {valid: FALSE, error: "Pending section change request exists"}
  }
  
  // Validate reason
  IF NOT reason OR reason.length < 10 {
    RETURN {valid: FALSE, error: "Valid reason required (min 10 characters)"}
  }
  
  // All validations passed
  RETURN {
    valid: TRUE,
    current_section: from_section,
    target_section: to_section,
    target_strength: target_section.strength,
    target_capacity: target_section.seating_capacity,
    available_space: target_section.seating_capacity - target_section.strength
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Timetable Module**: Section-wise timetable generation
2. **Attendance Module**: Section-wise attendance tracking
3. **Fee Management**: Section-based fee structure
4. **Transport Module**: Section-wise bus route planning
5. **Communication Module**: Section-wise announcements

### Inbound Integrations

1. **Student Registration**: New student section assignment
2. **Academic Module**: Performance-based section balancing
3. **Special Education**: Special needs accommodation

---

## 👥 USER WORKFLOWS

### Workflow: Automatic Section Allocation for New Academic Year

**Actor:** Academic Coordinator  
**Duration:** 30-45 minutes  
**Trigger:** Start of new academic year

**Steps:**

1. Login to Admin Panel
2. Navigate to: Academic → Section Allocation
3. Select Grade: 6
4. Select Academic Year: 2024-25
5. Click "Auto-Allocate Sections"
6. System fetches 180 students
7. System creates 6 sections (A-F)
8. System applies allocation algorithm
9. Review allocation summary
10. Approve allocation
11. System saves allocations
12. System assigns classrooms
13. System assigns class teachers
14. Generate allocation report
15. Notify parents via email/SMS

**Expected Outcome:** All 180 students allocated to balanced sections

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 17, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

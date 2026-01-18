# ELECTIVE & SUBJECT ENROLLMENT - COMPLETE DOCUMENTATION

**Module:** Student Management  
**Submodule Code:** STD-ELECTIVE-005  
**Category:** Core Foundation  
**Priority:** High (P1)  
**Owner:** Academic Administration

---

## 📋 OVERVIEW

The Elective & Subject Enrollment submodule manages student subject choices for different grades, particularly for higher classes where stream selection (Science, Commerce, Arts) and elective subjects become critical. It handles subject prerequisites, capacity management, teacher allocation, and ensures students meet eligibility criteria for their chosen subjects.

### Purpose

Enable students to select appropriate subjects and streams based on their interests, academic performance, and career goals. Manage subject capacity, validate prerequisites, handle stream changes, and ensure optimal distribution of students across available subjects.

### Scope

- **Stream Selection**: Science, Commerce, Arts (Grades 11-12)
- **Elective Subjects**: Optional subjects (Grades 6-10)
- **Language Choices**: Second/third language selection
- **Skill Subjects**: Vocational/skill-based subjects
- **Subject Changes**: Mid-year subject change requests
- **Capacity Management**: Subject-wise student limits

---

## 🎯 KEY FEATURES

### 1. Stream Selection (Grades 11-12)

#### 1.1 Science Stream

**Eligibility Criteria:**
```yaml
Stream: Science
Applicable: Grades 11-12
Minimum Percentage: 75% in Grade 10 (CBSE/ICSE)

Mandatory Subjects:
  - English (Core)
  - Physics (Core)
  - Chemistry (Core)
  - Mathematics/Biology (Core)
  
Elective Options (Choose 1):
  - Computer Science
  - Physical Education
  - Informatics Practices
  - Economics
  - Psychology
  
Additional Language:
  - Hindi/Sanskrit/French/German (Optional)

Subject Combinations:
  PCM (Physics, Chemistry, Mathematics):
    - For Engineering aspirants
    - Eligibility: 80%+ in Maths in Grade 10
    
  PCB (Physics, Chemistry, Biology):
    - For Medical aspirants
    - Eligibility: 80%+ in Science in Grade 10
    
  PCMB (All four):
    - For keeping options open
    - Eligibility: 85%+ overall in Grade 10
    - High workload - counseling required
```

**Example Enrollment:**
```yaml
Student: Aarav Patel (2024/06/0456)
Grade 10 Performance: 92% (Maths: 95%, Science: 94%)
Stream Selected: Science (PCM)

Subjects Enrolled:
  Core Subjects:
    1. English (Mandatory)
    2. Physics (Mandatory)
    3. Chemistry (Mandatory)
    4. Mathematics (Mandatory)
    
  Elective Subject:
    5. Computer Science (Chosen)
    
  Additional Language:
    6. Hindi (Optional - Selected)

Total Subjects: 6
Eligibility: VERIFIED ✓
Enrollment Status: CONFIRMED
Enrollment Date: 15/03/2024
```

#### 1.2 Commerce Stream

**Eligibility Criteria:**
```yaml
Stream: Commerce
Applicable: Grades 11-12
Minimum Percentage: 60% in Grade 10

Mandatory Subjects:
  - English (Core)
  - Accountancy (Core)
  - Business Studies (Core)
  - Economics (Core)
  
Elective Options (Choose 1-2):
  - Mathematics
  - Informatics Practices
  - Physical Education
  - Psychology
  - Entrepreneurship
  
Additional Language:
  - Hindi/Sanskrit/French (Optional)

Subject Combinations:
  Commerce with Maths:
    - For CA/CS/B.Com aspirants
    - Eligibility: 70%+ in Maths in Grade 10
    
  Commerce without Maths:
    - For BBA/Management aspirants
    - Eligibility: 60%+ overall
```

**Example Enrollment:**
```yaml
Student: Priya Sharma (2024/06/0459)
Grade 10 Performance: 85% (Maths: 88%)
Stream Selected: Commerce (with Maths)

Subjects Enrolled:
  Core Subjects:
    1. English
    2. Accountancy
    3. Business Studies
    4. Economics
    
  Elective Subjects:
    5. Mathematics (Chosen)
    6. Informatics Practices (Chosen)
    
  Additional Language:
    7. Hindi

Total Subjects: 7
Eligibility: VERIFIED ✓
```

#### 1.3 Arts/Humanities Stream

**Eligibility Criteria:**
```yaml
Stream: Arts/Humanities
Applicable: Grades 11-12
Minimum Percentage: 50% in Grade 10

Mandatory Subjects:
  - English (Core)
  - History (Core)
  - Political Science (Core)
  
Elective Options (Choose 2-3):
  - Geography
  - Economics
  - Psychology
  - Sociology
  - Fine Arts
  - Music
  - Physical Education
  - Home Science
  - Sanskrit/Hindi Literature
  
Additional Language:
  - Hindi/Sanskrit/French (Optional)

Popular Combinations:
  Humanities + Economics:
    - For Civil Services/Law aspirants
    
  Humanities + Psychology:
    - For Psychology/Social Work aspirants
    
  Humanities + Fine Arts:
    - For Creative fields aspirants
```

---

### 2. Elective Subjects (Grades 6-10)

#### 2.1 Middle School Electives (Grades 6-8)

**Available Electives:**
```yaml
Grade 6-8 Elective Subjects:

Third Language (Choose 1):
  - Sanskrit
  - French
  - German
  - Spanish
  Periods/Week: 3
  Eligibility: All students

Skill Subject (Choose 1):
  - Computer Science
  - Art & Craft
  - Music (Vocal/Instrumental)
  - Dance
  - Yoga
  Periods/Week: 2
  Eligibility: All students

Sports (Choose 1):
  - Cricket
  - Football
  - Basketball
  - Badminton
  - Table Tennis
  - Swimming
  Periods/Week: 2
  Eligibility: Medically fit students
```

**Example Enrollment:**
```yaml
Student: Rohan Kumar (Grade 6)

Mandatory Subjects:
  1. English
  2. Hindi
  3. Mathematics
  4. Science
  5. Social Studies
  
Elective Subjects:
  6. Third Language: Sanskrit (Selected)
  7. Skill Subject: Computer Science (Selected)
  8. Sports: Cricket (Selected)

Total Subjects: 8
Status: ENROLLED
```

#### 2.2 Secondary School Electives (Grades 9-10)

**CBSE Board Pattern:**
```yaml
Grade 9-10 Subject Structure:

Mandatory Subjects (5):
  1. English (Language & Literature)
  2. Hindi A/B (Language)
  3. Mathematics (Standard/Basic)
  4. Science
  5. Social Science

Elective Subject (Choose 1):
  - Computer Applications
  - Information Technology
  - Artificial Intelligence
  - Financial Literacy
  - Sanskrit
  - Physical Education
  - Music
  - Painting
  
Total Subjects: 6
Board Exam: Grade 10 (All 6 subjects)
```

**Mathematics Options:**
```yaml
Mathematics Standard:
  - For Science/Commerce stream aspirants
  - Difficulty: High
  - Eligibility: 60%+ in Grade 8 Maths
  - Recommended for: Engineering/Medical/CA

Mathematics Basic:
  - For Arts/Humanities aspirants
  - Difficulty: Moderate
  - Eligibility: All students
  - Recommended for: General studies
```

---

### 3. Subject Change Management

#### 3.1 Stream Change Request

**Change Request Process:**
```yaml
Student: Arjun Verma (Grade 11)
Current Stream: Commerce
Requested Stream: Science (PCM)

Request Details:
  Requested Date: 15/05/2024
  Reason: Changed career goal - wants Engineering
  Current Performance: 78% (Commerce subjects)
  Grade 10 Marks: 82% (Maths: 85%, Science: 84%)

Eligibility Check:
  Science Stream Requirement: 75% ✓
  Maths Requirement (80%): 85% ✓
  Science Requirement (80%): 84% ✓
  Overall: ELIGIBLE ✓

Impact Analysis:
  Missed Classes: 2 months (May-June)
  Catch-up Required: Yes
  Bridge Course: Mandatory
  Additional Coaching: Recommended

Approval Workflow:
  Step 1: Academic Counselor - APPROVED
  Step 2: Subject Teachers (Science) - APPROVED
  Step 3: Principal - APPROVED
  Step 4: Parent Consent - OBTAINED

Conditions:
  - Attend bridge course (June-July)
  - Extra classes for missed topics
  - Monthly progress review
  - Probation period: 3 months

Approval Status: APPROVED
Effective From: 01/07/2024
```

#### 3.2 Subject Change (Within Stream)

**Elective Subject Change:**
```yaml
Student: Priya Sharma (Grade 11 Commerce)
Current Elective: Informatics Practices
Requested Elective: Physical Education

Request Details:
  Date: 20/06/2024
  Reason: Difficulty coping with IP syllabus
  Current Marks: 55% in IP (struggling)

Validation:
  Change Window: Within first 2 months ✓
  PE Capacity: 35/40 (space available) ✓
  IP Minimum Strength: Will be 28 (acceptable) ✓

Approval: APPROVED
Effective: 25/06/2024

Action Items:
  - Return IP textbooks
  - Collect PE textbooks
  - Attend PE orientation
  - Update timetable
```

---

### 4. Subject Capacity Management

#### 4.1 Subject-wise Capacity

**Capacity Limits:**
```yaml
Grade 11 Science Stream:

Physics Lab:
  Capacity: 40 students per batch
  Current Enrollment: 120 students
  Batches: 3 (A, B, C)
  Lab Sessions: 2 periods/week per batch

Chemistry Lab:
  Capacity: 40 students per batch
  Current Enrollment: 120 students
  Batches: 3
  Lab Sessions: 2 periods/week per batch

Biology Lab:
  Capacity: 30 students per batch
  Current Enrollment: 45 students (PCB)
  Batches: 2
  Lab Sessions: 2 periods/week per batch

Computer Science Lab:
  Capacity: 30 workstations
  Current Enrollment: 35 students
  Batches: 2 (18 + 17)
  Lab Sessions: 3 periods/week per batch

Status: All within capacity ✓
```

#### 4.2 Teacher Allocation

**Subject-wise Teachers:**
```yaml
Grade 11 Science - Physics:
  Teacher 1: Dr. Ramesh Kumar (PCM Section A)
  Teacher 2: Mrs. Priya Singh (PCM Section B)
  Teacher 3: Mr. Suresh Mehta (PCM Section C)
  
  Total Students: 120
  Periods/Week: 5 per section
  Lab Sessions: 2 per section
  Total Workload: Optimal

Grade 11 Science - Computer Science:
  Teacher: Mr. Vikram Patel
  Students: 35
  Theory Periods: 3/week
  Lab Periods: 3/week
  Workload: Moderate
```

---

## 📊 DATABASE SCHEMA

### Subject Enrollment Table

```sql
CREATE TABLE subject_enrollments (
  enrollment_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Academic Details
  academic_year VARCHAR(9) NOT NULL,
  grade VARCHAR(10) NOT NULL,
  stream VARCHAR(50),
  
  -- Subject Details
  subject_id INT NOT NULL,
  subject_name VARCHAR(100) NOT NULL,
  subject_type ENUM('CORE', 'ELECTIVE', 'LANGUAGE', 'SKILL', 'SPORTS') NOT NULL,
  subject_code VARCHAR(20),
  
  -- Enrollment
  enrollment_date DATE NOT NULL,
  enrollment_status ENUM('PENDING', 'CONFIRMED', 'WAITLISTED', 'CANCELLED') DEFAULT 'PENDING',
  
  -- Teacher & Batch
  teacher_id INT,
  batch VARCHAR(10),
  
  -- Eligibility
  eligibility_verified BOOLEAN DEFAULT FALSE,
  prerequisite_met BOOLEAN DEFAULT TRUE,
  minimum_percentage_met BOOLEAN DEFAULT TRUE,
  
  -- Performance
  mid_term_marks DECIMAL(5,2),
  final_marks DECIMAL(5,2),
  grade VARCHAR(5),
  
  -- Status
  status ENUM('ACTIVE', 'DROPPED', 'COMPLETED', 'FAILED') DEFAULT 'ACTIVE',
  
  -- Change History
  changed_from_subject_id INT,
  change_reason TEXT,
  change_date DATE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  created_by INT,
  modified_by INT,
  notes TEXT,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  FOREIGN KEY (subject_id) REFERENCES subjects(subject_id),
  FOREIGN KEY (teacher_id) REFERENCES teachers(teacher_id),
  
  -- Indexes
  INDEX idx_student_year (student_id, academic_year),
  INDEX idx_subject (subject_id),
  INDEX idx_status (enrollment_status, status),
  UNIQUE INDEX idx_student_subject (student_id, subject_id, academic_year)
);
```

### Subjects Table

```sql
CREATE TABLE subjects (
  subject_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Subject Details
  subject_name VARCHAR(100) NOT NULL,
  subject_code VARCHAR(20) UNIQUE NOT NULL,
  subject_type ENUM('CORE', 'ELECTIVE', 'LANGUAGE', 'SKILL', 'SPORTS') NOT NULL,
  
  -- Applicable Grades
  applicable_grades VARCHAR(100),
  applicable_streams VARCHAR(100),
  
  -- Capacity
  max_students_per_batch INT DEFAULT 40,
  total_capacity INT,
  current_enrollment INT DEFAULT 0,
  
  -- Prerequisites
  prerequisite_subjects TEXT,
  minimum_percentage_required DECIMAL(5,2),
  
  -- Academic
  theory_periods_per_week INT,
  practical_periods_per_week INT,
  total_marks INT DEFAULT 100,
  passing_marks INT DEFAULT 33,
  
  -- Resources
  requires_lab BOOLEAN DEFAULT FALSE,
  lab_id INT,
  textbook_required BOOLEAN DEFAULT TRUE,
  
  -- Status
  is_active BOOLEAN DEFAULT TRUE,
  is_board_subject BOOLEAN DEFAULT FALSE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Indexes
  INDEX idx_subject_code (subject_code),
  INDEX idx_subject_type (subject_type),
  INDEX idx_is_active (is_active)
);
```

### Stream Selection Table

```sql
CREATE TABLE stream_selections (
  selection_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Stream Details
  academic_year VARCHAR(9) NOT NULL,
  grade VARCHAR(10) NOT NULL,
  stream VARCHAR(50) NOT NULL,
  stream_combination VARCHAR(100),
  
  -- Selection
  selection_date DATE NOT NULL,
  selection_status ENUM('PENDING', 'APPROVED', 'REJECTED') DEFAULT 'PENDING',
  
  -- Eligibility
  grade10_percentage DECIMAL(5,2),
  eligibility_verified BOOLEAN DEFAULT FALSE,
  counseling_done BOOLEAN DEFAULT FALSE,
  parent_consent BOOLEAN DEFAULT FALSE,
  
  -- Approval
  approved_by INT,
  approval_date DATE,
  rejection_reason TEXT,
  
  -- Change History
  previous_stream VARCHAR(50),
  change_reason TEXT,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_year (student_id, academic_year),
  INDEX idx_stream (stream),
  INDEX idx_status (selection_status)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Stream Eligibility Validation

```javascript
FUNCTION validate_stream_eligibility(student_id, stream, grade10_marks) {
  // Define stream requirements
  stream_requirements = {
    'SCIENCE_PCM': {
      min_overall: 75,
      min_maths: 80,
      min_science: 80
    },
    'SCIENCE_PCB': {
      min_overall: 75,
      min_maths: 70,
      min_science: 80
    },
    'SCIENCE_PCMB': {
      min_overall: 85,
      min_maths: 85,
      min_science: 85
    },
    'COMMERCE_WITH_MATHS': {
      min_overall: 60,
      min_maths: 70
    },
    'COMMERCE_WITHOUT_MATHS': {
      min_overall: 60
    },
    'ARTS': {
      min_overall: 50
    }
  }
  
  requirements = stream_requirements[stream]
  IF NOT requirements {
    RETURN {valid: FALSE, error: "Invalid stream"}
  }
  
  // Check overall percentage
  IF grade10_marks.overall < requirements.min_overall {
    RETURN {
      valid: FALSE,
      error: `Minimum ${requirements.min_overall}% required. You scored ${grade10_marks.overall}%`,
      recommendation: GET_ALTERNATIVE_STREAMS(grade10_marks.overall)
    }
  }
  
  // Check subject-specific requirements
  IF requirements.min_maths AND grade10_marks.maths < requirements.min_maths {
    RETURN {
      valid: FALSE,
      error: `Minimum ${requirements.min_maths}% in Maths required. You scored ${grade10_marks.maths}%`
    }
  }
  
  IF requirements.min_science AND grade10_marks.science < requirements.min_science {
    RETURN {
      valid: FALSE,
      error: `Minimum ${requirements.min_science}% in Science required. You scored ${grade10_marks.science}%`
    }
  }
  
  // All checks passed
  RETURN {
    valid: TRUE,
    message: "Eligible for selected stream",
    grade10_percentage: grade10_marks.overall
  }
}
```

### Rule 2: Subject Capacity Check

```javascript
FUNCTION check_subject_capacity(subject_id, academic_year) {
  // Get subject details
  subject = QUERY("
    SELECT * FROM subjects WHERE subject_id = ?
  ", [subject_id])
  
  // Get current enrollment
  current_enrollment = QUERY("
    SELECT COUNT(*) as count
    FROM subject_enrollments
    WHERE subject_id = ?
    AND academic_year = ?
    AND enrollment_status IN ('CONFIRMED', 'PENDING')
    AND status = 'ACTIVE'
  ", [subject_id, academic_year])
  
  available_seats = subject.total_capacity - current_enrollment.count
  
  IF available_seats <= 0 {
    RETURN {
      available: FALSE,
      status: "FULL",
      capacity: subject.total_capacity,
      enrolled: current_enrollment.count,
      waitlist_option: TRUE
    }
  } ELSE IF available_seats <= 5 {
    RETURN {
      available: TRUE,
      status: "ALMOST_FULL",
      capacity: subject.total_capacity,
      enrolled: current_enrollment.count,
      available_seats: available_seats,
      warning: "Limited seats available"
    }
  } ELSE {
    RETURN {
      available: TRUE,
      status: "OPEN",
      capacity: subject.total_capacity,
      enrolled: current_enrollment.count,
      available_seats: available_seats
    }
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Timetable Module**: Subject-wise schedule generation
2. **Fee Management**: Subject-based fee calculation
3. **Academic Module**: Subject-wise performance tracking
4. **Teacher Management**: Teacher workload allocation

### Inbound Integrations

1. **Student Registration**: Initial subject assignment
2. **Academic Records**: Grade 10 marks for stream eligibility
3. **Parent Portal**: Subject selection by parents/students

---

## 👥 USER WORKFLOWS

### Workflow: Stream Selection for Grade 11

**Actor:** Student + Parent  
**Duration:** 30-45 minutes  
**Trigger:** Grade 10 results declared

**Steps:**

1. Student logs into Student Portal
2. Navigate to: Academic → Stream Selection
3. View Grade 10 marks: 85%
4. System shows eligible streams:
   - Science (PCM/PCB) ✓
   - Commerce (with/without Maths) ✓
   - Arts ✓
5. Student selects: Science (PCM)
6. System shows subject list
7. Student selects elective: Computer Science
8. System checks capacity: Available ✓
9. Parent provides consent (OTP verification)
10. Submit selection
11. Academic counselor reviews
12. Counselor approves
13. Enrollment confirmed
14. Fee structure generated
15. Confirmation email sent

**Expected Outcome:** Student enrolled in Science stream with chosen subjects

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 17, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

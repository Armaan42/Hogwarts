# SUBJECT MANAGEMENT - COMPLETE DOCUMENTATION

**Module:** Academic Curriculum  
**Submodule Code:** CURR-SUBJECT-002  
**Category:** Core Academic  
**Priority:** Critical (P0)  
**Owner:** Academic Coordinator

---

## 📋 OVERVIEW

The Subject Management submodule manages all subjects offered by the school including core subjects, electives, language options, subject groupings for different grades and streams, subject codes, prerequisites, teacher assignments, and ensures comprehensive subject organization across all boards.

### Purpose

Define and organize subjects, manage subject codes and categories, handle subject prerequisites, maintain subject-teacher mapping, manage subject groupings, track subject offerings, ensure board compliance, and facilitate subject selection for students.

### Scope

- **Subject Categories**: Core, elective, skill-based, stream-specific
- **Subject Codes**: Standardized coding system
- **Subject Groupings**: IB groups, stream subjects
- **Prerequisites**: Subject eligibility criteria
- **Teacher Assignment**: Subject-teacher mapping
- **Subject Offerings**: Grade-wise, board-wise subjects
- **Subject Selection**: Student enrollment management

---

## 🎯 KEY FEATURES

### 1. Comprehensive Subject Categories

#### 1.1 Core Subjects (Mandatory)

**Primary Level (Grades 1-5):**
```yaml
Grade 1-2 (Foundational Stage):
  Languages:
    - English: 6 periods/week
    - Hindi/Regional Language: 6 periods/week
    
  Mathematics:
    - Numeracy Skills: 5 periods/week
    
  Environmental Studies:
    - EVS (Integrated Science & Social): 4 periods/week
    
  Arts & Physical Education:
    - Art & Craft: 2 periods/week
    - Music: 1 period/week
    - Physical Education: 3 periods/week
    
  Total: 27 periods/week

Grade 3-5 (Preparatory Stage):
  Languages:
    - English: 6 periods/week
    - Hindi/Regional Language: 5 periods/week
    
  Mathematics:
    - Mathematics: 6 periods/week
    
  Science & Social Studies:
    - EVS/Science: 4 periods/week
    - Social Studies: 3 periods/week
    
  Computer Science:
    - Basic Computer Skills: 2 periods/week
    
  Arts & Physical Education:
    - Art & Craft: 2 periods/week
    - Music: 1 period/week
    - Physical Education: 3 periods/week
    
  Total: 32 periods/week
```

**Middle School (Grades 6-8):**
```yaml
Core Subjects:
  Languages:
    - English: 6 periods/week (200 marks)
    - Hindi/Sanskrit: 5 periods/week (100 marks)
    
  Mathematics:
    - Mathematics: 6 periods/week (100 marks)
    
  Science:
    - Science (Integrated): 6 periods/week (100 marks)
    - Lab Work: 2 periods/week
    
  Social Studies:
    - History: 2 periods/week (50 marks)
    - Geography: 2 periods/week (50 marks)
    - Civics: 2 periods/week (50 marks)
    
  Computer Science:
    - Coding & Computational Thinking: 3 periods/week (50 marks)
    
  Arts & PE:
    - Art Education: 2 periods/week (Co-scholastic)
    - Physical Education: 3 periods/week (Co-scholastic)
    - Music/Dance: 1 period/week (Co-scholastic)
    
  Life Skills:
    - Life Skills Education: 1 period/week (Co-scholastic)
    
  Total: 41 periods/week
```

**Secondary Level (Grades 9-10):**
```yaml
CBSE Core Subjects:
  Group A (Languages):
    - English (Communicative/Language & Literature): 5 periods/week (100 marks)
    - Hindi A/B or Sanskrit: 5 periods/week (100 marks)
    
  Group B (Mathematics & Science):
    - Mathematics (Standard/Basic): 6 periods/week (100 marks)
    - Science: 6 periods/week (100 marks)
      - Physics: 2 periods
      - Chemistry: 2 periods
      - Biology: 2 periods
    - Lab Work: 2 periods/week
    
  Group C (Social Science):
    - Social Science: 5 periods/week (100 marks)
      - History: 1.5 periods
      - Geography: 1.5 periods
      - Political Science: 1 period
      - Economics: 1 period
    
  Group D (Additional):
    - Information Technology/AI: 3 periods/week (Optional - 100 marks)
    OR
    - Any other elective
    
  Co-scholastic:
    - Physical Education: 3 periods/week
    - Art Education: 2 periods/week
    - Work Education: 1 period/week
    
  Total: 38-40 periods/week
```

---

#### 1.2 Elective Subjects

**Language Electives:**
```yaml
Foreign Languages:
  French:
    - Levels: Beginner, Intermediate, Advanced
    - Periods: 4 per week
    - Board: CBSE (Optional), DELF Certification
    - Grades: 6-12
    
  German:
    - Levels: A1, A2, B1
    - Periods: 4 per week
    - Board: CBSE (Optional), Goethe Certification
    - Grades: 6-12
    
  Spanish:
    - Levels: Beginner, Intermediate
    - Periods: 4 per week
    - Board: CBSE (Optional), DELE Certification
    - Grades: 9-12
    
  Sanskrit (Advanced):
    - Beyond core curriculum
    - Periods: 3 per week
    - Grades: 9-12

Classical Languages:
  - Tamil
  - Telugu
  - Kannada
  - Malayalam
```

**Skill-Based Electives:**
```yaml
Technology & Innovation:
  Artificial Intelligence:
    - Introduction to AI/ML
    - Python programming
    - AI applications
    - Grades: 9-12
    - Periods: 3 per week
    
  Data Science:
    - Statistics basics
    - Data analysis
    - Visualization
    - Grades: 11-12
    - Periods: 3 per week
    
  Robotics:
    - Robot design
    - Programming
    - Competitions
    - Grades: 6-10
    - Periods: 2 per week

Business & Finance:
  Financial Literacy:
    - Personal finance
    - Investment basics
    - Banking
    - Grades: 9-12
    - Periods: 2 per week
    
  Entrepreneurship:
    - Business planning
    - Marketing
    - Startup basics
    - Grades: 11-12
    - Periods: 3 per week
    
  Commercial Applications:
    - Business software
    - Office automation
    - Grades: 9-10
    - Periods: 3 per week

Creative Arts:
  Music:
    Vocal:
      - Classical (Hindustani/Carnatic)
      - Western
      - Devotional
      - Periods: 2 per week
      
    Instrumental:
      - Keyboard/Piano
      - Guitar
      - Tabla
      - Flute
      - Periods: 2 per week
  
  Dance:
    - Bharatanatyam
    - Kathak
    - Contemporary
    - Periods: 2 per week
  
  Fine Arts:
    - Drawing & Painting
    - Sculpture
    - Digital Art
    - Periods: 2 per week
  
  Drama & Theatre:
    - Acting
    - Script writing
    - Stage production
    - Periods: 2 per week
```

---

#### 1.3 Stream-Specific Subjects (Grades 11-12)

**Science Stream:**
```yaml
Core Subjects (Mandatory):
  Physics:
    - Theory: 4 periods/week (70 marks)
    - Practical: 2 periods/week (30 marks)
    - Total: 100 marks
    
  Chemistry:
    - Theory: 4 periods/week (70 marks)
    - Practical: 2 periods/week (30 marks)
    - Total: 100 marks
    
  English:
    - Core/Elective: 5 periods/week
    - Total: 100 marks

Elective Group 1 (Choose 1):
  Mathematics:
    - Theory: 6 periods/week
    - Total: 100 marks
    
  Biology:
    - Theory: 4 periods/week (70 marks)
    - Practical: 2 periods/week (30 marks)
    - Total: 100 marks

Elective Group 2 (Choose 1):
  Computer Science:
    - Theory: 4 periods/week (70 marks)
    - Practical: 2 periods/week (30 marks)
    - Total: 100 marks
    
  Physical Education:
    - Theory: 3 periods/week (70 marks)
    - Practical: 2 periods/week (30 marks)
    - Total: 100 marks
    
  Psychology:
    - Theory: 5 periods/week
    - Total: 100 marks

Popular Combinations:
  Medical (PCB):
    - Physics, Chemistry, Biology, English, PE/Psychology
    
  Engineering (PCM):
    - Physics, Chemistry, Mathematics, English, Computer Science
    
  Research (PCMB):
    - Physics, Chemistry, Mathematics, Biology, English
```

**Commerce Stream:**
```yaml
Core Subjects (Mandatory):
  Accountancy:
    - Theory: 5 periods/week
    - Practical: 1 period/week
    - Total: 100 marks
    
  Business Studies:
    - Theory: 5 periods/week
    - Case Studies: 1 period/week
    - Total: 100 marks
    
  Economics:
    - Theory: 5 periods/week
    - Total: 100 marks
    
  English:
    - Core/Elective: 5 periods/week
    - Total: 100 marks

Elective Group (Choose 1):
  Mathematics:
    - Theory: 6 periods/week
    - Total: 100 marks
    
  Computer Science:
    - Theory: 4 periods/week (70 marks)
    - Practical: 2 periods/week (30 marks)
    - Total: 100 marks
    
  Informatics Practices:
    - Theory: 4 periods/week (70 marks)
    - Practical: 2 periods/week (30 marks)
    - Total: 100 marks
    
  Physical Education:
    - Theory: 3 periods/week (70 marks)
    - Practical: 2 periods/week (30 marks)
    - Total: 100 marks

Popular Combinations:
  CA/CS Track:
    - Accountancy, Business Studies, Economics, English, Mathematics
    
  BBA Track:
    - Accountancy, Business Studies, Economics, English, Computer Science
```

---

## 📊 DATABASE SCHEMA

### Subjects Table

```sql
CREATE TABLE subjects (
  subject_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Subject Details
  subject_name VARCHAR(200) NOT NULL,
  subject_code VARCHAR(50) UNIQUE NOT NULL,
  subject_short_name VARCHAR(50),
  
  -- Category
  subject_category ENUM(
    'CORE', 'ELECTIVE', 'SKILL_BASED',
    'STREAM_SPECIFIC', 'CO_SCHOLASTIC'
  ) NOT NULL,
  
  -- Board & Grade
  board ENUM('CBSE', 'ICSE', 'IB_PYP', 'IB_MYP', 'IB_DP', 'CAMBRIDGE', 'STATE') NOT NULL,
  grade_range_start INT NOT NULL,
  grade_range_end INT NOT NULL,
  
  -- Stream (for Grades 11-12)
  stream ENUM('SCIENCE', 'COMMERCE', 'ARTS', 'NA') DEFAULT 'NA',
  
  -- Subject Group (for IB)
  ib_subject_group INT,
  
  -- Academic Details
  periods_per_week INT NOT NULL,
  theory_periods INT,
  practical_periods INT,
  
  -- Marks
  total_marks INT,
  theory_marks INT,
  practical_marks INT,
  internal_assessment_marks INT,
  
  -- Credits (for IB/Cambridge)
  credits DECIMAL(4,2),
  
  -- Mandatory Status
  is_mandatory BOOLEAN DEFAULT FALSE,
  
  -- Prerequisites
  prerequisites JSON,
  minimum_grade_required DECIMAL(5,2),
  
  -- Teacher Assignment
  subject_coordinator_id INT,
  
  -- Resources
  textbook_required BOOLEAN DEFAULT TRUE,
  lab_required BOOLEAN DEFAULT FALSE,
  
  -- Status
  subject_status ENUM('ACTIVE', 'INACTIVE', 'ARCHIVED') DEFAULT 'ACTIVE',
  
  -- Metadata
  created_by INT,
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (subject_coordinator_id) REFERENCES teachers(teacher_id),
  
  -- Indexes
  INDEX idx_subject_code (subject_code),
  INDEX idx_board_grade (board, grade_range_start, grade_range_end),
  INDEX idx_category (subject_category),
  INDEX idx_stream (stream),
  INDEX idx_status (subject_status)
);
```

### Subject Teacher Assignment Table

```sql
CREATE TABLE subject_teacher_assignments (
  assignment_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Subject & Teacher
  subject_id INT NOT NULL,
  teacher_id INT NOT NULL,
  
  -- Class Details
  class_id INT NOT NULL,
  section VARCHAR(10),
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Assignment Type
  assignment_type ENUM('PRIMARY', 'SECONDARY', 'SUBSTITUTE') DEFAULT 'PRIMARY',
  
  -- Periods
  periods_assigned INT,
  
  -- Status
  assignment_status ENUM('ACTIVE', 'COMPLETED', 'CANCELLED') DEFAULT 'ACTIVE',
  
  -- Dates
  start_date DATE NOT NULL,
  end_date DATE,
  
  -- Metadata
  assigned_by INT,
  assigned_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (subject_id) REFERENCES subjects(subject_id),
  FOREIGN KEY (teacher_id) REFERENCES teachers(teacher_id) ON DELETE CASCADE,
  FOREIGN KEY (class_id) REFERENCES classes(class_id),
  
  -- Indexes
  INDEX idx_subject_teacher (subject_id, teacher_id),
  INDEX idx_class_year (class_id, academic_year),
  INDEX idx_status (assignment_status),
  
  -- Unique Constraint
  UNIQUE KEY unique_assignment (subject_id, teacher_id, class_id, section, academic_year)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Subject Assignment Validation

```javascript
FUNCTION assign_subject_to_student(student_id, subject_id) {
  student = GET_STUDENT(student_id)
  subject = GET_SUBJECT(subject_id)
  
  // Check grade eligibility
  IF student.grade < subject.grade_range_start OR student.grade > subject.grade_range_end {
    RETURN {
      success: FALSE,
      error: `Subject not available for Grade ${student.grade}`
    }
  }
  
  // Check stream eligibility (for Grades 11-12)
  IF student.grade >= 11 {
    IF subject.stream != 'NA' AND subject.stream != student.stream {
      RETURN {
        success: FALSE,
        error: `Subject only available for ${subject.stream} stream`
      }
    }
  }
  
  // Check prerequisites
  IF subject.prerequisites {
    FOR each prerequisite IN subject.prerequisites {
      prerequisite_subject = GET_SUBJECT(prerequisite.subject_id)
      student_grade = GET_STUDENT_GRADE(student_id, prerequisite_subject.id)
      
      IF NOT student_grade OR student_grade < prerequisite.minimum_grade {
        RETURN {
          success: FALSE,
          error: `Prerequisite not met: ${prerequisite_subject.name} (Minimum: ${prerequisite.minimum_grade}%)`
        }
      }
    }
  }
  
  // Check subject limit
  current_subjects = GET_STUDENT_SUBJECTS(student_id)
  max_subjects = student.grade >= 11 ? 6 : 8
  
  IF current_subjects.length >= max_subjects {
    RETURN {
      success: FALSE,
      error: `Maximum ${max_subjects} subjects allowed`
    }
  }
  
  // Check mandatory subjects
  IF subject.is_mandatory {
    // Mandatory subjects auto-assigned, cannot be removed
    RETURN {
      success: FALSE,
      error: "Mandatory subjects are auto-assigned"
    }
  }
  
  // Assign subject
  ASSIGN_SUBJECT(student_id, subject_id)
  
  // Notify timetable module
  NOTIFY_TIMETABLE_MODULE(student_id, subject_id)
  
  RETURN {success: TRUE}
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Timetable Module**: Schedule periods for subjects
2. **Teacher Management**: Assign teachers to subjects
3. **Assessment Module**: Create exams for subjects
4. **Student Enrollment**: Subject selection

### Inbound Integrations

1. **Curriculum Design**: Subjects part of curriculum framework
2. **Board Compliance**: Board-specific subject requirements

---

## 👥 USER WORKFLOWS

### Workflow: Create New Subject

**Actor:** Academic Coordinator  
**Duration:** 30 minutes

**Steps:**

1. Login to Academic Portal
2. Navigate to: Curriculum → Subject Management
3. Click "Create New Subject"
4. Enter subject details
5. Define grade range
6. Set periods and marks
7. Define prerequisites (if any)
8. Submit for approval
9. Principal approves
10. Subject activated

**Expected Outcome:** New subject created and available for assignment

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

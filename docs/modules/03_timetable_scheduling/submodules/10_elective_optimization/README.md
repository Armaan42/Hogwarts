# ELECTIVE OPTIMIZATION - COMPLETE DOCUMENTATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-ELECTIVE-010  
**Category:** Advanced Scheduling  
**Priority:** High (P1)  
**Owner:** Academic Planning Team

---

## 📋 OVERVIEW

The Elective Optimization submodule handles complex scheduling of elective subjects for senior grades (11-12), manages diverse student subject combinations, creates balanced sections, optimizes timetables for multiple streams, ensures teacher availability, and maximizes student preference satisfaction while maintaining academic integrity.

### Purpose

Handle student elective choices across multiple streams (Science, Commerce, Humanities), create balanced elective sections based on demand, optimize timetables for diverse subject combinations, ensure qualified teacher availability for all electives, maximize student preference satisfaction, manage waitlists and section adjustments, coordinate lab requirements for science electives, and provide analytics on subject popularity trends.

### Scope

- **Multi-Stream Management**: Science, Commerce, Humanities streams
- **Subject Combination Handling**: 100+ possible combinations
- **Section Formation**: Balanced grouping based on choices
- **Timetable Optimization**: Common and elective period allocation
- **Teacher Assignment**: Qualified faculty for each elective
- **Preference Satisfaction**: Maximize first-choice allocations
- **Waitlist Management**: Handle oversubscribed electives
- **Lab Coordination**: Science practical scheduling
- **Board Compliance**: CBSE/ICSE/IB requirements

---

## 🎯 KEY FEATURES

### 1. Comprehensive Elective Choice Management

#### 1.1 Stream-wise Subject Structure

**Grade 11-12 Elective Framework:**
```yaml
School: Hogwarts International
Grades: 11-12
Total Students: 210 (Grade 11: 120, Grade 12: 90)
Streams: 3 (Science, Commerce, Humanities)

SCIENCE STREAM (Grade 11: 60 students, Grade 12: 45 students):
  
  Compulsory Subjects (All Science Students):
    - English (4 periods/week)
    - Physics (5 periods: 3 theory + 2 lab)
    - Chemistry (5 periods: 3 theory + 2 lab)
    - Mathematics (6 periods/week)
    - Physical Education (2 periods/week)
    Total: 22 periods
  
  Elective Group (Choose 1):
    Option A: Biology (5 periods: 3 theory + 2 lab)
    Option B: Computer Science (5 periods: 3 theory + 2 lab)
    Option C: Electronics (5 periods: 3 theory + 2 lab)
    Option D: Physical Education (as main subject, 5 periods)
  
  Additional Elective (Optional):
    - Psychology (4 periods)
    - Informatics Practices (4 periods)
    - Engineering Graphics (4 periods)
  
  Total Periods: 27-31 periods/week

  Grade 11 Science Student Distribution:
    Biology: 35 students (58%)
    Computer Science: 20 students (33%)
    Electronics: 3 students (5%)
    Physical Education: 2 students (4%)
  
  Section Formation Challenge:
    - Biology: 2 sections (18 + 17 students)
    - Computer Science: 1 section (20 students)
    - Electronics + PE: Combined section (5 students) ⚠️ Too small
    
    Solution: Merge Electronics/PE with Computer Science for common periods

COMMERCE STREAM (Grade 11: 40 students, Grade 12: 30 students):
  
  Compulsory Subjects:
    - English (4 periods/week)
    - Accountancy (6 periods/week)
    - Business Studies (6 periods/week)
    - Economics (6 periods/week)
    - Physical Education (2 periods/week)
    Total: 24 periods
  
  Elective Group (Choose 1):
    Option A: Mathematics (6 periods)
    Option B: Informatics Practices (5 periods)
    Option C: Physical Education (as main, 5 periods)
  
  Additional Elective (Optional):
    - Entrepreneurship (4 periods)
    - Legal Studies (4 periods)
  
  Total Periods: 29-33 periods/week
  
  Grade 11 Commerce Distribution:
    Mathematics: 28 students (70%)
    Informatics Practices: 10 students (25%)
    Physical Education: 2 students (5%)
  
  Section Formation:
    - Mathematics: 1 section (28 students)
    - Informatics Practices: 1 section (10 students)
    - PE: Merged with Informatics (2 students)

HUMANITIES STREAM (Grade 11: 20 students, Grade 12: 15 students):
  
  Compulsory Subjects:
    - English (4 periods/week)
    - History (5 periods/week)
    - Political Science (5 periods/week)
    - Geography (5 periods/week)
    - Physical Education (2 periods/week)
    Total: 21 periods
  
  Elective Group (Choose 1):
    Option A: Economics (6 periods)
    Option B: Psychology (5 periods)
    Option C: Sociology (5 periods)
    Option D: Fine Arts (5 periods)
  
  Additional Elective (Optional):
    - Legal Studies (4 periods)
    - Home Science (4 periods)
  
  Total Periods: 26-30 periods/week
  
  Grade 11 Humanities Distribution:
    Economics: 12 students (60%)
    Psychology: 5 students (25%)
    Sociology: 2 students (10%)
    Fine Arts: 1 student (5%)
  
  Section Formation:
    - All combined: 1 section (20 students)
    - Different electives in parallel periods
```

#### 1.2 Subject Combination Complexity

**Possible Combinations:**
```yaml
Total Possible Combinations: 150+

Science Stream Combinations (30 combinations):
  1. Physics + Chemistry + Mathematics + Biology
  2. Physics + Chemistry + Mathematics + Computer Science
  3. Physics + Chemistry + Mathematics + Electronics
  4. Physics + Chemistry + Mathematics + PE
  5. Physics + Chemistry + Mathematics + Biology + Psychology
  [... 25 more combinations]

Commerce Stream Combinations (20 combinations):
  1. Accountancy + Business + Economics + Mathematics
  2. Accountancy + Business + Economics + Informatics
  3. Accountancy + Business + Economics + PE
  4. Accountancy + Business + Economics + Math + Entrepreneurship
  [... 16 more combinations]

Humanities Combinations (25 combinations):
  1. History + Political Science + Geography + Economics
  2. History + Political Science + Geography + Psychology
  3. History + Political Science + Geography + Sociology
  [... 22 more combinations]

Cross-Stream Combinations (75 combinations):
  - Science with Commerce subjects (e.g., Physics + Chemistry + Math + Economics)
  - Commerce with Humanities subjects (e.g., Accountancy + Business + Psychology)
  - Humanities with Science subjects (e.g., History + Political Science + Biology)

Timetable Challenge:
  - Create common periods for compulsory subjects
  - Allocate parallel periods for electives
  - Ensure no student has 2 electives at same time
  - Optimize teacher utilization
  - Manage lab scheduling for science electives
```

---

### 2. Advanced Section Formation Algorithm

#### 2.1 Balanced Grouping Strategy

**Section Creation Logic:**
```javascript
FUNCTION create_elective_sections(students, electives, constraints) {
  sections = []
  
  // Step 1: Group students by primary elective choice
  elective_groups = GROUP_BY_ELECTIVE(students)
  
  // Step 2: Create sections based on size
  FOR each elective IN electives {
    student_count = elective_groups[elective].count
    
    IF student_count == 0 {
      // No demand - don't create section
      LOG("No students for " + elective.name)
      CONTINUE
    }
    
    IF student_count < constraints.min_section_size {
      // Too small - merge with similar elective
      similar_elective = FIND_SIMILAR_ELECTIVE(elective, electives)
      
      IF similar_elective EXISTS {
        // Merge sections
        merged_section = CREATE_MERGED_SECTION(
          elective,
          similar_elective,
          elective_groups[elective],
          elective_groups[similar_elective]
        )
        sections.ADD(merged_section)
      } ELSE {
        // Cannot merge - create small section or cancel
        IF student_count >= constraints.absolute_min {
          sections.ADD(CREATE_SECTION(elective, elective_groups[elective]))
        } ELSE {
          // Cancel elective, reallocate students
          REALLOCATE_STUDENTS(elective_groups[elective], electives)
        }
      }
    }
    
    ELSE IF student_count <= constraints.max_section_size {
      // Perfect size - create single section
      sections.ADD(CREATE_SECTION(elective, elective_groups[elective]))
    }
    
    ELSE {
      // Too large - split into multiple sections
      num_sections = CEIL(student_count / constraints.ideal_section_size)
      
      // Balance sections
      students_per_section = FLOOR(student_count / num_sections)
      remainder = student_count % num_sections
      
      FOR i IN 1 TO num_sections {
        section_size = students_per_section
        IF i <= remainder {
          section_size += 1 // Distribute remainder
        }
        
        section_students = GET_NEXT_N_STUDENTS(
          elective_groups[elective],
          section_size
        )
        
        sections.ADD(CREATE_SECTION(
          elective,
          section_students,
          section_number: i
        ))
      }
    }
  }
  
  // Step 3: Validate sections
  validation = VALIDATE_SECTIONS(sections, constraints)
  
  IF NOT validation.is_valid {
    // Adjust sections
    sections = ADJUST_SECTIONS(sections, validation.issues)
  }
  
  RETURN sections
}

Constraints:
  min_section_size: 15 students
  ideal_section_size: 25 students
  max_section_size: 35 students
  absolute_min: 8 students (special cases)

Example Output:

Grade 11 Science - Biology Elective:
  Demand: 35 students
  Sections Created: 2
    - Section 11-Sci-Bio-A: 18 students
    - Section 11-Sci-Bio-B: 17 students
  
  Timetable Allocation:
    Common Periods (Physics, Chemistry, Math, English): Same for all
    Biology Periods: Parallel slots
      - Section A: Monday P1-P2 (Lab), Wednesday P3 (Theory)
      - Section B: Tuesday P1-P2 (Lab), Thursday P3 (Theory)

Grade 11 Science - Electronics Elective:
  Demand: 3 students
  Issue: Below minimum (15)
  
  Solution Options:
    1. Cancel elective, move students to Computer Science
    2. Create combined section with PE students (3 + 2 = 5, still too small)
    3. Offer as independent study with reduced periods
  
  Decision: Option 1 - Reallocate to Computer Science
  Result: Computer Science section grows to 23 students
```

---

### 3. Timetable Optimization for Electives

#### 3.1 Common Period Allocation

**Synchronized Scheduling:**
```yaml
Grade 11 Science Timetable Structure:

Common Periods (All Science Students Together):
  These subjects scheduled at same time for all sections
  
  Monday:
    P1-P2: Physics (All sections in different rooms)
      - 11-Sci-Bio-A: Room 301 (Dr. Sharma)
      - 11-Sci-Bio-B: Room 302 (Dr. Kumar)
      - 11-Sci-CS: Room 303 (Dr. Patel)
    
    P3: English (All sections)
      - 11-Sci-Bio-A: Room 304 (Ms. Gupta)
      - 11-Sci-Bio-B: Room 305 (Ms. Verma)
      - 11-Sci-CS: Room 306 (Ms. Sharma)
    
    P4: Mathematics (All sections)
      - 11-Sci-Bio-A: Room 307 (Mr. Singh)
      - 11-Sci-Bio-B: Room 308 (Mr. Saxena)
      - 11-Sci-CS: Room 309 (Mrs. Mehta)

Elective Periods (Different subjects in parallel):
  Students attend their chosen elective
  
  Tuesday:
    P1-P2: ELECTIVE SLOT 1 (Lab Period)
      - Biology Lab: Bio Lab 1 (11-Sci-Bio-A, 18 students)
      - Computer Science Lab: Comp Lab 1 (11-Sci-CS, 23 students)
      - [11-Sci-Bio-B has Chemistry Lab this slot]
    
    P3: ELECTIVE SLOT 2 (Theory)
      - Biology Theory: Room 310 (11-Sci-Bio-B, 17 students)
      - Computer Science Theory: Room 311 (11-Sci-CS, 23 students)
      - [11-Sci-Bio-A has Physics Theory this slot]

Optimization Goals:
  1. Minimize idle periods for students
  2. Maximize teacher utilization
  3. Ensure lab availability for practicals
  4. Prevent student conflicts
  5. Balance daily workload
```

---

## 📊 DATABASE SCHEMA

### Elective Allocation Table

```sql
CREATE TABLE elective_allocation (
  allocation_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Student Details
  student_id INT NOT NULL,
  grade VARCHAR(10) NOT NULL,
  stream ENUM('SCIENCE', 'COMMERCE', 'HUMANITIES', 'MIXED') NOT NULL,
  
  -- Elective Choices
  elective_subject_id INT NOT NULL,
  preference_rank INT NOT NULL, -- 1, 2, 3
  
  -- Allocation Status
  allocation_status ENUM(
    'PENDING',
    'ALLOCATED',
    'WAITLIST',
    'REJECTED',
    'REALLOCATED'
  ) DEFAULT 'PENDING',
  
  allocated_section VARCHAR(20),
  allocated_choice INT, -- Which preference was allocated
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Waitlist
  waitlist_position INT,
  waitlist_date DATE,
  
  -- Reallocation
  original_choice_id INT,
  reallocation_reason TEXT,
  
  -- Timestamps
  choice_submitted_date DATE NOT NULL,
  allocation_date DATE,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id),
  FOREIGN KEY (elective_subject_id) REFERENCES subjects(subject_id),
  
  -- Indexes
  INDEX idx_student (student_id),
  INDEX idx_elective (elective_subject_id),
  INDEX idx_status (allocation_status),
  INDEX idx_stream (stream),
  
  -- Unique Constraint
  UNIQUE KEY uk_student_elective_year (student_id, elective_subject_id, academic_year)
);
```

### Elective Sections Table

```sql
CREATE TABLE elective_sections (
  section_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Section Details
  section_code VARCHAR(50) NOT NULL UNIQUE,
  section_name VARCHAR(100) NOT NULL,
  
  -- Subject & Grade
  elective_subject_id INT NOT NULL,
  grade VARCHAR(10) NOT NULL,
  stream VARCHAR(50),
  
  -- Capacity
  min_capacity INT DEFAULT 15,
  max_capacity INT DEFAULT 35,
  current_enrollment INT DEFAULT 0,
  
  -- Teacher Assignment
  teacher_id INT NOT NULL,
  
  -- Timetable
  periods_per_week INT NOT NULL,
  lab_periods INT DEFAULT 0,
  theory_periods INT DEFAULT 0,
  
  -- Room Allocation
  primary_room_id INT,
  lab_room_id INT,
  
  -- Status
  section_status ENUM('PLANNED', 'ACTIVE', 'CANCELLED', 'MERGED') DEFAULT 'PLANNED',
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (elective_subject_id) REFERENCES subjects(subject_id),
  FOREIGN KEY (teacher_id) REFERENCES teachers(teacher_id),
  FOREIGN KEY (primary_room_id) REFERENCES rooms(room_id),
  
  -- Indexes
  INDEX idx_subject (elective_subject_id),
  INDEX idx_grade (grade),
  INDEX idx_status (section_status)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Elective Allocation Priority

```javascript
FUNCTION allocate_electives(students, sections) {
  allocations = []
  
  // Priority order for allocation
  priority_students = SORT_STUDENTS_BY_PRIORITY(students, [
    'academic_performance', // Top performers first
    'choice_submission_time', // Early submissions
    'grade_level', // Grade 12 before Grade 11
    'special_needs' // Students with accommodations
  ])
  
  FOR each student IN priority_students {
    choices = GET_STUDENT_CHOICES(student)
    allocated = FALSE
    
    // Try first choice
    IF SECTION_HAS_CAPACITY(sections[choices.first]) {
      ALLOCATE(student, sections[choices.first])
      allocated = TRUE
    }
    
    // Try second choice if first full
    ELSE IF SECTION_HAS_CAPACITY(sections[choices.second]) {
      ALLOCATE(student, sections[choices.second])
      allocated = TRUE
    }
    
    // Try third choice
    ELSE IF SECTION_HAS_CAPACITY(sections[choices.third]) {
      ALLOCATE(student, sections[choices.third])
      allocated = TRUE
    }
    
    // Add to waitlist
    ELSE {
      ADD_TO_WAITLIST(student, choices.first)
    }
  }
  
  RETURN allocations
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations
1. **Master Timetable**: Elective period allocation
2. **Teacher Allocation**: Elective teacher assignments
3. **Room Allocation**: Lab and classroom booking
4. **Student Management**: Subject combination tracking

### Inbound Integrations
1. **Curriculum Module**: Elective subject lists
2. **Student Management**: Student preferences
3. **Teacher Management**: Teacher qualifications
4. **Board Requirements**: CBSE/ICSE compliance

---

## 👥 USER WORKFLOWS

### Workflow: Student Elective Selection

**Actor:** Grade 11 Student  
**Duration:** 20-30 minutes  
**Frequency:** Once (at admission)

**Steps:**
1. Login to Student Portal
2. Navigate to "Elective Selection"
3. View stream: Science
4. See compulsory subjects (5 subjects)
5. View elective options (4 choices)
6. Read subject descriptions
7. Check teacher profiles
8. Review syllabus for each elective
9. Select preferences:
   - 1st Choice: Biology
   - 2nd Choice: Computer Science
   - 3rd Choice: Electronics
10. Submit choices
11. Receive confirmation
12. Wait for allocation (2 weeks)
13. Check result: Allocated Biology (1st choice)
14. Accept allocation
15. Timetable generated with Biology periods

**Expected Outcome:** Student successfully allocated to preferred elective with optimized timetable.

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

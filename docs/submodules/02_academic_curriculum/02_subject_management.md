# SUBJECT MANAGEMENT

**Module:** Academic Curriculum  
**Submodule Code:** CURR-SUBJECT-002  
**Category:** Core Academic  
**Priority:** Critical (P0)

## Overview

Manages all subjects offered by the school including core subjects, electives, language options, and subject groupings for different grades and streams.

## Purpose

Define and organize subjects, manage subject codes, assign subject categories, handle subject prerequisites, and maintain subject-teacher mapping.

## Key Features

### 2.1 Subject Categories

**Core Subjects (Mandatory):**
```
Primary (Grades 1-5):
- English
- Hindi/Regional Language
- Mathematics
- Environmental Studies (EVS)
- General Knowledge

Middle School (Grades 6-8):
- English
- Hindi/Sanskrit
- Mathematics
- Science
- Social Studies
- Computer Science

Secondary (Grades 9-10):
- English
- Hindi/Sanskrit
- Mathematics
- Science (Physics, Chemistry, Biology)
- Social Studies (History, Geography, Civics, Economics)
```

**Elective Subjects:**
```
Languages:
- French
- German
- Spanish
- Sanskrit (advanced)

Skill-based:
- Computer Science
- Artificial Intelligence
- Financial Literacy
- Entrepreneurship

Arts:
- Music (Vocal/Instrumental)
- Dance
- Fine Arts
- Drama
```

**Stream-specific (Grades 11-12):**
```
Science Stream:
- Physics (Core)
- Chemistry (Core)
- Mathematics (Core)
- Biology/Computer Science (Elective)
- English (Core)

Commerce Stream:
- Accountancy (Core)
- Business Studies (Core)
- Economics (Core)
- Mathematics/Computer Science (Elective)
- English (Core)

Arts/Humanities:
- History (Core)
- Political Science (Core)
- Economics/Psychology (Elective)
- English (Core)
- Optional: Sociology, Geography
```

### 2.2 Subject Codes

**Coding System:**
```
Format: BOARD-GRADE-SUBJECT-CODE

Examples:
CBSE-06-ENG-001: CBSE Grade 6 English
CBSE-06-MAT-002: CBSE Grade 6 Mathematics
CBSE-09-SCI-PHY: CBSE Grade 9 Science (Physics)
IB-11-DP-MAT-HL: IB Grade 11 Mathematics (Higher Level)
```

### 2.3 Subject Groupings

**Subject Groups (IB MYP/DP):**
```
IB MYP Groups:
1. Language & Literature
2. Language Acquisition
3. Individuals & Societies
4. Sciences
5. Mathematics
6. Arts
7. Physical & Health Education
8. Design

IB DP Groups:
1. Studies in Language & Literature
2. Language Acquisition
3. Individuals & Societies
4. Sciences
5. Mathematics
6. Arts
```

### 2.4 Prerequisites

**Subject Prerequisites:**
```
Subject: Advanced Mathematics (Grade 11)
Prerequisites:
- Grade 10 Mathematics: Minimum 75%
- Algebra proficiency test: PASS
- Teacher recommendation: Required

Subject: Biology (Grade 11 - Medical Stream)
Prerequisites:
- Grade 10 Science: Minimum 70%
- Interest in medical field
- Lab safety certification
```

## Data Fields

```
subject_id (PK)
subject_name
subject_code (UNIQUE)
subject_category (CORE/ELECTIVE/SKILL_BASED/STREAM_SPECIFIC)
board (CBSE/ICSE/IB/CAMBRIDGE)
grade_range_start
grade_range_end
stream (SCIENCE/COMMERCE/ARTS/NA)
periods_per_week
credits
is_mandatory (boolean)
prerequisites (JSON)
subject_group (for IB)
status (ACTIVE/INACTIVE)
```

## Business Rules

### Subject Assignment Logic
```
FUNCTION assign_subject_to_student(student, subject):
  // Check grade eligibility
  IF student.grade NOT IN subject.grade_range:
    RETURN ERROR "Subject not available for this grade"
  END IF
  
  // Check stream eligibility (for Grades 11-12)
  IF student.grade >= 11:
    IF subject.stream != student.stream:
      RETURN ERROR "Subject not available for your stream"
    END IF
  END IF
  
  // Check prerequisites
  IF subject.prerequisites EXISTS:
    FOR each prerequisite IN subject.prerequisites:
      IF NOT student_meets_prerequisite(student, prerequisite):
        RETURN ERROR "Prerequisites not met: " + prerequisite
      END IF
    END FOR
  END IF
  
  // Check subject limit (max 8 subjects)
  current_subjects = COUNT(student.subjects)
  IF current_subjects >= 8:
    RETURN ERROR "Maximum 8 subjects allowed"
  END IF
  
  // Assign subject
  student.subjects.ADD(subject)
  NOTIFY timetable_module(student, subject)
  
  RETURN SUCCESS
END FUNCTION
```

## Integration Points

### Connects TO:
1. **Curriculum Design** - Subjects part of curriculum
2. **Timetable Module** - Schedule periods for subjects
3. **Teacher Management** - Assign teachers to subjects
4. **Assessment Module** - Create exams for subjects
5. **Student Enrollment** - Students select subjects

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

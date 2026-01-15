# GRADE-WISE SYLLABUS

**Module:** Academic Curriculum  
**Submodule Code:** CURR-SYLLABUS-003  
**Category:** Core Academic  
**Priority:** Critical (P0)

## Overview

Detailed syllabus breakdown for each grade and subject including topics, sub-topics, learning outcomes, suggested teaching hours, and assessment weightage.

## Purpose

Provide comprehensive grade-wise syllabus for all subjects, define topic sequence, allocate teaching hours, and ensure complete curriculum coverage.

## Key Features

### 3.1 Syllabus Structure

**Grade 6 - Mathematics Syllabus:**
```
Unit 1: Number Systems (25 hours)
- Whole Numbers (5 hours)
  - Place value up to crores
  - Operations on whole numbers
  - Word problems
  
- Integers (8 hours)
  - Introduction to integers
  - Representation on number line
  - Addition and subtraction
  - Multiplication and division
  
- Fractions (12 hours)
  - Types of fractions
  - Equivalent fractions
  - Operations on fractions
  - Word problems

Unit 2: Algebra (20 hours)
- Introduction to Algebra (8 hours)
- Simple Equations (12 hours)

Unit 3: Geometry (30 hours)
- Basic Geometrical Ideas (10 hours)
- Symmetry (10 hours)
- Mensuration (10 hours)

Total Teaching Hours: 180 hours (academic year)
Assessment Weightage:
- Formative: 40%
- Summative: 60%
```

### 3.2 Topic Sequencing

**Logical Progression:**
```
Grade 6 Mathematics - Sequence:
1. Number Systems (Foundation)
2. Algebra (Build on numbers)
3. Geometry (Spatial understanding)
4. Data Handling (Application)
5. Practical Geometry (Hands-on)

Rationale: Start with concrete (numbers) → Abstract (algebra) → Visual (geometry) → Applied (data)
```

### 3.3 Learning Outcomes

**Topic-wise Outcomes:**
```
Topic: Integers (Grade 6)

Learning Outcomes:
1. Define integers and identify them on number line
2. Compare and order integers
3. Add and subtract integers using number line
4. Solve real-world problems involving integers (temperature, elevation)
5. Apply integer operations in daily life contexts

Assessment Criteria:
- Can identify integers: 20%
- Can perform operations: 40%
- Can solve word problems: 40%
```

## Data Fields

```
syllabus_id (PK)
subject_id (FK)
grade
academic_year
unit_number
unit_name
topics (JSON array)
teaching_hours
learning_outcomes (JSON)
assessment_weightage
sequence_order
status
```

## Business Rules

### Syllabus Coverage Tracking
```
FUNCTION track_syllabus_coverage(teacher, subject, grade):
  total_topics = GET topics WHERE subject = subject AND grade = grade
  covered_topics = GET topics WHERE taught_by = teacher AND status = "COMPLETED"
  
  coverage_percentage = (covered_topics.COUNT / total_topics.COUNT) * 100
  
  IF coverage_percentage < 50 AND months_remaining < 3:
    SEND_ALERT(teacher, "Syllabus coverage low: " + coverage_percentage + "%")
    SEND_ALERT(principal, "Teacher " + teacher.name + " behind schedule")
  END IF
  
  RETURN coverage_percentage
END FUNCTION
```

## Integration Points

### Connects TO:
1. **Lesson Planning** - Teachers plan lessons based on syllabus
2. **Assessment Module** - Exams aligned with syllabus
3. **Timetable** - Allocate periods based on teaching hours

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

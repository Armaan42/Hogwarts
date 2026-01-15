# TEACHER ALLOCATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-TEACHER-002  
**Category:** Operations  
**Priority:** Critical (P0)

## Overview

Assigns teachers to subjects and sections based on expertise, workload limits, and preferences while ensuring optimal distribution.

## Purpose

Allocate teachers efficiently to subjects, balance workload, respect teacher preferences, and ensure quality instruction.

## Key Features

### 2.1 Teacher-Subject Mapping

**Teacher Expertise:**
```
Teacher: Mrs. Sharma
Qualification: M.Sc. Mathematics, B.Ed.
Expertise: Mathematics (Grades 6-12)
Experience: 15 years
Preferred Grades: 9-10 (Board classes)

Current Allocation:
- Grade 9A: Mathematics (6 periods/week)
- Grade 9B: Mathematics (6 periods/week)
- Grade 10A: Mathematics (6 periods/week)
Total: 18 periods/week
Workload: 60% (Max: 30 periods/week)
```

### 2.2 Workload Management

**Teacher Workload Limits:**
```
Full-time Teacher:
- Min periods/week: 20
- Max periods/week: 30
- Optimal: 24-26 periods

Part-time Teacher:
- Max periods/week: 15

Workload Distribution:
Teacher          Periods    Workload    Status
Mrs. Sharma      24         80%         Optimal
Mr. Kumar        28         93%         High
Dr. Gupta        18         60%         Low (can take more)
```

### 2.3 Teacher Preferences

**Preference System:**
```
Teacher: Mr. Kumar
Preferences:
1. Preferred Days: Monday, Wednesday, Friday (full day)
2. Avoid: Early morning periods (Period 1)
3. Preferred Grades: 11-12 (Senior classes)
4. Lab periods: Prefers afternoon slots

System tries to accommodate 70% of preferences
```

## Data Fields

```
teacher_allocation_id (PK)
teacher_id (FK)
subject_id (FK)
section_id (FK)
periods_per_week
academic_year
allocation_date
preferences_met_percentage
status
```

## Business Rules

### Workload Balancing
```
FUNCTION balance_teacher_workload():
  teachers = GET all_teachers()
  
  FOR each teacher IN teachers:
    current_load = COUNT(periods WHERE teacher_id = teacher.id)
    
    IF current_load < 20:
      // Underutilized
      available_subjects = GET subjects WHERE teacher.expertise
      SUGGEST_ALLOCATION(teacher, available_subjects)
    ELSE IF current_load > 30:
      // Overloaded
      SEND_ALERT(timetable_coordinator, "Teacher " + teacher.name + " overloaded")
    END IF
  END FOR
END FUNCTION
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

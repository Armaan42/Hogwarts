# PERIOD SUBSTITUTION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-SUBST-004  
**Category:** Operations  
**Priority:** Critical (P0)

## Overview

Handles teacher absences by finding suitable substitute teachers, managing substitution requests, and tracking substitution history.

## Purpose

Ensure continuity of classes when teachers are absent by quickly finding and assigning substitute teachers.

## Key Features

### 4.1 Substitution Request

**Teacher Absence:**
```
Date: 15/04/2024
Absent Teacher: Mrs. Sharma (Mathematics)
Reason: Medical leave
Periods affected:
- Grade 9A, Period 1 (8:00-8:40 AM)
- Grade 9B, Period 2 (8:40-9:20 AM)
- Grade 10A, Period 6 (12:20-1:00 PM)

Total: 3 periods need substitution
```

### 4.2 Substitute Teacher Selection

**Auto-selection Algorithm:**
```
FUNCTION find_substitute_teacher(absent_teacher, period):
  // Criteria for substitute selection
  eligible_teachers = GET teachers WHERE:
    1. Free during the period (no class scheduled)
    2. Qualified for the subject (same or related subject)
    3. Not already substituting (max 2 substitutions/day)
    4. Available (not on leave)
  
  // Priority ranking
  FOR each teacher IN eligible_teachers:
    score = 0
    
    // Same subject expertise: +10 points
    IF teacher.expertise = absent_teacher.subject:
      score += 10
    END IF
    
    // Related subject: +5 points
    IF teacher.expertise IN related_subjects(absent_teacher.subject):
      score += 5
    END IF
    
    // Low current workload: +3 points
    IF teacher.current_periods < 25:
      score += 3
    END IF
    
    // Previous substitution experience: +2 points
    IF teacher.has_substituted(absent_teacher.subject):
      score += 2
    END IF
    
    teacher.score = score
  END FOR
  
  // Select highest scoring teacher
  substitute = GET teacher WITH MAX(score)
  
  RETURN substitute
END FUNCTION
```

### 4.3 Substitution Types

**Types:**
```
1. Planned Substitution:
   - Teacher applies for leave in advance
   - Substitute arranged beforehand
   - Lesson plan shared

2. Emergency Substitution:
   - Teacher absent unexpectedly
   - Substitute found same day
   - General supervision

3. Partial Substitution:
   - Teacher late/leaves early
   - Only few periods covered
```

### 4.4 Substitution Tracking

**Substitution Log:**
```
Date: 15/04/2024
Absent Teacher: Mrs. Sharma
Substitute: Mr. Kumar
Period: Grade 9A, Period 1
Subject: Mathematics
Topic Covered: Quadratic Equations (continued)
Attendance: 28/30 present
Notes: Students completed worksheet, 2 students absent
Compensation: 1 period credit
```

## Data Fields

```
substitution_id (PK)
date
absent_teacher_id (FK)
substitute_teacher_id (FK)
section_id (FK)
period_number
subject_id (FK)
substitution_type (PLANNED/EMERGENCY/PARTIAL)
reason
topic_covered
attendance_count
notes
compensation_type (PAID/CREDIT/VOLUNTARY)
status (PENDING/CONFIRMED/COMPLETED)
```

## Business Rules

### Substitution Limits
```
FUNCTION check_substitution_limits(teacher, date):
  substitutions_today = COUNT(substitutions WHERE substitute_teacher = teacher AND date = date)
  
  IF substitutions_today >= 2:
    RETURN ERROR "Teacher already has 2 substitutions today (max limit)"
  END IF
  
  substitutions_this_week = COUNT(substitutions WHERE substitute_teacher = teacher AND week = CURRENT_WEEK)
  
  IF substitutions_this_week >= 5:
    RETURN WARNING "Teacher has 5 substitutions this week (high load)"
  END IF
  
  RETURN SUCCESS
END FUNCTION
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

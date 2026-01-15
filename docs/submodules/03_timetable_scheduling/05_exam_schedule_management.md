# EXAM SCHEDULE MANAGEMENT

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-EXAM-005  
**Category:** Operations  
**Priority:** Critical (P0)

## Overview

Creates and manages examination schedules including term exams, board exams, unit tests, and practical exams with proper spacing and resource allocation.

## Purpose

Schedule exams efficiently, avoid student/teacher overload, allocate exam halls, and ensure fair distribution of exam dates.

## Key Features

### 5.1 Exam Types

**Exam Categories:**
```
1. Unit Tests:
   - Duration: 40 minutes (1 period)
   - Frequency: After each unit
   - Scheduling: During regular class time

2. Term Exams:
   - Mid-term (September): 2 hours/subject
   - Final (March): 2 hours/subject
   - Frequency: Twice a year

3. Board Exams (Grades 10, 12):
   - Duration: 3 hours/subject
   - Scheduled by board (CBSE/ICSE)
   - School follows board schedule

4. Practical Exams:
   - Duration: 2-3 hours
   - Lab-based
   - Scheduled separately
```

### 5.2 Exam Schedule Creation

**Term Exam Schedule (Grade 9):**
```
Mid-term Examination - September 2024

Date        Subject         Time            Duration    Hall
10-Sep      English         9:00-11:00 AM   2 hours     Hall A
12-Sep      Hindi           9:00-11:00 AM   2 hours     Hall A
14-Sep      Mathematics     9:00-11:00 AM   2 hours     Hall A
16-Sep      Science         9:00-11:00 AM   2 hours     Hall A
18-Sep      Social Studies  9:00-11:00 AM   2 hours     Hall A

Rules:
- 1 day gap between exams (study time)
- All exams at same time (9 AM)
- Same hall for consistency
```

### 5.3 Exam Hall Allocation

**Hall Assignment:**
```
Exam: Grade 10 Mathematics
Date: 16-Sep-2024
Students: 120 (4 sections combined)

Hall Allocation:
Hall A: 50 students (Sections A, B - partial)
Hall B: 50 students (Section B - partial, Section C)
Hall C: 20 students (Section D)

Seating: Alternate seating (1 student per bench)
Invigilators: 6 (2 per hall)
```

## Data Fields

```
exam_schedule_id (PK)
exam_type (UNIT_TEST/TERM/BOARD/PRACTICAL)
subject_id (FK)
grade
date
start_time
end_time
duration_minutes
hall_id (FK)
invigilator_ids (JSON)
total_students
seating_plan
status (DRAFT/PUBLISHED/COMPLETED)
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

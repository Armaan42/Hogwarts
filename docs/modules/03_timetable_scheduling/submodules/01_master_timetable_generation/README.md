# MASTER TIMETABLE GENERATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-MASTER-001  
**Category:** Operations  
**Priority:** Critical (P0)

## Overview

Automated generation of master timetable for entire school considering constraints like teacher availability, room capacity, subject requirements, and optimal learning schedules.

## Purpose

Create conflict-free, optimized timetables for all grades and sections, ensuring efficient resource utilization and balanced daily schedules for students.

## Key Features

### 1.1 Timetable Structure

**Standard School Day:**
```
School Timings: 8:00 AM - 3:00 PM

Period Structure (Grade 6-10):
Period 1: 8:00 AM - 8:40 AM (40 min)
Period 2: 8:40 AM - 9:20 AM (40 min)
Period 3: 9:20 AM - 10:00 AM (40 min)
Short Break: 10:00 AM - 10:20 AM (20 min)
Period 4: 10:20 AM - 11:00 AM (40 min)
Period 5: 11:00 AM - 11:40 AM (40 min)
Lunch Break: 11:40 AM - 12:20 PM (40 min)
Period 6: 12:20 PM - 1:00 PM (40 min)
Period 7: 1:00 PM - 1:40 PM (40 min)
Period 8: 1:40 PM - 2:20 PM (40 min)
Period 9: 2:20 PM - 3:00 PM (40 min)

Total: 9 periods/day × 6 days/week = 54 periods/week
```

### 1.2 Subject Allocation

**Grade 6 - Weekly Period Distribution:**
```
Subject          Periods/Week
English          6
Hindi            5
Mathematics      6
Science          6
Social Studies   5
Computer Science 2
Physical Ed.     2
Art/Music        2
Library          1
Activity Period  1
────────────────────
Total:           36 periods
Remaining:       18 periods (study halls, extra classes)
```

### 1.3 Constraints & Rules

**Hard Constraints (Must be satisfied):**
```
1. No teacher clash: Teacher cannot be in 2 places at once
2. No room clash: Room cannot host 2 classes simultaneously
3. No student clash: Student cannot attend 2 subjects at once
4. Subject frequency: Each subject gets allocated periods
5. Working hours: Timetable within school hours
6. Teacher workload: Max 6 periods/day per teacher
```

**Soft Constraints (Preferred):**
```
1. No consecutive periods: Same subject not >2 consecutive periods
2. Balanced distribution: Subjects spread across week
3. Optimal timing: Heavy subjects (Math, Science) in morning
4. Teacher preference: Teachers prefer certain time slots
5. Lab availability: Science/Computer labs scheduled efficiently
6. Break distribution: Breaks evenly distributed
```

### 1.4 Automated Generation Algorithm

**Timetable Generation Logic:**
```
FUNCTION generate_master_timetable(academic_year):
  // Step 1: Collect inputs
  grades = GET all_grades()
  subjects = GET all_subjects()
  teachers = GET all_teachers()
  rooms = GET all_rooms()
  
  // Step 2: Initialize timetable grid
  timetable = INITIALIZE_GRID(days=6, periods=9, sections=total_sections)
  
  // Step 3: Apply hard constraints
  FOR each section IN sections:
    FOR each subject IN section.subjects:
      required_periods = subject.periods_per_week
      
      // Find available slots
      available_slots = FIND_SLOTS(
        teacher_available = TRUE,
        room_available = TRUE,
        student_free = TRUE
      )
      
      // Allocate periods
      allocated = 0
      FOR each slot IN available_slots:
        IF allocated < required_periods:
          timetable[section][slot.day][slot.period] = {
            subject: subject,
            teacher: subject.teacher,
            room: subject.room
          }
          allocated++
        END IF
      END FOR
      
      IF allocated < required_periods:
        RETURN ERROR "Cannot satisfy hard constraints for " + subject
      END IF
    END FOR
  END FOR
  
  // Step 4: Optimize for soft constraints
  score = EVALUATE_TIMETABLE(timetable)
  
  // Genetic algorithm for optimization
  FOR iteration IN 1 to 1000:
    new_timetable = MUTATE(timetable)
    new_score = EVALUATE_TIMETABLE(new_timetable)
    
    IF new_score > score:
      timetable = new_timetable
      score = new_score
    END IF
  END FOR
  
  // Step 5: Validate final timetable
  IF VALIDATE_TIMETABLE(timetable):
    timetable.status = "APPROVED"
    SAVE(timetable)
    NOTIFY_ALL_STAKEHOLDERS(timetable)
    RETURN timetable
  ELSE:
    RETURN ERROR "Validation failed"
  END IF
END FUNCTION
```

### 1.5 Sample Generated Timetable

**Grade 6A - Monday Schedule:**
```
Period  Time            Subject         Teacher         Room
1       8:00-8:40       Mathematics     Mrs. Sharma     6A
2       8:40-9:20       English         Mr. Kumar       6A
3       9:20-10:00      Science         Dr. Gupta       Lab-1
        10:00-10:20     SHORT BREAK
4       10:20-11:00     Hindi           Mrs. Singh      6A
5       11:00-11:40     Social Studies  Mr. Verma       6A
        11:40-12:20     LUNCH BREAK
6       12:20-1:00      Computer Sci.   Ms. Reddy       Comp Lab
7       1:00-1:40       Mathematics     Mrs. Sharma     6A
8       1:40-2:20       Physical Ed.    Mr. Patel       Sports Ground
9       2:20-3:00       Library         Librarian       Library
```

## Data Fields

```
timetable_id (PK)
academic_year
section_id (FK)
day_of_week (ENUM: MON, TUE, WED, THU, FRI, SAT)
period_number (1-9)
subject_id (FK)
teacher_id (FK)
room_id (FK)
start_time
end_time
is_lab_period (boolean)
status (DRAFT/APPROVED/ACTIVE)
created_date
approved_by
```

## Business Rules

### Conflict Detection
```
FUNCTION detect_conflicts(timetable):
  conflicts = []
  
  // Check teacher conflicts
  FOR each slot IN timetable:
    teacher_slots = GET slots WHERE teacher = slot.teacher AND day = slot.day AND period = slot.period
    IF teacher_slots.COUNT > 1:
      conflicts.ADD("Teacher " + slot.teacher + " has clash on " + slot.day + " Period " + slot.period)
    END IF
  END FOR
  
  // Check room conflicts
  FOR each slot IN timetable:
    room_slots = GET slots WHERE room = slot.room AND day = slot.day AND period = slot.period
    IF room_slots.COUNT > 1:
      conflicts.ADD("Room " + slot.room + " has clash on " + slot.day + " Period " + slot.period)
    END IF
  END FOR
  
  RETURN conflicts
END FUNCTION
```

## Integration Points

### Connects TO:
1. **Subject Management** - Get subject period requirements
2. **Teacher Management** - Get teacher availability
3. **Room Management** - Get room availability
4. **Student Management** - Get section details
5. **Substitution Module** - Handle teacher absences

### Receives FROM:
1. **Curriculum Module** - Subject period allocation
2. **HR Module** - Teacher working hours

## User Workflows

### Workflow: Generate Timetable for New Academic Year

**Actor:** Timetable Coordinator

**Steps:**
1. Navigate to Timetable > Master Timetable Generation
2. Select Academic Year: 2025-26
3. Click "Generate New Timetable"
4. System collects inputs:
   - 50 sections (Grades 1-12)
   - 120 teachers
   - 60 rooms
   - 15 subjects per section (avg)
5. System runs generation algorithm (5-10 minutes)
6. System displays generated timetable
7. Coordinator reviews for quality
8. System shows conflicts: 3 minor conflicts found
9. Coordinator manually resolves conflicts
10. Coordinator clicks "Approve Timetable"
11. System activates timetable for 2025-26
12. System sends timetable to all teachers via email
13. System publishes on parent portal

**Expected Outcome:** Conflict-free timetable generated and activated for new academic year.

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

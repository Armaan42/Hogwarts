# ROOM/LAB ALLOCATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-ROOM-003  
**Category:** Operations  
**Priority:** High (P1)

## Overview

Manages allocation of classrooms, laboratories, and special rooms to sections and subjects ensuring optimal utilization and avoiding conflicts.

## Purpose

Assign appropriate rooms to classes, manage lab schedules, track room capacity, and ensure efficient space utilization.

## Key Features

### 3.1 Room Types

**Classroom Types:**
```
Regular Classrooms:
- Capacity: 40 students
- Equipment: Whiteboard, projector, AC
- Count: 50 rooms
- Usage: Regular classes

Science Labs:
- Physics Lab (2): Capacity 30, specialized equipment
- Chemistry Lab (2): Capacity 30, fume hoods
- Biology Lab (2): Capacity 30, microscopes
- Usage: Practical sessions

Computer Labs:
- Lab 1: 40 computers
- Lab 2: 40 computers
- Lab 3: 30 computers
- Usage: Computer Science, IT classes

Special Rooms:
- Music Room (2): Instruments, soundproof
- Art Room (2): Easels, art supplies
- Dance Studio: Mirrors, sound system
- Library: 100 seating capacity
- Auditorium: 500 capacity
```

### 3.2 Room Allocation Rules

**Priority-based Allocation:**
```
Priority 1: Labs (Limited availability)
- Science practicals
- Computer classes
- Must be scheduled first

Priority 2: Special Rooms
- Music, Art, Dance
- Library periods

Priority 3: Regular Classrooms
- Theory classes
- Can be flexible
```

### 3.3 Lab Schedule Management

**Science Lab Rotation:**
```
Monday - Physics Lab 1:
Period 3: Grade 9A (30 students)
Period 4: Grade 9B (30 students)
Period 6: Grade 10A (28 students)
Period 7: Grade 10B (32 students)

Lab Utilization: 80% (4 periods out of 9 used)
```

## Data Fields

```
room_allocation_id (PK)
room_id (FK)
section_id (FK)
subject_id (FK)
day_of_week
period_number
academic_year
room_type (CLASSROOM/LAB/SPECIAL)
capacity
actual_students
utilization_percentage
status
```

## Business Rules

### Room Capacity Validation
```
FUNCTION validate_room_capacity(room, section):
  IF section.student_count > room.capacity:
    RETURN ERROR "Room capacity exceeded: " + room.name + " (Capacity: " + room.capacity + ", Students: " + section.student_count + ")"
  END IF
  
  IF section.student_count < (room.capacity * 0.5):
    RETURN WARNING "Room underutilized: Consider smaller room"
  END IF
  
  RETURN SUCCESS
END FUNCTION
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

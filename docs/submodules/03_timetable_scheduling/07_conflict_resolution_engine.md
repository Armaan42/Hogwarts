# CONFLICT RESOLUTION ENGINE

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-CONFLICT-007  
**Category:** Operations  
**Priority:** High (P1)

## Overview

Automatically detects and resolves scheduling conflicts including teacher clashes, room double-bookings, and student timetable overlaps.

## Purpose

Identify conflicts in timetable, suggest resolutions, and maintain conflict-free schedules.

## Key Features

### 7.1 Conflict Types

**Common Conflicts:**
```
1. Teacher Clash:
   - Teacher assigned to 2 classes at same time
   - Example: Mrs. Sharma teaching 9A and 9B simultaneously

2. Room Clash:
   - Room booked for 2 classes at same time
   - Example: Lab-1 assigned to both Grade 9 and Grade 10

3. Student Clash:
   - Student has 2 subjects scheduled simultaneously
   - Example: Elective subject conflicts with core subject

4. Resource Clash:
   - Shared resource (projector, sports equipment) double-booked
```

### 7.2 Conflict Detection

**Automated Detection:**
```
FUNCTION detect_all_conflicts(timetable):
  conflicts = []
  
  // Check each time slot
  FOR each day IN days:
    FOR each period IN periods:
      // Get all scheduled classes for this slot
      classes = GET classes WHERE day = day AND period = period
      
      // Check teacher conflicts
      teachers = EXTRACT teachers FROM classes
      IF HAS_DUPLICATES(teachers):
        conflicts.ADD({
          type: "TEACHER_CLASH",
          day: day,
          period: period,
          teacher: DUPLICATE_TEACHER,
          classes: AFFECTED_CLASSES
        })
      END IF
      
      // Check room conflicts
      rooms = EXTRACT rooms FROM classes
      IF HAS_DUPLICATES(rooms):
        conflicts.ADD({
          type: "ROOM_CLASH",
          day: day,
          period: period,
          room: DUPLICATE_ROOM,
          classes: AFFECTED_CLASSES
        })
      END IF
    END FOR
  END FOR
  
  RETURN conflicts
END FUNCTION
```

### 7.3 Conflict Resolution

**Auto-resolution Strategies:**
```
FUNCTION resolve_conflict(conflict):
  IF conflict.type = "TEACHER_CLASH":
    // Strategy 1: Find free period for one class
    free_slots = FIND_FREE_SLOTS(conflict.teacher, conflict.classes[0].section)
    IF free_slots.EXISTS:
      MOVE_CLASS(conflict.classes[0], free_slots[0])
      RETURN "Resolved: Moved class to " + free_slots[0]
    END IF
    
    // Strategy 2: Find substitute teacher
    substitute = FIND_SUBSTITUTE(conflict.teacher, conflict.subject)
    IF substitute.EXISTS:
      ASSIGN_SUBSTITUTE(conflict.classes[0], substitute)
      RETURN "Resolved: Assigned substitute teacher"
    END IF
    
    RETURN "Manual intervention required"
    
  ELSE IF conflict.type = "ROOM_CLASH":
    // Find alternative room
    alternative_room = FIND_FREE_ROOM(conflict.day, conflict.period, conflict.classes[0].capacity)
    IF alternative_room.EXISTS:
      ASSIGN_ROOM(conflict.classes[0], alternative_room)
      RETURN "Resolved: Assigned alternative room"
    END IF
    
    RETURN "Manual intervention required"
  END IF
END FUNCTION
```

### 7.4 Conflict Report

**Conflict Summary:**
```
Timetable Conflict Report
Date: 15/04/2024

Total Conflicts: 5

Critical (Requires immediate action): 2
1. Teacher Clash: Mrs. Sharma (9A & 9B, Monday Period 3)
2. Room Clash: Lab-1 (Grade 9 & 10, Wednesday Period 4)

Minor (Can be resolved automatically): 3
3. Room underutilization: Room 6A (20 students in 40-capacity room)
4. Teacher preference not met: Mr. Kumar prefers morning, assigned afternoon
5. Subject spacing: Mathematics has 3 consecutive periods (not optimal)

Auto-resolved: 2
Pending manual resolution: 3
```

## Data Fields

```
conflict_id (PK)
conflict_type (TEACHER/ROOM/STUDENT/RESOURCE)
severity (CRITICAL/MINOR)
day_of_week
period_number
affected_entities (JSON)
detection_date
resolution_strategy
resolved_by
resolution_date
status (DETECTED/RESOLVED/PENDING)
```

## Business Rules

### Conflict Priority
```
FUNCTION prioritize_conflicts(conflicts):
  // Sort by severity
  critical = FILTER conflicts WHERE severity = "CRITICAL"
  minor = FILTER conflicts WHERE severity = "MINOR"
  
  // Critical conflicts must be resolved before timetable approval
  IF critical.COUNT > 0:
    timetable.status = "BLOCKED"
    SEND_ALERT(timetable_coordinator, "Critical conflicts detected")
  END IF
  
  RETURN {critical, minor}
END FUNCTION
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

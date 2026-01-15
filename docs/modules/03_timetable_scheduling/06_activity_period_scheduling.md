# ACTIVITY PERIOD SCHEDULING

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-ACTIVITY-006  
**Category:** Operations  
**Priority:** Medium (P2)

## Overview

Schedules co-curricular activities, sports periods, club meetings, and special events within the regular timetable.

## Purpose

Allocate time for non-academic activities, manage activity rotation, and ensure balanced participation.

## Key Features

### 6.1 Activity Types

**Co-curricular Activities:**
```
Sports:
- Cricket, Football, Basketball
- Athletics, Swimming
- Indoor games (Chess, Table Tennis)

Arts & Culture:
- Music (Vocal, Instrumental)
- Dance (Classical, Western)
- Drama, Debate
- Fine Arts, Craft

Clubs:
- Science Club
- Coding Club
- Environment Club
- Literary Club
```

### 6.2 Activity Schedule

**Weekly Activity Periods:**
```
Grade 6 - Activity Schedule

Monday, Period 8 (1:40-2:20 PM):
- Sports: Cricket (Boys), Basketball (Girls)

Wednesday, Period 9 (2:20-3:00 PM):
- Clubs: Rotating schedule
  Week 1: Science Club
  Week 2: Coding Club
  Week 3: Environment Club
  Week 4: Literary Club

Friday, Period 7-8 (1:00-2:20 PM):
- Arts: Music/Dance/Drama (Student choice)
```

## Data Fields

```
activity_schedule_id (PK)
activity_type (SPORTS/ARTS/CLUBS)
activity_name
grade
day_of_week
period_number
instructor_id (FK)
venue
max_participants
enrolled_students
rotation_schedule (JSON)
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

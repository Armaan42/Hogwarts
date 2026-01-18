# ACTIVITY PERIOD SCHEDULING - COMPLETE DOCUMENTATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-ACTIVITY-006  
**Category:** Co-curricular Management  
**Priority:** High (P1)  
**Owner:** Activities Coordinator

---

## 📋 OVERVIEW

Manages scheduling of co-curricular activities, club periods, sports activities, cultural programs, and special events within the regular timetable framework.

### Purpose

Schedule activity periods for clubs and societies, allocate resources for activities, manage student participation, coordinate with regular academic schedule, and ensure balanced activity distribution.

---

## 🎯 KEY FEATURES

### 1. Club Period Allocation

**Activity Schedule:**
```yaml
Activity Periods: Friday P7-P8 (1:45-3:15 PM)

Clubs Available:
  - Robotics Club (Computer Lab, 30 students)
  - Drama Club (Auditorium, 40 students)
  - Music Club (Music Room, 25 students)
  - Art Club (Art Studio, 20 students)
  - Science Club (Lab, 25 students)
  - Debate Club (Conference Room, 30 students)
  - Sports Club (Ground, 50 students)

Student Selection:
  - Each student chooses 1 club
  - Allocation based on preference and capacity
  - Quarterly rotation option

Schedule:
  Friday 1:45-3:15 PM:
    - All clubs run simultaneously
    - Students attend their assigned club
    - Faculty supervisors assigned
```

### 2. Event Scheduling

**Special Events:**
```yaml
Annual Day Preparation:
  Duration: 2 weeks before event
  Schedule Changes:
    - P7-P8 daily: Rehearsals
    - Saturday: Full day practice
  
  Resource Allocation:
    - Auditorium: Drama rehearsals
    - Music Room: Music practice
    - Dance Studio: Dance practice
```

---

## 📊 DATABASE SCHEMA

```sql
CREATE TABLE activity_schedule (
  activity_id INT PRIMARY KEY AUTO_INCREMENT,
  activity_name VARCHAR(200) NOT NULL,
  activity_type ENUM('CLUB', 'SPORTS', 'CULTURAL', 'SPECIAL_EVENT') NOT NULL,
  day_of_week VARCHAR(10),
  period_number INT,
  room_id INT,
  supervisor_id INT,
  max_capacity INT,
  enrolled_students INT DEFAULT 0,
  status ENUM('ACTIVE', 'INACTIVE') DEFAULT 'ACTIVE',
  FOREIGN KEY (supervisor_id) REFERENCES teachers(teacher_id)
);
```

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 1.0

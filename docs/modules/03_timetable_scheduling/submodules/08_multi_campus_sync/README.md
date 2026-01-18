# MULTI-CAMPUS SYNC - COMPLETE DOCUMENTATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-CAMPUS-008  
**Category:** Multi-Site Operations  
**Priority:** Medium (P2)  
**Owner:** Operations Team

---

## 📋 OVERVIEW

The Multi-Campus Sync submodule manages comprehensive timetable synchronization across multiple school campuses, coordinates shared resources including teachers and facilities, handles teacher mobility between locations, ensures consistent scheduling standards, manages inter-campus transportation, and provides unified reporting across all sites.

### Purpose

Synchronize timetables across all campuses in real-time, manage shared teacher assignments and travel schedules, coordinate resource sharing (labs, equipment, facilities), handle inter-campus transportation logistics, maintain unified scheduling standards and policies, prevent conflicts in shared resources, provide consolidated analytics and reporting, and ensure seamless student transfers between campuses.

### Scope

- **Real-Time Synchronization**: Instant timetable updates across campuses
- **Shared Teacher Management**: Cross-campus teaching assignments
- **Resource Coordination**: Shared labs, equipment, facilities
- **Transportation Scheduling**: Inter-campus travel planning
- **Conflict Prevention**: Cross-campus clash detection
- **Unified Standards**: Consistent policies across sites
- **Student Mobility**: Inter-campus class attendance
- **Consolidated Reporting**: Multi-campus analytics

---

## 🎯 KEY FEATURES

### 1. Multi-Campus Infrastructure

#### 1.1 Campus Network Architecture

**School Network:**
```yaml
Organization: Hogwarts International School System
Total Campuses: 4
Total Students: 2,500
Total Teachers: 200
Shared Teachers: 35 (17.5%)

Campus A (Main Campus - Central Delhi):
  Location: Connaught Place, New Delhi
  Established: 1995
  Grades: Pre-K to 12
  Students: 1,200
  Teachers: 90 (full-time: 75, shared: 15)
  Facilities:
    - 60 classrooms
    - 8 science labs
    - 5 computer labs
    - Auditorium (500 capacity)
    - Sports complex
    - Swimming pool
    - Library (15,000 books)
  Specialization: CBSE Board, IB Diploma

Campus B (North Branch - Rohini):
  Location: Rohini, North Delhi
  Established: 2005
  Grades: Pre-K to 8
  Students: 600
  Teachers: 45 (full-time: 35, shared: 10)
  Facilities:
    - 30 classrooms
    - 4 science labs
    - 2 computer labs
    - Multipurpose hall
    - Playground
    - Library (5,000 books)
  Specialization: CBSE Board, Primary focus

Campus C (South Branch - Vasant Kunj):
  Location: Vasant Kunj, South Delhi
  Established: 2010
  Grades: 9 to 12
  Students: 400
  Teachers: 40 (full-time: 30, shared: 10)
  Facilities:
    - 25 classrooms
    - 6 science labs (advanced)
    - 3 computer labs
    - Auditorium (300 capacity)
    - Sports ground
    - Library (8,000 books)
  Specialization: CBSE Board, Science stream focus

Campus D (East Branch - Noida):
  Location: Noida, UP
  Established: 2015
  Grades: Pre-K to 10
  Students: 300
  Teachers: 25 (full-time: 20, shared: 5)
  Facilities:
    - 20 classrooms
    - 3 science labs
    - 2 computer labs
    - Activity hall
    - Playground
    - Library (4,000 books)
  Specialization: CBSE Board, Emerging campus

Inter-Campus Distances:
  Campus A ↔ Campus B: 18 km (45 min drive)
  Campus A ↔ Campus C: 15 km (40 min drive)
  Campus A ↔ Campus D: 22 km (60 min drive)
  Campus B ↔ Campus C: 25 km (70 min drive)
  Campus B ↔ Campus D: 30 km (80 min drive)
  Campus C ↔ Campus D: 28 km (75 min drive)

Shared Resources:
  - 35 teachers teaching across campuses
  - Specialized lab equipment (rotated)
  - Guest speakers and workshops
  - Sports tournaments and events
  - Centralized library system
  - Unified ERP system
```

#### 1.2 Teacher Mobility Management

**Cross-Campus Teaching Schedule:**
```yaml
Shared Teacher Example 1:

Mrs. Sharma (Senior English Teacher):
  Home Campus: Campus A (Main)
  Teaching Across: Campus A, Campus C
  Total Periods: 28/week
  
  Weekly Schedule:
    Monday (Campus A):
      P1-P2: Grade 11A English (Room 301)
      P3-P4: Grade 12A English (Room 302)
      P5-P6: Free (planning)
      P7-P8: Grade 11B English (Room 303)
      Location: Campus A all day
    
    Tuesday (Campus C):
      [Travel: 7:30-8:10 AM, Campus A → Campus C]
      P1-P2: Grade 11C English (Room 201)
      P3-P4: Grade 12B English (Room 202)
      P5-P6: Lunch + Free
      P7-P8: Grade 11D English (Room 203)
      [Travel: 3:30-4:10 PM, Campus C → Campus A]
    
    Wednesday (Campus A):
      P1-P2: Grade 12C English (Room 304)
      P3-P4: Free (grading)
      P5-P6: Grade 11A English (Room 301)
      P7-P8: Departmental meeting
      Location: Campus A all day
    
    Thursday (Campus C):
      [Travel: 7:30-8:10 AM]
      P1-P2: Grade 12B English (Room 202)
      P3-P4: Grade 11C English (Room 201)
      P5-P6: Free
      P7-P8: Grade 11D English (Room 203)
      [Travel: 3:30-4:10 PM]
    
    Friday (Campus A):
      P1-P2: Grade 11B English (Room 303)
      P3-P4: Grade 12A English (Room 302)
      P5-P6: Grade 12C English (Room 304)
      P7-P8: Activity period supervision
      Location: Campus A all day
    
    Saturday (Campus A):
      P1-P2: Grade 11A English (Room 301)
      P3-P4: Free
      P5-P6: Exam duty (if scheduled)
      Location: Campus A (half day)

  Travel Logistics:
    - School-provided car with driver
    - Dedicated parking at both campuses
    - Travel time: 40 minutes (Campus A ↔ Campus C)
    - Fuel and toll reimbursement
    - Travel allowance: ₹5,000/month
  
  Constraints:
    - No back-to-back classes across campuses
    - Minimum 2-period gap for travel
    - Maximum 2 travel days per week
    - Home campus: 3 days minimum
    - No travel on exam days

Shared Teacher Example 2:

Dr. Kumar (Physics - Grades 11-12):
  Home Campus: Campus A
  Teaching Across: Campus A, Campus C
  Specialization: Advanced Physics, IB Diploma
  
  Schedule Pattern:
    - Campus A: Monday, Wednesday, Friday (3 days)
    - Campus C: Tuesday, Thursday (2 days)
    - Total Periods: 26/week
    - Lab periods: 8/week (4 at each campus)
  
  Lab Coordination:
    Campus A: Advanced Physics Lab
      - Monday P1-P2: Grade 12A Physics Lab
      - Wednesday P3-P4: Grade 11A Physics Lab
    
    Campus C: Physics Lab
      - Tuesday P1-P2: Grade 12B Physics Lab
      - Thursday P3-P4: Grade 11C Physics Lab
  
  Equipment Sharing:
    - Oscilloscope (rotates between campuses monthly)
    - Spectrometer (Campus A, borrowed by Campus C as needed)
    - Advanced experiment kits (shared calendar)
```

---

### 2. Synchronization Technology

#### 2.1 Real-Time Timetable Sync

**Synchronization Architecture:**
```yaml
Technology Stack:
  Database: MySQL with replication
  Sync Protocol: Real-time bidirectional sync
  Conflict Resolution: Last-write-wins with manual review
  Backup: Hourly incremental, daily full backup

Sync Process:

1. Change Detection:
   Event: Teacher creates period at Campus A
   Timestamp: 2026-01-18 10:30:15
   Change: Grade 9A Math, Monday P1, Room 205, Mr. Verma
   
2. Propagation:
   - Change logged in central database
   - Sync trigger fired
   - Update pushed to all campuses (A, B, C, D)
   - Propagation time: < 2 seconds
   
3. Conflict Check:
   - Check teacher availability across all campuses
   - Check room availability at Campus A
   - Validate no student clashes
   - Result: No conflicts
   
4. Confirmation:
   - Change committed at all campuses
   - Confirmation sent to originating campus
   - Audit log updated
   - Notifications sent (if configured)

Conflict Example:

Scenario: Simultaneous booking
  Campus A Coordinator: Books Mr. Verma for Monday P1 (10:30:15)
  Campus C Coordinator: Books Mr. Verma for Monday P1 (10:30:17)
  
  Conflict Detection:
    - Both changes reach central database
    - Conflict detected (same teacher, same time)
    - Last-write-wins: Campus C change (10:30:17) wins
    - Campus A change rejected
  
  Resolution:
    - Campus A coordinator notified of conflict
    - Suggested alternatives provided:
      1. Different teacher for Monday P1
      2. Different time slot for Mr. Verma
      3. Manual override (requires approval)
    
    - Campus A coordinator selects: Different time (Monday P2)
    - New change propagated successfully
```

---

## 📊 DATABASE SCHEMA

### Campus Timetable Sync Table

```sql
CREATE TABLE campus_timetable_sync (
  sync_id BIGINT PRIMARY KEY AUTO_INCREMENT,
  
  -- Campus Details
  campus_id INT NOT NULL,
  sync_type ENUM('TEACHER', 'ROOM', 'RESOURCE', 'EVENT', 'STUDENT') NOT NULL,
  
  -- Entity Details
  teacher_id INT,
  room_id INT,
  resource_id INT,
  student_id INT,
  
  -- Timing
  day_of_week VARCHAR(10),
  period_number INT,
  start_time TIME,
  end_time TIME,
  
  -- Sync Status
  sync_status ENUM('PENDING', 'SYNCED', 'CONFLICT', 'FAILED') DEFAULT 'PENDING',
  sync_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Conflict Resolution
  conflict_detected BOOLEAN DEFAULT FALSE,
  conflict_details JSON,
  resolution_status ENUM('UNRESOLVED', 'AUTO_RESOLVED', 'MANUAL_RESOLVED'),
  resolved_by INT,
  resolution_timestamp TIMESTAMP,
  
  -- Change Tracking
  change_type ENUM('CREATE', 'UPDATE', 'DELETE') NOT NULL,
  previous_value JSON,
  new_value JSON,
  
  -- Audit
  initiated_by INT NOT NULL,
  initiated_campus_id INT NOT NULL,
  
  -- Foreign Keys
  FOREIGN KEY (campus_id) REFERENCES campuses(campus_id),
  FOREIGN KEY (teacher_id) REFERENCES teachers(teacher_id),
  
  -- Indexes
  INDEX idx_campus (campus_id),
  INDEX idx_sync_status (sync_status),
  INDEX idx_timestamp (sync_timestamp),
  INDEX idx_conflict (conflict_detected)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Teacher Travel Time Validation

```javascript
FUNCTION validate_cross_campus_assignment(teacher, campus_from, campus_to, time) {
  // Get travel time between campuses
  travel_time = GET_TRAVEL_TIME(campus_from, campus_to)
  
  // Check previous assignment
  previous_assignment = GET_PREVIOUS_ASSIGNMENT(teacher, time.day, time.period - 1)
  
  IF previous_assignment.campus != campus_to {
    // Teacher needs to travel
    required_gap_periods = CEIL(travel_time / 45) // 45 min per period
    
    // Check if enough gap
    actual_gap = time.period - previous_assignment.period - 1
    
    IF actual_gap < required_gap_periods {
      RETURN {
        valid: FALSE,
        reason: `Insufficient travel time: need ${required_gap_periods} periods, have ${actual_gap}`,
        suggestion: `Schedule at period ${previous_assignment.period + required_gap_periods + 1} or later`
      }
    }
  }
  
  RETURN {valid: TRUE}
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations
1. **Master Timetable**: Cross-campus schedule coordination
2. **Teacher Management**: Shared teacher assignments
3. **Transportation Module**: Inter-campus travel scheduling
4. **Analytics Module**: Multi-campus reporting

### Inbound Integrations
1. **Campus Management**: Campus details, facilities
2. **HR Module**: Teacher home campus, travel allowances
3. **Student Management**: Inter-campus transfers

---

## 👥 USER WORKFLOWS

### Workflow: Schedule Cross-Campus Teacher

**Actor:** Timetable Coordinator (Campus C)  
**Duration:** 15-20 minutes  
**Frequency:** As needed

**Steps:**
1. Login to Timetable Portal (Campus C)
2. Navigate to "Teacher Assignment"
3. Select: Grade 11C English
4. Search for qualified teachers
5. Filter: "Show all campuses"
6. Find: Mrs. Sharma (Campus A)
7. Check availability: Tuesday P1-P2
8. System checks:
   - Mrs. Sharma free at Campus C: ✓
   - Travel time from Campus A: 40 min
   - Previous class: Monday P7-P8 at Campus A
   - Gap: Adequate (overnight)
9. Assign Mrs. Sharma to Grade 11C
10. System calculates travel:
    - Departure: 7:30 AM from Campus A
    - Arrival: 8:10 AM at Campus C
    - Class: 8:15 AM (P1)
11. Confirm assignment
12. System syncs to all campuses
13. Mrs. Sharma notified
14. Transport arranged automatically

**Expected Outcome:** Teacher successfully scheduled across campuses with travel logistics handled.

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

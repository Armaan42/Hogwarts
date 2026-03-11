# EXAM SCHEDULE & PLANNING

**Module:** Assessment & Exams  
**Submodule Code:** EXAM-SCHED-001  
**Category:** Core Exam Pipeline  
**Priority:** Critical (P0)  
**Owners:** Exam Controller, Vice Principal (Academics)

---

## OVERVIEW

The Exam Schedule & Planning submodule orchestrates the entire logistics of conducting examinations. It transforms the academic calendar's broad "Exam Week" blocks into precise, conflict-free timetables that assign every student a subject, a hall, a seat number, and an invigilator. It is the foundational submodule upon which all downstream exam processes (paper distribution, marks entry, results) depend.

### Purpose

To eliminate scheduling conflicts (e.g., a student having two exams at the same time), optimize hall utilization, ensure fair seating arrangements (no two adjacent students from the same class), and generate all logistical documents (date sheets, seating plans, invigilation duty rosters) automatically.

### Scope

-   **Exam Type Management:** Unit Tests, Mid-Terms, Finals, Practicals, Viva Voce, Board Pre-Boards.
-   **Date Sheet Generation:** Auto-scheduling subjects across available days with gap logic.
-   **Hall & Seat Allocation:** Assigning exam venues based on capacity and subject mix.
-   **Invigilation Roster:** Auto-assigning teachers to halls based on availability and ensuring the subject teacher does NOT invigilate their own paper.
-   **Special Accommodations:** Allocating separate rooms or extra time for students with learning disabilities (CWSN - Children with Special Needs).

---

## KEY FEATURES

### 1. Intelligent Date Sheet Generator

**Feature Description:**
Auto-creates an exam timetable that respects multiple constraints.

**Constraints Engine:**
*   **No Same-Day Clash:** A student cannot have two exams on the same date.
*   **Gap Day Rule:** At least 1 day gap between "hard" subjects (Math, Science) for student preparation.
*   **Subject Length Awareness:** 3-hour theory papers are scheduled in the morning slot; 1-hour practicals in the afternoon.
*   **Board Exam Alignment:** For Grade 10/12, the internal pre-board schedule is reverse-engineered to end at least 2 weeks before the actual Board Exam start date.

### 2. Dynamic Hall & Seating Allocation

**Feature Description:**
Optimizing physical space usage during exams.

**Logic:**
*   **Cross-Section Seating:** Grade 8A and Grade 8B students are interleaved (Seat 1: 8A-Roll1, Seat 2: 8B-Roll1) to minimize copying.
*   **Capacity Matching:** Hall with 60 seats is allocated 55 students (10% buffer for furniture gaps).
*   **Accessibility:** Students with physical disabilities are auto-assigned to ground-floor halls.

### 3. Invigilation Duty Roster

**Feature Description:**
Fair and automated teacher assignment.

**Rules:**
*   A teacher MUST NOT invigilate their own subject's exam (conflict of interest / prevents answer leaking).
*   Duty distribution is balanced: each teacher gets roughly equal number of invigilation slots.
*   Senior teachers are preferentially assigned as "Chief Invigilators" (supervisory role across multiple halls).

### 4. Admit Card Generation

**Feature Description:**
Per-student exam entry documents.
*   Contains: Student Photo, Roll Number, Exam Center, Seat Number, Subject-wise Date & Time Table.
*   Includes a barcode/QR code scanned at the hall entry for attendance verification.
*   Defaulters (Fee defaulters, Attendance < 75%) can have their Admit Cards blocked until clearance.

---

## DATABASE SCHEMA

### 1. Exam Definitions (`exam_definitions`)
The master record defining an exam event.

```sql
CREATE TABLE exam_definitions (
    exam_id INT PRIMARY KEY AUTO_INCREMENT,
    academic_year_id INT NOT NULL,
    
    exam_name VARCHAR(150), -- 'Mid-Term Exam Oct 2025'
    exam_type ENUM('UNIT_TEST', 'MID_TERM', 'FINAL', 'PRE_BOARD', 'PRACTICAL', 'VIVA'),
    
    applicable_grades JSON, -- [8, 9, 10]
    start_date DATE,
    end_date DATE,
    
    status ENUM('DRAFT', 'SCHEDULED', 'IN_PROGRESS', 'COMPLETED', 'CANCELLED'),
    created_by INT
);
```

### 2. Exam Schedule Detail (`exam_schedule_slots`)
The per-subject, per-date assignment.

```sql
CREATE TABLE exam_schedule_slots (
    slot_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    exam_id INT NOT NULL,
    subject_id INT NOT NULL,
    grade_id INT NOT NULL,
    
    exam_date DATE NOT NULL,
    start_time TIME NOT NULL,
    end_time TIME NOT NULL,
    duration_minutes INT,
    
    max_marks INT,
    
    FOREIGN KEY (exam_id) REFERENCES exam_definitions(exam_id)
);
```

### 3. Seating Arrangements (`exam_seating`)
Student-to-seat mapping.

```sql
CREATE TABLE exam_seating (
    seating_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    slot_id BIGINT NOT NULL,
    student_id INT NOT NULL,
    
    hall_id INT NOT NULL, -- FK to Facilities
    seat_number VARCHAR(10), -- 'A-15'
    
    is_special_needs BOOLEAN DEFAULT FALSE,
    extra_time_minutes INT DEFAULT 0,
    
    FOREIGN KEY (slot_id) REFERENCES exam_schedule_slots(slot_id),
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 4. Invigilation Assignments (`exam_invigilation`)

```sql
CREATE TABLE exam_invigilation (
    assignment_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    slot_id BIGINT NOT NULL,
    hall_id INT NOT NULL,
    teacher_id INT NOT NULL,
    
    role ENUM('CHIEF_INVIGILATOR', 'INVIGILATOR', 'FLYING_SQUAD'),
    
    FOREIGN KEY (slot_id) REFERENCES exam_schedule_slots(slot_id)
);
```

---

## BUSINESS RULES

### Rule 1: Minimum Preparation Gap
*   The system enforces a configurable gap (default: 1 day) between consecutive exams for the same student. Administrators can override this for shorter Unit Tests.

### Rule 2: Admit Card Blocking
*   Students with Attendance < 75% or Outstanding Fee Dues are flagged. Their Admit Cards show status `BLOCKED`. The Principal must explicitly override to release.

### Rule 3: Clash Detection & Resolution
*   If a student takes an elective that causes a scheduling conflict (e.g., Music and Art both on Monday), the system flags this during draft creation and suggests: "Move Music to Wednesday" or "Create a Special Sitting for 3 affected students on Tuesday afternoon."

### Rule 4: Teacher Availability
*   If a teacher is on approved Leave during an exam slot, the system automatically skips them during invigilation assignment. Duty reassignment runs after the Leave Module syncs.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Timetable Module:** Automatically blocks regular classes during exam periods, preventing scheduling conflicts.
*   **To Parent Portal:** Publishes the Date Sheet and Admit Card PDFs.
*   **To Attendance Module:** Sends exam-day attendance roster (separate from class attendance).

### Inbound Relationships
*   **From Academic Calendar:** Reads "Exam Week" blocks to determine available date ranges.
*   **From Fee Module:** Checks `defaulter_status` before issuing Admit Cards.
*   **From Student Module:** Pulls student lists, elective choices, and CWSN flags.

---

## USER WORKFLOWS

### Workflow 1: Creating the Mid-Term Schedule
**Actor:** Exam Controller

1.  **Define Exam:** Creates "Mid-Term Oct 2025" for Grades 6-10.
2.  **Set Dates:** Available window: Oct 6-18 (10 working days).
3.  **Auto-Generate:** Clicks "Generate Draft Schedule". Algorithm places 8 subjects across 10 days with gap days.
4.  **Review:** Controller notices Hindi and Sanskrit are back-to-back. Manually swaps Sanskrit with Physical Education.
5.  **Hall Assignment:** Clicks "Auto-Assign Halls". System maps 500 students across 10 halls.
6.  **Invigilation:** Clicks "Generate Duty Roster". 60 teachers are assigned across all slots.
7.  **Publish:** Clicks "Publish". Date sheets go live on portal and notice boards.

### Workflow 2: Handling a CWSN Student
**Actor:** Special Educator

1.  **Flag:** Student "Aarav" is registered with Dyslexia (CWSN category).
2.  **Accommodation:** System automatically allocates Aarav a separate room (or a corner seat away from distractions) and adds 20 minutes extra time per hour of exam.
3.  **Scribe:** If Aarav needs a scribe (writer), the system records the scribe's name and ID for the invigilator's reference.
4.  **Admit Card:** Aarav's Admit Card includes a footnote: "Eligible for 33% extra time and use of scribe."

---

## EDGE CASES

### Edge Case 1: Student Enrolled in Two Streams
*   **Scenario:** A Grade 11 student takes both Science and Commerce electives (possible in some boards). Two exams clash.
*   **Resolution:** System creates a "Special Sitting" in a supervised room. The student writes Exam A in the morning under isolation (no phone, no leaving the room) and Exam B in the afternoon. The system generates a special "Isolation Protocol" document for the invigilator.

### Edge Case 2: Exam Postponed Due to Weather
*   **Scenario:** Heavy rains cause school closure on Oct 10. Three exams were scheduled.
*   **Resolution:** Admin marks Oct 10 as "Exam Holiday". The system's "Reschedule Wizard" shifts all remaining exams forward by 1 day (if buffer days exist) or appends them to the end of the exam window.

### Edge Case 3: Hall Unavailable (Power Outage / Flooding)
*   **Scenario:** Hall 3 (capacity 60) is flooded on exam day. 55 students need to be relocated.
*   **Resolution:** System runs "Emergency Reallocation" distributing 55 students across remaining halls with available seats. New seating charts are printed in 5 minutes.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `exam_gap_days_between_hard_subjects` | 1 | Min gap between Math/Science exams |
| `exam_hall_capacity_buffer_pct` | 10% | Empty seats to leave per hall |
| `exam_admit_card_attendance_threshold` | 75% | Min attendance to auto-issue admit card |
| `exam_admit_card_fee_check` | `true` | Block admit card for fee defaulters? |
| `exam_invigilation_own_subject_block` | `true` | Prevent teacher from invigilating own subject? |
| `exam_cwsn_extra_time_pct` | 33% | Extra time for special needs students |
| `exam_cross_section_seating` | `true` | Interleave students from different sections? |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

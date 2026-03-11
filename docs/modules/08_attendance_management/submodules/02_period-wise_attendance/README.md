# PERIOD-WISE ATTENDANCE

**Module:** Attendance Management  
**Submodule Code:** ATT-PWA-002  
**Category:** Core Operations  
**Priority:** Critical (P0)  
**Owners:** Subject Teachers, Academic Coordinator, Admin Office

---

## OVERVIEW

The Period-wise Attendance submodule extends daily attendance by tracking student presence for EACH individual class period throughout the day. While daily attendance answers "Was the student in school today?", period-wise attendance answers "Was the student in the Physics class during Period 4?" This is critical for detecting students who are physically in school but skip specific classes, and for calculating subject-specific attendance percentages.

### Purpose

To provide granular, per-class attendance tracking that enables: subject-wise attendance reports (a student might have 90% overall attendance but only 65% in Mathematics), targeted interventions by subject teachers, and accurate compliance with board requirements that mandate minimum attendance per subject (not just per day).

### Scope

-   **Per-Period Marking:** Each subject teacher marks attendance for their period.
-   **Subject-Specific Reports:** "Student X attended 42 of 50 Physics classes."
-   **Gap Detection:** Identifying students who are present at school but absent in specific periods (class-bunking).
-   **Half-Day Reconciliation:** If a student leaves at lunch, all afternoon periods are auto-marked absent.
-   **Timetable Integration:** The attendance grid mirrors the timetable, showing the correct subject for each slot.

---

## KEY FEATURES

### 1. Subject Teacher Quick-Mark

**Feature Description:**
Lightweight marking at the start of each period.
*   When a subject teacher opens the app during Period 3, the system auto-detects: "You teach Chemistry to Grade 10B during Period 3 (Mon/Wed/Fri)."
*   Pre-fills all students from 10B as "Present."
*   Teacher marks absentees (typically 0-3 students per period).
*   Average marking time: 15-20 seconds per period.

### 2. Class-Bunk Detection

**Feature Description:**
Algorithmic detection of selective truancy.
*   If a student is marked "Present" in Period 1, "Absent" in Periods 3-4, and "Present" again in Period 5, the system flags: "Possible class bunking by [Student] during Periods 3-4."
*   Alert sent to the Class Teacher (not the subject teacher) for investigation.
*   The student could legitimately be in the library, sick room, or another activity -- the teacher investigates.

### 3. Auto-Fill from Daily Attendance

**Feature Description:**
Reducing redundant data entry.
*   If the daily attendance shows "Rohan = ABSENT" for the entire day, all 8 period-wise slots for Rohan are auto-filled as "Absent."
*   Subject teachers don't need to re-mark full-day absentees.
*   Only students marked "Present" or "Late" in the daily roll need period-wise tracking.

### 4. Elective & Lab Period Handling

**Feature Description:**
Managing non-standard timetable slots.
*   During elective periods, students from the same section split into different rooms (e.g., 10 students go to Music, 30 stay for Computer Science).
*   Each elective teacher marks their own subset.
*   Lab periods (double periods) are tracked as a single 2-hour block with one attendance entry.

---

## DATABASE SCHEMA

### 1. Period-wise Records (`att_period_records`)

```sql
CREATE TABLE att_period_records (
    record_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    section_id INT NOT NULL,
    
    attendance_date DATE NOT NULL,
    period_number INT NOT NULL, -- 1 to 8
    subject_id INT NOT NULL,
    teacher_id INT NOT NULL,
    
    status ENUM('PRESENT', 'ABSENT', 'LATE', 'EXCUSED'),
    
    marked_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE KEY unique_student_period (student_id, attendance_date, period_number),
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Subject Attendance Summary (`att_subject_summary`)

```sql
CREATE TABLE att_subject_summary (
    summary_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    subject_id INT NOT NULL,
    academic_year_id INT NOT NULL,
    term_id INT,
    
    total_periods INT DEFAULT 0,
    present_count INT DEFAULT 0,
    absent_count INT DEFAULT 0,
    
    attendance_percentage DECIMAL(5,2),
    
    last_updated DATETIME,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## BUSINESS RULES

### Rule 1: Subject Attendance Minimums
*   Some boards (e.g., CBSE for Grade 12) require minimum 75% attendance PER SUBJECT, not just overall. If "Aarav" has 70% in Chemistry practicals, he is ineligible for the Chemistry practical exam, even if his overall attendance is 85%.

### Rule 2: Period-wise Marking is Optional for Junior Classes
*   Grades 1-5 (where one teacher handles all subjects) may use daily attendance only. Period-wise tracking is configurable and typically enabled for Grades 6 and above.

### Rule 3: Bunk Detection Threshold
*   A "possible bunk" is flagged ONLY if the student is present in at least 1 period before AND 1 period after the absent period. If a student is absent for Periods 5-8 (afternoon), it's likely a legitimate early departure, not bunking.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Compliance Tracking (07):** Feeds per-subject attendance data for eligibility checks.
*   **To Teacher Dashboard:** Shows subject teachers which students have low attendance in their subject.
*   **To Report Card:** Provides subject-wise attendance percentages for detailed report cards.

### Inbound Relationships
*   **From Daily Attendance (01):** Auto-fills full-day absentees across all periods.
*   **From Timetable Module:** Reads the timetable to know which subject/teacher maps to which period.

---

## USER WORKFLOWS

### Workflow 1: Subject Teacher Marks Period Attendance
**Actor:** Chemistry Teacher (Period 4, Grade 10B)

1.  **Open:** Walks into Grade 10B at 11:00 AM. Opens Attendance App.
2.  **Auto-Detect:** App knows it's Period 4, Chemistry, 10B.
3.  **Mark:** All 40 students pre-marked Present. Teacher notices Seat 18 and Seat 22 are empty. Marks both Absent.
4.  **Submit:** "38 Present, 2 Absent. Submit?" Done in 15 seconds.

### Workflow 2: Detecting a Class Bunker
**Actor:** Class Teacher (automatic alert)

1.  **Alert:** System sends: "Arjun (Grade 10A) was Present in Period 1, Absent in Periods 3-4, Present in Period 5."
2.  **Investigate:** Class Teacher checks with Period 3-4 teachers. They confirm Arjun was not in class.
3.  **Query:** Asks Arjun. He says he was in the library. Teacher checks with the librarian -- not registered.
4.  **Action:** Marks as "Unauthorized Absence." Incident logged. Parent notified.

---

## EDGE CASES

### Edge Case 1: Free Period / Study Hall
*   **Scenario:** Grade 12 students have a "Free Period" (no assigned subject) during Period 6. Who marks attendance?
*   **Resolution:** The system supports a "Free Period Attendance" mode where the study hall supervisor marks attendance. Alternatively, KR (RFID swipe at the library or study hall) auto-marks.

### Edge Case 2: Combined Sections for a Subject
*   **Scenario:** Sections 10A and 10B are combined for a guest lecture in the auditorium. The guest lecturer is not on the system.
*   **Resolution:** The Academic Coordinator marks combined attendance for both sections using a "Merged Class" view showing 80 students.

### Edge Case 3: Student Moves Between Sections Mid-Day
*   **Scenario:** Student is transferred from 10A to 10B effective today. Morning attendance shows them in 10A, but afternoon they sit in 10B.
*   **Resolution:** The system allows the admin to set a "Transfer Effective Period." Periods 1-4: counted under 10A. Periods 5-8: counted under 10B.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `period_att_enabled_from_grade` | 6 | Grade from which period-wise tracking starts |
| `period_att_periods_per_day` | 8 | Number of periods in a day |
| `period_att_bunk_detection` | `true` | Enable class-bunk detection algorithm? |
| `period_att_lab_double_period` | `true` | Treat lab periods as a single block? |
| `period_att_auto_fill_from_daily` | `true` | Auto-mark full-day absentees across all periods? |
| `period_att_subject_min_attendance_pct` | 75% | Min per-subject attendance for exam eligibility |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

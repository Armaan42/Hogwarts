# DAILY ATTENDANCE MARKING

**Module:** Attendance Management  
**Submodule Code:** ATT-DAM-001  
**Category:** Core Operations  
**Priority:** Critical (P0)  
**Owners:** Class Teachers, Homeroom Teachers, Admin Office

---

## OVERVIEW

The Daily Attendance Marking submodule digitizes the most fundamental daily school operation -- recording which students are present, absent, or late. It replaces the traditional paper attendance register with a digital interface optimized for speed (marking 40 students in under 60 seconds), supports multiple marking methods (teacher app, biometric auto-sync, RFID gate entry), and immediately feeds downstream systems (parent alerts, compliance tracking, report cards).

### Purpose

To provide a real-time, accurate record of student presence for every school day. This data drives: parent notifications (immediate SMS on absence), regulatory compliance (75% minimum attendance for exam eligibility), safety (knowing which students are on campus during emergencies), and academic insights (correlation between attendance and performance).

### Scope

-   **Morning Assembly Attendance:** Whole-school marking at start of day.
-   **Homeroom/First Period:** Class teacher marks attendance for their section.
-   **Multi-Status Support:** Present, Absent, Late, On Leave (Pre-approved), Half-Day.
-   **Bulk Marking:** "Mark All Present" with exceptions for absentees only.
-   **Retroactive Correction:** Correcting a wrong entry after submission with audit trail.
-   **Holiday & Non-Working Day Handling:** Auto-skipping attendance for gazetted holidays.

---

## KEY FEATURES

### 1. Rapid Marking Interface

**Feature Description:**
Designed for speed during the hectic first 5 minutes of class.
*   Default state: All students are PRE-MARKED as "Present" (since the majority are usually present).
*   Teacher only needs to tap the absent students to toggle them to "Absent" or "Late."
*   Color-coded: Green (Present), Red (Absent), Yellow (Late), Blue (On Leave).
*   Swipe gesture support on mobile: Swipe left = Absent, Swipe right = Present.
*   "Submit" button locks the attendance. Confirmation: "40 Present, 2 Absent, 1 Late. Submit?"

### 2. Late Arrival Tracking

**Feature Description:**
Differentiating between "absent" and "late."
*   If a student arrives after the 8:00 AM bell but before 8:30 AM, they are marked "Late" (not Absent).
*   Late arrivals beyond 8:30 AM are marked "Absent for Morning" but can be corrected to "Half-Day Present" if the student arrives by lunch.
*   Chronic lateness (> 5 late marks/month) triggers an auto-alert to the parent and class teacher.

### 3. Pre-Approved Leave Integration

**Feature Description:**
Students whose leave was already approved appear differently.
*   If a parent submitted a leave application via the portal (approved by class teacher), the student's row shows "On Leave" (Blue) automatically.
*   The teacher doesn't need to mark them -- it's pre-filled.
*   This saves time and eliminates confusion between "absent without notice" and "absent with prior approval."

### 4. Substitute Teacher Support

**Feature Description:**
Handling attendance when the regular teacher is absent.
*   If the class teacher is on leave, the system assigns attendance duties to the substitute teacher.
*   Substitute can mark attendance but cannot edit previous days' records.
*   The class teacher can review the substitute's marking upon return.

---

## DATABASE SCHEMA

### 1. Daily Attendance Records (`att_daily_records`)

```sql
CREATE TABLE att_daily_records (
    record_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    section_id INT NOT NULL,
    academic_year_id INT NOT NULL,
    
    attendance_date DATE NOT NULL,
    
    status ENUM('PRESENT', 'ABSENT', 'LATE', 'ON_LEAVE', 'HALF_DAY', 'HOLIDAY'),
    
    marked_by INT NOT NULL, -- Teacher ID
    marked_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    late_arrival_time TIME, -- If status=LATE, what time they arrived
    
    remarks TEXT, -- "Medical emergency", "Bus breakdown"
    
    is_corrected BOOLEAN DEFAULT FALSE,
    correction_reason TEXT,
    corrected_by INT,
    corrected_at DATETIME,
    
    UNIQUE KEY unique_student_date (student_id, attendance_date),
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Attendance Summary (`att_daily_summary`)
Aggregated section-level statistics per day.

```sql
CREATE TABLE att_daily_summary (
    summary_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    section_id INT NOT NULL,
    attendance_date DATE NOT NULL,
    
    total_students INT,
    present_count INT,
    absent_count INT,
    late_count INT,
    on_leave_count INT,
    
    attendance_percentage DECIMAL(5,2),
    
    submitted_by INT,
    submitted_at DATETIME,
    
    UNIQUE KEY unique_section_date (section_id, attendance_date)
);
```

---

## BUSINESS RULES

### Rule 1: Attendance Window
*   Attendance for the day must be submitted within the first period (before 9:00 AM by default). After the window closes, late submission requires Admin override. This ensures real-time parent notifications are not delayed.

### Rule 2: No Future Dating
*   The system prevents marking attendance for future dates. A teacher cannot pre-mark tomorrow's attendance.

### Rule 3: Holiday Auto-Skip
*   On gazetted holidays, school-declared holidays, and vacation periods, the attendance system is inactive. No marking is required. The calendar syncs with the Academic Calendar module.

### Rule 4: Correction Audit Trail
*   If a teacher changes "Absent" to "Present" (e.g., the student was actually present but marked wrong), the system requires a reason and logs: Original Status, New Status, Changed By, Timestamp, Reason. This prevents fraudulent attendance manipulation.

### Rule 5: Minimum Marking Threshold
*   The system alerts the Admin if any section's attendance has NOT been submitted by 9:30 AM: "Attendance pending for Grade 8B. Please follow up with the class teacher."

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Parent Portal & SMS:** Immediate absence notification to parents.
*   **To Compliance Tracking (07):** Feeds data for 75% attendance rule enforcement.
*   **To Report Card Module:** Provides the attendance summary printed on report cards.
*   **To Fee Module:** Attendance data used for hostel mess rebate calculations.

### Inbound Relationships
*   **From Leave Management (04):** Pre-fills "On Leave" status for approved leaves.
*   **From Biometric/RFID (03):** Auto-marks attendance based on gate entry scans.
*   **From Academic Calendar:** Provides holiday and vacation schedules.

---

## USER WORKFLOWS

### Workflow 1: Morning Attendance Marking
**Actor:** Class Teacher (Grade 8A)

1.  **Open:** At 8:05 AM, opens the Attendance App on their phone.
2.  **View:** Sees 40 students, all pre-marked "Present" (green).
3.  **Mark:** Taps "Rohan" -> Absent. Taps "Priya" -> Late (arrived at 8:12 AM). Notices "Aarav" is blue (On Leave - parent applied yesterday).
4.  **Submit:** "38 Present, 1 Absent, 1 Late, 0 On Leave (Aarav pre-filled). Submit?"
5.  **Confirm:** Clicks Submit. Parents of Rohan receive SMS within 30 seconds: "Your ward Rohan is absent today (March 11)."

### Workflow 2: Correcting a Mistake
**Actor:** Class Teacher

1.  **Alert:** At 9:30 AM, Rohan walks in. He was stuck in traffic, not absent.
2.  **Correct:** Teacher opens Attendance -> Today -> Rohan -> "Change to Late."
3.  **Reason:** System prompts: "Reason for correction?" Teacher types: "Student arrived late due to traffic. Correction from Absent to Late."
4.  **Audit:** Original Absent mark is preserved in the audit trail. Current status: Late (8:45 AM).
5.  **Parent Update:** Parent receives a follow-up: "Correction: Rohan marked as Late (arrived 8:45 AM)."

---

## EDGE CASES

### Edge Case 1: Student Present but Teacher Marks Absent
*   **Scenario:** Student was sitting in the back corner. Teacher didn't see them and marked Absent.
*   **Resolution:** Student shows their Admit Card/ID to the teacher. Teacher corrects the entry (with reason "Student was present, marking error"). The system sends a corrected notification to the parent.

### Edge Case 2: School Closes Mid-Day (Emergency)
*   **Scenario:** Heavy rainfall at 11 AM. Principal declares early dismissal.
*   **Resolution:** Admin marks the day as "Half-Day (Emergency)." Students present in the morning retain their "Present" mark. The afternoon period attendance is auto-cancelled.

### Edge Case 3: Student on School Trip (Off-Campus)
*   **Scenario:** 30 students are on an inter-school quiz competition. They are physically absent from school but not "absent."
*   **Resolution:** The trip coordinator marks all 30 students as "Present (Off-Campus Activity)" via a bulk action. This counts as present for compliance but is categorized separately in reports.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `att_marking_window_end_time` | `09:00` | Deadline for submitting daily attendance |
| `att_late_threshold_time` | `08:15` | Arrival after this = "Late" |
| `att_half_day_threshold_time` | `12:00` | Arrival after this = "Half Day" only |
| `att_default_status` | `PRESENT` | Pre-filled status for all students |
| `att_correction_requires_reason` | `true` | Must give reason when correcting? |
| `att_chronic_late_threshold` | 5 | Late marks/month before triggering alert |
| `att_sms_on_absence` | `true` | Send SMS to parent on absence? |
| `att_sms_delay_seconds` | 30 | Delay before sending SMS (allows quick corrections) |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

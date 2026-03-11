# ATTENDANCE REPORTS & ANALYTICS

**Module:** Attendance Management  
**Submodule Code:** ATT-RPT-006  
**Category:** Reporting & Intelligence  
**Priority:** High (P1)  
**Owners:** Academic Coordinator, Principal, Admin Office

---

## OVERVIEW

The Attendance Reports & Analytics submodule transforms raw attendance data into actionable dashboards, scheduled reports, and statistical insights. It serves all stakeholders: teachers need daily/weekly section reports, parents need their child's attendance summary, administrators need school-wide statistics for board meetings, and regulators need compliance certificates.

### Purpose

To provide multi-dimensional views of attendance data that enable proactive decision-making. Instead of discovering attendance problems at report card time (too late), this submodule surfaces issues in real-time through dashboards, automated alerts, and trend analysis.

### Scope

-   **Real-Time Dashboards:** Live school-wide, grade-wise, section-wise attendance views.
-   **Scheduled Reports:** Daily, weekly, monthly, and annual reports auto-generated and emailed.
-   **Student Individual Reports:** Comprehensive attendance history for a specific student.
-   **Trend Analysis:** Month-over-month, year-over-year trend lines.
-   **Comparative Analytics:** Section-vs-Section, Day-of-Week patterns.
-   **Export & Compliance:** Reports in formats required by the education board (DISE, UDISE).

---

## KEY FEATURES

### 1. Live School Dashboard

**Feature Description:**
A real-time overview on the admin's screen.
*   "Today: 92.3% school-wide attendance. Lowest: Grade 7C (82%). Highest: Grade 12A (100%)."
*   Auto-refreshes every 5 minutes.
*   Click on any section to drill down to individual students.
*   Color-coded heatmap: Red (< 80%), Yellow (80-90%), Green (> 90%).

### 2. Individual Student Report

**Feature Description:**
A comprehensive view of one student's attendance.
*   Calendar view: Each day colored by status (Green/Red/Yellow/Blue).
*   Statistics: Total Days: 220. Present: 198. Absent: 15. Late: 5. Leave: 2. Percentage: 90%.
*   Subject-wise (if period-wise tracking enabled): "Physics: 88%, Chemistry: 92%, Math: 78%."
*   Year-over-year comparison: "Grade 8: 91%. Grade 9: 86% (Declining)."

### 3. Automated Compliance Reports

**Feature Description:**
Board-mandated statistical submissions.
*   **UDISE (Unified District Information System):** Annual student attendance data submitted to the Government.
*   **Board Eligibility Report:** List of students with < 75% attendance who are at risk of being barred from final exams.
*   **Right to Education (RTE) Compliance:** Attendance of RTE quota students tracked separately.

### 4. Day-of-Week & Seasonal Patterns

**Feature Description:**
Identifying attendance trends based on time patterns.
*   "Mondays have 3% lower attendance than Tuesdays across the school."
*   "Post-Diwali week always sees a 10% attendance dip."
*   "Rainy season (July-Aug) correlates with 5% higher absenteeism due to transport issues."
*   These insights help the school plan: extra buses on Mondays, rescheduling important activities away from post-festival weeks.

---

## DATABASE SCHEMA

### 1. Report Definitions (`att_report_definitions`)

```sql
CREATE TABLE att_report_definitions (
    report_id INT PRIMARY KEY AUTO_INCREMENT,
    report_name VARCHAR(100), -- 'Daily Section Report'
    report_type ENUM('DAILY', 'WEEKLY', 'MONTHLY', 'ANNUAL', 'CUSTOM'),
    
    scope ENUM('SCHOOL', 'GRADE', 'SECTION', 'STUDENT'),
    scope_id INT, -- Grade ID, Section ID, or NULL for school
    
    schedule_cron VARCHAR(50), -- '0 8 * * 1' (Mon 8AM)
    recipients JSON, -- ["admin@school.com", "principal@school.com"]
    
    export_format ENUM('PDF', 'XLSX', 'CSV'),
    
    is_active BOOLEAN DEFAULT TRUE
);
```

### 2. Report Snapshots (`att_report_snapshots`)

```sql
CREATE TABLE att_report_snapshots (
    snapshot_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    report_id INT NOT NULL,
    
    generated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    period_start DATE,
    period_end DATE,
    
    data_payload JSON, -- Full computed data
    file_path VARCHAR(255), -- Generated PDF/Excel path
    
    FOREIGN KEY (report_id) REFERENCES att_report_definitions(report_id)
);
```

---

## BUSINESS RULES

### Rule 1: Report Archival
*   All generated reports are archived for 7 years (compliance requirement). Monthly and annual reports are stored indefinitely for historical trend analysis.

### Rule 2: Data Aggregation Timing
*   Daily reports are generated at 11:00 AM (after the morning attendance window closes and corrections are made). Running them at 8:30 AM would include incomplete data.

### Rule 3: Privacy in Reports
*   Student-level reports shared with external parties (regulators) are anonymized. Only aggregate statistics (percentages, counts) are shared. Individual names require a formal data request.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Principal's Dashboard:** Feeds the "Attendance Health" widget.
*   **To UDISE Portal:** Exports data in the government-mandated format.
*   **To Report Card Module:** Provides the attendance percentage printed on report cards.

### Inbound Relationships
*   **From Daily Attendance (01):** Primary raw data source.
*   **From Period-wise (02):** Provides subject-level granularity.
*   **From Leave Management (04):** Distinguishes excused vs. unexcused absences.

---

## USER WORKFLOWS

### Workflow 1: Principal Reviews Monthly Report
**Actor:** Principal

1.  **Receive:** On the 1st of each month, PDF arrives in inbox: "Attendance Report - February 2026."
2.  **Overview:** School average: 91.2%. Target: 93%. Gap: -1.8%.
3.  **Drill Down:** Grade 9 has the lowest at 86.5%. Specifically, Grade 9C at 81%.
4.  **Investigate:** Checks if 9C has a specific teacher issue or a cluster of chronic absentees.
5.  **Action:** Instructs VP to arrange a parent-teacher meeting for the 5 most frequently absent students in 9C.

### Workflow 2: UDISE Annual Data Submission
**Actor:** Admin Office

1.  **Generate:** Clicks "Generate UDISE Report" for the academic year.
2.  **Format:** System produces data in the standard UDISE+ format: total enrolled, average daily attendance, gender-wise splits.
3.  **Review:** Admin spot-checks the numbers against manual records.
4.  **Submit:** Uploads to the Government UDISE portal.

---

## EDGE CASES

### Edge Case 1: Mid-Year Student Transfer Distorts Section Averages
*   **Scenario:** 5 students transfer out of 8B mid-year. The section average attendance percentage fluctuates because the denominator changes.
*   **Resolution:** Reports calculate attendance as `Present Days / Working Days WHILE ENROLLED`. Students who transferred out are excluded from reports after their transfer date.

### Edge Case 2: Teacher Forgets to Mark Attendance for 3 Days
*   **Scenario:** A substitute teacher didn't know they had to mark attendance. 3 days of data are missing for Grade 7A.
*   **Resolution:** The system flags "Missing Attendance Data" in the daily report. Admin contacts the teacher to retroactively add attendance within the correction window (configurable, default 7 days).

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `report_daily_generation_time` | `11:00` | Time to generate daily reports |
| `report_weekly_day` | `MONDAY` | Day for weekly report generation |
| `report_archive_retention_years` | 7 | Years to keep generated reports |
| `report_export_formats` | `['PDF','XLSX']` | Available export formats |
| `report_udise_enabled` | `true` | Enable UDISE format export? |
| `report_missing_data_alert` | `true` | Alert admin about missing attendance entries? |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

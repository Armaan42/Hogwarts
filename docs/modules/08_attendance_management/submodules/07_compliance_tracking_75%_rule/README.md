# COMPLIANCE TRACKING (75% RULE)

**Module:** Attendance Management  
**Submodule Code:** ATT-COMP-007  
**Category:** Regulatory Compliance  
**Priority:** Critical (P0)  
**Owners:** Exam Controller, Vice Principal, Academic Coordinator

---

## OVERVIEW

The Compliance Tracking submodule enforces the regulatory requirement that students must maintain a minimum attendance percentage to be eligible for examinations. In India, CBSE mandates a minimum 75% attendance for Board classes (Grades 10 and 12), and many state boards and schools extend similar rules to all grades. This submodule continuously monitors attendance percentages, provides early warning dashboards, and generates official eligibility/ineligibility lists before exams.

### Purpose

To proactively identify students at risk of falling below the minimum attendance threshold and enable timely intervention, rather than discovering the issue 2 days before the exam when it's too late. It protects the school legally by maintaining documented evidence of compliance enforcement.

### Scope

-   **Real-Time Attendance Percentage Monitoring:** Continuously calculated for each student.
-   **Early Warning System:** Graduated alerts at 85%, 80%, and 76% thresholds.
-   **Condonation Workflow:** Students between 65%-74% can apply for attendance condonation with medical/genuine reasons.
-   **Exam Eligibility Lists:** Official lists generated and posted before exams.
-   **Board Submission:** Attendance data submitted to CBSE/Board for verification.
-   **Parent Communication:** Transparent tracking shared with parents throughout the year.

---

## KEY FEATURES

### 1. Live Compliance Dashboard

**Feature Description:**
A traffic-light view of attendance compliance.
*   **Green Zone (> 85%):** Safe. No action required.
*   **Yellow Zone (76-85%):** Warning. Parent notification sent.
*   **Orange Zone (65-75%):** Critical. Condonation application window opens.
*   **Red Zone (< 65%):** Ineligible. Student barred from exams unless the Principal overrides.

### 2. Early Warning Notification System

**Feature Description:**
Progressive alerts as attendance drops.
*   At **85%:** SMS to parent: "Your ward's attendance is 85%. Please ensure regular attendance. Minimum required: 75%."
*   At **80%:** Email to parent + Class Teacher meeting scheduled.
*   At **76%:** Registered letter to parent + VP meeting mandatory. Student put on "Attendance Probation."

### 3. Condonation Application

**Feature Description:**
A formal process for students between 65-74% to seek exam eligibility.
*   Student (through parent) submits a "Condonation Application" with supporting documents: Medical certificates, family emergency proof, sports competition documentation.
*   Reviewed by a "Condonation Committee" (VP, Exam Controller, Counselor).
*   Decision: "Condonation Granted" (student can appear for exams) or "Condonation Denied."
*   CBSE allows schools to condone up to 25% of total absences with valid reasons.

### 4. Official Eligibility List Generation

**Feature Description:**
The final roster of exam-eligible students.
*   Before each major exam (Mid-Term, Final), the Exam Controller clicks "Generate Eligibility List."
*   System produces: "Grade 10: 158 Eligible, 2 Ineligible, 3 Condonation Pending."
*   List is posted on the school notice board and sent to parents.
*   Ineligible students' parents receive a formal letter with appeal instructions.

---

## DATABASE SCHEMA

### 1. Compliance Status (`att_compliance_status`)

```sql
CREATE TABLE att_compliance_status (
    compliance_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    academic_year_id INT NOT NULL,
    
    total_working_days INT,
    days_present INT,
    days_absent INT,
    days_on_leave INT,
    
    attendance_percentage DECIMAL(5,2),
    
    compliance_zone ENUM('GREEN', 'YELLOW', 'ORANGE', 'RED'),
    
    is_exam_eligible BOOLEAN DEFAULT TRUE,
    condonation_status ENUM('NOT_REQUIRED', 'APPLIED', 'GRANTED', 'DENIED'),
    
    last_calculated DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Condonation Applications (`att_condonation_applications`)

```sql
CREATE TABLE att_condonation_applications (
    app_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    academic_year_id INT NOT NULL,
    
    attendance_at_application DECIMAL(5,2),
    absent_days_to_condone INT,
    
    reason TEXT,
    supporting_documents JSON, -- ["/docs/medical_cert.pdf"]
    
    committee_decision ENUM('PENDING', 'GRANTED', 'DENIED'),
    decision_date DATE,
    decision_remarks TEXT,
    decided_by INT,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## BUSINESS RULES

### Rule 1: Leave Days Count as Present
*   Pre-approved leaves (casual, medical with certificate, school-approved sports) count toward the "Present" tally for compliance calculation. Only unexcused absences reduce the percentage.

### Rule 2: Compliance Recalculation Frequency
*   Attendance percentage is recalculated daily at 11:00 AM (after the morning marking window closes). The dashboard updates in real-time.

### Rule 3: Board Exam Priority
*   For CBSE Board classes (10 and 12), the 75% rule is non-negotiable. Even the Principal's override requires submission of a formal condonation report to the CBSE Regional Office.

### Rule 4: Subject-Level Compliance
*   For subjects with mandatory practicals (Physics, Chemistry, Biology), attendance in practical sessions is tracked separately. A student may meet 75% overall but be below threshold for Physics practicals, making them ineligible for the practical exam.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Exam Module (05-01):** Provides the eligibility list for the exam scheduling system.
*   **To Parent Portal:** Real-time compliance zone visibility.
*   **To CBSE Board:** Submits attendance data for Board exam candidates.

### Inbound Relationships
*   **From Daily Attendance (01):** Primary data source for attendance counts.
*   **From Leave Management (04):** Determines which absences are "excused."
*   **From Academic Calendar:** Defines total working days.

---

## USER WORKFLOWS

### Workflow 1: Pre-Exam Eligibility Check
**Actor:** Exam Controller

1.  **Dashboard:** 2 weeks before Finals, opens Compliance Dashboard for Grade 10.
2.  **Overview:** 155 students Green, 3 students Yellow (78-80%), 2 students Orange (68%, 72%).
3.  **Action on Orange:** Generates formal notice to parents of the 2 Orange students. Condonation window opens (3-day deadline).
4.  **Condonation:** Both parents submit applications with medical certificates. Committee meets.
5.  **Decision:** 1 student granted condonation (genuine chronic illness). 1 denied (no valid documentation).
6.  **Final List:** "159 Eligible, 1 Ineligible." List posted. Ineligible student's parent informed of re-exam in next session.

### Workflow 2: Parent Monitors Compliance Throughout Year
**Actor:** Parent

1.  **Dashboard:** Opens school app > Attendance > Compliance Status.
2.  **View:** "Aarav: 83% attendance. Zone: YELLOW. 14 days absent. Target: 75% minimum."
3.  **Warning:** "At current trajectory, Aarav will reach 74% by March. Please improve attendance."
4.  **Action:** Parent ensures Aarav attends school regularly. Attendance improves to 86% by March.

---

## EDGE CASES

### Edge Case 1: Natural Disaster Closes School for 2 Weeks
*   **Scenario:** Heavy floods close the school for 10 working days. Total working days decrease from 220 to 210. Students with borderline attendance suddenly cross the 75% mark because the denominator shrank.
*   **Resolution:** The system recalculates compliance based on the revised working days. Students who were in the Orange zone may move to Yellow/Green.

### Edge Case 2: Student Joins Mid-Year
*   **Scenario:** A transfer student joins in November. They have only 3 months of attendance data. Their "percentage" is 90% but based on only 60 days, not 220.
*   **Resolution:** Compliance is calculated from the date of joining, not from the start of the academic year. Transfer certificate from the previous school provides the earlier attendance data, which is appended.

### Edge Case 3: Teacher Didn't Mark Attendance for 5 Days
*   **Scenario:** Missing attendance data for a section for 5 days. All students in that section show artificially low percentages.
*   **Resolution:** The compliance system flags "Missing Data Alert." Until the data is backfilled, those 5 days are excluded from the denominator (not counted as working days for that section).

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `compliance_min_attendance_pct` | 75% | Minimum attendance for exam eligibility |
| `compliance_warning_threshold` | 85% | First warning level |
| `compliance_critical_threshold` | 76% | Final warning before ineligibility |
| `compliance_condonation_min_pct` | 65% | Min attendance to be eligible for condonation |
| `compliance_leave_counts_as_present` | `true` | Do approved leaves count toward 75%? |
| `compliance_board_classes` | `[10, 12]` | Grades with Board-mandated 75% rule |
| `compliance_recalc_time` | `11:00` | Daily recalculation time |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

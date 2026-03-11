# LEAVE MANAGEMENT

**Module:** Attendance Management  
**Submodule Code:** ATT-LM-004  
**Category:** Core Operations  
**Priority:** High (P1)  
**Owners:** Class Teachers, Admin Office, Parents

---

## OVERVIEW

The Leave Management submodule digitizes the student leave application and approval process. Parents can submit leave requests through the portal or app, class teachers approve/reject them, and the attendance system auto-marks "On Leave" for approved dates. It eliminates paper-based leave letters, provides a visible leave balance, and ensures that medical leaves are backed by documentation.

### Purpose

To provide a structured leave workflow that differentiates between types of leave (casual, medical, family emergency), enforces a leave balance, ensures medical documentation for health-related absences, and auto-integrates with attendance so teachers don't need to manually track who is on approved leave.

### Scope

-   **Leave Application:** Parent/Student submits via portal, app, or manual form.
-   **Leave Types:** Casual, Medical, Family Emergency, Religious Festival, Sports/Activity.
-   **Approval Workflow:** Class Teacher -> (Vice Principal for extended leaves).
-   **Medical Certificate Upload:** Required for medical leaves > 2 days.
-   **Leave Balance Tracking:** Days allowed vs. days consumed per category.
-   **Auto-Attendance Integration:** Approved leaves pre-fill attendance as "On Leave."

---

## KEY FEATURES

### 1. Online Leave Application

**Feature Description:**
Self-service for parents via portal and mobile app.
*   Parent selects: Student -> Leave Type -> Start Date -> End Date -> Reason.
*   Supports single-day and multi-day applications.
*   Attachments: Upload medical certificate, event invitation, or other supporting documents.
*   Real-time status tracking: "Submitted -> Under Review -> Approved/Rejected."

### 2. Leave Balance Dashboard

**Feature Description:**
Transparency on leave consumption.
*   Each student is allocated leave per category per academic year:
    *   Casual Leave: 12 days.
    *   Medical Leave: Unlimited (with documentation).
    *   Sports/Extra-curricular: 10 days.
*   Dashboard shows: "Casual: 8/12 remaining. Medical: 3 days availed (certificates uploaded)."

### 3. Approval Cascade

**Feature Description:**
Multi-level approval based on leave duration.
*   1-3 days: Class Teacher approves directly.
*   4-7 days: Class Teacher recommends, Vice Principal approves.
*   7+ days: Vice Principal recommends, Principal approves.
*   Urgent leaves (family emergency): Fast-track approval via SMS to approver.

### 4. Auto-Attendance Integration

**Feature Description:**
Eliminating double work for teachers.
*   Once a leave is approved (e.g., March 12-15), the attendance system automatically fills "On Leave (Blue)" for those 4 days.
*   When the class teacher opens the attendance screen on March 12, the student already shows as "On Leave."
*   If the student surprisingly shows up on March 13 (leave was 4 days but only took 2), the teacher overrides to "Present" and the remaining leave days are credited back.

---

## DATABASE SCHEMA

### 1. Leave Applications (`att_leave_applications`)

```sql
CREATE TABLE att_leave_applications (
    leave_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    
    leave_type ENUM('CASUAL', 'MEDICAL', 'FAMILY_EMERGENCY', 'RELIGIOUS', 'SPORTS_ACTIVITY', 'OTHER'),
    
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    total_days INT,
    
    reason TEXT,
    supporting_document_url VARCHAR(255),
    
    applied_by ENUM('PARENT', 'STUDENT', 'ADMIN'),
    applied_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    status ENUM('SUBMITTED', 'APPROVED', 'REJECTED', 'CANCELLED', 'EXPIRED'),
    
    approved_by INT,
    approved_at DATETIME,
    rejection_reason TEXT,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Leave Balance (`att_leave_balance`)

```sql
CREATE TABLE att_leave_balance (
    balance_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    academic_year_id INT NOT NULL,
    
    casual_total INT DEFAULT 12,
    casual_consumed INT DEFAULT 0,
    
    medical_consumed INT DEFAULT 0, -- No limit, but tracked
    
    sports_total INT DEFAULT 10,
    sports_consumed INT DEFAULT 0,
    
    other_consumed INT DEFAULT 0,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## BUSINESS RULES

### Rule 1: Medical Certificate Mandatory
*   For medical leaves exceeding 2 consecutive days, a medical certificate must be uploaded within 3 days of the student's return. If not uploaded, the leave reverts to "Casual Leave" and deducts from the casual balance.

### Rule 2: Casual Leave Cannot Be Negative
*   If a student has consumed all 12 casual days, further casual leave applications are auto-rejected with the message: "Casual leave balance exhausted. Please apply under a different category or speak with the class teacher."

### Rule 3: Retroactive Leave
*   If a student was absent without prior application, the parent can submit a "Retroactive Leave" within 3 days. This converts the "Absent (unexcused)" to "On Leave (excused)" in the records.

### Rule 4: Blackout Periods
*   Leave applications during exam weeks and the last 2 weeks of the academic year are auto-flagged for Vice Principal review, regardless of duration.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Daily Attendance (01):** Pre-fills "On Leave" for approved dates.
*   **To Compliance (07):** Leave days are excluded from the absent count for the 75% rule.
*   **To Parent Portal:** Real-time leave status and balance visibility.

### Inbound Relationships
*   **From Academic Calendar:** Knows exam periods for blackout enforcement.
*   **From Health Module:** Medical leave may be initiated by the school nurse after a sick room visit.

---

## USER WORKFLOWS

### Workflow 1: Parent Applies for Casual Leave
**Actor:** Parent (via Mobile App)

1.  **Navigate:** Opens App -> Leave Application -> Select Student.
2.  **Fill:** Type: Casual, Dates: March 14-15 (2 days), Reason: "Family function."
3.  **Submit:** Application goes to Class Teacher.
4.  **Approval:** Teacher reviews. Sees student has 8 casual days remaining. Approves.
5.  **Notification:** Parent receives: "Leave approved for March 14-15."
6.  **Auto-Mark:** On March 14, student's attendance shows "On Leave (Blue)" automatically.

### Workflow 2: Retroactive Medical Leave
**Actor:** Parent

1.  **Situation:** Child was absent March 8-10 (3 days) due to fever. Parent didn't apply in advance.
2.  **Apply:** On March 11, parent submits Retroactive Leave: Medical, March 8-10, attaches doctor's note.
3.  **Approval:** Teacher reviews the medical certificate. Approves.
4.  **Correction:** March 8-10 attendance changes from "Absent (Red)" to "On Leave (Blue)."

---

## EDGE CASES

### Edge Case 1: Leave Approved, But Student Comes to School
*   **Scenario:** 5-day leave approved. Student returns on Day 3 feeling better.
*   **Resolution:** Teacher marks "Present" on Day 3. System detects approved leave overlap and prompts: "Cancel remaining leave days (Day 4-5)?" If teacher confirms, 2 days are credited back to the leave balance.

### Edge Case 2: Overlapping Leave Applications
*   **Scenario:** Parent submits leave for March 14-16, then submits another for March 15-18 (overlapping).
*   **Resolution:** System detects overlap: "A leave application already exists for March 15-16. Please modify the existing application instead." The second application is rejected.

### Edge Case 3: Leave During Board Exam
*   **Scenario:** Parent applies for casual leave during the Grade 10 Board Exam week.
*   **Resolution:** System auto-blocks: "Leave applications cannot be submitted during scheduled exam periods (March 1-15). Please contact the Exam Controller for special circumstances."

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `leave_casual_annual_limit` | 12 | Casual leave days per year |
| `leave_sports_annual_limit` | 10 | Sports/activity leave days per year |
| `leave_medical_cert_required_after_days` | 2 | Consecutive sick days before certificate needed |
| `leave_medical_cert_upload_deadline_days` | 3 | Days after return to upload certificate |
| `leave_retroactive_window_days` | 3 | Days within which retroactive leave can be applied |
| `leave_escalation_threshold_days` | 3 | Leaves > this go to VP for approval |
| `leave_blackout_exam_weeks` | `true` | Block leave during exam periods? |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

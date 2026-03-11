# ABSENCE ALERTS & NOTIFICATIONS

**Module:** Attendance Management  
**Submodule Code:** ATT-ALERT-005  
**Category:** Communication  
**Priority:** High (P1)  
**Owners:** Admin Office, Class Teachers, IT Administrator

---

## OVERVIEW

The Absence Alerts & Notifications submodule is the communication engine that ensures parents, teachers, and administrators are immediately informed about student absences. When a student is marked absent, a multi-channel notification cascade triggers within seconds -- SMS, push notification, email, and WhatsApp. This submodule also handles escalation for chronic absenteeism, sends weekly attendance summaries to parents, and alerts the principal about school-wide attendance anomalies.

### Purpose

To close the information gap between school and home. A parent should know within minutes that their child did not reach school, enabling immediate action (is the child safe? Did they miss the bus?). For chronic cases, automated escalation ensures no at-risk student goes unnoticed.

### Scope

-   **Instant Absence Notification:** SMS/Push/Email to parents within seconds of marking.
-   **Chronic Absence Escalation:** Auto-flagging students with consecutive or cumulative absences.
-   **Weekly Attendance Summary:** Scheduled digest to all parents.
-   **Teacher/Admin Alerts:** "5 students absent today" dashboard widget.
-   **Attendance Anomaly Detection:** Flagging unusual patterns (e.g., "15 students absent from Grade 8B -- possible contagion?").
-   **Multi-Channel Delivery:** SMS, Push Notification, Email, WhatsApp Business API.

---

## KEY FEATURES

### 1. Real-Time Absence SMS/Push

**Feature Description:**
Immediate notification triggered by attendance marking.
*   Within 30 seconds of a teacher marking "Absent," both parents (Father + Mother phone numbers) receive an SMS: "Dear Parent, your ward [Name] is absent from school today [Date]. If this is unexpected, please contact the school immediately."
*   Push notification is sent simultaneously to the School App on parent's phone.
*   Delivery receipt is tracked: "SMS Delivered / Failed."

### 2. Chronic Absenteeism Auto-Escalation

**Feature Description:**
Progressive response to persistent absence.

**Escalation Ladder:**
| Trigger | Action | Recipient |
|---|---|---|
| 3 consecutive days absent | Phone call from Class Teacher | Parent |
| 5 consecutive days absent | Email from Vice Principal | Parent + Class Teacher |
| 10 cumulative absences in a term | Meeting request from Counselor | Parent |
| 15+ cumulative absences | Principal's letter (registered post) | Parent |
| 20+ cumulative absences | Report to District Education Officer | DEO + Parent |

### 3. Weekly Attendance Summary

**Feature Description:**
A scheduled digest sent every Saturday morning.
*   SMS/Email: "Weekly Attendance Report for [Student]: Mon-P, Tue-P, Wed-A, Thu-P, Fri-P. Attendance this week: 80%. Term Average: 88%."
*   Parent can see a trend: is attendance improving or declining?

### 4. School-Wide Anomaly Detection

**Feature Description:**
AI-powered pattern detection.
*   If overall school attendance drops below 70% on a specific day (normal: 92%), the system alerts the Principal: "Unusual absence: 30% students absent. Possible causes: Holiday nearby, Weather, Epidemic."
*   If a specific section (e.g., 8B) has 50% absence, the Class Teacher and Nurse are alerted: "Potential cluster illness in Grade 8B."

---

## DATABASE SCHEMA

### 1. Notification Log (`att_notification_log`)

```sql
CREATE TABLE att_notification_log (
    notification_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    
    notification_type ENUM('ABSENCE_ALERT', 'LATE_ALERT', 'WEEKLY_SUMMARY', 'CHRONIC_ESCALATION', 'ANOMALY'),
    
    channel ENUM('SMS', 'PUSH', 'EMAIL', 'WHATSAPP', 'LETTER'),
    recipient_phone VARCHAR(15),
    recipient_email VARCHAR(100),
    
    message_content TEXT,
    
    delivery_status ENUM('SENT', 'DELIVERED', 'FAILED', 'PENDING'),
    sent_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    delivered_at DATETIME,
    
    failure_reason TEXT,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Escalation Tracker (`att_escalation_tracker`)

```sql
CREATE TABLE att_escalation_tracker (
    escalation_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    academic_year_id INT NOT NULL,
    
    current_escalation_level INT DEFAULT 0, -- 0 = No escalation, 1-5 = Ladder levels
    
    consecutive_absent_days INT DEFAULT 0,
    cumulative_absent_days INT DEFAULT 0,
    
    last_escalation_date DATE,
    last_escalation_action TEXT,
    
    counselor_meeting_scheduled BOOLEAN DEFAULT FALSE,
    principal_letter_sent BOOLEAN DEFAULT FALSE,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## BUSINESS RULES

### Rule 1: SMS Delay Buffer
*   The system waits 30 seconds after attendance submission before sending SMS. This buffer allows the teacher to correct accidental markings without triggering a false alarm to parents.

### Rule 2: DND Number Handling
*   If a parent's phone number is on the "Do Not Disturb" (DND) registry, promotional SMS will be blocked. The system should send absence alerts via "Transactional SMS" (not promotional), which bypasses DND as it's considered essential communication.

### Rule 3: De-Escalation
*   When a student returns to school after chronic absence, the escalation level gradually de-escalates: after 10 consecutive "Present" days, the escalation level drops by 1. This prevents permanent stigma.

### Rule 4: Parent Read Receipt
*   The system tracks if the parent opened the push notification or email. If no "read" signal is received within 2 hours for a critical alert (e.g., unexpected absence), the system sends a follow-up SMS: "Urgent: Your ward was absent today. No acknowledgment received. Please respond."

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To SMS Gateway:** Sends transactional SMS via API.
*   **To Push Service (FCM/APNS):** Sends push notifications to the school app.
*   **To WhatsApp Business API:** Sends template messages for alerts.
*   **To Counselor Dashboard:** Routes chronic absenteeism cases for intervention.

### Inbound Relationships
*   **From Daily Attendance (01):** Triggers on any "Absent" status entry.
*   **From Leave Management (04):** Suppresses alerts for pre-approved leaves.

---

## USER WORKFLOWS

### Workflow 1: Immediate Absence Alert Flow
**Actor:** System (Automated)

1.  **Trigger:** Teacher marks "Rohan = Absent" at 8:05 AM.
2.  **Buffer:** System waits 30 seconds (no correction made).
3.  **Send:** SMS sent to Father: "Your ward Rohan is absent from school today (March 11)."
4.  **Push:** Push notification sent to Mother's phone app.
5.  **Log:** Notification logged: SMS Delivered (Father), Push Opened (Mother at 8:07 AM).
6.  **Action:** Father calls school: "He left home at 7:30 AM!" School alerts security. Child found in the playground (had arrived late, biometric scan missed).

### Workflow 2: Chronic Absence Escalation
**Actor:** System + Class Teacher + Counselor

1.  **Day 3:** System alerts Class Teacher: "Priya absent 3 consecutive days."
2.  **Day 3 Action:** Teacher calls parent. Parent says "She is unwell."
3.  **Day 5:** System alerts VP: "Priya absent 5 consecutive days. Medical certificate not submitted."
4.  **Day 5 Action:** VP sends formal email requesting medical documentation.
5.  **Day 10:** System schedules counselor meeting: "Cumulative 10 absences this term."
6.  **Counselor:** Meets parent. Discovers underlying issue (bullying). Intervention plan created.

---

## EDGE CASES

### Edge Case 1: Both Parents' Phones Unreachable
*   **Scenario:** SMS fails (number changed) and push notification is undelivered (app not installed).
*   **Resolution:** System falls back to email. If email also bounces, an "Unreachable Parent" flag is raised for the Admin to update contact details.

### Edge Case 2: Student Marked Absent Then Corrected to Present
*   **Scenario:** SMS "Absent" already sent. Teacher corrects to "Late" 10 minutes later.
*   **Resolution:** System sends a correction SMS: "Update: Your ward Rohan has arrived at school (Late at 8:15 AM). Previous absence alert is cancelled."

### Edge Case 3: Mass Absence Due to Festival
*   **Scenario:** 40% of students are absent on Diwali (which is a working day for the school).
*   **Resolution:** The anomaly detection system alerts the Principal but also checks: "Is there a festival today?" If yes, the alert is downgraded to "Informational" rather than "Critical." The system maintains a cultural calendar.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `alert_sms_delay_seconds` | 30 | Buffer before sending absence SMS |
| `alert_channels` | `['SMS','PUSH','EMAIL']` | Active notification channels |
| `alert_weekly_summary_day` | `SATURDAY` | Day of weekly digest |
| `alert_chronic_consecutive_days` | 3 | Consecutive absent days to trigger escalation |
| `alert_chronic_cumulative_term` | 10 | Term absences to schedule counselor meeting |
| `alert_anomaly_threshold_pct` | 70% | Below this school-wide attendance triggers anomaly |
| `alert_correction_sms_enabled` | `true` | Send correction message when status changes? |
| `alert_parent_read_followup_hours` | 2 | Hours to wait before follow-up if no read receipt |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

# ATTENDANCE & LEAVE TRACKING - COMPLETE DOCUMENTATION

**Module:** Student Management  
**Submodule Code:** STD-ATTENDANCE-007  
**Category:** Core Foundation  
**Priority:** Critical (P0)  
**Owner:** Academic Administration & Class Teachers

---

## 📋 OVERVIEW

The Attendance & Leave Tracking submodule manages comprehensive student attendance records including daily attendance, period-wise tracking, leave applications, late arrivals, early departures, biometric integration, parent notifications, and attendance-based analytics. It ensures compliance with minimum attendance requirements (75% for CBSE/ICSE boards) and automates attendance-related alerts.

### Purpose

Track student presence/absence accurately, manage leave requests with approvals, monitor attendance percentages, identify defaulters, automate parent notifications, integrate with biometric systems, generate attendance reports, and ensure board exam eligibility compliance.

### Scope

- **Daily Attendance**: Morning assembly attendance
- **Period-wise Attendance**: Subject-wise tracking
- **Leave Management**: Applications, approvals, types
- **Late/Early Tracking**: Arrivals and departures
- **Biometric Integration**: Fingerprint/RFID attendance
- **Parent Notifications**: SMS/email alerts
- **Attendance Analytics**: Reports, defaulters, trends
- **Board Compliance**: 75% minimum requirement

---

## 🎯 KEY FEATURES

### 1. Daily Attendance System

#### 1.1 Morning Assembly Attendance

**Attendance Marking Process:**
```yaml
Date: 17/01/2024 (Wednesday)
Grade: 6-A
Class Teacher: Mrs. Anjali Gupta
Total Students: 30

Attendance Marking Time: 8:00 AM - 8:15 AM
Method: Manual (Class Teacher)

Present Students: 28
Absent Students: 2
  - Rohan Kumar (2024/06/0457) - Sick Leave (Applied)
  - Priya Sharma (2024/06/0458) - No information

Late Arrivals: 1
  - Arjun Verma (2024/06/0459) - Arrived 8:20 AM (20 min late)

Attendance Percentage: 93.3% (28/30)

Status Breakdown:
  Present: 28 (93.3%)
  Absent (With Leave): 1 (3.3%)
  Absent (Without Leave): 1 (3.3%)
  Late: 1 (3.3%)
```

**Attendance Statuses:**
```yaml
Status Types:

1. PRESENT (P):
   - Student present during morning assembly
   - Marked by class teacher
   - Default status if not marked otherwise

2. ABSENT (A):
   - Student not present
   - No leave application
   - Parent notification sent automatically

3. SICK LEAVE (SL):
   - Medical reason
   - Medical certificate required (>3 days)
   - Approved by class teacher/principal

4. CASUAL LEAVE (CL):
   - Personal/family reasons
   - Prior approval required
   - Limited days per term (usually 10-15)

5. LATE (L):
   - Arrived after 8:15 AM
   - Marked as late
   - Counted as 0.5 attendance

6. HALF DAY (HD):
   - Left before 12:00 PM
   - Medical/emergency reasons
   - Counted as 0.5 attendance

7. ON DUTY (OD):
   - School-related activity
   - Sports event, competition, etc.
   - Counted as present

8. HOLIDAY (H):
   - Declared holiday
   - Not counted in attendance calculation

9. SUSPENDED (S):
   - Disciplinary suspension
   - Not counted in attendance
```

#### 1.2 Biometric Attendance Integration

**Entry/Exit Tracking:**
```yaml
Student: Aarav Patel (2024/06/0456)
Date: 17/01/2024

Entry Record:
  Time: 7:55 AM
  Method: Fingerprint (Right Thumb)
  Device: Gate 1 - Main Entrance
  Match Quality: 95%
  Status: VERIFIED ✓
  Attendance: PRESENT (Auto-marked)

Exit Record:
  Time: 3:30 PM
  Method: Fingerprint (Right Thumb)
  Device: Gate 1 - Main Entrance
  Match Quality: 93%
  Status: VERIFIED ✓
  Duration: 7 hours 35 minutes

Parent Notification:
  Entry SMS: "Aarav entered school at 7:55 AM - Hogwarts"
  Exit SMS: "Aarav left school at 3:30 PM - Hogwarts"
```

**Biometric System Features:**
- **Real-time Tracking**: Instant entry/exit recording
- **Parent Alerts**: SMS on entry/exit
- **Duplicate Prevention**: Cannot mark entry twice
- **Mismatch Alerts**: Failed fingerprint attempts logged
- **Backup Methods**: RFID card, face recognition
- **Offline Mode**: Sync when connection restored

---

### 2. Period-wise Attendance

#### 2.1 Subject-wise Tracking

**Daily Period Attendance:**
```yaml
Student: Rohan Kumar (2024/06/0457)
Date: 17/01/2024
Grade: 6-A

Period 1 (8:30-9:15 AM): English
  Teacher: Mrs. Anjali Gupta
  Status: PRESENT ✓
  Marked: 8:32 AM

Period 2 (9:15-10:00 AM): Mathematics
  Teacher: Mr. Suresh Kumar
  Status: PRESENT ✓
  Marked: 9:17 AM

Period 3 (10:00-10:45 AM): Science
  Teacher: Dr. Ramesh Mehta
  Status: ABSENT ❌
  Reason: Went to school clinic (headache)
  Marked: 10:02 AM

Period 4 (11:00-11:45 AM): Social Studies
  Teacher: Mrs. Priya Singh
  Status: PRESENT ✓
  Marked: 11:02 AM

Period 5 (11:45-12:30 PM): Hindi
  Teacher: Mrs. Meera Sharma
  Status: PRESENT ✓
  Marked: 11:47 AM

Period 6 (1:00-1:45 PM): Computer Science
  Teacher: Mr. Vikram Patel
  Status: PRESENT ✓
  Marked: 1:03 PM

Daily Summary:
  Total Periods: 6
  Present: 5 (83.3%)
  Absent: 1 (16.7%)
  Overall: PRESENT (Morning attendance marked)
```

**Subject-wise Attendance Calculation:**
```yaml
Student: Rohan Kumar
Subject: Science
Month: January 2024

Total Classes: 20
Present: 18
Absent: 2
Attendance %: 90%

Breakdown:
  Week 1 (1-5 Jan): 4/4 (100%)
  Week 2 (8-12 Jan): 4/4 (100%)
  Week 3 (15-19 Jan): 4/5 (80%) - Absent on 17th
  Week 4 (22-26 Jan): 4/4 (100%)
  Week 5 (29-31 Jan): 2/3 (66.7%) - Absent on 30th

Status: SATISFACTORY (>75%)
Alert: None
```

---

### 3. Leave Management System

#### 3.1 Leave Application Process

**Leave Application:**
```yaml
Application ID: LEAVE-2024-0456
Student: Aarav Patel (2024/06/0456)
Grade: 6-A

Leave Details:
  Type: SICK LEAVE
  From Date: 20/01/2024 (Monday)
  To Date: 22/01/2024 (Wednesday)
  Total Days: 3
  Reason: Viral fever with high temperature
  
Applied By: Parent (Mr. Rajesh Patel)
Applied On: 19/01/2024 at 8:30 PM
Applied Via: Parent Portal

Supporting Documents:
  - Medical Certificate: Dr. Suresh Mehta (Uploaded)
  - Prescription: Attached
  
Approval Workflow:
  Step 1: Class Teacher (Mrs. Anjali Gupta)
    Status: APPROVED
    Date: 19/01/2024 at 9:00 PM
    Comments: "Medical certificate verified. Get well soon!"
    
  Step 2: Principal (Auto-approved for <5 days)
    Status: APPROVED
    Date: 19/01/2024 at 9:01 PM
    
Overall Status: APPROVED
Effective: 20/01/2024 - 22/01/2024

Attendance Impact:
  Dates Marked: 20, 21, 22 Jan 2024
  Status: SICK LEAVE (SL)
  Counted as: Approved absence (not affecting %)
  
Parent Notification:
  SMS: "Leave approved for Aarav (20-22 Jan). Get well soon!"
  Email: Detailed approval with dates
```

#### 3.2 Leave Types & Policies

**Leave Categories:**
```yaml
1. SICK LEAVE (SL):
   Maximum: Unlimited (with medical certificate)
   Medical Certificate: Required for >3 consecutive days
   Approval: Class Teacher (≤3 days), Principal (>3 days)
   Counted: Not affecting attendance % if approved
   
2. CASUAL LEAVE (CL):
   Maximum: 15 days per academic year
   Reason: Personal/family matters
   Approval: Class Teacher (≤2 days), Principal (>2 days)
   Advance Notice: Minimum 1 day (except emergencies)
   Counted: Not affecting attendance % if approved
   
3. EMERGENCY LEAVE (EL):
   Maximum: 5 days per year
   Reason: Family emergency, death, accident
   Approval: Principal
   Advance Notice: Not required (can apply retrospectively)
   Counted: Not affecting attendance % if approved
   
4. PLANNED LEAVE (PL):
   Maximum: 10 days per year
   Reason: Family vacation, wedding, etc.
   Approval: Principal (mandatory)
   Advance Notice: Minimum 7 days
   Counted: Not affecting attendance % if approved
   Restrictions: Not allowed during exams
   
5. SPORTS/COMPETITION LEAVE (OD):
   Maximum: As required
   Reason: Representing school in events
   Approval: Sports Coordinator + Principal
   Counted: Marked as ON DUTY (counted as present)
   
6. MEDICAL APPOINTMENT:
   Maximum: As required
   Reason: Doctor/hospital visits
   Approval: Class Teacher
   Duration: Usually half-day
   Counted: 0.5 attendance if approved
```

**Leave Application Rules:**
```yaml
General Rules:
  - Advance application preferred (except emergencies)
  - Medical certificate mandatory for sick leave >3 days
  - Leave during exams discouraged (except medical)
  - Maximum consecutive leave: 15 days (requires principal approval)
  - Leave balance tracked per student
  
Approval Authority:
  ≤2 days: Class Teacher
  3-5 days: Class Teacher + Academic Coordinator
  >5 days: Principal approval mandatory
  During Exams: Principal only
  
Rejection Criteria:
  - Insufficient reason
  - Exam period (unless medical emergency)
  - Excessive casual leave usage
  - Pattern of frequent leaves (Monday/Friday)
  - Missing medical certificate
```

---

### 4. Late Arrival & Early Departure

#### 4.1 Late Arrival Management

**Late Entry Record:**
```yaml
Student: Arjun Verma (2024/06/0459)
Date: 17/01/2024
School Start Time: 8:00 AM
Arrival Time: 8:25 AM
Late By: 25 minutes

Entry Point: Main Gate
Recorded By: Security Guard
Biometric: Verified ✓

Late Entry Form:
  Reason: Traffic jam on highway
  Parent Signature: Required
  Submitted: Yes
  
Attendance Status: LATE (L)
Counted As: 0.5 attendance

Late Arrival Count (This Month):
  Total: 3 times
  Dates: 5th Jan, 12th Jan, 17th Jan
  Status: WARNING (>3 times triggers action)
  
Action Taken:
  - Parent notification sent
  - Warning letter issued
  - Next late: Parent meeting required
```

**Late Arrival Policy:**
```yaml
Grace Period: 15 minutes (8:00-8:15 AM)
  - No penalty
  - Marked as PRESENT
  
16-30 minutes late (8:16-8:30 AM):
  - Marked as LATE
  - Counted as 0.5 attendance
  - Entry allowed
  - Reason required
  
31-60 minutes late (8:31-9:00 AM):
  - Marked as LATE
  - Counted as 0.5 attendance
  - Parent call required
  - Entry allowed with permission
  
>60 minutes late (after 9:00 AM):
  - Marked as ABSENT (half-day)
  - Counted as 0.5 attendance
  - Principal permission required
  - Parent must accompany/call
  
Repeated Late Arrivals:
  3 times/month: Warning letter to parents
  5 times/month: Parent meeting mandatory
  >7 times/month: Disciplinary action
```

#### 4.2 Early Departure Management

**Early Departure Record:**
```yaml
Student: Priya Sharma (2024/06/0458)
Date: 17/01/2024
School End Time: 3:30 PM
Departure Time: 12:00 PM
Early By: 3 hours 30 minutes

Reason: Doctor appointment (Dentist)
Requested By: Parent (Mr. Sharma)
Request Time: 11:30 AM (Phone call)
Approved By: Class Teacher

Pickup Details:
  Picked By: Father (Mr. Sharma)
  ID Verified: Yes (Aadhaar)
  Signature: Obtained
  Exit Time: 12:05 PM
  
Attendance Status: HALF DAY (HD)
Counted As: 0.5 attendance

Early Departure Form:
  Reason: Medical appointment
  Parent Signature: Yes
  Return Expected: No (for the day)
  
Parent Notification:
  SMS: "Priya picked up at 12:05 PM by Mr. Sharma"
```

**Early Departure Policy:**
```yaml
Before 12:00 PM:
  - Counted as HALF DAY
  - Attendance: 0.5
  - Parent pickup mandatory
  - Valid reason required
  
12:00 PM - 2:00 PM:
  - Counted as HALF DAY
  - Attendance: 0.5
  - Parent permission required
  
After 2:00 PM:
  - Counted as PRESENT
  - Attendance: 1.0
  - Parent permission required
  
Emergency Departure:
  - Immediate parent call
  - Principal permission
  - Counted based on time
  - Follow-up required next day
```

---

### 5. Attendance Analytics & Reports

#### 5.1 Student Attendance Dashboard

**Individual Student Report:**
```yaml
Student: Aarav Patel (2024/06/0456)
Grade: 6-A
Academic Year: 2024-25
Report Period: April 2024 - January 2025 (10 months)

Overall Attendance:
  Total School Days: 200
  Present: 185
  Absent: 10
  Late: 3
  Half Day: 2
  On Duty: 5
  
  Attendance %: 92.5%
  Status: GOOD ✓ (>75% required)
  Board Eligibility: ELIGIBLE ✓

Monthly Breakdown:
  April 2024: 20/22 (90.9%)
  May 2024: 18/20 (90.0%)
  June 2024: 19/21 (90.5%)
  July 2024: 20/22 (90.9%)
  August 2024: 18/19 (94.7%)
  September 2024: 21/22 (95.5%)
  October 2024: 20/21 (95.2%)
  November 2024: 19/20 (95.0%)
  December 2024: 18/18 (100%)
  January 2025: 17/18 (94.4%)

Subject-wise Attendance:
  English: 185/195 (94.9%)
  Mathematics: 180/195 (92.3%)
  Science: 178/195 (91.3%)
  Social Studies: 182/195 (93.3%)
  Hindi: 184/195 (94.4%)
  Computer Science: 75/78 (96.2%)

Leave Summary:
  Sick Leave: 7 days
  Casual Leave: 3 days
  Emergency Leave: 0 days
  Planned Leave: 0 days
  
Trends:
  - Consistent attendance (>90% monthly)
  - No concerning patterns
  - Good improvement in recent months
  - No action required
```

#### 5.2 Class Attendance Report

**Section-wise Analysis:**
```yaml
Grade: 6-A
Class Teacher: Mrs. Anjali Gupta
Total Students: 30
Report Period: January 2025

Class Attendance Statistics:
  Average Attendance: 91.2%
  Highest: 98.5% (Diya Patel)
  Lowest: 68.3% (Rahul Singh) ⚠️
  
Attendance Distribution:
  >95%: 8 students (26.7%) - Excellent
  90-95%: 12 students (40.0%) - Good
  85-90%: 6 students (20.0%) - Satisfactory
  75-85%: 3 students (10.0%) - Warning
  <75%: 1 student (3.3%) - Critical ⚠️

Defaulters (<75%):
  1. Rahul Singh (2024/06/0460): 68.3%
     - Action: Parent meeting scheduled
     - Reason: Frequent illness
     - Medical certificate: Submitted
     - Recommendation: Medical checkup

Students at Risk (75-80%):
  1. Amit Kumar: 78.2%
  2. Neha Gupta: 76.5%
  3. Vikram Reddy: 75.8%
  
  Action: Warning letters sent to parents

Monthly Trends:
  - Overall attendance improving
  - January: 91.2% (vs December: 89.5%)
  - Sick leave increased (winter season)
  - Late arrivals reduced
```

#### 5.3 Attendance Alerts & Notifications

**Automated Alert System:**
```yaml
Alert Type 1: Daily Absent Alert
  Trigger: Student marked absent without leave
  Recipients: Parents
  Method: SMS + Email
  Time: 9:00 AM (1 hour after school start)
  
  Example SMS:
  "Dear Parent, Rohan Kumar is marked ABSENT today (17/01/2024).
   If on leave, please apply via portal. - Hogwarts School"

Alert Type 2: Low Attendance Warning
  Trigger: Attendance falls below 80%
  Recipients: Parents + Class Teacher
  Method: Email + Portal Notification
  Frequency: Weekly
  
  Example Email:
  "Dear Mr. Patel,
   
   Aarav's attendance has fallen to 78.5%, below the required 75%.
   This may affect board exam eligibility.
   
   Please ensure regular attendance.
   
   Current Status: 157/200 days present
   Required: 150/200 (75%)
   
   Regards,
   Hogwarts School"

Alert Type 3: Critical Attendance Alert
  Trigger: Attendance falls below 75%
  Recipients: Parents + Principal + Class Teacher
  Method: SMS + Email + Phone Call
  Frequency: Immediate
  
  Action Required:
  - Parent meeting mandatory
  - Improvement plan required
  - Medical certificate if health issues
  - May affect board exam eligibility

Alert Type 4: Late Arrival Pattern
  Trigger: >3 late arrivals in a month
  Recipients: Parents
  Method: SMS + Email
  
  Example:
  "Dear Parent, Arjun has been late 3 times this month.
   Please ensure timely arrival. Repeated lates affect attendance."

Alert Type 5: Consecutive Absence
  Trigger: Absent for 3+ consecutive days without leave
  Recipients: Parents + Principal
  Method: Phone Call + SMS
  
  Action: Immediate contact required
```

---

## 📊 DATABASE SCHEMA

### Daily Attendance Table

```sql
CREATE TABLE daily_attendance (
  attendance_id BIGINT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Date & Session
  attendance_date DATE NOT NULL,
  academic_year VARCHAR(9) NOT NULL,
  day_of_week VARCHAR(10),
  
  -- Attendance Status
  attendance_status ENUM(
    'PRESENT', 'ABSENT', 'LATE', 'HALF_DAY',
    'SICK_LEAVE', 'CASUAL_LEAVE', 'EMERGENCY_LEAVE',
    'PLANNED_LEAVE', 'ON_DUTY', 'HOLIDAY', 'SUSPENDED'
  ) NOT NULL,
  
  -- Timing
  entry_time TIME,
  exit_time TIME,
  late_by_minutes INT DEFAULT 0,
  early_departure_minutes INT DEFAULT 0,
  
  -- Biometric
  biometric_entry BOOLEAN DEFAULT FALSE,
  biometric_exit BOOLEAN DEFAULT FALSE,
  entry_device_id VARCHAR(50),
  exit_device_id VARCHAR(50),
  
  -- Leave Details
  leave_application_id INT,
  leave_approved BOOLEAN DEFAULT FALSE,
  
  -- Marked By
  marked_by INT,
  marked_time TIMESTAMP,
  marking_method ENUM('MANUAL', 'BIOMETRIC', 'RFID', 'MOBILE_APP', 'BULK_UPLOAD') DEFAULT 'MANUAL',
  
  -- Attendance Value
  attendance_value DECIMAL(3,2) DEFAULT 1.00,
  
  -- Remarks
  remarks TEXT,
  parent_notified BOOLEAN DEFAULT FALSE,
  notification_time TIMESTAMP,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  FOREIGN KEY (leave_application_id) REFERENCES leave_applications(leave_application_id),
  
  -- Indexes
  INDEX idx_student_date (student_id, attendance_date),
  INDEX idx_date (attendance_date),
  INDEX idx_status (attendance_status),
  UNIQUE INDEX idx_student_attendance (student_id, attendance_date)
);
```

### Period Attendance Table

```sql
CREATE TABLE period_attendance (
  period_attendance_id BIGINT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Period Details
  attendance_date DATE NOT NULL,
  period_number INT NOT NULL,
  subject_id INT NOT NULL,
  teacher_id INT NOT NULL,
  
  -- Attendance
  status ENUM('PRESENT', 'ABSENT', 'LATE', 'EXCUSED') NOT NULL DEFAULT 'PRESENT',
  
  -- Timing
  marked_time TIMESTAMP,
  late_by_minutes INT DEFAULT 0,
  
  -- Reason (if absent)
  absence_reason VARCHAR(255),
  
  -- Marked By
  marked_by INT,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  FOREIGN KEY (subject_id) REFERENCES subjects(subject_id),
  FOREIGN KEY (teacher_id) REFERENCES teachers(teacher_id),
  
  -- Indexes
  INDEX idx_student_date (student_id, attendance_date),
  INDEX idx_subject (subject_id, attendance_date),
  INDEX idx_teacher (teacher_id, attendance_date),
  UNIQUE INDEX idx_period_attendance (student_id, attendance_date, period_number)
);
```

### Leave Applications Table

```sql
CREATE TABLE leave_applications (
  leave_application_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Leave Details
  leave_type ENUM('SICK', 'CASUAL', 'EMERGENCY', 'PLANNED', 'MEDICAL_APPOINTMENT') NOT NULL,
  from_date DATE NOT NULL,
  to_date DATE NOT NULL,
  total_days INT NOT NULL,
  
  -- Application
  reason TEXT NOT NULL,
  applied_by INT NOT NULL,
  applied_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  application_method ENUM('PORTAL', 'EMAIL', 'PHONE', 'IN_PERSON') DEFAULT 'PORTAL',
  
  -- Supporting Documents
  medical_certificate_uploaded BOOLEAN DEFAULT FALSE,
  medical_certificate_path VARCHAR(500),
  other_documents TEXT,
  
  -- Approval Workflow
  approval_status ENUM('PENDING', 'APPROVED', 'REJECTED', 'CANCELLED') DEFAULT 'PENDING',
  approved_by INT,
  approval_date TIMESTAMP,
  rejection_reason TEXT,
  
  -- Notifications
  parent_notified BOOLEAN DEFAULT FALSE,
  student_notified BOOLEAN DEFAULT FALSE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  notes TEXT,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_dates (from_date, to_date),
  INDEX idx_status (approval_status)
);
```

### Attendance Summary Table

```sql
CREATE TABLE attendance_summary (
  summary_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Period
  academic_year VARCHAR(9) NOT NULL,
  month VARCHAR(7),
  
  -- Statistics
  total_school_days INT NOT NULL,
  days_present INT DEFAULT 0,
  days_absent INT DEFAULT 0,
  days_late INT DEFAULT 0,
  days_half_day INT DEFAULT 0,
  days_on_duty INT DEFAULT 0,
  days_sick_leave INT DEFAULT 0,
  days_casual_leave INT DEFAULT 0,
  
  -- Calculated
  attendance_percentage DECIMAL(5,2),
  attendance_value DECIMAL(6,2),
  
  -- Status
  status ENUM('EXCELLENT', 'GOOD', 'SATISFACTORY', 'WARNING', 'CRITICAL') NOT NULL,
  board_eligible BOOLEAN DEFAULT TRUE,
  
  -- Alerts
  alert_sent BOOLEAN DEFAULT FALSE,
  alert_date DATE,
  
  -- Metadata
  last_updated TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_year (student_id, academic_year),
  INDEX idx_status (status),
  UNIQUE INDEX idx_student_period (student_id, academic_year, month)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Attendance Percentage Calculation

```javascript
FUNCTION calculate_attendance_percentage(student_id, from_date, to_date) {
  // Get all attendance records
  attendance_records = QUERY("
    SELECT attendance_status, attendance_value
    FROM daily_attendance
    WHERE student_id = ?
    AND attendance_date BETWEEN ? AND ?
    AND attendance_status NOT IN ('HOLIDAY', 'SUSPENDED')
  ", [student_id, from_date, to_date])
  
  total_days = 0
  present_value = 0
  
  FOR each record IN attendance_records {
    total_days++
    
    // Calculate attendance value
    IF record.attendance_status IN ['PRESENT', 'ON_DUTY'] {
      present_value += 1.0
    } ELSE IF record.attendance_status IN ['LATE', 'HALF_DAY'] {
      present_value += 0.5
    } ELSE IF record.attendance_status IN ['SICK_LEAVE', 'CASUAL_LEAVE', 'EMERGENCY_LEAVE', 'PLANNED_LEAVE'] {
      // Approved leaves count as present for percentage calculation
      present_value += 1.0
    } ELSE IF record.attendance_status == 'ABSENT' {
      present_value += 0.0
    }
  }
  
  IF total_days == 0 {
    RETURN {percentage: 0, status: 'NO_DATA'}
  }
  
  percentage = (present_value / total_days) * 100
  percentage = ROUND(percentage, 2)
  
  // Determine status
  status = NULL
  board_eligible = TRUE
  
  IF percentage >= 95 {
    status = 'EXCELLENT'
  } ELSE IF percentage >= 90 {
    status = 'GOOD'
  } ELSE IF percentage >= 85 {
    status = 'SATISFACTORY'
  } ELSE IF percentage >= 75 {
    status = 'WARNING'
  } ELSE {
    status = 'CRITICAL'
    board_eligible = FALSE
  }
  
  // Send alerts if needed
  IF percentage < 80 AND NOT alert_sent_recently(student_id) {
    SEND_ATTENDANCE_ALERT(student_id, percentage)
  }
  
  // Update summary table
  UPDATE attendance_summary SET
    total_school_days = total_days,
    attendance_percentage = percentage,
    attendance_value = present_value,
    status = status,
    board_eligible = board_eligible,
    last_updated = NOW()
  WHERE student_id = student_id
  AND academic_year = GET_ACADEMIC_YEAR(from_date)
  
  RETURN {
    total_days: total_days,
    present_value: present_value,
    percentage: percentage,
    status: status,
    board_eligible: board_eligible
  }
}
```

### Rule 2: Leave Application Validation

```javascript
FUNCTION validate_leave_application(student_id, leave_type, from_date, to_date, reason) {
  // Calculate total days
  total_days = DAYS_BETWEEN(from_date, to_date) + 1
  
  // Check if dates are valid
  IF from_date > to_date {
    RETURN {valid: FALSE, error: "From date cannot be after to date"}
  }
  
  IF from_date < CURRENT_DATE() - 7 {
    RETURN {valid: FALSE, error: "Cannot apply for leave more than 7 days in the past"}
  }
  
  // Check for overlapping leave applications
  overlapping = QUERY("
    SELECT * FROM leave_applications
    WHERE student_id = ?
    AND approval_status IN ('PENDING', 'APPROVED')
    AND (
      (from_date BETWEEN ? AND ?)
      OR (to_date BETWEEN ? AND ?)
      OR (? BETWEEN from_date AND to_date)
    )
  ", [student_id, from_date, to_date, from_date, to_date, from_date])
  
  IF overlapping.exists() {
    RETURN {
      valid: FALSE,
      error: "Overlapping leave application exists",
      existing_application: overlapping.leave_application_id
    }
  }
  
  // Check leave balance
  academic_year = GET_ACADEMIC_YEAR(from_date)
  
  IF leave_type == 'CASUAL' {
    casual_leaves_used = QUERY("
      SELECT SUM(total_days) as total
      FROM leave_applications
      WHERE student_id = ?
      AND leave_type = 'CASUAL'
      AND approval_status = 'APPROVED'
      AND from_date >= ?
    ", [student_id, GET_ACADEMIC_YEAR_START(academic_year)])
    
    IF casual_leaves_used.total + total_days > 15 {
      RETURN {
        valid: FALSE,
        error: "Casual leave limit exceeded",
        used: casual_leaves_used.total,
        limit: 15,
        requested: total_days
      }
    }
  }
  
  // Check if leave during exams
  exam_dates = GET_EXAM_DATES(student_id, from_date, to_date)
  IF exam_dates.length > 0 AND leave_type != 'SICK' {
    RETURN {
      valid: FALSE,
      error: "Leave during exam period not allowed (except medical emergency)",
      exam_dates: exam_dates
    }
  }
  
  // Check if medical certificate required
  IF leave_type == 'SICK' AND total_days > 3 {
    RETURN {
      valid: TRUE,
      medical_certificate_required: TRUE,
      message: "Medical certificate mandatory for sick leave >3 days"
    }
  }
  
  // Check reason length
  IF reason.length < 20 {
    RETURN {
      valid: FALSE,
      error: "Please provide detailed reason (minimum 20 characters)"
    }
  }
  
  // All validations passed
  RETURN {
    valid: TRUE,
    total_days: total_days,
    approval_required_by: GET_APPROVAL_AUTHORITY(leave_type, total_days)
  }
}
```

### Rule 3: Automatic Attendance Alerts

```javascript
FUNCTION send_attendance_alerts() {
  // Run daily at 9:00 AM
  today = CURRENT_DATE()
  
  // Alert 1: Daily absent without leave
  absent_students = QUERY("
    SELECT da.*, s.full_name, s.admission_number,
           f.father_mobile, f.mother_mobile, f.father_email
    FROM daily_attendance da
    JOIN students s ON da.student_id = s.student_id
    JOIN families f ON s.family_id = f.family_id
    WHERE da.attendance_date = ?
    AND da.attendance_status = 'ABSENT'
    AND da.leave_application_id IS NULL
    AND da.parent_notified = FALSE
  ", [today])
  
  FOR each student IN absent_students {
    // Send SMS
    SEND_SMS(student.father_mobile, 
      `Dear Parent, ${student.full_name} is marked ABSENT today (${today}). 
       If on leave, please apply via portal. - Hogwarts School`)
    
    // Send Email
    SEND_EMAIL(student.father_email, {
      subject: "Attendance Alert - Student Absent",
      template: "daily_absent_alert",
      data: {
        student_name: student.full_name,
        date: today,
        admission_number: student.admission_number
      }
    })
    
    // Update notification flag
    UPDATE daily_attendance SET
      parent_notified = TRUE,
      notification_time = NOW()
    WHERE attendance_id = student.attendance_id
  }
  
  // Alert 2: Low attendance warning (weekly check on Fridays)
  IF GET_DAY_OF_WEEK(today) == 'FRIDAY' {
    low_attendance_students = QUERY("
      SELECT s.*, asm.attendance_percentage,
             f.father_mobile, f.father_email
      FROM students s
      JOIN attendance_summary asm ON s.student_id = asm.student_id
      JOIN families f ON s.family_id = f.family_id
      WHERE asm.academic_year = ?
      AND asm.attendance_percentage < 80
      AND asm.attendance_percentage >= 75
      AND (asm.alert_sent = FALSE OR asm.alert_date < DATE_SUB(NOW(), INTERVAL 7 DAY))
    ", [GET_CURRENT_ACADEMIC_YEAR()])
    
    FOR each student IN low_attendance_students {
      SEND_EMAIL(student.father_email, {
        subject: "Low Attendance Warning",
        template: "low_attendance_warning",
        data: {
          student_name: student.full_name,
          attendance_percentage: student.attendance_percentage,
          required_percentage: 75
        }
      })
      
      UPDATE attendance_summary SET
        alert_sent = TRUE,
        alert_date = today
      WHERE student_id = student.student_id
    }
  }
  
  // Alert 3: Critical attendance (immediate)
  critical_students = QUERY("
    SELECT s.*, asm.attendance_percentage,
           f.father_mobile, f.father_email
    FROM students s
    JOIN attendance_summary asm ON s.student_id = asm.student_id
    JOIN families f ON s.family_id = f.family_id
    WHERE asm.academic_year = ?
    AND asm.attendance_percentage < 75
    AND asm.board_eligible = FALSE
  ", [GET_CURRENT_ACADEMIC_YEAR()])
  
  FOR each student IN critical_students {
    // Send SMS
    SEND_SMS(student.father_mobile,
      `URGENT: ${student.full_name}'s attendance is ${student.attendance_percentage}% 
       (below 75% required). Board exam eligibility at risk. Contact school immediately.`)
    
    // Send Email
    SEND_EMAIL(student.father_email, {
      subject: "URGENT: Critical Attendance Alert",
      template: "critical_attendance_alert",
      priority: "HIGH"
    })
    
    // Notify Principal and Class Teacher
    SEND_NOTIFICATION("principal", {
      title: "Critical Attendance Alert",
      message: `${student.full_name} attendance: ${student.attendance_percentage}%`,
      priority: "HIGH"
    })
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Communication Module**: SMS/email alerts to parents
2. **Academic Module**: Attendance-based exam eligibility
3. **Fee Management**: Attendance-based fee concessions
4. **Discipline Module**: Attendance in disciplinary records
5. **Reports Module**: Attendance reports and analytics

### Inbound Integrations

1. **Biometric System**: Automated attendance marking
2. **Parent Portal**: Leave applications from parents
3. **Mobile App**: Real-time attendance updates
4. **Timetable Module**: Period-wise attendance tracking

---

## 👥 USER WORKFLOWS

### Workflow 1: Daily Attendance Marking by Class Teacher

**Actor:** Class Teacher  
**Duration:** 5-10 minutes  
**Trigger:** Morning assembly (8:00-8:15 AM)

**Steps:**

1. Login to Teacher Portal
2. Navigate to: Attendance → Daily Attendance
3. Select: Grade 6-A, Date: 17/01/2024
4. System displays student list (30 students)
5. Mark attendance for each student:
   - Present: 28 students (default)
   - Absent: Rohan Kumar, Priya Sharma
   - Late: Arjun Verma (arrived 8:20 AM)
6. Add remarks if needed
7. Click "Submit Attendance"
8. System validates and saves
9. System sends SMS to absent students' parents
10. Confirmation displayed

**Expected Outcome:** Daily attendance recorded, parents notified

---

### Workflow 2: Leave Application by Parent

**Actor:** Parent  
**Duration:** 10-15 minutes  
**Trigger:** Student needs leave

**Steps:**

1. Login to Parent Portal
2. Navigate to: My Child → Leave Application
3. Click "Apply for Leave"
4. Fill form:
   - Leave Type: Sick Leave
   - From: 20/01/2024
   - To: 22/01/2024
   - Reason: Viral fever
5. Upload medical certificate
6. Submit application
7. System validates dates
8. Application sent to class teacher
9. Class teacher approves
10. Parent receives approval SMS
11. Attendance auto-marked for those dates

**Expected Outcome:** Leave approved, attendance updated

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 17, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade  
**Compliance:** CBSE/ICSE 75% Attendance Requirement

# PERIOD SUBSTITUTION - COMPLETE DOCUMENTATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-SUBSTITUTE-004  
**Category:** Operations Management  
**Priority:** Critical (P0)  
**Owner:** Academic Operations Team

---

## 📋 OVERVIEW

The Period Substitution submodule manages teacher absences by automatically finding qualified substitute teachers, notifying all stakeholders, tracking substitution history, managing substitute payments, and ensuring minimal disruption to academic schedules.

### Purpose

Handle teacher absences efficiently, find qualified substitutes automatically, notify students and parents immediately, track substitution records for payroll, maintain academic continuity, and provide analytics on substitution patterns.

### Scope

- **Automated Substitute Finding**: AI-powered teacher matching
- **Real-Time Notifications**: SMS, email, app push to all stakeholders
- **Qualification Matching**: Ensure substitute is qualified
- **Workload Balancing**: Fair distribution of substitute duties
- **Payment Tracking**: Substitute compensation management
- **Emergency Coverage**: Same-day absence handling
- **Substitute Pool Management**: Maintain available teachers
- **Analytics & Reporting**: Substitution patterns and costs

---

## 🎯 KEY FEATURES

### 1. Automated Substitute Teacher Finding

#### 1.1 Substitute Selection Algorithm

**Smart Matching System:**
```javascript
FUNCTION find_substitute_teacher(absent_teacher, date, periods) {
  substitutes_found = []
  
  FOR each period IN periods {
    // Step 1: Find teachers with same subject expertise
    qualified_teachers = QUERY("
      SELECT t.* FROM teachers t
      JOIN teacher_subjects ts ON t.teacher_id = ts.teacher_id
      WHERE ts.subject_id = ?
      AND t.teacher_id != ?
      AND t.status = 'ACTIVE'
    ", [period.subject_id, absent_teacher.id])
    
    // Step 2: Filter by availability (free period)
    available = []
    FOR each teacher IN qualified_teachers {
      is_free = CHECK_FREE_PERIOD(teacher, date, period.time)
      IF is_free {
        available.ADD(teacher)
      }
    }
    
    IF available.length == 0 {
      // No qualified substitute available
      substitutes_found.ADD({
        period: period,
        substitute: NULL,
        status: 'NO_SUBSTITUTE_FOUND',
        action: 'CANCEL_CLASS'
      })
      CONTINUE
    }
    
    // Step 3: Score candidates
    scored_candidates = []
    FOR each candidate IN available {
      score = 0
      
      // Expertise level (0-30 points)
      expertise = GET_EXPERTISE_LEVEL(candidate, period.subject, period.grade)
      IF expertise == 'EXPERT': score += 30
      ELSE IF expertise == 'HIGHLY_QUALIFIED': score += 20
      ELSE IF expertise == 'QUALIFIED': score += 10
      
      // Substitution count this month (0-25 points)
      sub_count = GET_SUBSTITUTION_COUNT(candidate, CURRENT_MONTH())
      score += MAX(0, 25 - sub_count * 2) // Fewer subs = higher score
      
      // Same grade level experience (0-20 points)
      IF HAS_TAUGHT_GRADE(candidate, period.grade): score += 20
      
      // Workload (0-15 points)
      current_load = GET_CURRENT_WORKLOAD(candidate)
      workload_percentage = (current_load / candidate.max_periods) * 100
      IF workload_percentage < 80: score += 15
      ELSE IF workload_percentage < 90: score += 10
      ELSE: score += 5
      
      // Preference (0-10 points)
      IF period.time IN candidate.preferred_slots: score += 10
      
      scored_candidates.ADD({
        teacher: candidate,
        score: score
      })
    }
    
    // Step 4: Select best candidate
    scored_candidates = SORT(scored_candidates, by: score, desc: TRUE)
    best_substitute = scored_candidates[0].teacher
    
    substitutes_found.ADD({
      period: period,
      substitute: best_substitute,
      score: scored_candidates[0].score,
      status: 'SUBSTITUTE_ASSIGNED'
    })
  }
  
  RETURN substitutes_found
}
```

#### 1.2 Real-World Substitution Example

**Complete Substitution Workflow:**
```yaml
Scenario: Teacher Absence - Same Day

Date: Monday, January 20, 2026
Time: 7:30 AM (30 minutes before school starts)
Absent Teacher: Mrs. Sharma (English, Grade 9)
Reason: Sudden illness
Notification: SMS to coordinator

Affected Classes:
  1. Grade 9A - Period 2 (9:00-9:45 AM) - English
  2. Grade 9B - Period 4 (10:45-11:30 AM) - English
  3. Grade 9C - Period 6 (12:45-1:30 PM) - English

Automated Substitution Process:

7:32 AM - System Triggered:
  - Absence recorded in system
  - Affected periods identified: 3
  - Substitute search initiated

7:33 AM - Period 1 Analysis (9A, P2):
  Qualified Teachers (English):
    - Mr. Gupta (English, 15 years exp)
    - Ms. Kapoor (English, 8 years exp)
    - Mr. Verma (English, 5 years exp)
  
  Availability Check (9:00-9:45 AM):
    - Mr. Gupta: FREE ✓
    - Ms. Kapoor: Teaching 10A ✗
    - Mr. Verma: FREE ✓
  
  Scoring:
    Mr. Gupta:
      - Expertise: Expert (30 points)
      - Substitutions this month: 2 (21 points)
      - Grade 9 experience: Yes (20 points)
      - Workload: 24/30 = 80% (15 points)
      - Preference: Morning slot (10 points)
      Total: 96 points
    
    Mr. Verma:
      - Expertise: Qualified (10 points)
      - Substitutions this month: 5 (15 points)
      - Grade 9 experience: Yes (20 points)
      - Workload: 28/30 = 93% (5 points)
      - Preference: Not preferred (0 points)
      Total: 50 points
  
  Selected: Mr. Gupta (highest score)

7:34 AM - Period 2 Analysis (9B, P4):
  Available: Mr. Verma (Mr. Gupta already assigned)
  Selected: Mr. Verma

7:35 AM - Period 3 Analysis (9C, P6):
  Available: Mr. Gupta (free again)
  Selected: Mr. Gupta

7:36 AM - Notifications Sent:

  To Mr. Gupta (SMS + App):
    "Substitute duty today: Grade 9A English (P2, 9:00 AM, Room 206) 
    and Grade 9C English (P6, 12:45 PM, Room 208). 
    Mrs. Sharma absent. Lesson plan: Chapter 5 revision."
  
  To Mr. Verma (SMS + App):
    "Substitute duty today: Grade 9B English (P4, 10:45 AM, Room 207). 
    Mrs. Sharma absent. Lesson plan: Chapter 5 revision."
  
  To Grade 9A Students (App Push):
    "Schedule change: English Period 2 - Mr. Gupta (substitute). 
    Room 206, 9:00 AM."
  
  To Grade 9A Parents (SMS + Email):
    "Hogwarts: English class today will be taught by Mr. Gupta 
    (substitute for Mrs. Sharma)."
  
  [Similar notifications for 9B and 9C]

7:40 AM - Confirmation:
  - Mr. Gupta: Confirmed ✓
  - Mr. Verma: Confirmed ✓
  - Timetable updated
  - Attendance system updated

9:00 AM - Classes Conducted:
  - Grade 9A: Mr. Gupta teaching
  - Students informed, no confusion
  - Academic continuity maintained

End of Day - Records Updated:
  - Substitution logged for payroll
  - Mr. Gupta: +2 substitute periods
  - Mr. Verma: +1 substitute period
  - Compensation: ₹500 per period

Result: ✅ All classes covered, zero disruption
```

---

### 2. Notification System

#### 2.1 Multi-Channel Notifications

**Comprehensive Stakeholder Communication:**
```yaml
Notification Recipients:

1. Substitute Teacher:
   Channels: SMS + App Push + Email
   Priority: URGENT
   Timing: Immediate (within 2 minutes)
   
   Content:
     - Period details (time, room, section)
     - Subject and topic
     - Lesson plan reference
     - Student strength
     - Special instructions
     - Confirmation required
   
   Example SMS:
     "URGENT: Substitute duty today - Grade 9A English, 
     Period 2 (9:00 AM), Room 206. 36 students. 
     Topic: Chapter 5 revision. Reply YES to confirm."

2. Students (Affected Sections):
   Channels: App Push + In-App Alert
   Priority: HIGH
   Timing: 30 min before period
   
   Content:
     - Teacher change notification
     - Substitute teacher name
     - Room confirmation
     - Time confirmation
   
   Example Push:
     "Schedule Update: English Period 2 today - 
     Mr. Gupta (substitute for Mrs. Sharma). 
     Room 206, 9:00 AM."

3. Parents:
   Channels: SMS + Email
   Priority: MEDIUM
   Timing: 1 hour before period
   
   Content:
     - Teacher absence notification
     - Substitute teacher details
     - Assurance of continuity
   
   Example SMS:
     "Hogwarts: Aarav's English class today (9:00 AM) 
     will be taught by Mr. Gupta (substitute). 
     Mrs. Sharma on leave."

4. Coordinator:
   Channels: App Push + Email
   Priority: HIGH
   Timing: Immediate
   
   Content:
     - Absence summary
     - Substitutes assigned
     - Any unassigned periods
     - Action required (if any)
   
   Example:
     "Mrs. Sharma absent today. 
     3 periods covered: Mr. Gupta (2), Mr. Verma (1). 
     All classes assigned successfully."

5. Principal (if critical):
   Channels: Email
   Priority: MEDIUM
   Timing: Daily summary
   
   Content:
     - Daily absence report
     - Substitution summary
     - Unassigned classes (if any)
```

---

### 3. Substitute Payment Tracking

#### 3.1 Compensation Management

**Payment Calculation System:**
```yaml
Substitute Payment Structure:

Regular Substitute Period:
  Rate: ₹500 per period (45 minutes)
  Calculation: Periods × ₹500
  
Double Period (Lab):
  Rate: ₹1,000 per double period (90 minutes)
  Calculation: Double periods × ₹1,000

Emergency Substitute (< 2 hours notice):
  Rate: ₹750 per period (50% premium)
  Calculation: Periods × ₹750

Weekend Substitute:
  Rate: ₹600 per period (20% premium)
  Calculation: Periods × ₹600

Monthly Calculation Example:

Mr. Gupta - January 2026:
  Regular Substitutes: 8 periods × ₹500 = ₹4,000
  Emergency Substitutes: 2 periods × ₹750 = ₹1,500
  Lab Substitutes: 1 double × ₹1,000 = ₹1,000
  Weekend Substitutes: 1 period × ₹600 = ₹600
  ────────────────────────────────────────────
  Total Substitute Earnings: ₹7,100
  
  Regular Salary: ₹45,000
  Total Compensation: ₹52,100

Payment Processing:
  - Tracked automatically in system
  - Verified by coordinator
  - Approved by principal
  - Processed with monthly salary
  - Payslip shows breakdown
```

---

## 📊 DATABASE SCHEMA

### Substitute Assignments Table

```sql
CREATE TABLE substitute_assignments (
  assignment_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Absence Details
  absent_teacher_id INT NOT NULL,
  absence_date DATE NOT NULL,
  absence_reason VARCHAR(200),
  
  -- Period Details
  period_number INT NOT NULL,
  start_time TIME NOT NULL,
  end_time TIME NOT NULL,
  
  -- Class Details
  section_id INT NOT NULL,
  subject_id INT NOT NULL,
  room_id INT,
  
  -- Substitute Details
  substitute_teacher_id INT,
  assignment_status ENUM(
    'SEARCHING',
    'ASSIGNED',
    'CONFIRMED',
    'COMPLETED',
    'CANCELLED',
    'NO_SUBSTITUTE_FOUND'
  ) NOT NULL,
  
  -- Matching Score
  match_score DECIMAL(5,2),
  
  -- Notifications
  notifications_sent BOOLEAN DEFAULT FALSE,
  notification_timestamp TIMESTAMP,
  
  -- Confirmation
  confirmed_by_substitute BOOLEAN DEFAULT FALSE,
  confirmation_timestamp TIMESTAMP,
  
  -- Completion
  class_conducted BOOLEAN DEFAULT FALSE,
  attendance_marked BOOLEAN DEFAULT FALSE,
  
  -- Payment
  payment_amount DECIMAL(10,2),
  payment_status ENUM('PENDING', 'APPROVED', 'PAID') DEFAULT 'PENDING',
  
  -- Metadata
  created_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  assigned_by INT,
  
  -- Foreign Keys
  FOREIGN KEY (absent_teacher_id) REFERENCES teachers(teacher_id),
  FOREIGN KEY (substitute_teacher_id) REFERENCES teachers(teacher_id),
  FOREIGN KEY (section_id) REFERENCES sections(section_id),
  FOREIGN KEY (subject_id) REFERENCES subjects(subject_id),
  
  -- Indexes
  INDEX idx_absence_date (absence_date),
  INDEX idx_absent_teacher (absent_teacher_id),
  INDEX idx_substitute (substitute_teacher_id),
  INDEX idx_status (assignment_status)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Substitute Eligibility

```javascript
FUNCTION is_eligible_for_substitute(teacher, subject, grade, time) {
  // Rule 1: Must be qualified for subject
  IF NOT IS_QUALIFIED(teacher, subject, grade) {
    RETURN {eligible: FALSE, reason: "Not qualified"}
  }
  
  // Rule 2: Must have free period
  IF NOT HAS_FREE_PERIOD(teacher, time) {
    RETURN {eligible: FALSE, reason: "Not available"}
  }
  
  // Rule 3: Workload limit check
  current_load = GET_CURRENT_WORKLOAD(teacher)
  IF current_load >= teacher.max_periods_per_week {
    RETURN {eligible: FALSE, reason: "At capacity"}
  }
  
  // Rule 4: Not on leave
  IF IS_ON_LEAVE(teacher, time.date) {
    RETURN {eligible: FALSE, reason: "On leave"}
  }
  
  RETURN {eligible: TRUE}
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations
1. **Notification Service**: Send SMS, email, push notifications
2. **Payroll Module**: Substitute payment data
3. **Attendance Module**: Update attendance records
4. **Analytics Module**: Substitution patterns

### Inbound Integrations
1. **Teacher Management**: Teacher availability, qualifications
2. **Leave Management**: Teacher absence data
3. **Master Timetable**: Period schedules

---

## 👥 USER WORKFLOWS

### Workflow: Handle Same-Day Teacher Absence

**Actor:** System (Automated) + Coordinator  
**Duration:** 5-10 minutes  
**Frequency:** As needed

**Steps:**
1. Teacher calls in sick (7:30 AM)
2. Coordinator marks absent in system
3. System identifies affected periods (3)
4. System searches for substitutes
5. System assigns best matches
6. Notifications sent to all stakeholders
7. Substitutes confirm via SMS
8. Timetable updated automatically
9. Classes conducted normally
10. Substitution logged for payment

**Expected Outcome:** All classes covered with qualified substitutes, minimal disruption.

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 1.0  
**Documentation Standard:** Enterprise Grade

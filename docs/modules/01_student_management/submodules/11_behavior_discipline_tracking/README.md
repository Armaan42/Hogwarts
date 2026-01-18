# BEHAVIOR & DISCIPLINE TRACKING - COMPLETE DOCUMENTATION

**Module:** Student Management  
**Submodule Code:** STD-BEHAVIOR-011  
**Category:** Student Conduct  
**Priority:** High (P1)  
**Owner:** Discipline Committee & Counseling Department

---

## 📋 OVERVIEW

The Behavior & Discipline Tracking submodule manages comprehensive records of student conduct including positive behaviors, behavioral incidents, disciplinary actions, counseling interventions, behavior improvement plans, parent communications, and ensures fair and consistent discipline procedures aligned with school policies.

### Purpose

Track student behavior patterns, document incidents, implement progressive discipline, maintain behavioral records, coordinate counseling interventions, communicate with parents, ensure procedural fairness, and promote positive behavior through recognition and support systems.

### Scope

- **Incident Tracking**: Behavioral violations and conflicts
- **Disciplinary Actions**: Warnings, detentions, suspensions
- **Positive Behavior**: Recognition and rewards
- **Counseling**: Behavioral interventions
- **Behavior Plans**: Individual behavior improvement plans
- **Parent Communication**: Incident notifications
- **Appeals Process**: Disciplinary appeal procedures

---

## 🎯 KEY FEATURES

### 1. Behavioral Incident Management

#### 1.1 Incident Categories

**Minor Incidents:**
```yaml
Category: MINOR
Severity: Level 1
Examples:
  - Talking in class
  - Not completing homework
  - Dress code violation
  - Late to class (without valid reason)
  - Chewing gum
  - Using mobile phone in class

Typical Consequences:
  - Verbal warning
  - Written warning
  - Parent notification
  - Detention (1 hour)
  - Confiscation of item

Escalation: After 3 minor incidents → Major incident
```

**Major Incidents:**
```yaml
Category: MAJOR
Severity: Level 2
Examples:
  - Disrespect to teacher
  - Bullying (verbal)
  - Cheating in exam
  - Skipping class
  - Repeated minor violations
  - Vandalism (minor)
  - Fighting (minor)

Typical Consequences:
  - Parent meeting
  - Detention (multiple sessions)
  - In-school suspension (1-3 days)
  - Behavioral contract
  - Counseling mandatory
  - Community service

Escalation: After 2 major incidents → Serious incident
```

**Serious Incidents:**
```yaml
Category: SERIOUS
Severity: Level 3
Examples:
  - Physical violence
  - Bullying (severe/repeated)
  - Substance abuse
  - Theft
  - Vandalism (major)
  - Sexual harassment
  - Weapons possession
  - Threatening behavior

Typical Consequences:
  - Out-of-school suspension (3-15 days)
  - Police involvement (if required)
  - Mandatory counseling
  - Behavior improvement plan
  - Possible expulsion hearing
  - Transfer recommendation

Escalation: May lead to expulsion
```

#### 1.2 Incident Recording

**Incident Report:**
```yaml
Incident ID: INC-2024-0456
Date: 17/01/2024
Time: 10:30 AM
Location: Classroom 6-A

Student Involved:
  Primary: Rohan Kumar (2024/06/0457)
  Others: Arjun Verma (2024/06/0459)

Incident Type: Physical Altercation
Category: MAJOR
Severity: Level 2

Description:
  During Mathematics class, Rohan and Arjun got into a physical
  fight over a pencil. Rohan pushed Arjun, who retaliated by
  hitting Rohan. The fight lasted approximately 30 seconds before
  the teacher intervened.

Reported By: Mrs. Anjali Gupta (Class Teacher)
Witnesses:
  - Mr. Suresh Kumar (Mathematics Teacher)
  - 28 students (classmates)
  - CCTV footage available

Immediate Action Taken:
  - Students separated immediately
  - Sent to Principal's office
  - Parents called
  - First aid provided (minor bruises)

Investigation:
  Conducted By: Vice Principal
  Date: 17/01/2024
  
  Rohan's Statement:
    "Arjun took my pencil without asking. When I asked for it back,
     he refused. I got angry and pushed him."
  
  Arjun's Statement:
    "I borrowed the pencil because mine broke. Rohan pushed me first,
     so I defended myself."
  
  Teacher's Observation:
    "Both students were at fault. Rohan initiated physical contact,
     but Arjun escalated by hitting back instead of reporting."
  
  CCTV Review:
    Confirms both students' involvement. Rohan pushed first,
    Arjun retaliated with a punch.

Disciplinary Decision:
  Committee: Discipline Committee
  Meeting Date: 17/01/2024 at 2:00 PM
  Members: Principal, Vice Principal, Counselor, Class Teacher
  
  Decision:
    Both Students:
      - 2-day in-school suspension
      - Written apology to each other
      - Behavioral counseling (3 sessions each)
      - Parent meeting mandatory
      - Behavioral contract
      
    Rohan (Initiator):
      - Additional 1-day detention
      - Anger management sessions
      
    Arjun:
      - Conflict resolution training

Parent Communication:
  Rohan's Parents:
    - Called: 17/01/2024 at 11:00 AM
    - Meeting: 18/01/2024 at 4:00 PM
    - Email sent with incident details
    
  Arjun's Parents:
    - Called: 17/01/2024 at 11:00 AM
    - Meeting: 18/01/2024 at 4:00 PM
    - Email sent with incident details

Follow-up:
  - Counseling sessions scheduled
  - Progress monitoring: Weekly
  - Review date: 31/01/2024
  - Behavioral contract signed: 19/01/2024

Status: RESOLVED
Closed Date: 31/01/2024
Outcome: Both students completed consequences, showing improvement
```

---

### 2. Disciplinary Actions

#### 2.1 Progressive Discipline System

**Discipline Ladder:**
```yaml
Level 1: Verbal Warning
  Issued For: First-time minor offense
  Issued By: Teacher
  Documentation: Informal note
  Parent Notification: Optional
  
  Example:
    Student: Priya Sharma
    Offense: Talking during class
    Action: Verbal reminder
    Date: 15/01/2024

Level 2: Written Warning
  Issued For: Repeated minor offense
  Issued By: Teacher/Class Teacher
  Documentation: Written warning form
  Parent Notification: Yes (email)
  
  Example:
    Student: Priya Sharma
    Offense: Talking during class (3rd time)
    Action: Written warning
    Parent Email: Sent 16/01/2024
    Date: 16/01/2024

Level 3: Detention
  Issued For: Continued minor offenses or single major offense
  Duration: 1-3 hours after school
  Issued By: Class Teacher/Principal
  Documentation: Detention slip
  Parent Notification: Yes (phone + email)
  
  Example:
    Student: Priya Sharma
    Offense: Continued disruption
    Action: 2-hour detention
    Date: 17/01/2024, 3:30-5:30 PM
    Activity: Essay on classroom behavior
    Parent Called: Yes

Level 4: In-School Suspension (ISS)
  Issued For: Major offense
  Duration: 1-3 days
  Issued By: Principal
  Documentation: Suspension letter
  Parent Notification: Yes (meeting required)
  
  Details:
    - Student isolated in separate room
    - Completes regular classwork
    - No participation in activities
    - Behavioral counseling
    - Parent meeting mandatory
  
  Example:
    Student: Rohan Kumar
    Offense: Fighting
    Duration: 2 days (18-19 Jan 2024)
    Supervision: Vice Principal
    Work: All assignments completed
    Counseling: 2 sessions during ISS

Level 5: Out-of-School Suspension (OSS)
  Issued For: Serious offense
  Duration: 3-15 days
  Issued By: Principal (with committee approval)
  Documentation: Formal suspension letter
  Parent Notification: Yes (in-person meeting)
  
  Details:
    - Student not allowed on campus
    - Responsible for missed work
    - Mandatory counseling before return
    - Behavioral contract required
    - Probation period after return
  
  Example:
    Student: Amit Kumar
    Offense: Substance abuse (smoking)
    Duration: 5 days (20-24 Jan 2024)
    Conditions for Return:
      - Counseling completion
      - Parent meeting
      - Behavioral contract
      - Random checks for 3 months

Level 6: Expulsion Hearing
  Issued For: Extremely serious or repeated serious offenses
  Process: Formal hearing with board
  Issued By: School Board
  Documentation: Complete case file
  Parent Notification: Formal notice
  
  Possible Outcomes:
    - Expulsion (permanent removal)
    - Transfer to another school
    - Extended suspension with conditions
    - Probation with strict monitoring
```

#### 2.2 Detention Management

**Detention Record:**
```yaml
Detention ID: DET-2024-0123
Student: Priya Sharma (2024/06/0458)
Date: 17/01/2024
Time: 3:30 PM - 5:30 PM (2 hours)
Location: Detention Room (Room 105)

Reason: Repeated disruption in class (3rd offense)
Issued By: Mrs. Anjali Gupta (Class Teacher)
Approved By: Principal

Detention Activities:
  1. Reflective Essay:
     Topic: "Importance of Classroom Discipline"
     Length: 500 words
     Completed: Yes
     
  2. Behavioral Worksheet:
     Topic: Self-control strategies
     Completed: Yes
     
  3. Apology Letter:
     To: Mrs. Anjali Gupta
     Completed: Yes

Supervision: Mr. Rajesh Kumar (Detention Monitor)

Attendance:
  Arrival: 3:30 PM ✓
  Departure: 5:30 PM ✓
  Behavior During Detention: Cooperative

Parent Notification:
  Called: 17/01/2024 at 12:00 PM
  Email: Sent with detention details
  Parent Acknowledgment: Received

Follow-up:
  - Essay reviewed by teacher
  - Behavioral improvement noted
  - No further incidents for 2 weeks
  
Status: COMPLETED
Outcome: Positive behavior change observed
```

---

### 3. Positive Behavior Recognition

#### 3.1 Reward System

**Positive Behavior Points:**
```yaml
Student: Aarav Patel (2024/06/0456)
Academic Year: 2024-25

Points System:
  Earning Points:
    - Helping classmate: +5 points
    - Perfect attendance (week): +10 points
    - Excellent homework: +3 points
    - Class participation: +2 points
    - Leadership: +10 points
    - Community service: +20 points
    
  Redeeming Points:
    - Free dress day: 50 points
    - Homework pass: 30 points
    - Lunch with principal: 100 points
    - School store voucher: 25 points
    - Certificate of excellence: 150 points

Current Points: 285
Total Earned: 450
Total Redeemed: 165

Recent Earnings:
  15/01/2024: Helped new student (+5)
  16/01/2024: Perfect attendance week (+10)
  17/01/2024: Excellent project (+15)
  
Redemptions:
  10/01/2024: Homework pass (-30)
  14/01/2024: School store voucher (-25)

Rank: Top 10% in grade
```

#### 3.2 Awards & Recognition

**Monthly Awards:**
```yaml
Award: Student of the Month
Month: January 2024
Recipient: Diya Patel (2018/08/0123)

Selection Criteria:
  - Academic Excellence: 95%+
  - Perfect Attendance: 100%
  - Positive Behavior: No incidents
  - Leadership: School prefect
  - Community Service: 10+ hours

Recognition:
  - Certificate presented at assembly
  - Photo on school notice board
  - Featured in newsletter
  - Prize: ₹1,000 book voucher
  - Parent appreciation letter

Impact:
  - Motivates other students
  - Reinforces positive behavior
  - Builds school culture
```

---

### 4. Behavior Improvement Plans

#### 4.1 Individual Behavior Plan

**Behavior Improvement Plan (BIP):**
```yaml
BIP ID: BIP-2024-0456
Student: Rohan Kumar (2024/06/0457)
Start Date: 20/01/2024
Review Date: 20/02/2024 (Monthly)
End Date: 20/04/2024 (3 months)

Reason for BIP:
  - Repeated fighting incidents (3 in 2 months)
  - Anger management issues
  - Difficulty controlling impulses

Target Behaviors:
  1. Physical Aggression:
     Current: 3 incidents/month
     Goal: 0 incidents/month
     
  2. Verbal Outbursts:
     Current: 5-7 times/week
     Goal: 0-1 times/week
     
  3. Following Directions:
     Current: 60% compliance
     Goal: 90% compliance

Interventions:
  1. Counseling:
     Frequency: 2 sessions/week
     Focus: Anger management, impulse control
     Counselor: Ms. Priya Singh
     
  2. Behavioral Strategies:
     - Cool-down corner in classroom
     - Breathing exercises
     - Positive reinforcement
     - Daily behavior chart
     
  3. Parent Involvement:
     - Weekly progress reports
     - Home behavior strategies
     - Consistent consequences
     
  4. Academic Support:
     - Check-ins with teacher (daily)
     - Preferential seating
     - Break cards (3/day)

Monitoring:
  - Daily behavior tracking
  - Weekly teacher reports
  - Bi-weekly counselor updates
  - Monthly BIP review meetings

Progress (After 1 Month):
  Physical Aggression: 0 incidents ✓
  Verbal Outbursts: 2 times/week (Improving)
  Following Directions: 80% compliance (Improving)
  
  Overall: SIGNIFICANT IMPROVEMENT

Next Steps:
  - Continue current interventions
  - Gradually reduce counseling to 1/week
  - Introduce peer mediation training
  - Review for possible plan modification
```

---

## 📊 DATABASE SCHEMA

### Behavioral Incidents Table

```sql
CREATE TABLE behavioral_incidents (
  incident_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Student(s) Involved
  primary_student_id INT NOT NULL,
  other_students TEXT,
  
  -- Incident Details
  incident_date DATE NOT NULL,
  incident_time TIME,
  location VARCHAR(200),
  
  -- Classification
  incident_type VARCHAR(200) NOT NULL,
  category ENUM('MINOR', 'MAJOR', 'SERIOUS') NOT NULL,
  severity_level INT NOT NULL,
  
  -- Description
  description TEXT NOT NULL,
  reported_by INT NOT NULL,
  witnesses TEXT,
  
  -- Evidence
  cctv_footage BOOLEAN DEFAULT FALSE,
  cctv_path VARCHAR(500),
  photos_path VARCHAR(500),
  documents_path VARCHAR(500),
  
  -- Investigation
  investigation_conducted BOOLEAN DEFAULT FALSE,
  investigation_date DATE,
  investigation_by INT,
  investigation_notes TEXT,
  
  -- Student Statements
  student_statement TEXT,
  other_statements TEXT,
  
  -- Immediate Action
  immediate_action TEXT,
  
  -- Status
  status ENUM('REPORTED', 'UNDER_INVESTIGATION', 'RESOLVED', 'CLOSED') DEFAULT 'REPORTED',
  
  -- Resolution
  resolution_date DATE,
  resolution_notes TEXT,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (primary_student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (primary_student_id),
  INDEX idx_date (incident_date),
  INDEX idx_category (category),
  INDEX idx_status (status)
);
```

### Disciplinary Actions Table

```sql
CREATE TABLE disciplinary_actions (
  action_id INT PRIMARY KEY AUTO_INCREMENT,
  incident_id INT NOT NULL,
  student_id INT NOT NULL,
  
  -- Action Details
  action_type ENUM(
    'VERBAL_WARNING', 'WRITTEN_WARNING', 'DETENTION',
    'IN_SCHOOL_SUSPENSION', 'OUT_OF_SCHOOL_SUSPENSION',
    'EXPULSION', 'OTHER'
  ) NOT NULL,
  
  action_description TEXT NOT NULL,
  
  -- Duration
  start_date DATE NOT NULL,
  end_date DATE,
  duration_days INT,
  duration_hours INT,
  
  -- Issued By
  issued_by INT NOT NULL,
  approved_by INT,
  approval_date DATE,
  
  -- Conditions
  conditions TEXT,
  requirements TEXT,
  
  -- Completion
  completed BOOLEAN DEFAULT FALSE,
  completion_date DATE,
  completion_notes TEXT,
  
  -- Parent Communication
  parent_notified BOOLEAN DEFAULT FALSE,
  parent_meeting_required BOOLEAN DEFAULT FALSE,
  parent_meeting_date DATE,
  
  -- Appeal
  appeal_filed BOOLEAN DEFAULT FALSE,
  appeal_date DATE,
  appeal_outcome TEXT,
  
  -- Status
  status ENUM('PENDING', 'ACTIVE', 'COMPLETED', 'APPEALED', 'CANCELLED') DEFAULT 'PENDING',
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (incident_id) REFERENCES behavioral_incidents(incident_id),
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_incident_id (incident_id),
  INDEX idx_type (action_type),
  INDEX idx_status (status)
);
```

### Positive Behavior Points Table

```sql
CREATE TABLE positive_behavior_points (
  points_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Transaction
  transaction_type ENUM('EARN', 'REDEEM') NOT NULL,
  points INT NOT NULL,
  
  -- Details
  reason VARCHAR(500) NOT NULL,
  description TEXT,
  
  -- Date
  transaction_date DATE NOT NULL,
  
  -- Issued/Redeemed By
  issued_by INT,
  
  -- Balance (calculated)
  balance_after INT NOT NULL,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_date (transaction_date),
  INDEX idx_type (transaction_type)
);
```

### Behavior Improvement Plans Table

```sql
CREATE TABLE behavior_improvement_plans (
  bip_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Plan Details
  plan_name VARCHAR(200) NOT NULL,
  start_date DATE NOT NULL,
  end_date DATE NOT NULL,
  
  -- Reason
  reason TEXT NOT NULL,
  target_behaviors TEXT NOT NULL,
  
  -- Goals
  goals TEXT NOT NULL,
  
  -- Interventions
  interventions TEXT NOT NULL,
  
  -- Monitoring
  monitoring_frequency VARCHAR(100),
  progress_tracking TEXT,
  
  -- Team
  case_manager INT,
  team_members TEXT,
  
  -- Parent Involvement
  parent_involvement TEXT,
  
  -- Reviews
  review_frequency VARCHAR(100),
  last_review_date DATE,
  next_review_date DATE,
  
  -- Progress
  progress_notes TEXT,
  effectiveness_rating INT,
  
  -- Status
  status ENUM('ACTIVE', 'COMPLETED', 'DISCONTINUED', 'MODIFIED') DEFAULT 'ACTIVE',
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_status (status),
  INDEX idx_review_date (next_review_date)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Automatic Escalation

```javascript
FUNCTION check_behavior_escalation(student_id) {
  // Get incidents in last 30 days
  recent_incidents = QUERY("
    SELECT * FROM behavioral_incidents
    WHERE primary_student_id = ?
    AND incident_date >= DATE_SUB(CURRENT_DATE(), INTERVAL 30 DAY)
    AND status IN ('RESOLVED', 'CLOSED')
  ", [student_id])
  
  // Count by category
  minor_count = COUNT(recent_incidents WHERE category = 'MINOR')
  major_count = COUNT(recent_incidents WHERE category = 'MAJOR')
  serious_count = COUNT(recent_incidents WHERE category = 'SERIOUS')
  
  // Check escalation rules
  IF minor_count >= 3 {
    // 3 minor incidents → escalate to major
    SEND_NOTIFICATION("discipline_committee", {
      title: "Behavior Escalation Alert",
      message: `Student ${student_id} has 3+ minor incidents in 30 days`,
      action: "Consider major consequence"
    })
  }
  
  IF major_count >= 2 {
    // 2 major incidents → escalate to serious
    SEND_NOTIFICATION("principal", {
      title: "Serious Behavior Concern",
      message: `Student ${student_id} has 2+ major incidents in 30 days`,
      action: "Behavior Improvement Plan recommended",
      priority: "HIGH"
    })
    
    // Recommend BIP
    RECOMMEND_BIP(student_id)
  }
  
  IF serious_count >= 1 {
    // 1 serious incident → immediate action
    SEND_NOTIFICATION("principal", {
      title: "Critical Behavior Incident",
      message: `Student ${student_id} involved in serious incident`,
      action: "Immediate intervention required",
      priority: "URGENT"
    })
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Communication Module**: Parent notifications
2. **Counseling Module**: Behavioral interventions
3. **Academic Module**: Impact on academic performance
4. **Attendance Module**: Suspension tracking

### Inbound Integrations

1. **Teacher Portal**: Incident reporting
2. **CCTV System**: Evidence footage
3. **Parent Portal**: Incident notifications

---

## 👥 USER WORKFLOWS

### Workflow: Report Behavioral Incident

**Actor:** Teacher  
**Duration:** 10-15 minutes

**Steps:**

1. Login to Teacher Portal
2. Navigate to: Discipline → Report Incident
3. Select student(s) involved
4. Fill incident details
5. Upload evidence (if any)
6. Submit report
7. Discipline committee reviews
8. Action decided
9. Parents notified
10. Consequences implemented

**Expected Outcome:** Incident documented and resolved

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 17, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

# BREAK & RECESS MANAGEMENT - COMPLETE DOCUMENTATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-BREAK-013  
**Category:** Operations Management  
**Priority:** High (P1)  
**Owner:** Academic Operations Team

---

## 📋 OVERVIEW

The Break & Recess Management submodule handles staggered break scheduling across grades, manages cafeteria and playground capacity, assigns teacher duty rosters, optimizes break timing to prevent overcrowding, and ensures smooth transitions between academic periods and break times.

### Purpose

Optimize break schedules to prevent overcrowding, ensure adequate supervision through duty assignments, manage cafeteria and facility capacity, provide age-appropriate break durations, enable staggered timing for different grade levels, and maintain safety and discipline during non-academic periods.

### Scope

- **Staggered Break Scheduling**: Different break times for different grades
- **Cafeteria Capacity Management**: Optimize lunch break timing
- **Playground Allocation**: Assign play areas by grade/age
- **Duty Roster Management**: Teacher supervision assignments
- **Break Duration Optimization**: Age-appropriate break lengths
- **Crowd Management**: Prevent facility overcrowding
- **Emergency Duty Coverage**: Handle teacher absences
- **Special Event Adjustments**: Modified breaks for events
- **Break Time Analytics**: Utilization and capacity tracking

---

## 🎯 KEY FEATURES

### 1. Staggered Break Scheduling

#### 1.1 Multi-Tier Break System

**Grade-wise Break Schedule:**
```yaml
School: Hogwarts International School
Total Students: 1,200 (Grades 1-12)
Cafeteria Capacity: 300 students
Playground Capacity: 400 students

Break Schedule Strategy: 3-Tier Staggered System

TIER 1: PRIMARY (Grades 1-5)
  Students: 450
  Short Break:
    Time: 10:00-10:20 AM (20 minutes)
    Location: Primary Playground
    Capacity: 450 students (adequate)
    Supervision: 8 teachers
    
  Lunch Break:
    Time: 12:00-12:40 PM (40 minutes)
    Location: Cafeteria (First Batch)
    Capacity: 300 students
    Batches: 2 (300 + 150)
    Batch 1: 12:00-12:20 PM (Grades 1-3)
    Batch 2: 12:20-12:40 PM (Grades 4-5)
    Supervision: 10 teachers
    
TIER 2: MIDDLE SCHOOL (Grades 6-8)
  Students: 360
  Short Break:
    Time: 10:20-10:40 AM (20 minutes)
    Location: Middle School Playground
    Capacity: 400 students (adequate)
    Supervision: 6 teachers
    
  Lunch Break:
    Time: 12:40-1:20 PM (40 minutes)
    Location: Cafeteria (Second Batch)
    Capacity: 300 students
    Batches: 2 (300 + 60)
    Batch 1: 12:40-1:00 PM (Grades 6-7)
    Batch 2: 1:00-1:20 PM (Grade 8)
    Supervision: 8 teachers
    
TIER 3: SENIOR SCHOOL (Grades 9-12)
  Students: 390
  Short Break:
    Time: 10:40-11:00 AM (20 minutes)
    Location: Senior Playground / Canteen
    Capacity: 400 students (adequate)
    Supervision: 6 teachers
    
  Lunch Break:
    Time: 1:20-2:00 PM (40 minutes)
    Location: Cafeteria (Third Batch)
    Capacity: 300 students
    Batches: 2 (300 + 90)
    Batch 1: 1:20-1:40 PM (Grades 9-10)
    Batch 2: 1:40-2:00 PM (Grades 11-12)
    Supervision: 8 teachers

Benefits of Staggered System:
  ✓ No cafeteria overcrowding (max 300 at a time)
  ✓ Adequate playground space per tier
  ✓ Age-appropriate separation (safety)
  ✓ Efficient teacher duty rotation
  ✓ Smooth transitions between periods
  ✓ Reduced noise and chaos
```

#### 1.2 Break Duration by Age Group

**Age-Appropriate Break Timing:**
```yaml
Break Duration Guidelines (Based on Educational Research):

Primary (Grades 1-5):
  Age: 6-10 years
  Short Break: 20 minutes
  Rationale:
    - Younger children need more frequent breaks
    - Higher energy levels require longer play time
    - Attention span: 15-20 minutes per session
    - Physical activity essential for development
  
  Lunch Break: 40 minutes
  Rationale:
    - Slower eating pace
    - Need supervision for hygiene
    - Social interaction time
    - Digestive rest before next period

Middle School (Grades 6-8):
  Age: 11-13 years
  Short Break: 20 minutes
  Rationale:
    - Moderate energy levels
    - Social interaction important
    - Attention span: 25-30 minutes
    - Physical activity still important
  
  Lunch Break: 40 minutes
  Rationale:
    - Adequate eating time
    - Socialization period
    - Transition from morning to afternoon

Senior School (Grades 9-12):
  Age: 14-18 years
  Short Break: 20 minutes
  Rationale:
    - Mental refresh between periods
    - Social networking
    - Attention span: 40-45 minutes
    - Less physical activity focus
  
  Lunch Break: 40 minutes
  Rationale:
    - Adequate eating time
    - Study/discussion time
    - Relaxation before afternoon sessions

Special Breaks:

Assembly Break (Monday):
  Duration: 30 minutes (8:00-8:30 AM)
  All Grades: Together
  Purpose: Weekly assembly, announcements, cultural programs
  
Activity Break (Friday):
  Duration: 30 minutes (2:30-3:00 PM)
  All Grades: Club activities
  Purpose: Co-curricular activities, clubs, sports
```

---

### 2. Cafeteria Capacity Management

#### 2.1 Lunch Break Optimization

**Cafeteria Scheduling Algorithm:**
```yaml
Cafeteria Details:
  Total Capacity: 300 students
  Seating Areas: 3 sections (100 seats each)
  Service Counters: 4
  Average Service Time: 2 minutes per student
  Average Eating Time: 15-20 minutes

Optimization Goals:
  1. No overcrowding (max 300 at any time)
  2. Adequate eating time (minimum 20 minutes)
  3. Efficient counter utilization
  4. Minimal wait time
  5. Smooth entry/exit flow

Lunch Schedule (Staggered):

Batch 1: Primary (Grades 1-3)
  Time: 12:00-12:20 PM
  Students: 300
  Entry: 12:00 PM (staggered by grade)
    - Grade 1: 12:00 PM (100 students)
    - Grade 2: 12:05 PM (100 students)
    - Grade 3: 12:10 PM (100 students)
  Service Time: 5 minutes (4 counters)
  Eating Time: 15 minutes
  Exit: 12:20 PM
  Supervision: 6 teachers + 2 support staff

Batch 2: Primary (Grades 4-5) + Middle (Grade 6)
  Time: 12:20-12:40 PM
  Students: 300 (150 + 120 + 30 buffer)
  Entry: 12:20 PM
  Service Time: 5 minutes
  Eating Time: 15 minutes
  Exit: 12:40 PM
  Supervision: 6 teachers

Batch 3: Middle (Grades 7-8)
  Time: 12:40-1:00 PM
  Students: 240
  Entry: 12:40 PM
  Service Time: 4 minutes
  Eating Time: 16 minutes
  Exit: 1:00 PM
  Supervision: 5 teachers

Batch 4: Senior (Grades 9-10)
  Time: 1:00-1:20 PM
  Students: 300
  Entry: 1:00 PM
  Service Time: 5 minutes
  Eating Time: 15 minutes
  Exit: 1:20 PM
  Supervision: 5 teachers

Batch 5: Senior (Grades 11-12)
  Time: 1:20-1:40 PM
  Students: 180
  Entry: 1:20 PM
  Service Time: 3 minutes
  Eating Time: 17 minutes
  Exit: 1:40 PM
  Supervision: 4 teachers

Cafeteria Utilization:
  Total Lunch Duration: 100 minutes (12:00-1:40 PM)
  Total Students Served: 1,200
  Average Batch Size: 240 students
  Capacity Utilization: 80% (optimal)
  Peak Occupancy: 300 students (no overcrowding)
  
Efficiency Metrics:
  Average Wait Time: 2 minutes
  Average Service Time: 4 minutes
  Average Eating Time: 16 minutes
  Total Time per Student: 22 minutes
  Satisfaction Rate: 92%
```

#### 2.2 Cafeteria Queue Management

**Smart Queue System:**
```yaml
Queue Management Features:

1. Digital Queue Display:
   Location: Cafeteria entrance
   Display Type: LED screen
   Information Shown:
     - Current batch number
     - Estimated wait time
     - Counter availability
     - Menu of the day
   
2. Counter Allocation:
   Counter 1: Vegetarian Meals
   Counter 2: Non-Vegetarian Meals
   Counter 3: Snacks & Beverages
   Counter 4: Special Dietary (Jain, Vegan, Allergies)
   
   Smart Routing:
     - Students directed to appropriate counter
     - Load balancing across counters
     - Special needs priority
   
3. Pre-Order System (Optional):
   Platform: Mobile app
   Features:
     - Order lunch in advance
     - Skip queue (direct pickup)
     - Payment integration
     - Dietary preferences saved
   
   Benefits:
     - Reduced wait time
     - Better food planning
     - Less crowding
     - Faster service

4. Capacity Monitoring:
   Sensors: Entry/exit counters
   Real-time Tracking:
     - Current occupancy
     - Available seats
     - Counter queue length
   
   Alerts:
     - Approaching capacity (280/300)
     - Overcrowding detected
     - Uneven counter distribution
   
   Dashboard:
     - Live occupancy graph
     - Historical trends
     - Peak time analysis
```

---

### 3. Playground Allocation & Management

#### 3.1 Play Area Assignment

**Age-Appropriate Playground Zones:**
```yaml
School Playground Layout:

Total Area: 15,000 sq ft
Zones: 4

Zone 1: Primary Playground (Grades 1-3)
  Area: 4,000 sq ft
  Capacity: 200 students
  Equipment:
    - Swings (10)
    - Slides (5)
    - Climbing frames (3)
    - Sandbox (1)
    - See-saws (4)
  
  Safety Features:
    - Soft rubber flooring
    - Low-height equipment
    - Fenced boundary
    - Shaded areas
  
  Supervision: 4 teachers
  Break Time: 10:00-10:20 AM
  
Zone 2: Upper Primary Playground (Grades 4-5)
  Area: 3,000 sq ft
  Capacity: 150 students
  Equipment:
    - Basketball court (half)
    - Volleyball net
    - Badminton court
    - Hopscotch area
  
  Safety Features:
    - Concrete flooring
    - Medium-height equipment
    - Marked boundaries
  
  Supervision: 3 teachers
  Break Time: 10:00-10:20 AM

Zone 3: Middle School Playground (Grades 6-8)
  Area: 4,000 sq ft
  Capacity: 250 students
  Facilities:
    - Basketball court (full)
    - Football area (small)
    - Cricket practice nets
    - Table tennis tables (4)
  
  Safety Features:
    - Sports-grade flooring
    - Proper court markings
    - Spectator area
  
  Supervision: 4 teachers
  Break Time: 10:20-10:40 AM

Zone 4: Senior School Area (Grades 9-12)
  Area: 4,000 sq ft
  Capacity: 300 students
  Facilities:
    - Canteen seating (outdoor)
    - Reading benches
    - Discussion areas
    - Sports equipment room
  
  Note: Seniors have access to main sports ground
  
  Supervision: 3 teachers
  Break Time: 10:40-11:00 AM

Allocation Rules:
  ✓ Age-based segregation (safety)
  ✓ Staggered timing (no overlap)
  ✓ Equipment appropriate for age
  ✓ Adequate supervision ratio
  ✓ Emergency access maintained
  ✓ Weather contingency (indoor areas)
```

#### 3.2 Rainy Day / Bad Weather Plan

**Indoor Break Management:**
```yaml
Bad Weather Protocol:

Trigger Conditions:
  - Heavy rain
  - Extreme heat (>40°C)
  - Air quality index >200
  - Storm warning
  - Other safety concerns

Indoor Break Locations:

Primary (Grades 1-5):
  Location: Multipurpose Hall A
  Capacity: 450 students
  Activities:
    - Indoor games (board games, puzzles)
    - Story time
    - Art & craft corner
    - Movie screening (educational)
  Supervision: 8 teachers
  
Middle School (Grades 6-8):
  Location: Multipurpose Hall B
  Capacity: 360 students
  Activities:
    - Table tennis
    - Carrom, chess
    - Library access
    - Music room
  Supervision: 6 teachers

Senior School (Grades 9-12):
  Location: Auditorium + Library
  Capacity: 390 students
  Activities:
    - Library reading
    - Study groups
    - Auditorium seating
    - Canteen (indoor)
  Supervision: 5 teachers

Notification Process:
  1. Weather monitored by admin
  2. Decision made 30 min before break
  3. PA announcement: "Indoor break today"
  4. Teachers guide students to indoor areas
  5. Duty teachers positioned at indoor locations
  6. Normal break duration maintained
```

---

### 4. Teacher Duty Roster Management

#### 4.1 Duty Assignment Algorithm

**Fair and Balanced Duty Allocation:**
```yaml
Duty Roster Principles:

1. Equal Distribution:
   - All teachers share duty responsibility
   - Rotational basis (weekly/monthly)
   - No teacher overloaded
   
2. Expertise Matching:
   - Primary teachers for primary duty
   - Senior teachers for senior duty
   - PE teachers for playground duty
   
3. Workload Consideration:
   - Teachers with 6+ periods/day: Reduced duty
   - Part-time teachers: Proportional duty
   - Coordinators: Administrative duty priority

4. Preference Accommodation:
   - Teachers can request preferred slots
   - Swap duty with colleagues (approval needed)
   - Avoid consecutive days

Duty Types:

A. Short Break Duty (10:00-11:00 AM, staggered):
   Duration: 20 minutes per tier
   Teachers Required: 20 (across 3 tiers)
   
   Tier 1 (10:00-10:20): 8 teachers
   Tier 2 (10:20-10:40): 6 teachers
   Tier 3 (10:40-11:00): 6 teachers
   
B. Lunch Break Duty (12:00-2:00 PM, staggered):
   Duration: 120 minutes (5 batches)
   Teachers Required: 28 (across 5 batches)
   
   Batch 1 (12:00-12:20): 6 teachers
   Batch 2 (12:20-12:40): 6 teachers
   Batch 3 (12:40-1:00): 5 teachers
   Batch 4 (1:00-1:20): 5 teachers
   Batch 5 (1:20-1:40): 4 teachers
   
C. Gate Duty (Entry/Exit):
   Morning (7:30-8:30 AM): 4 teachers
   Afternoon (3:00-4:00 PM): 4 teachers
   
D. Corridor Duty (Between Periods):
   Duration: 5 minutes per period change
   Teachers: 6 (distributed across floors)

Total Duty Slots per Day: 60
Total Teachers: 120
Average Duty per Teacher: 0.5 slots/day = 3 slots/week

Sample Weekly Duty Roster:

Week: January 20-25, 2026

Monday:
  Short Break (Tier 1):
    - Mrs. Sharma (Primary Playground)
    - Mr. Kumar (Cafeteria Area)
    - Ms. Gupta (Washroom Area)
    - [... 5 more teachers]
  
  Lunch Break (Batch 1):
    - Dr. Patel (Cafeteria Counter 1)
    - Mrs. Verma (Cafeteria Counter 2)
    - [... 4 more teachers]
  
  [... other duties]

Tuesday:
  [Rotated assignments]

[... rest of week]

Duty Rotation:
  Frequency: Weekly
  Pattern: Alphabetical + Random
  Constraints:
    - No teacher on duty 2 consecutive days
    - Max 1 duty per day per teacher
    - Free period preferred for duty assignment
```

#### 4.2 Emergency Duty Coverage

**Handling Teacher Absences:**
```yaml
Emergency Duty Protocol:

Scenario: Teacher on duty calls in sick

Example:
  Date: Monday, January 20, 2026
  Absent Teacher: Mrs. Sharma
  Scheduled Duty: Short Break (Tier 1), 10:00-10:20 AM
  Notification: 7:30 AM (30 min before school)

Automatic Coverage Process:

Step 1: System Identifies Replacement
  Algorithm:
    - Find teachers with free period at 10:00 AM
    - Filter by primary expertise (for Tier 1 duty)
    - Exclude teachers already on duty
    - Select teacher with least duties this week
  
  Result: Mr. Singh (Free Period 3, 10:15-11:00 AM)
  
Step 2: Notification Sent
  Time: 7:35 AM
  Channel: SMS + App Push
  Message:
    "Emergency duty assignment: Short Break (Tier 1) today 10:00-10:20 AM 
    (covering for Mrs. Sharma). Location: Primary Playground."
  
  Confirmation Required: YES
  
Step 3: Confirmation
  Mr. Singh confirms: 7:40 AM
  Status: ASSIGNED
  
Step 4: Backup Plan (if no confirmation)
  If no confirmation by 8:00 AM:
    - Escalate to Coordinator
    - Manual assignment
    - Coordinator covers if needed

Step 5: Update Roster
  - Duty roster updated
  - Mrs. Sharma marked absent
  - Mr. Singh added for today
  - Compensation: Extra duty credit (can skip next duty)

Duty Coverage Statistics:
  Average Response Time: 10 minutes
  Confirmation Rate: 95%
  Manual Intervention: 5%
  Coverage Success: 100%
```

---

### 5. Break Time Optimization & Analytics

#### 5.1 Utilization Tracking

**Break Time Analytics Dashboard:**
```yaml
Analytics Metrics:

1. Cafeteria Utilization:
   Metric: Occupancy Rate
   Data Points:
     - Real-time occupancy (sensors)
     - Peak occupancy time
     - Average occupancy per batch
     - Wait time per student
   
   Weekly Report:
     Average Occupancy: 82%
     Peak Time: 12:00-12:20 PM (Batch 1)
     Peak Occupancy: 300/300 (100%)
     Average Wait Time: 2.5 minutes
     Satisfaction Score: 89%
   
   Insights:
     ✓ Batch 1 at full capacity (consider splitting)
     ✓ Batch 5 underutilized (60%) - can accommodate more
     ⚠️ Counter 4 (Special Dietary) has longer wait (5 min)

2. Playground Usage:
   Metric: Student Activity Level
   Data Points:
     - Number of students using playground
     - Equipment utilization
     - Incident reports
     - Weather impact
   
   Weekly Report:
     Primary Playground: 85% utilization
     Middle Playground: 78% utilization
     Senior Area: 65% utilization (many prefer canteen)
     
     Popular Equipment:
       - Basketball court: 95%
       - Swings: 90%
       - Cricket nets: 85%
     
     Incidents: 2 (minor scrapes, first aid given)
     Weather Days: 1 (indoor break on Wednesday)

3. Duty Compliance:
   Metric: Teacher Duty Adherence
   Data Points:
     - Duty attendance (on-time arrival)
     - Coverage rate (absences covered)
     - Incident response time
   
   Weekly Report:
     Duty Attendance: 98%
     Late Arrivals: 3 (< 5 minutes)
     Absences: 2 (both covered within 10 min)
     Incident Response: Average 2 minutes
     
     Top Performers:
       - Mr. Kumar: 100% attendance, 0 late
       - Mrs. Gupta: 100% attendance, 0 late

4. Break Duration Analysis:
   Metric: Effective Break Time
   Data Points:
     - Scheduled break time
     - Actual break time (entry to exit)
     - Transition time (class to break)
   
   Analysis:
     Scheduled: 20 minutes (short break)
     Actual: 18 minutes (avg)
     Transition Time: 2 minutes (class dismissal to break area)
     
     Recommendation:
       ⚠️ Consider 22-minute scheduled break to ensure 20 min actual
```

#### 5.2 Optimization Recommendations

**Data-Driven Improvements:**
```yaml
Optimization Suggestions (Based on Analytics):

1. Cafeteria Batch Adjustment:
   Current Issue: Batch 1 at 100% capacity, Batch 5 at 60%
   
   Recommendation:
     - Move 50 students from Batch 1 to Batch 5
     - Adjust: Grade 3 (50 students) → Batch 5
     - New Distribution:
       - Batch 1: 250 students (83%)
       - Batch 5: 230 students (77%)
   
   Expected Impact:
     - Reduced wait time in Batch 1 (2.5 → 1.5 min)
     - Better capacity utilization
     - Improved student satisfaction

2. Special Dietary Counter:
   Current Issue: Longer wait time (5 min vs 2 min avg)
   
   Recommendation:
     - Add 1 additional staff to Counter 4
     - Implement pre-order for special dietary needs
     - Create separate pickup area
   
   Expected Impact:
     - Wait time: 5 min → 2 min
     - Better service for special needs
     - Reduced congestion

3. Senior Playground Usage:
   Current Issue: Low utilization (65%)
   
   Recommendation:
     - Add more seating areas (social space)
     - Install charging stations (phone charging)
     - Create study corners
     - Improve canteen menu
   
   Expected Impact:
     - Increased usage: 65% → 80%
     - Better student engagement
     - Reduced indoor crowding

4. Break Transition Time:
   Current Issue: 2-minute transition reduces effective break
   
   Recommendation:
     - Implement bell system (5 min before break)
     - Allow students to pack up early
     - Stagger class dismissal by floor
   
   Expected Impact:
     - Transition time: 2 min → 1 min
     - Effective break time: 18 min → 19 min
     - Smoother flow
```

---

## 📊 DATABASE SCHEMA

### Break Schedules Table

```sql
CREATE TABLE break_schedules (
  break_schedule_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Break Identification
  break_type ENUM('SHORT_BREAK', 'LUNCH_BREAK', 'ASSEMBLY', 'ACTIVITY') NOT NULL,
  break_name VARCHAR(100) NOT NULL,
  
  -- Timing
  start_time TIME NOT NULL,
  end_time TIME NOT NULL,
  duration_minutes INT NOT NULL,
  
  -- Applicability
  applicable_grades JSON, -- ['1', '2', '3'] or ['ALL']
  tier_number INT, -- 1, 2, 3 for staggered breaks
  
  -- Location
  primary_location VARCHAR(200),
  alternate_location VARCHAR(200), -- For bad weather
  
  -- Capacity
  location_capacity INT,
  expected_students INT,
  utilization_percentage DECIMAL(5,2),
  
  -- Days
  applicable_days JSON, -- ['MONDAY', 'TUESDAY', ...]
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Status
  is_active BOOLEAN DEFAULT TRUE,
  
  -- Special Conditions
  weather_dependent BOOLEAN DEFAULT FALSE,
  requires_supervision BOOLEAN DEFAULT TRUE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Indexes
  INDEX idx_break_type (break_type),
  INDEX idx_timing (start_time, end_time),
  INDEX idx_grades (applicable_grades(100)),
  INDEX idx_active (is_active)
);
```

### Teacher Duty Roster Table

```sql
CREATE TABLE teacher_duty_roster (
  duty_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Duty Details
  duty_date DATE NOT NULL,
  duty_type ENUM('SHORT_BREAK', 'LUNCH_BREAK', 'GATE_DUTY', 'CORRIDOR_DUTY') NOT NULL,
  
  -- Timing
  duty_start_time TIME NOT NULL,
  duty_end_time TIME NOT NULL,
  duration_minutes INT NOT NULL,
  
  -- Teacher Assignment
  teacher_id INT NOT NULL,
  
  -- Location
  duty_location VARCHAR(200) NOT NULL,
  duty_area ENUM('CAFETERIA', 'PLAYGROUND', 'CORRIDOR', 'GATE', 'WASHROOM', 'OTHER') NOT NULL,
  
  -- Tier/Batch (for staggered breaks)
  tier_number INT,
  batch_number INT,
  
  -- Supervision Details
  students_supervised INT,
  grade_levels JSON, -- ['1', '2', '3']
  
  -- Status
  duty_status ENUM('SCHEDULED', 'CONFIRMED', 'COMPLETED', 'ABSENT', 'COVERED') DEFAULT 'SCHEDULED',
  
  -- Attendance
  check_in_time TIME,
  check_out_time TIME,
  on_time BOOLEAN,
  
  -- Coverage (if original teacher absent)
  is_coverage BOOLEAN DEFAULT FALSE,
  original_teacher_id INT,
  coverage_reason TEXT,
  
  -- Incidents
  incidents_reported INT DEFAULT 0,
  incident_details JSON,
  
  -- Compensation
  duty_credit DECIMAL(3,1) DEFAULT 1.0, -- 1.0 = normal, 1.5 = emergency coverage
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  assigned_by INT,
  
  -- Foreign Keys
  FOREIGN KEY (teacher_id) REFERENCES teachers(teacher_id),
  FOREIGN KEY (original_teacher_id) REFERENCES teachers(teacher_id),
  
  -- Indexes
  INDEX idx_duty_date (duty_date),
  INDEX idx_teacher (teacher_id),
  INDEX idx_status (duty_status),
  INDEX idx_duty_type (duty_type),
  
  -- Unique Constraint
  UNIQUE KEY uk_teacher_duty_time (teacher_id, duty_date, duty_start_time)
);
```

### Cafeteria Utilization Log Table

```sql
CREATE TABLE cafeteria_utilization_log (
  log_id BIGINT PRIMARY KEY AUTO_INCREMENT,
  
  -- Timestamp
  log_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  log_date DATE NOT NULL,
  
  -- Batch Information
  batch_number INT NOT NULL,
  batch_start_time TIME NOT NULL,
  batch_end_time TIME NOT NULL,
  
  -- Occupancy
  current_occupancy INT NOT NULL,
  max_capacity INT NOT NULL,
  utilization_percentage DECIMAL(5,2),
  
  -- Queue Information
  counter_1_queue INT DEFAULT 0,
  counter_2_queue INT DEFAULT 0,
  counter_3_queue INT DEFAULT 0,
  counter_4_queue INT DEFAULT 0,
  total_queue INT DEFAULT 0,
  
  -- Wait Time
  average_wait_time_minutes DECIMAL(5,2),
  max_wait_time_minutes DECIMAL(5,2),
  
  -- Service Metrics
  students_served INT,
  service_rate_per_minute DECIMAL(5,2),
  
  -- Indexes
  INDEX idx_log_date (log_date),
  INDEX idx_batch (batch_number),
  INDEX idx_timestamp (log_timestamp)
);
```

### Break Incidents Table

```sql
CREATE TABLE break_incidents (
  incident_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Incident Details
  incident_date DATE NOT NULL,
  incident_time TIME NOT NULL,
  break_type ENUM('SHORT_BREAK', 'LUNCH_BREAK') NOT NULL,
  
  -- Location
  incident_location VARCHAR(200) NOT NULL,
  area ENUM('CAFETERIA', 'PLAYGROUND', 'CORRIDOR', 'WASHROOM', 'OTHER') NOT NULL,
  
  -- Involved Parties
  student_id INT,
  student_grade VARCHAR(10),
  other_students_involved JSON,
  
  -- Incident Type
  incident_type ENUM(
    'MINOR_INJURY',
    'MAJOR_INJURY',
    'FIGHT',
    'BULLYING',
    'PROPERTY_DAMAGE',
    'MEDICAL_EMERGENCY',
    'BEHAVIORAL_ISSUE',
    'OTHER'
  ) NOT NULL,
  
  -- Description
  incident_description TEXT NOT NULL,
  severity ENUM('LOW', 'MEDIUM', 'HIGH', 'CRITICAL') NOT NULL,
  
  -- Response
  duty_teacher_id INT NOT NULL,
  response_time_minutes INT,
  action_taken TEXT,
  
  -- Medical
  first_aid_given BOOLEAN DEFAULT FALSE,
  medical_attention_required BOOLEAN DEFAULT FALSE,
  parent_notified BOOLEAN DEFAULT FALSE,
  
  -- Follow-up
  follow_up_required BOOLEAN DEFAULT FALSE,
  follow_up_notes TEXT,
  resolved BOOLEAN DEFAULT FALSE,
  resolution_date DATE,
  
  -- Metadata
  reported_by INT NOT NULL,
  report_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id),
  FOREIGN KEY (duty_teacher_id) REFERENCES teachers(teacher_id),
  
  -- Indexes
  INDEX idx_incident_date (incident_date),
  INDEX idx_student (student_id),
  INDEX idx_severity (severity),
  INDEX idx_type (incident_type)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Staggered Break Validation

```javascript
FUNCTION validate_break_schedule(break_schedule) {
  errors = []
  warnings = []
  
  // Rule 1: No overlapping breaks for same location
  overlapping = QUERY("
    SELECT * FROM break_schedules
    WHERE primary_location = ?
    AND is_active = TRUE
    AND (
      (start_time BETWEEN ? AND ?)
      OR (end_time BETWEEN ? AND ?)
      OR (? BETWEEN start_time AND end_time)
    )
  ", [
    break_schedule.primary_location,
    break_schedule.start_time,
    break_schedule.end_time,
    break_schedule.start_time,
    break_schedule.end_time,
    break_schedule.start_time
  ])
  
  IF overlapping.count > 0 {
    errors.ADD(`Break time overlaps with existing break at ${break_schedule.primary_location}`)
  }
  
  // Rule 2: Capacity check
  IF break_schedule.expected_students > break_schedule.location_capacity {
    errors.ADD(`Expected students (${break_schedule.expected_students}) exceeds location capacity (${break_schedule.location_capacity})`)
  } ELSE IF break_schedule.expected_students > break_schedule.location_capacity * 0.9 {
    warnings.ADD(`Near capacity: ${break_schedule.expected_students}/${break_schedule.location_capacity}`)
  }
  
  // Rule 3: Minimum break duration
  min_duration = GET_MIN_BREAK_DURATION(break_schedule.break_type)
  IF break_schedule.duration_minutes < min_duration {
    errors.ADD(`Break duration (${break_schedule.duration_minutes} min) less than minimum (${min_duration} min)`)
  }
  
  // Rule 4: Supervision requirement
  IF break_schedule.requires_supervision {
    required_teachers = CALCULATE_REQUIRED_TEACHERS(break_schedule.expected_students)
    assigned_teachers = COUNT_ASSIGNED_DUTY_TEACHERS(break_schedule)
    
    IF assigned_teachers < required_teachers {
      errors.ADD(`Insufficient supervision: ${assigned_teachers}/${required_teachers} teachers`)
    }
  }
  
  RETURN {
    is_valid: errors.length == 0,
    errors: errors,
    warnings: warnings
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Master Timetable**: Break times integrated into period schedules
2. **Teacher Duty Module**: Duty roster assignments
3. **Cafeteria Management**: Meal planning and service
4. **Facilities Module**: Playground and cafeteria booking
5. **Notification Service**: Duty reminders and alerts

### Inbound Integrations

1. **Student Management**: Student count per grade
2. **Teacher Management**: Teacher availability for duty
3. **Weather Service**: Weather-based break location decisions
4. **Incident Management**: Break-time incident reporting

---

## 👥 USER WORKFLOWS

### Workflow: Assign Weekly Duty Roster

**Actor:** Academic Coordinator  
**Duration:** 15-20 minutes  
**Frequency:** Weekly

**Steps:**
1. Login to Timetable Portal
2. Navigate to "Break Management" → "Duty Roster"
3. Select week: "January 20-25, 2026"
4. Click "Auto-Generate Roster"
5. System allocates duties based on:
   - Teacher availability (free periods)
   - Fair distribution (rotation)
   - Previous duty history
6. Review generated roster
7. Make manual adjustments if needed
8. Click "Publish Roster"
9. System sends notifications to all teachers
10. Roster activated for the week

**Expected Outcome:** Weekly duty roster published and teachers notified.

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 1.0  
**Documentation Standard:** Enterprise Grade

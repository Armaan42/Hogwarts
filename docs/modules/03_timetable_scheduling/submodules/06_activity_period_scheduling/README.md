# ACTIVITY PERIOD SCHEDULING - COMPLETE DOCUMENTATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-ACTIVITY-006  
**Category:** Co-curricular Management  
**Priority:** High (P1)  
**Owner:** Activities Coordinator

---

## 📋 OVERVIEW

The Activity Period Scheduling submodule manages comprehensive scheduling of co-curricular activities, clubs, societies, sports, cultural programs, and special events within the regular timetable framework, ensuring balanced participation and optimal resource utilization.

### Purpose

Schedule activity periods for clubs and societies, allocate resources for activities, manage student participation and preferences, coordinate with regular academic schedule, ensure balanced activity distribution across grades, handle event-based schedule modifications, and track student engagement in co-curricular activities.

### Scope

- **Club Period Scheduling**: Weekly club activity slots
- **Sports Activity Management**: Inter-house and inter-school sports
- **Cultural Program Coordination**: Annual day, festivals, competitions
- **Student Preference Management**: Club selection and allocation
- **Resource Allocation**: Rooms, equipment, supervisors
- **Event Schedule Modifications**: Temporary timetable changes
- **Participation Tracking**: Student engagement analytics
- **Certificate Generation**: Participation certificates

---

## 🎯 KEY FEATURES

### 1. Comprehensive Club Period Allocation

#### 1.1 Weekly Club Schedule

**Activity Period Structure:**
```yaml
School: Hogwarts International
Activity Day: Friday
Activity Periods: P7-P8 (1:45-3:15 PM, 90 minutes)
Total Students: 1,200 (Grades 1-12)

Club Categories:

1. STEM Clubs (6 clubs, 180 students):
   - Robotics Club:
       Location: Computer Lab 1
       Capacity: 30 students
       Supervisor: Mr. Patel (Computer Science)
       Equipment: 15 robotics kits, laptops
       Activities: Robot building, programming, competitions
       
   - Science Club:
       Location: Physics Lab
       Capacity: 30 students
       Supervisor: Dr. Kumar (Physics)
       Equipment: Lab apparatus, microscopes
       Activities: Experiments, science fair projects
       
   - Coding Club:
       Location: Computer Lab 2
       Capacity: 40 students
       Supervisor: Ms. Sharma (IT)
       Equipment: 40 computers
       Activities: Python, web development, hackathons
       
   - Mathematics Club:
       Location: Room 301
       Capacity: 30 students
       Supervisor: Mrs. Verma (Mathematics)
       Activities: Olympiad prep, puzzles, competitions
       
   - Astronomy Club:
       Location: Terrace/Planetarium
       Capacity: 25 students
       Supervisor: Mr. Singh (Physics)
       Equipment: Telescopes, star charts
       Activities: Stargazing, astrophotography
       
   - Innovation Lab:
       Location: Maker Space
       Capacity: 25 students
       Supervisor: Mr. Gupta (Design)
       Equipment: 3D printers, electronics
       Activities: Prototyping, invention projects

2. Arts & Culture Clubs (7 clubs, 195 students):
   - Drama Club:
       Location: Auditorium
       Capacity: 40 students
       Supervisor: Ms. Kapoor (English)
       Activities: Theater, plays, acting workshops
       Annual Production: Yes
       
   - Music Club:
       Location: Music Room 1
       Capacity: 25 students
       Supervisor: Mr. Desai (Music)
       Equipment: Instruments (tabla, guitar, keyboard)
       Activities: Vocal, instrumental, band practice
       
   - Dance Club:
       Location: Dance Studio
       Capacity: 30 students
       Supervisor: Ms. Rao (Dance)
       Activities: Classical, contemporary, choreography
       
   - Art Club:
       Location: Art Studio 1
       Capacity: 25 students
       Supervisor: Mrs. Mehta (Art)
       Equipment: Easels, paints, canvas
       Activities: Painting, sketching, exhibitions
       
   - Photography Club:
       Location: Media Room
       Capacity: 20 students
       Supervisor: Mr. Joshi (Media)
       Equipment: DSLR cameras, editing software
       Activities: Photography, editing, exhibitions
       
   - Literary Club:
       Location: Library
       Capacity: 30 students
       Supervisor: Ms. Iyer (English)
       Activities: Creative writing, poetry, magazine
       
   - Craft Club:
       Location: Art Studio 2
       Capacity: 25 students
       Supervisor: Mrs. Nair (Art)
       Activities: Handicrafts, origami, pottery

3. Sports & Fitness Clubs (5 clubs, 200 students):
   - Football Club:
       Location: Sports Ground
       Capacity: 50 students
       Supervisor: Coach Sharma
       Activities: Training, inter-house matches
       
   - Basketball Club:
       Location: Basketball Court
       Capacity: 40 students
       Supervisor: Coach Patel
       Activities: Skills training, tournaments
       
   - Cricket Club:
       Location: Cricket Ground
       Capacity: 40 students
       Supervisor: Coach Kumar
       Activities: Coaching, practice matches
       
   - Badminton Club:
       Location: Indoor Sports Hall
       Capacity: 30 students
       Supervisor: Coach Verma
       Activities: Training, competitions
       
   - Yoga & Fitness Club:
       Location: Multipurpose Hall
       Capacity: 40 students
       Supervisor: Ms. Reddy (Yoga)
       Activities: Yoga, meditation, fitness

4. Academic & Debate Clubs (5 clubs, 150 students):
   - Debate Club:
       Location: Conference Room 1
       Capacity: 30 students
       Supervisor: Mr. Saxena (Social Studies)
       Activities: Debates, MUNs, public speaking
       
   - Quiz Club:
       Location: Conference Room 2
       Capacity: 30 students
       Supervisor: Mrs. Gupta (General Knowledge)
       Activities: Quiz competitions, trivia
       
   - Environment Club:
       Location: Biology Lab
       Capacity: 30 students
       Supervisor: Dr. Rao (Biology)
       Activities: Eco-projects, tree plantation
       
   - Entrepreneurship Club:
       Location: Room 302
       Capacity: 30 students
       Supervisor: Mr. Agarwal (Commerce)
       Activities: Business plans, startups
       
   - Social Service Club:
       Location: Room 303
       Capacity: 30 students
       Supervisor: Ms. Jain (Social Work)
       Activities: Community service, NGO visits

5. Special Interest Clubs (4 clubs, 100 students):
   - Chess Club:
       Location: Games Room
       Capacity: 25 students
       Supervisor: Mr. Pandey
       Activities: Chess training, tournaments
       
   - Culinary Club:
       Location: Home Science Lab
       Capacity: 25 students
       Supervisor: Mrs. Kulkarni (Home Science)
       Activities: Cooking, baking, nutrition
       
   - Gardening Club:
       Location: School Garden
       Capacity: 25 students
       Supervisor: Mr. Mishra (Biology)
       Activities: Organic farming, landscaping
       
   - Language Club (French/Spanish):
       Location: Language Lab
       Capacity: 25 students
       Supervisor: Ms. Fernandes
       Activities: Language learning, culture

Total Clubs: 27
Total Capacity: 825 students
Current Enrollment: 725 students (60% of school)
Available Slots: 100 students
```

#### 1.2 Student Preference Management

**Club Selection Process:**
```yaml
Club Selection Timeline:

Week 1 (First week of academic year):
  - Club fair: All clubs showcase activities
  - Students visit club stalls
  - Collect information brochures
  
Week 2:
  - Online preference form opens
  - Students submit top 3 choices
  - Deadline: Friday 5 PM
  
Week 3:
  - Automated allocation algorithm runs
  - Students allocated based on:
      1. Preference ranking (50% weight)
      2. Grade distribution (20% weight)
      3. Previous club history (15% weight)
      4. Random lottery (15% weight)
  
  - Results published
  - Waitlist created for oversubscribed clubs
  
Week 4:
  - Club activities begin
  - Attendance tracking starts

Allocation Algorithm:
```javascript
FUNCTION allocate_students_to_clubs(students, preferences, clubs) {
  allocations = {}
  waitlists = {}
  
  // Step 1: Sort students by priority
  prioritized_students = SORT_STUDENTS_BY_PRIORITY(students)
  
  FOR each student IN prioritized_students {
    student_prefs = GET_PREFERENCES(student, preferences)
    allocated = FALSE
    
    // Try first choice
    first_choice = student_prefs.choice_1
    IF clubs[first_choice].current_enrollment < clubs[first_choice].capacity {
      ALLOCATE(student, first_choice)
      clubs[first_choice].current_enrollment++
      allocated = TRUE
    }
    
    // Try second choice if first full
    IF NOT allocated {
      second_choice = student_prefs.choice_2
      IF clubs[second_choice].current_enrollment < clubs[second_choice].capacity {
        ALLOCATE(student, second_choice)
        clubs[second_choice].current_enrollment++
        allocated = TRUE
      }
    }
    
    // Try third choice
    IF NOT allocated {
      third_choice = student_prefs.choice_3
      IF clubs[third_choice].current_enrollment < clubs[third_choice].capacity {
        ALLOCATE(student, third_choice)
        clubs[third_choice].current_enrollment++
        allocated = TRUE
      }
    }
    
    // Add to waitlist if all choices full
    IF NOT allocated {
      waitlists[student_prefs.choice_1].ADD(student)
    }
  }
  
  RETURN {
    allocations: allocations,
    waitlists: waitlists,
    satisfaction_rate: CALCULATE_SATISFACTION(allocations, preferences)
  }
}
```

Allocation Results Example:
```yaml
Total Students: 1,200
Submitted Preferences: 1,100 (92%)
Allocated: 725 (66%)
Not Interested: 100 (8%)
Waitlisted: 275 (25%)

Satisfaction Metrics:
  Got 1st Choice: 520 students (72%)
  Got 2nd Choice: 150 students (21%)
  Got 3rd Choice: 55 students (7%)
  Waitlisted: 275 students

Most Popular Clubs (Oversubscribed):
  1. Robotics Club: 75 applicants, 30 capacity (2.5x)
  2. Football Club: 95 applicants, 50 capacity (1.9x)
  3. Drama Club: 68 applicants, 40 capacity (1.7x)

Least Popular Clubs:
  1. Gardening Club: 15 enrolled, 25 capacity (60%)
  2. Chess Club: 18 enrolled, 25 capacity (72%)
```

---

### 2. Event-Based Schedule Modifications

#### 2.1 Annual Day Preparation

**Temporary Timetable Changes:**
```yaml
Event: Annual Day 2026
Date: March 15, 2026
Preparation Period: Feb 15 - Mar 14 (4 weeks)

Schedule Modifications:

Week 1-2 (Feb 15-28):
  Regular Classes: Continue
  Activity Periods: Extended
    - Friday P7-P8: Regular (90 min)
    - Saturday P1-P4: Extra rehearsals (180 min)
  
  Participants: 200 students (drama, music, dance)
  
  Hall Allocation:
    - Auditorium: Drama rehearsals (Mon-Fri, 3:30-5:30 PM)
    - Music Room: Music practice (Mon-Fri, 3:30-5:30 PM)
    - Dance Studio: Dance practice (Mon-Fri, 3:30-5:30 PM)

Week 3 (Mar 1-7):
  Regular Classes: Reduced (P1-P6 only)
  Rehearsals: Intensified
    - Daily P7-P8: All participants (2 hours)
    - After school: 3:30-6:00 PM (2.5 hours)
  
  Full Dress Rehearsal: Saturday Mar 7 (9 AM - 5 PM)

Week 4 (Mar 8-14):
  Regular Classes: Minimal (P1-P4 only)
  Final Preparations: Full day
    - P5-P8: Stage setup, decoration
    - After school: Final rehearsals
  
  Final Dress Rehearsal: Mar 13 (Full day)

Event Day (Mar 15):
  No Classes: Holiday for all students
  Event: 10 AM - 1 PM (Morning show)
          3 PM - 6 PM (Evening show)
  
  Participants: Report 8 AM
  Audience: Parents, guests (1,000+ expected)

Post-Event (Mar 16):
  Regular Schedule: Resumes
  Activity Periods: Back to normal
```

---

## 📊 DATABASE SCHEMA

### Activity Schedule Table

```sql
CREATE TABLE activity_schedule (
  activity_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Activity Details
  activity_name VARCHAR(200) NOT NULL,
  activity_type ENUM('CLUB', 'SPORTS', 'CULTURAL', 'SPECIAL_EVENT', 'COMPETITION') NOT NULL,
  category VARCHAR(100),
  
  -- Timing
  day_of_week VARCHAR(10),
  period_number INT,
  start_time TIME,
  end_time TIME,
  duration_minutes INT,
  
  -- Location
  room_id INT,
  location_name VARCHAR(200),
  
  -- Supervision
  supervisor_id INT NOT NULL,
  assistant_supervisor_id INT,
  
  -- Capacity
  max_capacity INT NOT NULL,
  enrolled_students INT DEFAULT 0,
  waitlist_count INT DEFAULT 0,
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Status
  status ENUM('ACTIVE', 'INACTIVE', 'FULL', 'CANCELLED') DEFAULT 'ACTIVE',
  
  -- Equipment
  equipment_required JSON,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (supervisor_id) REFERENCES teachers(teacher_id),
  FOREIGN KEY (room_id) REFERENCES rooms(room_id),
  
  -- Indexes
  INDEX idx_activity_type (activity_type),
  INDEX idx_day (day_of_week),
  INDEX idx_status (status)
);
```

### Student Activity Enrollment Table

```sql
CREATE TABLE student_activity_enrollment (
  enrollment_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Student & Activity
  student_id INT NOT NULL,
  activity_id INT NOT NULL,
  
  -- Preferences
  preference_rank INT, -- 1, 2, or 3
  allocated_choice INT, -- Which preference was allocated
  
  -- Enrollment
  enrollment_status ENUM('ENROLLED', 'WAITLIST', 'WITHDRAWN') DEFAULT 'ENROLLED',
  enrollment_date DATE NOT NULL,
  
  -- Attendance
  sessions_attended INT DEFAULT 0,
  total_sessions INT DEFAULT 0,
  attendance_percentage DECIMAL(5,2),
  
  -- Performance
  performance_rating ENUM('EXCELLENT', 'GOOD', 'AVERAGE', 'NEEDS_IMPROVEMENT'),
  supervisor_comments TEXT,
  
  -- Certificate
  certificate_issued BOOLEAN DEFAULT FALSE,
  certificate_date DATE,
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id),
  FOREIGN KEY (activity_id) REFERENCES activity_schedule(activity_id),
  
  -- Indexes
  INDEX idx_student (student_id),
  INDEX idx_activity (activity_id),
  INDEX idx_status (enrollment_status),
  
  -- Unique Constraint
  UNIQUE KEY uk_student_activity_year (student_id, activity_id, academic_year)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Activity Capacity Management

```javascript
FUNCTION validate_activity_enrollment(student, activity) {
  // Rule 1: Check capacity
  IF activity.enrolled_students >= activity.max_capacity {
    RETURN {
      allowed: FALSE,
      reason: 'Activity at full capacity',
      action: 'ADD_TO_WAITLIST'
    }
  }
  
  // Rule 2: Check conflicts
  student_activities = GET_STUDENT_ACTIVITIES(student)
  FOR each existing_activity IN student_activities {
    IF TIMES_OVERLAP(activity, existing_activity) {
      RETURN {
        allowed: FALSE,
        reason: 'Time conflict with existing activity'
      }
    }
  }
  
  // Rule 3: Maximum activities per student
  IF student_activities.count >= 2 {
    RETURN {
      allowed: FALSE,
      reason: 'Maximum 2 activities per student'
    }
  }
  
  RETURN {allowed: TRUE}
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations
1. **Student Management**: Student lists, preferences
2. **Facilities Module**: Room bookings
3. **Certificate Module**: Participation certificates
4. **Parent Portal**: Activity updates

### Inbound Integrations
1. **Master Timetable**: Activity period slots
2. **Teacher Management**: Supervisor availability
3. **Event Management**: Special event schedules

---

## 👥 USER WORKFLOWS

### Workflow: Student Club Selection

**Actor:** Student  
**Duration:** 10-15 minutes  
**Frequency:** Once per year

**Steps:**
1. Login to Student Portal
2. Navigate to "Club Selection"
3. View available clubs (27 clubs)
4. Read club descriptions
5. Check capacity and current enrollment
6. Select 3 preferences:
   - 1st Choice: Robotics Club
   - 2nd Choice: Coding Club
   - 3rd Choice: Science Club
7. Submit preferences
8. Receive confirmation email
9. Wait for allocation (1 week)
10. Check allocation result
11. Result: Allocated to Coding Club (2nd choice)
12. Accept allocation
13. Attend first session on Friday

**Expected Outcome:** Student successfully enrolled in club activity.

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

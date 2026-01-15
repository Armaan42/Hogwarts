# TIMETABLE & SCHEDULING MODULE - COMPLETE DEPENDENCY ANALYSIS

## 🎯 MODULE OVERVIEW

**Name:** Timetable & Scheduling Module  
**Role:** Academic Schedule Optimization & Resource Allocation Engine  
**Type:** Core Academic Operations Module  
**Dependencies:** 15+ modules rely on timetable for daily operations  

**Primary Functions:**
- Master Timetable Generation
- Teacher-Subject-Section Allocation
- Classroom & Lab Assignment
- Period Scheduling & Optimization
- Substitute Teacher Management
- Exam Schedule Creation
- Special Activity Scheduling
- Timetable Conflict Resolution
- Resource Utilization Analytics
- Holiday & Event Calendar Integration
- Timetable Distribution & Publishing
- Real-time Schedule Updates

---

## 📤 OUTBOUND CONNECTIONS (Timetable → Other Modules)

### 1. TO TEACHER MANAGEMENT MODULE

**WHY This Connection Exists:**
Teacher availability, workload capacity, and subject expertise are fundamental constraints for timetable generation. Fair workload distribution prevents teacher burnout and ensures quality teaching. Subject specialization determines which teacher can teach which class.

**DATA FLOW:**
- **Teaching Assignments:**
  - Teacher-subject-section mapping
  - Periods allocated per teacher per week
  - Grade levels assigned
  - Multiple subject assignments (if applicable)
- **Workload Distribution:**
  - Total periods allocated (max 30/week for full-time)
  - Free periods (min 6/week for preparation)
  - Consecutive period limits (max 3 consecutive)
  - Daily workload balance (4-6 periods/day)
- **Invigilation Duties:**
  - Exam invigilation schedule
  - Duty periods (lunch, break, gate)
  - Extra-curricular supervision
- **Substitute Coverage:**
  - Free period availability for substitution
  - Subject compatibility for covering absent teachers
- **Teacher Preferences:**
  - Preferred time slots (if possible)
  - Avoided consecutive periods
  - Lab vs classroom preferences

**TRIGGER EVENT:**
- **Timetable Generation:** Teacher assignments created
- **Teacher Joins:** New teacher added to timetable
- **Teacher Leaves:** Timetable adjusted, classes redistributed
- **Workload Review:** Periodic workload balancing

**IMPACT:**
- **Balanced Workload:**
  - Mr. Verma (Math, Senior): 4 sections (9A, 9B, 10A, 10B) × 6 periods = 24 periods
  - Free periods: 6 (Mon 2, Tue 1, Wed 1, Thu 1, Fri 1)
  - Invigilation: 2 periods (exam duty)
  - Total: 24 + 2 = 26/30 periods (87% utilization)
- **Subject Expertise Matching:**
  - Ms. Sharma (English Literature specialist): Assigned Grades 11-12 (advanced)
  - Mr. Patel (English, junior): Assigned Grades 6-8 (foundational)
  - Ensures quality teaching at appropriate levels
- **Overload Prevention:**
  - Mrs. Gupta initially assigned 32 periods
  - System alerts: "Teacher overload"
  - Timetable coordinator redistributes: 2 sections moved to Mr. Singh
  - Final: Mrs. Gupta 28 periods, Mr. Singh 26 periods
- **Substitute Availability:**
  - Ms. Sharma absent Tuesday
  - Her classes: 9A (10 AM), 9B (11 AM), 9C (2 PM)
  - System checks: Mr. Gupta (English) has free periods at 10 AM, 11 AM, 2 PM
  - Mr. Gupta assigned as substitute (3 periods)
  - Notification sent: "Cover 9A, 9B, 9C English on Tuesday"

**BUSINESS LOGIC:**
```
FUNCTION allocate_teacher_workload(teacher, sections, subject):
  total_periods = 0
  daily_distribution = {Mon: 0, Tue: 0, Wed: 0, Thu: 0, Fri: 0, Sat: 0}
  
  FOR each section IN sections:
    periods_per_week = GET_SUBJECT_PERIODS(subject, section.grade)
    
    // Distribute periods across week
    FOR i = 1 TO periods_per_week:
      // Find day with minimum load for this teacher
      min_day = FIND_MIN_LOAD_DAY(daily_distribution)
      
      // Find available slot on that day
      slot = FIND_AVAILABLE_SLOT(teacher, min_day, section)
      
      IF slot EXISTS:
        ASSIGN_PERIOD(teacher, section, subject, min_day, slot)
        daily_distribution[min_day] += 1
        total_periods += 1
      ELSE:
        ALERT("Cannot allocate period for {teacher.name}, {section.name}, {subject}")
      END IF
    END FOR
  END FOR
  
  // Check constraints
  IF total_periods > teacher.max_capacity:
    RETURN {status: "OVERLOAD", periods: total_periods, max: teacher.max_capacity}
  END IF
  
  // Calculate free periods
  free_periods = (6 days × 8 periods) - total_periods - invigilation_duties
  
  IF free_periods < 6:
    ALERT("Insufficient free periods for {teacher.name}: {free_periods}")
  END IF
  
  teacher.allocated_periods = total_periods
  teacher.free_periods = free_periods
  teacher.daily_distribution = daily_distribution
  
  RETURN {status: "SUCCESS", workload: total_periods, free: free_periods}
END FUNCTION

FUNCTION find_substitute_teacher(absent_teacher, date, period_time):
  // Get classes to be covered
  classes = GET_CLASSES(absent_teacher, date)
  
  substitutes_assigned = []
  
  FOR each class IN classes:
    // Find teachers with same subject expertise
    eligible_teachers = GET_TEACHERS(subject=class.subject, status="ACTIVE")
    
    // Filter by availability (free period at that time)
    available = []
    FOR each teacher IN eligible_teachers:
      IF HAS_FREE_PERIOD(teacher, date, class.time):
        available.add(teacher)
      END IF
    END FOR
    
    IF available.count > 0:
      // Select teacher with minimum substitutions this week
      substitute = SELECT_MIN_SUBSTITUTIONS(available)
      
      ASSIGN_SUBSTITUTE(substitute, class, date)
      substitutes_assigned.add({class: class, substitute: substitute})
      
      NOTIFY substitute("Substitute duty: {class.section}, {class.subject}, {class.time}, Room {class.room}")
    ELSE:
      // No substitute available
      ALERT("No substitute for {class.section}, {class.subject}, {class.time}")
      NOTIFY students, parents("Class cancelled: {class.subject}, {class.time}")
    END IF
  END FOR
  
  RETURN substitutes_assigned
END FUNCTION
```

**EXAMPLE:**
- **Mr. Verma's Weekly Timetable (Math Teacher):**
  - **Monday:** 9A (P1), 9B (P3), 10A (P5), Free (P2, P4, P6, P7, P8)
  - **Tuesday:** 9A (P2), 10B (P4), Free (P1, P3, P5, P6, P7, P8)
  - **Wednesday:** 9B (P1), 10A (P3), 10B (P6), Free (P2, P4, P5, P7, P8)
  - **Thursday:** 9A (P4), 9B (P5), Free (P1, P2, P3, P6, P7, P8)
  - **Friday:** 10A (P2), 10B (P3), Free (P1, P4, P5, P6, P7, P8)
  - **Saturday:** 9A (P1), 9B (P2), 10A (P4), 10B (P5), Free (P3, P6, P7, P8)
  - **Total:** 24 teaching periods, 24 free periods
  - **Daily Load:** 3-4 periods/day (balanced)
  - **Consecutive Periods:** Max 2 (meets constraint)

---

### 2. TO STUDENT MANAGEMENT MODULE

**WHY This Connection Exists:**
Students need personalized timetables showing their daily schedule. Section assignment determines which timetable applies. Timetable accessibility via student portal enables planning and preparation.

**DATA FLOW:**
- **Student Timetable:**
  - Period-wise daily schedule (Monday-Saturday)
  - Subject name and code
  - Teacher name
  - Classroom/lab location
  - Period timing
- **Section-based Schedule:**
  - All students in same section have identical timetable
  - Elective subjects may vary (if applicable)
- **Timetable Access:**
  - Student portal/app
  - Downloadable PDF
  - Printable format
  - Mobile-friendly view

**TRIGGER EVENT:**
- **Timetable Published:** Students can access their schedule
- **Timetable Updated:** Students notified of changes
- **Section Assignment:** Student linked to section timetable

**IMPACT:**
- **Student Access:**
  - Aarav (Grade 9A) logs into student portal
  - Views Monday timetable:
    - P1 (8:30-9:15): Math - Mr. Verma - Room 205
    - P2 (9:15-10:00): English - Ms. Sharma - Room 206
    - Break (10:00-10:15)
    - P3 (10:15-11:00): Science - Mr. Patel - Lab 1
    - P4 (11:00-11:45): Social Studies - Mrs. Gupta - Room 207
    - P5 (11:45-12:30): Hindi - Mr. Singh - Room 208
    - Lunch (12:30-1:00)
    - P6 (1:00-1:45): Computer - Mr. Kumar - Computer Lab
    - P7 (1:45-2:30): PE - Mr. Reddy - Sports Ground
    - P8 (2:30-3:15): Library - Ms. Mehta - Library
  - Downloads PDF for offline access
  - Sets reminders for lab periods (bring lab coat)
- **Preparation:**
  - Priya checks tomorrow's timetable
  - Sees: Math test (P1), Science lab (P3)
  - Prepares: Math revision, lab coat, lab manual
- **Schedule Changes:**
  - Ms. Sharma absent Tuesday
  - Timetable updated: English (P2) - Mr. Gupta (substitute)
  - Push notification sent: "Schedule change: English P2 - Mr. Gupta"

**BUSINESS LOGIC:**
```
FUNCTION get_student_timetable(student):
  section = student.section
  grade = student.grade
  
  // Get section timetable
  timetable = GET_SECTION_TIMETABLE(grade, section)
  
  // Format for student view
  student_schedule = {}
  
  FOR each day IN ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"]:
    daily_schedule = []
    
    periods = timetable[day]
    FOR each period IN periods:
      period_info = {
        period_number: period.number,
        time: period.start_time + " - " + period.end_time,
        subject: period.subject.name,
        teacher: period.teacher.name,
        room: period.room.number,
        type: period.type  // LECTURE, LAB, LIBRARY, PE, etc.
      }
      daily_schedule.add(period_info)
    END FOR
    
    student_schedule[day] = daily_schedule
  END FOR
  
  RETURN student_schedule
END FUNCTION
```

**EXAMPLE:**
- **Grade 9A Monday Timetable (All 36 students):**
  - Same schedule for entire section
  - Displayed in student portal, parent portal, teacher app
  - Accessible 24/7 online
  - Updated in real-time if changes occur

---

### 3. TO PARENT PORTAL

**WHY This Connection Exists:**
Parents need visibility into their child's daily schedule to plan study time, parent-teacher meetings, and understand academic structure. Schedule changes must be communicated immediately.

**DATA FLOW:**
- Child's complete timetable
- Teacher contact information
- Classroom locations
- Real-time schedule updates
- Substitute teacher notifications

**TRIGGER EVENT:**
- Timetable published
- Schedule changes (teacher absence, room change)
- Parent requests timetable

**IMPACT:**
- Parent views Priya's Monday schedule
- Plans study time: Math homework after school (P1 topic)
- Schedules parent-teacher meeting: Ms. Sharma (English) available Thursday 2 PM
- Receives notification: "English P2 Tuesday - Mr. Gupta (substitute)"

**BUSINESS LOGIC:**
```
FUNCTION parent_view_timetable(student):
  timetable = GET_STUDENT_TIMETABLE(student)
  
  // Add teacher contact info
  FOR each period IN timetable:
    period.teacher_email = period.teacher.email
    period.teacher_phone = period.teacher.phone
    period.teacher_free_periods = GET_FREE_PERIODS(period.teacher)
  END FOR
  
  RETURN timetable
END FUNCTION
```

---

### 4. TO CLASSROOM & FACILITIES MODULE

**WHY This Connection Exists:**
Timetable determines room utilization. Labs, special rooms, and classrooms must be allocated without conflicts. Maintenance schedules require timetable adjustments.

**DATA FLOW:**
- Room allocations (which section uses which room when)
- Lab schedules (Science, Computer, Art, Music)
- Special room bookings (Auditorium, Sports Hall, Library)
- Utilization analytics

**TRIGGER EVENT:**
- Timetable created
- Room maintenance scheduled
- Special event requires room

**IMPACT:**
- Science Lab 1 schedule:
  - Mon 10 AM: Grade 9A (Mr. Patel)
  - Mon 11 AM: Grade 9B (Mr. Patel)
  - Tue 9 AM: Grade 10A (Mrs. Gupta)
  - Utilization: 75% (optimal)
- Room 205 under repair (1 week):
  - Grade 9A shifted to Room 210
  - Timetable updated, students notified

**BUSINESS LOGIC:**
```
FUNCTION allocate_rooms(timetable):
  FOR each period IN timetable:
    IF period.subject.requires_lab:
      lab = FIND_AVAILABLE_LAB(period.subject.lab_type, period.time)
      period.room = lab
    ELSE:
      classroom = FIND_AVAILABLE_CLASSROOM(period.grade, period.time)
      period.room = classroom
    END IF
  END FOR
  
  // Check for conflicts
  conflicts = DETECT_ROOM_CONFLICTS(timetable)
  IF conflicts.count > 0:
    RESOLVE_CONFLICTS(conflicts)
  END IF
END FUNCTION
```

---

### 5. TO SUBSTITUTE TEACHER MODULE

**WHY This Connection Exists:**
Teacher absence requires immediate substitute assignment based on free period availability and subject expertise.

**DATA FLOW:**
- Teacher absence notification
- Classes to be covered
- Available substitutes (free periods)
- Substitute assignments
- Notification to substitute and students

**TRIGGER EVENT:**
- Teacher applies leave
- Teacher marked absent (emergency)
- Substitute assignment needed

**IMPACT:**
- Ms. Sharma (English) absent Tuesday
- Classes: 9A (10 AM), 9B (11 AM), 9C (2 PM)
- System finds: Mr. Gupta (English) free at these times
- Mr. Gupta assigned, notified via SMS
- Students notified: "English P2 - Mr. Gupta (substitute)"

**BUSINESS LOGIC:**
```
FUNCTION assign_substitute(absent_teacher, date):
  classes = GET_CLASSES(absent_teacher, date)
  
  FOR each class IN classes:
    substitutes = GET_TEACHERS(
      subject=class.subject,
      has_free_period=class.time
    )
    
    IF substitutes.exists:
      substitute = SELECT_MIN_SUBSTITUTIONS(substitutes)
      ASSIGN(substitute, class)
      NOTIFY substitute, students, parents
    ELSE:
      ALERT("No substitute for {class}")
      CANCEL_CLASS(class)
    END IF
  END FOR
END FUNCTION
```

---

### 6. TO EXAM SCHEDULING MODULE

**WHY This Connection Exists:**
Exam schedules must avoid regular class clashes. Exam halls allocated based on classroom availability. Invigilation duties assigned based on teacher free periods.

**DATA FLOW:**
- Exam dates (working days)
- Exam halls (available classrooms)
- Invigilation duties
- Exam duration and timing

**TRIGGER EVENT:**
- Exam scheduled
- Invigilation duties assigned

**IMPACT:**
- Mid-term exams: March 10-20
- Grade 9-10 exams: Regular classes suspended
- Grade 6-8: Continue normal timetable
- Halls: Rooms 201-210 (not used by Grade 6-8)
- Invigilation: Teachers with free periods assigned

---

### 7. TO ATTENDANCE MODULE

**WHY This Connection Exists:**
Period-wise attendance tracked based on timetable. Each period has specific teacher-subject-section mapping for attendance.

**DATA FLOW:**
- Period schedule (subject, teacher, time)
- Attendance marking slots
- Teacher-student mapping

**TRIGGER EVENT:**
- Period starts
- Attendance marking time

**IMPACT:**
- Monday 9 AM: Math period (Grade 9A, Mr. Verma)
- Mr. Verma marks attendance in app
- System knows: Math, Grade 9A, 9:00-9:45 AM
- Absent students flagged, parents notified

---

### 8. TO ACADEMIC CALENDAR MODULE

**WHY This Connection Exists:**
Timetable aligned with academic calendar. Holidays, events, exams integrated into scheduling.

**DATA FLOW:**
- Working days
- Holidays (national, school-specific)
- Events (sports day, annual function)
- Exam periods

**TRIGGER EVENT:**
- Calendar updated
- Holiday declared
- Event scheduled

**IMPACT:**
- March 15: Holi (holiday) - No classes
- March 13: Sports Day - Special schedule
- Regular timetable suspended, sports events scheduled

---

## 📥 INBOUND CONNECTIONS (Other Modules → Timetable)

### FROM TEACHER MANAGEMENT

**WHY:** Teacher availability, leaves, expertise determine timetable feasibility.

**DATA RECEIVED:**
- Teacher availability (full-time/part-time)
- Leave applications
- Subject expertise
- Preferred teaching slots

**IMPACT:**
- Ms. Gupta on leave (3 days): Substitute assigned
- Mr. Verma part-time (15 periods/week): Allocated only 15 periods
- New teacher joins: Added to timetable

**TRIGGER:** Teacher joins, leaves, availability changes

---

### FROM STUDENT MANAGEMENT

**WHY:** Student enrollment determines section count and capacity.

**DATA RECEIVED:**
- Grade-wise enrollment
- Section count (40 students/section)
- Subject choices (electives)

**IMPACT:**
- Grade 9: 180 students
- Sections: 5 (A, B, C, D, E)
- Timetable created for 5 sections

**TRIGGER:** Admissions complete, sections finalized

---

### FROM ACADEMIC CALENDAR

**WHY:** Holidays, events affect timetable.

**DATA RECEIVED:**
- Holiday list
- Event dates
- Exam schedules

**IMPACT:**
- Diwali holidays: Oct 20-25 - No classes
- Sports Day: Nov 15 - Special schedule

**TRIGGER:** Calendar published, events scheduled

---

### FROM FACILITIES MODULE

**WHY:** Room availability affects timetable.

**DATA RECEIVED:**
- Classroom availability
- Lab schedules
- Maintenance schedules

**IMPACT:**
- Room 205 under repair: Grade 9A shifted to Room 210

**TRIGGER:** Room unavailable, maintenance scheduled

---

### FROM CURRICULUM MODULE

**WHY:** Subject periods per week defined by curriculum.

**DATA RECEIVED:**
- Subject list
- Periods per week (Math: 6, Science: 6, English: 5)
- Lab requirements

**IMPACT:**
- Grade 9 curriculum: Math 6 periods, Science 6 periods
- Timetable allocates exactly 6 periods each

**TRIGGER:** Curriculum finalized

---

## 📊 SUMMARY

**Timetable & Scheduling Module connects to 15+ modules**

**Timetable Constraints:**
- School hours: 8:30 AM - 3:30 PM (7 hours)
- Periods: 8 periods/day × 45 mins = 6 hours
- Breaks: 2 breaks (15 mins + 30 mins lunch) = 45 mins
- Assembly: 15 mins (Monday)

**Period Structure:**
- Period 1: 8:30-9:15 AM
- Period 2: 9:15-10:00 AM
- Break: 10:00-10:15 AM
- Period 3: 10:15-11:00 AM
- Period 4: 11:00-11:45 AM
- Period 5: 11:45-12:30 PM
- Lunch: 12:30-1:00 PM
- Period 6: 1:00-1:45 PM
- Period 7: 1:45-2:30 PM
- Period 8: 2:30-3:15 PM

**Weekly Schedule:**
- Working days: 6 (Monday-Saturday)
- Periods/week: 48 (8 periods × 6 days)
- Subject allocation (Grade 9):
  - Math: 6 periods
  - Science: 6 periods
  - English: 5 periods
  - Social Studies: 5 periods
  - Hindi: 4 periods
  - Computer: 2 periods
  - PE: 2 periods
  - Art/Music: 2 periods
  - Library: 1 period
  - Moral Science: 1 period
  - **Total: 34 periods** (14 for activities/study)

**Optimization Goals:**
- No teacher overload (max 30 periods/week)
- No consecutive periods for same subject
- Lab periods scheduled appropriately
- Balanced daily workload
- Minimize teacher idle time
- Optimal classroom utilization

**Timetable Types:**
- Regular (daily classes)
- Exam (exam schedule)
- Special (events, sports day)
- Substitute (teacher absence)
- Remedial (extra classes)

**Change Management:**
- Teacher absence: Substitute within 1 hour
- Room unavailable: Alternate room allocated
- Schedule conflict: Manual resolution
- Last-minute changes: SMS/app notification

**Analytics:**
- Teacher utilization: 85% (avg 25.5/30 periods)
- Classroom utilization: 90%
- Lab utilization: 75%
- Timetable conflicts: <5%

**Data Freshness:**
- Real-time: Teacher absence, substitute assignment
- Daily: Period schedules, attendance slots
- Weekly: Timetable adjustments
- Monthly: Utilization reports
- Annually: Master timetable creation

**Timetable Generation Process:**
1. Collect constraints (teachers, rooms, curriculum)
2. Allocate core subjects (Math, Science, English)
3. Allocate secondary subjects
4. Assign labs and special rooms
5. Balance teacher workload
6. Resolve conflicts
7. Optimize (minimize idle time, consecutive periods)
8. Publish and distribute

**Conflict Resolution:**
- Room conflict: Find alternate room
- Teacher conflict: Adjust period timing
- Lab conflict: Reschedule lab period
- Overload: Redistribute sections

This module is the **"academic operations backbone"** - orchestrating daily school operations through optimized scheduling of teachers, students, and resources while maintaining flexibility for changes and special events.

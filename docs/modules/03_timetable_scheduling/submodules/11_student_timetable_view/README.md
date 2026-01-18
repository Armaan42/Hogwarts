# STUDENT TIMETABLE VIEW & DISTRIBUTION - COMPLETE DOCUMENTATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-STUDENT-VIEW-011  
**Category:** Student Services  
**Priority:** Critical (P0)  
**Owner:** Academic Operations Team

---

## 📋 OVERVIEW

The Student Timetable View & Distribution submodule provides personalized, accessible timetable views for students and parents through multiple channels including web portal, mobile app, and downloadable formats. It ensures real-time synchronization, offline access, and instant notifications for schedule changes.

### Purpose

Provide students and parents with easy access to personalized timetables, enable schedule planning, support offline viewing, deliver real-time updates for changes, and ensure seamless distribution across all platforms (web, mobile, print).

### Scope

- **Personalized Views**: Student-specific timetable display
- **Multi-Platform Access**: Web portal, mobile app, parent portal
- **Multiple Formats**: Daily, weekly, subject-wise views
- **Export Options**: PDF, iCal, Google Calendar integration
- **Real-Time Updates**: Instant notifications for changes
- **Offline Access**: Cached timetables for offline viewing
- **Print Support**: Printer-friendly formats
- **Parent Integration**: Parent portal synchronization

---

## 🎯 KEY FEATURES

### 1. Personalized Timetable Display

#### 1.1 Student-Specific Timetable

**Individual Student View:**
```yaml
Student: Aarav Sharma
Grade: 9
Section: A
Roll Number: 15
Academic Year: 2025-26

Monday Schedule:
  Period 1 (8:30-9:15 AM):
    Subject: Mathematics
    Teacher: Mrs. Verma
    Room: 205
    Type: Theory
    
  Period 2 (9:15-10:00 AM):
    Subject: English Literature
    Teacher: Ms. Kapoor
    Room: 206
    Type: Theory
    
  Break (10:00-10:15 AM):
    Type: Short Break
    Location: Cafeteria
    
  Period 3 (10:15-11:00 AM):
    Subject: Physics
    Teacher: Dr. Patel
    Room: Science Lab 1
    Type: Laboratory
    Notes: "Bring lab coat and manual"
    
  Period 4 (11:00-11:45 AM):
    Subject: Social Studies
    Teacher: Mr. Singh
    Room: 207
    Type: Theory
    
  Period 5 (11:45-12:30 PM):
    Subject: Hindi
    Teacher: Mrs. Gupta
    Room: 208
    Type: Theory
    
  Lunch Break (12:30-1:00 PM):
    Type: Lunch
    Location: Cafeteria - Section A (12:30-12:45)
    
  Period 6 (1:00-1:45 PM):
    Subject: Computer Science
    Teacher: Mr. Kumar
    Room: Computer Lab 2
    Type: Practical
    Notes: "Bring project files"
    
  Period 7 (1:45-2:30 PM):
    Subject: Physical Education
    Teacher: Mr. Reddy
    Location: Sports Ground
    Type: Activity
    Notes: "Wear sports uniform"
    
  Period 8 (2:30-3:15 PM):
    Subject: Library Period
    Teacher: Ms. Mehta
    Location: School Library
    Type: Reading/Study
```

#### 1.2 Class-wise Timetable View

**Section Timetable (All Students in 9A):**
```yaml
Grade 9 - Section A
Total Students: 36
Class Teacher: Mrs. Verma

Weekly Overview:
  Total Periods: 48 (8 periods × 6 days)
  Academic Periods: 42
  Activity Periods: 4
  Library/Study: 2

Subject Distribution:
  Mathematics: 6 periods/week
  Science (Physics, Chemistry, Biology): 6 periods/week
  English: 5 periods/week
  Social Studies: 5 periods/week
  Hindi: 4 periods/week
  Computer Science: 2 periods/week
  Physical Education: 2 periods/week
  Art/Music: 2 periods/week
  Library: 1 period/week
  Moral Science: 1 period/week

Monday-Saturday Schedule:
  [Complete weekly grid showing all periods for entire section]
```

---

### 2. Multiple View Options

#### 2.1 Daily View

**Today's Schedule (Quick View):**
```yaml
Date: Monday, January 20, 2026
Student: Aarav Sharma (9A)

Today's Classes:
  ✓ Period 1 - Mathematics (8:30 AM) - Room 205
  ✓ Period 2 - English (9:15 AM) - Room 206
  → Period 3 - Physics Lab (10:15 AM) - Lab 1 [NEXT]
  • Period 4 - Social Studies (11:00 AM) - Room 207
  • Period 5 - Hindi (11:45 AM) - Room 208
  • Period 6 - Computer (1:00 PM) - Comp Lab 2
  • Period 7 - PE (1:45 PM) - Sports Ground
  • Period 8 - Library (2:30 PM) - Library

Reminders:
  ⚠️ Physics Lab today - Bring lab coat
  ⚠️ Computer class - Bring project files
  ⚠️ PE period - Wear sports uniform

Homework Due:
  📝 Mathematics - Chapter 5 exercises (Due today)
  📝 English - Essay submission (Due today)

Tests Scheduled:
  📊 Social Studies - Unit Test (Period 4)
```

#### 2.2 Weekly View

**Week at a Glance:**
```yaml
Week: January 20-25, 2026
Student: Aarav Sharma (9A)

        MON         TUE         WED         THU         FRI         SAT
P1      Math        Science     English     Math        Science     English
        Verma       Patel       Kapoor      Verma       Patel       Kapoor
        
P2      English     Math        Science     English     Math        Hindi
        Kapoor      Verma       Patel       Kapoor      Verma       Gupta
        
        [BREAK]     [BREAK]     [BREAK]     [BREAK]     [BREAK]     [BREAK]
        
P3      Physics     Chemistry   Biology     Physics     Chemistry   Math
        Lab 1       Lab 2       Lab 1       Lab 1       Lab 2       205
        
P4      Social      Hindi       Computer    Social      Art         Music
        Singh       Gupta       Kumar       Singh       Sharma      Reddy
        
P5      Hindi       Social      Math        Computer    English     Social
        Gupta       Singh       Verma       Kumar       Kapoor      Singh
        
        [LUNCH]     [LUNCH]     [LUNCH]     [LUNCH]     [LUNCH]     [LUNCH]
        
P6      Computer    English     Social      Hindi       Math        Science
        Kumar       Kapoor      Singh       Gupta       Verma       Patel
        
P7      PE          Library     PE          Moral Sci   Activity    Study
        Ground      Library     Ground      208         Hall        Library
        
P8      Library     Activity    Study       Library     Study       Activity
        Library     Hall        205         Library     205         Hall

Weekly Highlights:
  🔬 3 Lab sessions (Physics, Chemistry, Biology)
  📚 3 Library periods
  ⚽ 2 PE sessions
  🎨 1 Art, 1 Music period
```

#### 2.3 Subject-wise View

**Mathematics Schedule (All Math Periods):**
```yaml
Subject: Mathematics
Teacher: Mrs. Verma
Periods per Week: 6

This Week's Math Classes:
  Monday:
    Period 1 (8:30-9:15 AM) - Room 205
    Topic: Polynomials - Factorization
    
  Tuesday:
    Period 2 (9:15-10:00 AM) - Room 205
    Topic: Polynomials - Practice Problems
    
  Wednesday:
    Period 5 (11:45-12:30 PM) - Room 205
    Topic: Linear Equations - Introduction
    
  Thursday:
    Period 1 (8:30-9:15 AM) - Room 205
    Topic: Linear Equations - Graphing
    
  Friday:
    Period 2 (9:15-10:00 AM) - Room 205
    Topic: Linear Equations - Applications
    
  Saturday:
    Period 3 (10:15-11:00 AM) - Room 205
    Topic: Revision and Problem Solving

Upcoming Assessments:
  📊 Unit Test - Polynomials (January 28)
  📝 Assignment - Linear Equations (Due: February 2)
  
Teacher's Free Periods (for doubt clearing):
  Monday: Period 4 (11:00 AM)
  Wednesday: Period 2 (9:15 AM)
  Friday: Period 7 (1:45 PM)
```

---

### 3. Parent Portal Integration

#### 3.1 Parent View Features

**Parent Dashboard - Child's Timetable:**
```yaml
Parent: Mr. Rajesh Sharma
Child: Aarav Sharma (Grade 9A)

Quick Access:
  ✓ View Today's Schedule
  ✓ View Weekly Timetable
  ✓ Download PDF Timetable
  ✓ Export to Google Calendar
  ✓ View Teacher Contact Info
  ✓ Schedule Parent-Teacher Meeting

Today's Schedule (Parent View):
  Current Time: 10:30 AM
  
  Completed Classes:
    ✓ Period 1 - Mathematics (8:30 AM)
      Teacher: Mrs. Verma
      Attendance: Present
      
    ✓ Period 2 - English (9:15 AM)
      Teacher: Ms. Kapoor
      Attendance: Present
  
  Ongoing:
    → Period 3 - Physics Lab (10:15-11:00 AM)
      Teacher: Dr. Patel
      Location: Science Lab 1
      Status: In Progress
  
  Upcoming:
    • Period 4 - Social Studies (11:00 AM)
    • Period 5 - Hindi (11:45 AM)
    • [Lunch Break] (12:30 PM)
    • Period 6 - Computer (1:00 PM)
    • Period 7 - PE (1:45 PM)
    • Period 8 - Library (2:30 PM)

Teacher Contact Information:
  Mrs. Verma (Mathematics):
    Email: verma.math@hogwarts.edu
    Phone: +91-98765-43210
    Free Periods: Mon P4, Wed P2, Fri P7
    
  Ms. Kapoor (English):
    Email: kapoor.english@hogwarts.edu
    Phone: +91-98765-43211
    Free Periods: Tue P3, Thu P5
    
  [... other teachers]

Notifications:
  🔔 Schedule Change: English Period 2 (Tuesday) moved to Period 3
  🔔 Reminder: Physics Lab tomorrow - Ensure lab coat is ready
  🔔 Alert: Social Studies Unit Test on Thursday
```

#### 3.2 Parent Notifications

**Real-Time Parent Alerts:**
```yaml
Notification Types:

1. Schedule Changes:
   Title: "Timetable Update - English Class Rescheduled"
   Message: "English Period 2 on Tuesday has been moved to Period 3 due to teacher meeting. New time: 10:15-11:00 AM"
   Sent: SMS + Email + App Push
   Priority: High
   
2. Teacher Substitution:
   Title: "Substitute Teacher - Mathematics"
   Message: "Mrs. Verma is on leave today. Mr. Saxena will cover Mathematics Period 1 (8:30 AM)"
   Sent: SMS + App Push
   Priority: Medium
   
3. Special Events:
   Title: "Special Assembly - Adjusted Schedule"
   Message: "Republic Day assembly at 9:00 AM. Regular classes start from Period 3 (10:15 AM)"
   Sent: Email + App Push
   Priority: High
   
4. Exam Schedule:
   Title: "Mid-term Exam Timetable Published"
   Message: "Mid-term exams from March 10-20. View exam schedule in parent portal"
   Sent: Email + App Push
   Priority: High
```

---

### 4. Mobile App Synchronization

#### 4.1 Mobile App Features

**Student Mobile App - Timetable Module:**
```yaml
App: Hogwarts Student App
Platform: iOS, Android
Version: 3.2.0

Features:

1. Dashboard Widget:
   - Current period display
   - Next class countdown
   - Today's remaining classes
   - Quick access to full timetable
   
2. Timetable Views:
   - Daily schedule (default)
   - Weekly grid view
   - Subject-wise view
   - Calendar integration
   
3. Smart Notifications:
   - Period start reminders (5 min before)
   - Room change alerts
   - Teacher substitution notices
   - Exam schedule updates
   
4. Offline Mode:
   - Cached timetable (30 days)
   - Works without internet
   - Auto-sync when online
   
5. Interactive Features:
   - Tap period for details
   - View teacher info
   - Navigate to room location (campus map)
   - Add to device calendar
   
6. Customization:
   - Notification preferences
   - Reminder timing
   - View preferences (daily/weekly)
   - Color coding by subject

Sample Mobile Screen:

┌─────────────────────────┐
│  Hogwarts Student       │
│  Aarav Sharma (9A)      │
├─────────────────────────┤
│  Monday, Jan 20         │
│  ⏰ 10:25 AM            │
├─────────────────────────┤
│  CURRENT CLASS          │
│  ▶ Physics Lab          │
│    Dr. Patel            │
│    Science Lab 1        │
│    10:15 - 11:00 AM     │
│    [35 min remaining]   │
├─────────────────────────┤
│  NEXT CLASS             │
│  → Social Studies       │
│    Mr. Singh            │
│    Room 207             │
│    11:00 - 11:45 AM     │
│    [In 35 minutes]      │
├─────────────────────────┤
│  TODAY'S SCHEDULE       │
│  ✓ Math (8:30)          │
│  ✓ English (9:15)       │
│  ▶ Physics Lab (10:15)  │
│  • Social (11:00)       │
│  • Hindi (11:45)        │
│  • [Lunch] (12:30)      │
│  • Computer (1:00)      │
│  • PE (1:45)            │
│  • Library (2:30)       │
├─────────────────────────┤
│  [View Week] [Calendar] │
└─────────────────────────┘
```

#### 4.2 Real-Time Synchronization

**Sync Architecture:**
```yaml
Synchronization Strategy:

1. Initial Load:
   - Download full timetable on first login
   - Cache locally (SQLite database)
   - Store for 30 days
   
2. Real-Time Updates:
   - WebSocket connection for live updates
   - Push notifications for changes
   - Background sync every 6 hours
   
3. Change Detection:
   - Server sends delta updates
   - App merges changes with local cache
   - Conflict resolution (server wins)
   
4. Offline Support:
   - Full functionality with cached data
   - Queue changes for upload
   - Sync when connection restored
   
5. Data Freshness:
   - Timetable version tracking
   - Last updated timestamp
   - Force refresh option

Sync Events:
  - Timetable published: Full download
  - Period changed: Delta update
  - Teacher substitution: Immediate push
  - Room change: Immediate push
  - Exam schedule: Full download
```

---

### 5. Export & Print Options

#### 5.1 PDF Generation

**PDF Timetable Format:**
```yaml
PDF Export Options:

1. Weekly Timetable (Landscape A4):
   Header:
     - School logo and name
     - Student name, grade, section
     - Academic year
     - Generated date
   
   Content:
     - 6-day grid (Mon-Sat)
     - 8-9 periods per day
     - Subject, teacher, room for each period
     - Color-coded by subject type
     - Break times highlighted
   
   Footer:
     - Class teacher name
     - Emergency contact
     - Page number
   
2. Daily Timetable (Portrait A4):
   - Single day detailed view
   - Period timings
   - Teacher contact info
   - Room locations
   - Special notes
   
3. Subject-wise Timetable:
   - All periods for one subject
   - Teacher details
   - Syllabus coverage
   - Assessment schedule

PDF Features:
  ✓ High-quality print (300 DPI)
  ✓ Color and B&W options
  ✓ Printer-friendly layout
  ✓ QR code for digital access
  ✓ Watermark with student name
  ✓ Password protection option
```

#### 5.2 Calendar Integration

**iCal / Google Calendar Export:**
```yaml
Calendar Export Format:

Event Structure:
  Title: "[Subject] - [Teacher]"
  Location: "[Room/Lab]"
  Start Time: [Period start]
  End Time: [Period end]
  Recurrence: Weekly (Mon-Sat)
  Duration: Academic year
  
  Description:
    Subject: Mathematics
    Teacher: Mrs. Verma
    Room: 205
    Type: Theory
    Notes: [Any special instructions]
  
  Reminders:
    - 10 minutes before
    - 1 day before (for lab periods)
  
  Color Coding:
    Mathematics: Blue
    Science: Green
    English: Red
    Social Studies: Orange
    Languages: Purple
    Activities: Yellow

Sample iCal Export:
  BEGIN:VCALENDAR
  VERSION:2.0
  PRODID:-//Hogwarts ERP//Timetable//EN
  
  BEGIN:VEVENT
  UID:math-9a-mon-p1@hogwarts.edu
  DTSTART:20260120T083000
  DTEND:20260120T091500
  RRULE:FREQ=WEEKLY;BYDAY=MO;UNTIL=20260331
  SUMMARY:Mathematics - Mrs. Verma
  LOCATION:Room 205
  DESCRIPTION:Grade 9A Mathematics Class
  STATUS:CONFIRMED
  END:VEVENT
  
  [... all periods as separate events]
  
  END:VCALENDAR

Google Calendar Integration:
  - One-click export button
  - Auto-sync option
  - Update propagation
  - Remove old events on timetable change
```

---

### 6. Real-Time Updates & Notifications

#### 6.1 Change Notification System

**Notification Workflow:**
```yaml
Scenario: Teacher Absence - Substitute Assigned

1. Event Trigger:
   - Mrs. Verma (Math) applies leave for Tuesday
   - System assigns Mr. Saxena as substitute
   - Timetable updated automatically
   
2. Notification Generation:
   Time: 6:00 AM Tuesday (before school)
   
   Recipients:
     - All Grade 9A students (36 students)
     - Parents of Grade 9A students (36 parents)
     - Mr. Saxena (substitute teacher)
     - Grade 9 coordinator
   
3. Multi-Channel Delivery:
   
   SMS (Students):
     "Hogwarts: Math Period 1 today (8:30 AM) - Mr. Saxena (substitute for Mrs. Verma). Room 205."
   
   SMS (Parents):
     "Hogwarts: Aarav's Math class today will be taught by Mr. Saxena (substitute). Mrs. Verma on leave."
   
   App Push (Students):
     Title: "Teacher Change - Mathematics"
     Body: "Mr. Saxena will teach Math Period 1 today"
     Action: "View Timetable"
   
   Email (Parents):
     Subject: "Timetable Update - Mathematics Substitute Teacher"
     Body: [Detailed information with full day schedule]
   
   In-App Alert:
     - Red badge on timetable icon
     - Highlighted period in schedule
     - "SUBSTITUTE" label on period

4. Timetable Update:
   - Web portal: Immediate update
   - Mobile app: Push update via WebSocket
   - Cached data: Invalidated and refreshed
   - PDF: Regenerated with "Updated" watermark
```

#### 6.2 Push Notification Types

**Notification Categories:**
```yaml
1. Schedule Changes (Priority: High):
   - Period time change
   - Room change
   - Teacher substitution
   - Class cancellation
   - Makeup class scheduled
   
2. Reminders (Priority: Medium):
   - Next period starting in 5 min
   - Lab period tomorrow (bring equipment)
   - Special uniform required
   - Project submission due
   
3. Announcements (Priority: Medium):
   - Special assembly
   - Event schedule
   - Holiday notification
   - Exam timetable published
   
4. Alerts (Priority: High):
   - Emergency schedule change
   - School closure
   - Exam postponed
   - Important meeting

Notification Settings (User Configurable):
  ✓ Period reminders: ON/OFF
  ✓ Reminder timing: 5/10/15 minutes
  ✓ Schedule changes: Always ON
  ✓ Announcements: ON/OFF
  ✓ Delivery channels: SMS/Email/Push
  ✓ Quiet hours: 9 PM - 7 AM
```

---

### 7. Offline Access Capability

#### 7.1 Offline Storage

**Caching Strategy:**
```yaml
Local Storage (Mobile App):

1. Timetable Data:
   - Full academic year timetable
   - All periods, teachers, rooms
   - Size: ~500 KB per student
   - Storage: SQLite database
   
2. Teacher Information:
   - Name, subject, contact
   - Photo (thumbnail)
   - Free periods
   - Size: ~50 KB
   
3. Room/Location Data:
   - Room numbers, names
   - Building/floor info
   - Campus map (optional)
   - Size: ~200 KB
   
4. Notifications History:
   - Last 30 days
   - Read/unread status
   - Size: ~100 KB

Total Offline Storage: ~1 MB per student

Offline Functionality:
  ✓ View full timetable
  ✓ Daily/weekly/subject views
  ✓ Teacher information
  ✓ Room locations
  ✓ Notification history
  ✗ Real-time updates (queued)
  ✗ New notifications (queued)
  ✗ PDF generation (cached PDFs available)

Sync on Reconnection:
  1. Check timetable version
  2. Download delta updates
  3. Merge with local cache
  4. Deliver queued notifications
  5. Update last sync timestamp
```

#### 7.2 Progressive Web App (PWA)

**Web App Offline Support:**
```yaml
PWA Features:

1. Service Worker:
   - Cache timetable data
   - Cache static assets (CSS, JS, images)
   - Offline page fallback
   
2. Cached Resources:
   - Timetable HTML/CSS/JS
   - Student timetable data (JSON)
   - Teacher photos
   - School logo
   
3. Background Sync:
   - Queue failed requests
   - Retry when online
   - Sync timetable updates
   
4. Install Prompt:
   - "Add to Home Screen"
   - Native app-like experience
   - Standalone mode

Offline Experience:
  - Full timetable viewing
  - Cached PDF downloads
  - Notification history
  - "Offline Mode" indicator
  - Auto-sync when online
```

---

## 📊 DATABASE SCHEMA

### Student Timetable Views Table

```sql
CREATE TABLE student_timetable_views (
  view_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Student Reference
  student_id INT NOT NULL,
  section_id INT NOT NULL,
  academic_year VARCHAR(9) NOT NULL,
  
  -- Timetable Reference
  timetable_id INT NOT NULL,
  
  -- Period Details
  day_of_week ENUM('MONDAY', 'TUESDAY', 'WEDNESDAY', 'THURSDAY', 'FRIDAY', 'SATURDAY') NOT NULL,
  period_number INT NOT NULL,
  
  -- Class Information
  subject_id INT NOT NULL,
  teacher_id INT NOT NULL,
  room_id INT,
  
  -- Timing
  start_time TIME NOT NULL,
  end_time TIME NOT NULL,
  
  -- Period Type
  period_type ENUM('THEORY', 'PRACTICAL', 'LAB', 'LIBRARY', 'PE', 'ACTIVITY', 'STUDY') NOT NULL,
  
  -- Special Instructions
  special_notes TEXT,
  requires_equipment BOOLEAN DEFAULT FALSE,
  equipment_list JSON,
  
  -- Status
  is_active BOOLEAN DEFAULT TRUE,
  is_cancelled BOOLEAN DEFAULT FALSE,
  cancellation_reason TEXT,
  
  -- Substitute Information
  is_substitute BOOLEAN DEFAULT FALSE,
  original_teacher_id INT,
  substitute_teacher_id INT,
  substitute_date DATE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  last_viewed_date TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id),
  FOREIGN KEY (section_id) REFERENCES sections(section_id),
  FOREIGN KEY (timetable_id) REFERENCES master_timetable(timetable_id),
  FOREIGN KEY (subject_id) REFERENCES subjects(subject_id),
  FOREIGN KEY (teacher_id) REFERENCES teachers(teacher_id),
  FOREIGN KEY (room_id) REFERENCES rooms(room_id),
  
  -- Indexes
  INDEX idx_student_day (student_id, day_of_week),
  INDEX idx_section_period (section_id, day_of_week, period_number),
  INDEX idx_active (is_active),
  INDEX idx_academic_year (academic_year),
  
  -- Unique Constraint
  UNIQUE KEY uk_student_period (student_id, day_of_week, period_number, academic_year)
);
```

### Timetable Access Log Table

```sql
CREATE TABLE timetable_access_log (
  log_id BIGINT PRIMARY KEY AUTO_INCREMENT,
  
  -- User Information
  user_id INT NOT NULL,
  user_type ENUM('STUDENT', 'PARENT', 'TEACHER', 'ADMIN') NOT NULL,
  student_id INT, -- If parent/teacher viewing student timetable
  
  -- Access Details
  access_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  access_type ENUM('VIEW', 'DOWNLOAD_PDF', 'EXPORT_ICAL', 'PRINT') NOT NULL,
  
  -- Platform
  platform ENUM('WEB', 'MOBILE_APP', 'PARENT_PORTAL', 'API') NOT NULL,
  device_type ENUM('DESKTOP', 'MOBILE', 'TABLET') NOT NULL,
  
  -- View Type
  view_type ENUM('DAILY', 'WEEKLY', 'SUBJECT_WISE', 'FULL_YEAR') NOT NULL,
  
  -- Export Details
  export_format VARCHAR(20), -- PDF, ICAL, CSV
  export_file_path VARCHAR(500),
  
  -- Session Info
  session_id VARCHAR(100),
  ip_address VARCHAR(45),
  user_agent TEXT,
  
  -- Indexes
  INDEX idx_user_access (user_id, access_timestamp),
  INDEX idx_student (student_id),
  INDEX idx_access_type (access_type),
  INDEX idx_platform (platform)
);
```

### Timetable Notifications Table

```sql
CREATE TABLE timetable_notifications (
  notification_id BIGINT PRIMARY KEY AUTO_INCREMENT,
  
  -- Recipient
  recipient_id INT NOT NULL,
  recipient_type ENUM('STUDENT', 'PARENT', 'TEACHER') NOT NULL,
  
  -- Notification Details
  notification_type ENUM(
    'SCHEDULE_CHANGE',
    'TEACHER_SUBSTITUTE',
    'ROOM_CHANGE',
    'CLASS_CANCELLED',
    'PERIOD_REMINDER',
    'EXAM_SCHEDULE',
    'SPECIAL_EVENT'
  ) NOT NULL,
  
  -- Content
  title VARCHAR(200) NOT NULL,
  message TEXT NOT NULL,
  
  -- Related Entities
  timetable_id INT,
  period_id INT,
  subject_id INT,
  teacher_id INT,
  
  -- Affected Period
  affected_date DATE,
  affected_period INT,
  
  -- Delivery
  delivery_channels JSON, -- ['SMS', 'EMAIL', 'PUSH', 'IN_APP']
  sent_via_sms BOOLEAN DEFAULT FALSE,
  sent_via_email BOOLEAN DEFAULT FALSE,
  sent_via_push BOOLEAN DEFAULT FALSE,
  sent_via_in_app BOOLEAN DEFAULT TRUE,
  
  -- Status
  notification_status ENUM('PENDING', 'SENT', 'DELIVERED', 'READ', 'FAILED') DEFAULT 'PENDING',
  sent_timestamp TIMESTAMP,
  delivered_timestamp TIMESTAMP,
  read_timestamp TIMESTAMP,
  
  -- Priority
  priority ENUM('LOW', 'MEDIUM', 'HIGH', 'URGENT') DEFAULT 'MEDIUM',
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Indexes
  INDEX idx_recipient (recipient_id, recipient_type),
  INDEX idx_status (notification_status),
  INDEX idx_type (notification_type),
  INDEX idx_date (affected_date),
  INDEX idx_priority (priority)
);
```

### Timetable Cache Table (Mobile/Web)

```sql
CREATE TABLE timetable_cache (
  cache_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- User Reference
  user_id INT NOT NULL,
  user_type ENUM('STUDENT', 'PARENT') NOT NULL,
  student_id INT NOT NULL,
  
  -- Cache Data
  timetable_data JSON NOT NULL, -- Complete timetable JSON
  
  -- Version Control
  timetable_version VARCHAR(50) NOT NULL,
  cache_version INT NOT NULL,
  
  -- Validity
  valid_from DATE NOT NULL,
  valid_until DATE NOT NULL,
  
  -- Cache Metadata
  cache_size_bytes INT,
  compression_used BOOLEAN DEFAULT FALSE,
  
  -- Sync Information
  last_sync_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  sync_status ENUM('SYNCED', 'PENDING', 'OUTDATED') DEFAULT 'SYNCED',
  
  -- Device Information
  device_id VARCHAR(100),
  platform ENUM('WEB', 'IOS', 'ANDROID') NOT NULL,
  app_version VARCHAR(20),
  
  -- Indexes
  INDEX idx_user (user_id, user_type),
  INDEX idx_student (student_id),
  INDEX idx_sync_status (sync_status),
  INDEX idx_version (timetable_version),
  
  -- Unique Constraint
  UNIQUE KEY uk_user_device (user_id, device_id)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Timetable Access Control

```javascript
FUNCTION check_timetable_access(user_id, user_type, requested_student_id) {
  // Rule: Students can only view their own timetable
  IF user_type == 'STUDENT' {
    IF user_id != requested_student_id {
      RETURN {
        access: FALSE,
        reason: "Students can only view their own timetable"
      }
    }
  }
  
  // Rule: Parents can only view their children's timetables
  IF user_type == 'PARENT' {
    children = QUERY("
      SELECT student_id 
      FROM parent_student_mapping 
      WHERE parent_id = ?
    ", [user_id])
    
    IF requested_student_id NOT IN children {
      RETURN {
        access: FALSE,
        reason: "Parents can only view their children's timetables"
      }
    }
  }
  
  // Rule: Teachers can view timetables of students they teach
  IF user_type == 'TEACHER' {
    teaches_student = QUERY("
      SELECT COUNT(*) as count
      FROM student_timetable_views
      WHERE student_id = ?
      AND teacher_id = ?
      AND is_active = TRUE
    ", [requested_student_id, user_id])
    
    IF teaches_student.count == 0 {
      RETURN {
        access: FALSE,
        reason: "Teachers can only view timetables of students they teach"
      }
    }
  }
  
  // Rule: Admins and coordinators have full access
  IF user_type IN ['ADMIN', 'COORDINATOR', 'PRINCIPAL'] {
    RETURN {access: TRUE}
  }
  
  // Log access
  LOG_ACCESS(user_id, user_type, requested_student_id, 'VIEW')
  
  RETURN {access: TRUE}
}
```

### Rule 2: Real-Time Update Propagation

```javascript
FUNCTION propagate_timetable_change(change_event) {
  // Extract change details
  affected_students = GET_AFFECTED_STUDENTS(change_event)
  affected_parents = GET_AFFECTED_PARENTS(affected_students)
  change_type = change_event.type
  
  // Determine notification priority
  priority = DETERMINE_PRIORITY(change_type, change_event.timing)
  
  // Priority Rules:
  // - Same-day changes: URGENT
  // - Next-day changes: HIGH
  // - Future changes (>1 day): MEDIUM
  
  current_time = CURRENT_TIMESTAMP()
  change_date = change_event.affected_date
  hours_until_change = (change_date - current_time) / 3600
  
  IF hours_until_change < 24 {
    priority = 'URGENT'
    delivery_channels = ['SMS', 'PUSH', 'EMAIL', 'IN_APP']
  } ELSE IF hours_until_change < 48 {
    priority = 'HIGH'
    delivery_channels = ['PUSH', 'EMAIL', 'IN_APP']
  } ELSE {
    priority = 'MEDIUM'
    delivery_channels = ['EMAIL', 'IN_APP']
  }
  
  // Create notifications
  FOR each student IN affected_students {
    CREATE_NOTIFICATION({
      recipient_id: student.id,
      recipient_type: 'STUDENT',
      type: change_type,
      title: GENERATE_TITLE(change_event),
      message: GENERATE_MESSAGE(change_event, 'STUDENT'),
      priority: priority,
      delivery_channels: delivery_channels
    })
  }
  
  FOR each parent IN affected_parents {
    CREATE_NOTIFICATION({
      recipient_id: parent.id,
      recipient_type: 'PARENT',
      type: change_type,
      title: GENERATE_TITLE(change_event),
      message: GENERATE_MESSAGE(change_event, 'PARENT'),
      priority: priority,
      delivery_channels: delivery_channels
    })
  }
  
  // Invalidate cache
  INVALIDATE_CACHE(affected_students)
  
  // Push real-time updates to connected clients
  PUSH_WEBSOCKET_UPDATE(affected_students, change_event)
  
  // Send notifications
  SEND_NOTIFICATIONS(priority)
}
```

### Rule 3: Cache Management & Synchronization

```javascript
FUNCTION manage_timetable_cache(user_id, platform, device_id) {
  // Check if cache exists
  cache = QUERY("
    SELECT * FROM timetable_cache
    WHERE user_id = ?
    AND device_id = ?
  ", [user_id, device_id])
  
  IF cache.exists {
    // Check cache validity
    current_date = CURRENT_DATE()
    
    IF current_date > cache.valid_until {
      // Cache expired
      cache.sync_status = 'OUTDATED'
      UPDATE_CACHE_STATUS(cache.cache_id, 'OUTDATED')
      
      // Trigger refresh
      RETURN {
        status: 'REFRESH_REQUIRED',
        message: 'Cache expired, please refresh'
      }
    }
    
    // Check timetable version
    latest_version = GET_LATEST_TIMETABLE_VERSION(user_id)
    
    IF cache.timetable_version != latest_version {
      // Timetable updated
      cache.sync_status = 'PENDING'
      UPDATE_CACHE_STATUS(cache.cache_id, 'PENDING')
      
      // Download delta update
      delta = GET_TIMETABLE_DELTA(cache.timetable_version, latest_version)
      
      // Merge with existing cache
      updated_data = MERGE_CACHE_DATA(cache.timetable_data, delta)
      
      // Update cache
      UPDATE_CACHE({
        cache_id: cache.cache_id,
        timetable_data: updated_data,
        timetable_version: latest_version,
        cache_version: cache.cache_version + 1,
        last_sync_timestamp: CURRENT_TIMESTAMP(),
        sync_status: 'SYNCED'
      })
      
      RETURN {
        status: 'UPDATED',
        message: 'Timetable synchronized',
        changes: delta
      }
    }
    
    // Cache is valid and up-to-date
    RETURN {
      status: 'VALID',
      cache_data: cache.timetable_data
    }
  } ELSE {
    // No cache exists, create new
    timetable_data = GET_FULL_TIMETABLE(user_id)
    timetable_version = GET_LATEST_TIMETABLE_VERSION(user_id)
    
    CREATE_CACHE({
      user_id: user_id,
      user_type: GET_USER_TYPE(user_id),
      student_id: GET_STUDENT_ID(user_id),
      timetable_data: timetable_data,
      timetable_version: timetable_version,
      cache_version: 1,
      valid_from: CURRENT_DATE(),
      valid_until: END_OF_ACADEMIC_YEAR(),
      device_id: device_id,
      platform: platform,
      last_sync_timestamp: CURRENT_TIMESTAMP(),
      sync_status: 'SYNCED'
    })
    
    RETURN {
      status: 'CREATED',
      cache_data: timetable_data
    }
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Parent Portal Module**
   - Send child's timetable data
   - Provide teacher contact information
   - Enable parent-teacher meeting scheduling
   - Share schedule change notifications

2. **Mobile App Backend**
   - Sync timetable data
   - Push real-time updates
   - Handle offline cache
   - Deliver push notifications

3. **Notification Service**
   - Send SMS notifications
   - Send email notifications
   - Send push notifications
   - Manage notification preferences

4. **Calendar Services**
   - Export to Google Calendar
   - Export to iCal format
   - Sync calendar events
   - Update recurring events

5. **PDF Generation Service**
   - Generate weekly timetable PDFs
   - Generate daily schedule PDFs
   - Create subject-wise PDFs
   - Add watermarks and branding

6. **Analytics Module**
   - Track timetable access patterns
   - Monitor notification delivery
   - Analyze user engagement
   - Generate usage reports

### Inbound Integrations

1. **Master Timetable Module**
   - Receive timetable data
   - Get period schedules
   - Receive timetable updates
   - Get teacher assignments

2. **Teacher Allocation Module**
   - Receive teacher information
   - Get substitute assignments
   - Receive teacher contact details
   - Get free period schedules

3. **Room Allocation Module**
   - Receive room assignments
   - Get location information
   - Receive room change notifications
   - Get facility details

4. **Student Management Module**
   - Get student information
   - Receive section assignments
   - Get enrollment status
   - Receive student updates

5. **Exam Scheduling Module**
   - Receive exam timetables
   - Get exam hall assignments
   - Receive exam notifications
   - Get invigilation schedules

---

## 👥 USER WORKFLOWS

### Workflow 1: Student Views Daily Timetable

**Actor:** Student (Aarav Sharma, Grade 9A)  
**Duration:** 1-2 minutes  
**Frequency:** Daily

**Steps:**

1. **Login to Student Portal**
   - Open Hogwarts Student App on mobile
   - Biometric authentication / PIN login
   - Dashboard loads

2. **Access Timetable**
   - Tap "Today's Schedule" widget on dashboard
   - OR Navigate to Menu → Timetable → Today
   - System loads cached timetable (instant)

3. **View Daily Schedule**
   - See current period highlighted
   - View next class countdown
   - Check remaining classes for the day
   - See special notes (lab coat, sports uniform)

4. **Check Period Details**
   - Tap on "Period 3 - Physics Lab"
   - View detailed information:
     - Teacher: Dr. Patel
     - Room: Science Lab 1
     - Time: 10:15-11:00 AM
     - Notes: "Bring lab coat and manual"
   - View teacher's free periods for doubt clearing

5. **Set Reminders** (Optional)
   - Enable period start reminders
   - Set reminder timing (5 minutes before)
   - Save preferences

6. **Export to Calendar** (Optional)
   - Tap "Add to Calendar"
   - Select "Google Calendar"
   - Confirm export
   - All periods added as recurring events

**Expected Outcome:** Student successfully views daily schedule, knows upcoming classes, and is prepared with required materials.

---

### Workflow 2: Parent Downloads Child's Timetable PDF

**Actor:** Parent (Mr. Rajesh Sharma)  
**Duration:** 2-3 minutes  
**Frequency:** Once per term

**Steps:**

1. **Login to Parent Portal**
   - Open parent portal website
   - Enter credentials (email + password)
   - Dashboard loads showing children

2. **Select Child**
   - Click on "Aarav Sharma (Grade 9A)"
   - Child's dashboard loads

3. **Navigate to Timetable**
   - Click "Academic" → "Timetable"
   - Timetable page loads

4. **Choose View Type**
   - Select "Weekly View" (default)
   - OR Select "Daily View" / "Subject-wise View"
   - Timetable grid displays

5. **Download PDF**
   - Click "Download PDF" button
   - Select format:
     - Weekly Timetable (Landscape)
     - Daily Timetable (Portrait)
     - Subject-wise Timetable
   - Select "Weekly Timetable (Landscape)"

6. **Generate PDF**
   - System generates PDF (5-10 seconds)
   - PDF preview shown
   - Verify content

7. **Save/Print PDF**
   - Click "Download"
   - Save to device: "Aarav_Timetable_2025-26.pdf"
   - OR Click "Print" for immediate printing

8. **Receive Confirmation**
   - Success message: "Timetable downloaded successfully"
   - PDF saved in Downloads folder

**Expected Outcome:** Parent successfully downloads child's timetable PDF for offline reference and planning.

---

### Workflow 3: Student Receives Schedule Change Notification

**Actor:** Student (Priya Mehta, Grade 10B)  
**Duration:** Real-time  
**Frequency:** As needed

**Scenario:** English teacher (Ms. Kapoor) is absent on Tuesday. Substitute teacher (Mr. Gupta) assigned.

**Steps:**

1. **Change Event Occurs**
   - Monday 5:00 PM: Ms. Kapoor applies leave for Tuesday
   - System assigns Mr. Gupta as substitute
   - Timetable updated automatically

2. **Notification Generated**
   - System identifies affected students (Grade 10B - 40 students)
   - Creates notification:
     - Type: TEACHER_SUBSTITUTE
     - Priority: HIGH (next day change)
     - Channels: SMS + Push + Email + In-App

3. **Notification Delivered**
   - Monday 6:00 PM: Notifications sent
   
   **SMS Received:**
   ```
   Hogwarts: English Period 2 tomorrow (9:15 AM) - Mr. Gupta (substitute for Ms. Kapoor). Room 206.
   ```
   
   **Push Notification:**
   ```
   Title: Teacher Change - English
   Body: Mr. Gupta will teach English Period 2 tomorrow
   Tap to view timetable
   ```
   
   **Email:**
   ```
   Subject: Timetable Update - English Substitute Teacher
   
   Dear Priya,
   
   This is to inform you that Ms. Kapoor will be on leave tomorrow (Tuesday, January 21, 2026).
   
   Mr. Gupta will teach your English class as a substitute.
   
   Class Details:
   - Subject: English Literature
   - Period: 2 (9:15-10:00 AM)
   - Room: 206
   - Teacher: Mr. Gupta (Substitute)
   
   Please attend the class as scheduled.
   
   Thank you,
   Hogwarts Academic Team
   ```

4. **Student Views Notification**
   - Priya sees push notification on phone
   - Taps notification
   - App opens to timetable page
   - Tuesday's schedule shown with updated information
   - Period 2 highlighted with "SUBSTITUTE" label

5. **Timetable Updated**
   - App cache automatically updated
   - Tuesday Period 2 now shows:
     - Subject: English Literature
     - Teacher: Mr. Gupta (Substitute)
     - Original Teacher: Ms. Kapoor (On Leave)
     - Room: 206
     - Time: 9:15-10:00 AM

6. **Parent Notified**
   - Priya's parent also receives notification
   - Parent portal updated with substitute information

**Expected Outcome:** Student is informed of schedule change in advance, knows substitute teacher, and attends class without confusion.

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 1.0  
**Documentation Standard:** Enterprise Grade

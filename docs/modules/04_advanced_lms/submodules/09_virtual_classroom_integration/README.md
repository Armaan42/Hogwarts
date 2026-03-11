# VIRTUAL CLASSROOM INTEGRATION

**Module:** Advanced LMS  
**Submodule Code:** LMS-VC-009  
**Category:** Real-Time Learning  
**Priority:** Critical (P0)  
**Owners:** IT Administrator, Academic Coordinator, Teachers

---

## OVERVIEW

The Virtual Classroom Integration submodule provides a seamless bridge between the school's LMS and live video conferencing platforms (Zoom, Google Meet, Microsoft Teams, or a self-hosted solution like BigBlueButton). It enables teachers to conduct real-time classes with features such as screen sharing, interactive whiteboards, breakout rooms, live polls, and automatic attendance marking -- all initiated from within the LMS without students needing to manage separate meeting links.

### Purpose

To create a unified teaching experience where the teacher starts a live class from the LMS, students join with one click, the session is auto-recorded, attendance is auto-marked, and the recording is auto-published to the LMS for absentees to watch later. This eliminates the friction of managing multiple platforms.

### Scope

-   **Platform Integration:** Zoom SDK, Google Meet API, MS Teams Graph API, BigBlueButton.
-   **One-Click Class Launch:** Teacher starts a live class from the course page.
-   **Interactive Tools:** Whiteboard, screen sharing, hand raise, polls, Q&A.
-   **Breakout Rooms:** Small-group discussion rooms within a live session.
-   **Auto-Recording & Publishing:** Sessions recorded and uploaded to the LMS Video Library.
-   **Attendance Auto-Capture:** Student join/leave timestamps tracked for attendance.
-   **Parental Monitoring:** Parents can optionally view class recordings.

---

## KEY FEATURES

### 1. Unified Class Launch

**Feature Description:**
Starting a live class without leaving the LMS.
*   Teacher navigates to Course > Grade 10A Physics > clicks "Start Live Class."
*   LMS creates a meeting room via the configured platform API.
*   Students on the course page see a "Join Live Class" button (no meeting URL to copy-paste).
*   The session is bound to the timetable slot: "Period 3, 10:30 AM - 11:15 AM."

### 2. Interactive Whiteboard & Annotation

**Feature Description:**
Digital teaching canvas.
*   Teacher opens a collaborative whiteboard within the virtual classroom.
*   Supports: Freehand drawing, text, shapes, LaTeX equation rendering, image upload.
*   Students can be given "Annotate" access (useful for solving problems on the board).
*   Whiteboard state is saved and attached to the session recording as a separate PDF.

### 3. Breakout Rooms for Group Work

**Feature Description:**
Splitting a class into smaller discussion groups.
*   Teacher clicks "Create Breakout Rooms" > Selects "4 rooms, 10 students each" or "Auto-assign randomly."
*   Each room has independent audio/video.
*   Teacher can "Visit" each room, broadcast a message to all rooms, or recall everyone to the main room.
*   Use Case: Group discussion on a case study, then each group presents findings to the class.

### 4. Live Polls & Quick Quizzes

**Feature Description:**
Instant engagement checks during a live class.
*   Teacher triggers: "What is Newton's 3rd Law?" with 4 MCQ options.
*   Students respond in real-time. Results are displayed as a live bar chart.
*   Correct answer is revealed after all responses are in.
*   Poll results are saved to the LMS as "Participation Data" for that session.

---

## DATABASE SCHEMA

### 1. Virtual Classroom Sessions (`lms_vc_sessions`)

```sql
CREATE TABLE lms_vc_sessions (
    session_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    course_id INT NOT NULL,
    section_id INT NOT NULL,
    teacher_id INT NOT NULL,
    
    platform ENUM('ZOOM', 'GOOGLE_MEET', 'MS_TEAMS', 'BIGBLUEBUTTON'),
    external_meeting_id VARCHAR(100),
    join_url VARCHAR(500),
    
    scheduled_start DATETIME,
    scheduled_end DATETIME,
    actual_start DATETIME,
    actual_end DATETIME,
    
    recording_url VARCHAR(500),
    recording_duration_minutes INT,
    whiteboard_pdf_path VARCHAR(255),
    
    status ENUM('SCHEDULED', 'LIVE', 'ENDED', 'CANCELLED'),
    participant_count INT DEFAULT 0,
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### 2. Participant Logs (`lms_vc_participants`)

```sql
CREATE TABLE lms_vc_participants (
    log_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    session_id BIGINT NOT NULL,
    student_id INT NOT NULL,
    
    join_time DATETIME,
    leave_time DATETIME,
    duration_minutes INT,
    
    attention_score DECIMAL(5,2), -- AI-based engagement metric (optional)
    
    device_type ENUM('DESKTOP', 'TABLET', 'MOBILE'),
    
    FOREIGN KEY (session_id) REFERENCES lms_vc_sessions(session_id)
);
```

### 3. Session Polls (`lms_vc_polls`)

```sql
CREATE TABLE lms_vc_polls (
    poll_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    session_id BIGINT NOT NULL,
    
    question TEXT,
    options JSON, -- ["Option A", "Option B", "Option C", "Option D"]
    correct_option INT,
    
    responses JSON, -- {"student_1": 2, "student_5": 3}
    total_responses INT DEFAULT 0,
    
    launched_at DATETIME,
    closed_at DATETIME,
    
    FOREIGN KEY (session_id) REFERENCES lms_vc_sessions(session_id)
);
```

---

## BUSINESS RULES

### Rule 1: Timetable-Bound Sessions
*   A virtual class can ONLY be created for a valid timetable slot assigned to that teacher. The system prevents ad-hoc sessions outside of scheduled class times (unless the teacher has "Extra Class" permissions).

### Rule 2: Attendance Auto-Marking
*   A student is marked "Present" for the virtual class ONLY if their `duration_minutes >= 75%` of the session duration. If the class is 45 minutes, the student must be connected for at least 34 minutes. Brief logins (under 5 minutes) are flagged as "Insufficient Attendance."

### Rule 3: Recording Retention
*   All recordings are retained for the current academic term. At term-end, teachers can choose to "Archive" (move to cold storage) or "Delete." Students lose access to recordings after the academic year ends unless the teacher explicitly extends access.

### Rule 4: Platform Failover
*   If the primary platform (e.g., Zoom) is down, the system attempts to create the meeting on the backup platform (e.g., BigBlueButton). The teacher is notified: "Zoom is unavailable. Your session will be on BigBlueButton."

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Attendance Module:** Sends auto-captured attendance data for each virtual session.
*   **To Video Lectures (05):** Published recordings feed into the Video Library.
*   **To Student Progress (06):** Poll participation data contributes to engagement metrics.

### Inbound Relationships
*   **From Timetable Module:** Validates that the session aligns with the teacher's schedule.
*   **From Student Module:** Pulls the enrolled student list for the section.

---

## USER WORKFLOWS

### Workflow 1: Teacher Conducts a Live Class
**Actor:** Physics Teacher

1.  **Prepare:** Opens LMS > Grade 10A Physics > Period 3 > Clicks "Start Live Class."
2.  **Launch:** System creates a Zoom meeting. Teacher's browser opens the Zoom web client.
3.  **Teach:** Teacher shares screen (presentation), uses the whiteboard to derive an equation.
4.  **Engage:** At the 20-minute mark, launches a poll: "What is F = m * ?" with options: a, v, x. 38 of 40 students respond. 35 correct.
5.  **Group Work:** Creates 4 breakout rooms. Students discuss a problem for 10 minutes.
6.  **End:** Clicks "End Class." Recording starts processing. Attendance auto-syncs.
7.  **Post-Class:** 30 minutes later, the recording and whiteboard PDF are available on the course page.

### Workflow 2: Student Watches a Missed Class
**Actor:** Student (Absent)

1.  **Notification:** Receives: "You missed Physics Live Class on Oct 5. Recording available."
2.  **Watch:** Opens LMS > Course > Recordings > Plays the 45-minute video.
3.  **Review:** Downloads the whiteboard PDF for notes.
4.  **Mark:** Teacher sees "Aarav watched the recording on Oct 6." (Counts as asynchronous learning, NOT as attendance.)

---

## EDGE CASES

### Edge Case 1: Internet Drop Mid-Class (Teacher Side)
*   **Scenario:** The teacher's internet drops 20 minutes into a 45-minute class.
*   **Resolution:** The meeting stays active (students see "Waiting for host to reconnect"). If the teacher reconnects within 5 minutes, the class continues. If not, the system auto-assigns a backup action: "Class will resume in 10 minutes" notification to students. The partial recording is saved.

### Edge Case 2: Student Joins from Multiple Devices
*   **Scenario:** Student logs in from both a laptop and a phone (e.g., internet is flaky on one).
*   **Resolution:** The system tracks the device with the longest active session for attendance. Duplicate logins are merged into a single participant record. Total attendance time = max(laptop_time, phone_time), not sum.

### Edge Case 3: Inappropriate Content Sharing
*   **Scenario:** A student shares their screen (if permission was granted) and displays inappropriate content.
*   **Resolution:** The teacher has a "Force Mute / Remove Participant" button. The incident is auto-logged with a screenshot. A "Classroom Incident Report" is generated and sent to the Discipline Module.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `vc_default_platform` | `ZOOM` | Primary video conferencing platform |
| `vc_backup_platform` | `BIGBLUEBUTTON` | Failover platform |
| `vc_attendance_min_duration_pct` | 75% | Min session time for marking present |
| `vc_auto_record` | `true` | Auto-record every session? |
| `vc_recording_retention_days` | 90 | Days before recording is auto-archived |
| `vc_max_participants` | 100 | Max students per session |
| `vc_student_screen_share` | `false` | Allow students to share their screen? |
| `vc_breakout_rooms_max` | 10 | Max breakout rooms per session |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

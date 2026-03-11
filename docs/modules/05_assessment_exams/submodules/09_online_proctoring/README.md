# ONLINE PROCTORING

**Module:** Assessment & Exams  
**Submodule Code:** EXAM-PROCT-009  
**Category:** Advanced / AI-Powered  
**Priority:** Medium (P2)  
**Owners:** IT Administrator, Exam Controller, Academic Integrity Officer

---

## OVERVIEW

The Online Proctoring submodule provides AI-driven and human-monitored exam integrity enforcement for remote and online examinations. With the rise of online learning and hybrid education models, schools need robust mechanisms to ensure that students taking exams from home are doing so honestly. This submodule integrates webcam monitoring, browser lockdown, AI-based anomaly detection, and human proctor review into a seamless exam supervision experience.

### Purpose

To maintain the credibility of online examinations by detecting and deterring cheating behaviors (looking away from the screen, using unauthorized materials, having another person present, switching tabs/applications) while being sensitive to false positives and student privacy.

### Scope

-   **Browser Lockdown:** Secure exam browser that prevents tab switching, copy-paste, and screen sharing.
-   **AI Webcam Monitoring:** Face detection, gaze tracking, multiple face detection.
-   **Audio Monitoring:** Detecting background voices or dictation.
-   **Flag & Review System:** AI flags suspicious events; human proctors review flagged clips.
-   **Integrity Scoring:** An overall "Exam Integrity Score" per student per exam.
-   **Live Proctoring Option:** Real-time human monitoring for high-stakes exams.

---

## KEY FEATURES

### 1. Secure Exam Browser (Lockdown Mode)

**Feature Description:**
A controlled testing environment.
*   Student launches the "Exam Safe Browser" (custom Electron-based app or browser extension).
*   Browser enters full-screen kiosk mode. Escape key, Alt+Tab, and taskbar are disabled.
*   Copy, Paste, Print Screen are all blocked at OS level.
*   Clipboard is cleared at exam start.
*   If the student exits full-screen (e.g., force alt-tab via OS shortcut), the system captures a screenshot of the desktop and flags a "Browser Exit Event."

### 2. AI-Powered Video Monitoring

**Feature Description:**
Continuous webcam surveillance with ML-based anomaly detection.

**Detection Capabilities:**
*   **Face Not Detected:** Student has moved out of frame (bathroom break without notifying).
*   **Multiple Faces:** Another person is visible (someone dictating answers).
*   **Gaze Deviation:** Student consistently looking away from screen (reading notes on the wall).
*   **Object Detection:** Phone visible in frame, earbuds detected.
*   **Lip Movement Analysis:** Excessive lip movement suggesting reading aloud or receiving dictation.

### 3. Human Proctor Review Dashboard

**Feature Description:**
Manual oversight for AI-flagged incidents.
*   AI flags are queued as "Incidents" with a severity score (Low/Medium/High/Critical).
*   A human proctor reviews the 10-second video clip around the flagged event.
*   Proctor marks the flag as: `FALSE_POSITIVE` (student adjusting glasses), `SUSPICIOUS` (needs further review), or `VIOLATION` (confirmed cheating).
*   Violations are escalated to the Academic Integrity Officer for disciplinary action.

### 4. Exam Integrity Score

**Feature Description:**
A composite score summarizing the student's exam behavior.

**Score Calculation:**
*   Starts at 100 (perfect trust).
*   Each AI flag deducts points: Browser Exit (-10), Face Not Detected (-5 per occurrence), Multiple Faces (-25), Gaze Deviation (-3 per 30-sec window).
*   Final Score: 100 = Clean, 70-99 = Minor Flags, 50-69 = Review Required, Below 50 = Potential Malpractice.

---

## DATABASE SCHEMA

### 1. Proctoring Sessions (`exam_proctor_sessions`)

```sql
CREATE TABLE exam_proctor_sessions (
    session_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    exam_id INT NOT NULL,
    student_id INT NOT NULL,
    
    session_start DATETIME,
    session_end DATETIME,
    
    browser_fingerprint VARCHAR(255), -- Device/browser identification
    ip_address VARCHAR(45),
    
    integrity_score INT DEFAULT 100,
    
    recording_video_path VARCHAR(255),
    recording_audio_path VARCHAR(255),
    
    status ENUM('IN_PROGRESS', 'COMPLETED', 'TERMINATED', 'UNDER_REVIEW'),
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Proctoring Flags (`exam_proctor_flags`)

```sql
CREATE TABLE exam_proctor_flags (
    flag_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    session_id BIGINT NOT NULL,
    
    flag_type ENUM('BROWSER_EXIT', 'FACE_MISSING', 'MULTIPLE_FACES', 'GAZE_DEVIATION', 
                   'PHONE_DETECTED', 'AUDIO_ANOMALY', 'CLIPBOARD_ATTEMPT'),
    
    severity ENUM('LOW', 'MEDIUM', 'HIGH', 'CRITICAL'),
    timestamp_in_exam DATETIME,
    clip_path VARCHAR(255), -- 10-sec video clip
    
    ai_confidence DECIMAL(5,2), -- 0.00 to 100.00
    
    human_review_status ENUM('PENDING', 'FALSE_POSITIVE', 'SUSPICIOUS', 'VIOLATION'),
    reviewed_by INT,
    reviewed_at DATETIME,
    
    FOREIGN KEY (session_id) REFERENCES exam_proctor_sessions(session_id)
);
```

---

## BUSINESS RULES

### Rule 1: Minimum System Requirements Check
*   Before the exam starts, the system runs a "Pre-Flight Check": Webcam active, Microphone working, Internet speed >= 2 Mbps, Browser version supported. If any check fails, the student cannot proceed.

### Rule 2: Grace Period for Technical Glitches
*   If the student's internet drops mid-exam, the system gives a 5-minute grace period. The exam timer pauses. If they reconnect within 5 minutes, they resume. If they exceed 5 minutes, the session is marked `TERMINATED` and flagged for manual review.

### Rule 3: Privacy & Data Retention
*   All video/audio recordings are stored for a maximum of 90 days post-exam (or until all revaluation applications are resolved, whichever is later). After that, recordings are permanently deleted per data privacy policy.

### Rule 4: Human Override for AI Flags
*   No student can be penalized based solely on AI flags. Every flag must be reviewed by a human proctor before any disciplinary action. The AI assists; it does not adjudicate.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Discipline Module:** Confirmed violations are forwarded as "Unfair Means Reports" for disciplinary proceedings.
*   **To Marks Entry (04):** If a student is debarred, their marks entry row is locked with status `MALPRACTICE_DEBARRED`.

### Inbound Relationships
*   **From Exam Schedule (01):** Receives the exam slot details (date, time, duration) to configure the proctoring session.
*   **From Student Module:** Pulls student photo for face recognition matching.

---

## USER WORKFLOWS

### Workflow 1: Student Taking a Proctored Exam
**Actor:** Student (from Home)

1.  **Launch:** Opens the Exam Safe Browser. Logs in with credentials.
2.  **Pre-Flight:** System checks webcam, mic, and internet. Shows "All Clear."
3.  **Identity Verification:** Student shows their School ID card to the webcam. AI matches face against school photo record.
4.  **Exam Start:** Timer begins. Student answers questions on-screen.
5.  **Incident:** Student's parent walks into the room to offer water. AI flags "Multiple Faces Detected" (Medium severity).
6.  **Resume:** Parent leaves. Student continues.
7.  **Submit:** Student clicks "Submit Exam". Session ends. Integrity Score: 87/100.

### Workflow 2: Proctor Reviewing Flags
**Actor:** Academic Integrity Officer

1.  **Dashboard:** Sees "156 flags across 80 proctored sessions for Grade 10 Maths."
2.  **Filter:** Sorts by severity: "CRITICAL" first. Sees 3 critical flags.
3.  **Review Flag 1:** "Multiple Faces - Confidence 94%". Watches 10-sec clip. Clear alternate person dictating answers. Marks as `VIOLATION`. Student is escalated.
4.  **Review Flag 2:** "Gaze Deviation - Confidence 62%". Student was looking at their physical rough work notebook (which is allowed). Marks as `FALSE_POSITIVE`.
5.  **Summary:** 1 Violation, 150 False Positives, 5 Suspicious (further review).

---

## EDGE CASES

### Edge Case 1: Power Cut During Online Exam
*   **Scenario:** Student loses power and internet simultaneously. No reconnection within the grace period.
*   **Resolution:** The session is marked `TERMINATED`. The Exam Controller reviews the case. If genuine (supported by area-wide outage data), the student is granted a re-sit exam on an alternate date.

### Edge Case 2: Twins Taking the Same Exam
*   **Scenario:** One twin takes the exam for the other. Face recognition AI cannot distinguish between identical twins.
*   **Resolution:** For students flagged as siblings in the same grade, the system requires a secondary verification: Student must show a unique document (ID card with a unique number) during the identity verification step. Additionally, IP address matching ensures both twins are not using the same device.

### Edge Case 3: Student with Visual Impairment
*   **Scenario:** A visually impaired student uses a screen magnifier, which causes the browser lockdown to fail.
*   **Resolution:** The system supports a "Accessibility Mode" that relaxes certain lockdown features (allows screen magnification, text-to-speech) while maintaining webcam proctoring. The AI's gaze deviation threshold is also relaxed for these students.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `proctor_ai_enabled` | `true` | Enable AI-based monitoring? |
| `proctor_live_human_enabled` | `false` | Enable real-time human proctor? |
| `proctor_min_internet_speed_mbps` | 2 | Minimum bandwidth for proctoring |
| `proctor_reconnection_grace_mins` | 5 | Grace period for internet drops |
| `proctor_recording_retention_days` | 90 | Days to keep video recordings |
| `proctor_integrity_violation_threshold` | 50 | Score below which session is auto-flagged |
| `proctor_gaze_deviation_window_secs` | 30 | Window for measuring gaze direction |
| `proctor_face_match_confidence_min` | 80% | Minimum AI confidence for identity verification |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

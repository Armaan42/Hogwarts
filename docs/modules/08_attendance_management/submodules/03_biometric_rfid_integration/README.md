# BIOMETRIC & RFID INTEGRATION

**Module:** Attendance Management  
**Submodule Code:** ATT-BIO-003  
**Category:** Hardware Integration  
**Priority:** High (P1)  
**Owners:** IT Administrator, Admin Office, Security Team

---

## OVERVIEW

The Biometric & RFID Integration submodule automates attendance capture by connecting hardware devices (fingerprint scanners, facial recognition cameras, RFID card readers, and smart gates) to the attendance system. Instead of relying on manual teacher marking, students' physical presence is detected automatically as they enter the school campus or classroom, providing tamper-proof, real-time attendance data.

### Purpose

To eliminate proxy attendance (where one student marks another as present), reduce teacher burden, provide exact entry/exit timestamps, and enable real-time campus safety monitoring (knowing exactly who is on campus at any moment). This is particularly valuable for large schools with 2000+ students where manual tracking is error-prone.

### Scope

-   **Fingerprint Scanning:** Biometric enrollment and daily scan-based attendance.
-   **RFID Smart Cards:** Tap-and-go card readers at school gates and classroom doors.
-   **Facial Recognition:** Camera-based recognition at entry points (contactless alternative).
-   **QR Code Alternative:** For budget-constrained setups, students scan their unique QR code.
-   **Device Health Monitoring:** Real-time status of all hardware devices, alerting on failures.
-   **Offline Sync:** Handling device operation during internet outages with data sync on reconnection.

---

## KEY FEATURES

### 1. Fingerprint Biometric Attendance

**Feature Description:**
The most tamper-proof method.
*   At enrollment (start of year), all students scan their fingerprints (both index fingers) on a biometric device.
*   Daily: Student places finger on the scanner at the school gate. System matches in < 1 second.
*   Result: Entry time logged. Attendance auto-marked as "Present" in the daily roll.
*   Anti-spoofing: Modern scanners detect fake fingerprints (silicone, printed).

### 2. RFID Card-Based Attendance

**Feature Description:**
Fast, contactless tap-and-go.
*   Each student receives an RFID card (embedded in their School ID Card).
*   Readers are installed at: School main gate, classroom doors (optional), library, hostel.
*   Student taps card on the reader. Beeps green = entry logged. Beeps red = card invalid or expired.
*   Dual swipe detection: Entry tap + Exit tap = total campus duration calculated.

### 3. AI Facial Recognition

**Feature Description:**
Fully contactless, camera-based identification.
*   Cameras at the school entrance capture faces as students walk in.
*   AI matches the face against the enrolled photo database.
*   Advantages: Students don't need to carry anything or touch anything (hygiene-friendly, post-COVID).
*   Challenges: Accuracy drops with glasses, masks, or lighting changes -- handled via continuous model training.

### 4. Device Health Dashboard

**Feature Description:**
Monitoring all attendance hardware in real-time.
*   Dashboard shows: "Gate 1 RFID Reader: ONLINE. Gate 2 Fingerprint Scanner: OFFLINE (last heartbeat: 8:02 AM)."
*   If a device goes offline during peak entry time (7:30-8:30 AM), an emergency alert is sent to IT.
*   Fallback protocol: If biometric fails, teachers are notified to switch to manual marking.

---

## DATABASE SCHEMA

### 1. Biometric Enrollment (`att_biometric_enrollment`)

```sql
CREATE TABLE att_biometric_enrollment (
    enrollment_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    
    biometric_type ENUM('FINGERPRINT', 'FACE', 'RFID', 'QR_CODE'),
    biometric_template BLOB, -- Fingerprint template / Face encoding
    rfid_card_number VARCHAR(50),
    qr_code_hash VARCHAR(64),
    
    enrolled_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    enrolled_by INT,
    
    is_active BOOLEAN DEFAULT TRUE,
    deactivated_reason VARCHAR(100), -- 'Card Lost', 'Student Left'
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Device Scan Logs (`att_device_scan_logs`)

```sql
CREATE TABLE att_device_scan_logs (
    scan_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    device_id INT NOT NULL,
    
    student_id INT, -- NULL if scan failed to identify
    scan_type ENUM('ENTRY', 'EXIT'),
    
    scan_timestamp DATETIME NOT NULL,
    
    match_confidence DECIMAL(5,2), -- For facial recognition
    is_manual_override BOOLEAN DEFAULT FALSE,
    
    raw_data TEXT, -- Device-specific raw scan data
    
    FOREIGN KEY (device_id) REFERENCES att_devices(device_id)
);
```

### 3. Attendance Devices (`att_devices`)

```sql
CREATE TABLE att_devices (
    device_id INT PRIMARY KEY AUTO_INCREMENT,
    device_name VARCHAR(100), -- 'Main Gate RFID Reader'
    device_type ENUM('FINGERPRINT', 'RFID_READER', 'FACE_CAMERA', 'QR_SCANNER'),
    
    location VARCHAR(100), -- 'Main Gate', 'Block B Entrance'
    
    status ENUM('ONLINE', 'OFFLINE', 'MAINTENANCE'),
    last_heartbeat DATETIME,
    
    ip_address VARCHAR(45),
    firmware_version VARCHAR(20)
);
```

---

## BUSINESS RULES

### Rule 1: Biometric-to-Attendance Sync
*   A gate entry scan auto-creates a "Present" record in `att_daily_records` ONLY if the scan timestamp is before the `att_marking_window_end_time`. Scans after the window do not auto-mark attendance (to prevent after-hours visits from counting as school attendance).

### Rule 2: Duplicate Scan Prevention
*   If a student scans RFID twice within 5 minutes, the second scan is ignored (prevents accidental double-taps).

### Rule 3: Lost Card / Forgot Card Protocol
*   If a student forgets their RFID card, the front desk manually verifies identity and creates a "Manual Override" entry. The system logs this separately. If a student reports their card as lost, the old card is deactivated and a new one is issued (with a new RFID number).

### Rule 4: Facial Recognition Minimum Confidence
*   A face match is accepted ONLY if the AI confidence is >= 85%. Below that threshold, the system flags it as "Uncertain Match" and asks the student to use an alternate method (RFID/fingerprint) as verification.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Daily Attendance (01):** Automatically populates the daily attendance record.
*   **To Transport Module:** Bus RFID scans feed into transport attendance (who boarded the bus?).
*   **To Security Module:** Campus entry/exit logs for visitor management correlation.

### Inbound Relationships
*   **From Student Module:** Reads student photos for facial recognition enrollment.
*   **From IT Infrastructure:** Network and power status for device health monitoring.

---

## USER WORKFLOWS

### Workflow 1: Student Entry via RFID
**Actor:** Student

1.  **Arrive:** Student walks to the school gate at 7:45 AM.
2.  **Tap:** Holds their ID card near the RFID reader. Beeps green. Gate opens.
3.  **Auto-Mark:** System logs: "Student 1001, Entry, 7:45:12 AM, Device: Main Gate RFID."
4.  **Attendance:** Daily attendance for Student 1001 is auto-set to "Present" in Grade 8A's roll.
5.  **Parent:** Parent's app shows: "Aarav entered school at 7:45 AM."

### Workflow 2: Fingerprint Device Failure During Peak Hours
**Actor:** IT Administrator

1.  **Alert:** Dashboard shows "Block B Fingerprint Scanner OFFLINE at 7:52 AM. Last heartbeat: 7:50 AM."
2.  **Immediate:** IT rushes to Block B. Finds the device has hung due to a firmware glitch.
3.  **Fallback:** Admin broadcasts to Block B teachers: "Biometric offline in Block B. Please mark attendance manually."
4.  **Fix:** IT reboots the device. Back online at 8:10 AM.
5.  **Reconcile:** Students who entered during the downtime and were manually marked are cross-checked against the device logs after it recovers.

---

## EDGE CASES

### Edge Case 1: Student's Fingerprint Not Recognized (Wet Hands)
*   **Scenario:** After recess, a student's sweaty fingers fail biometric scan 3 times.
*   **Resolution:** After 3 failed attempts, the device falls back to RFID or QR code scan. An "Alternate Auth" event is logged. If this happens frequently for a specific student, the system recommends re-enrolling their fingerprint template.

### Edge Case 2: Identical Twins
*   **Scenario:** Identical twins have very similar facial features. The facial recognition system matches Student A's face to Student B's profile.
*   **Resolution:** For students flagged as siblings in the same school, the facial recognition threshold is raised to 95%. Additionally, secondary verification (RFID card number) is required for these students.

### Edge Case 3: Power Outage Affecting All Devices
*   **Scenario:** School loses power at 7:30 AM (peak entry). All RFID readers and scanners go offline.
*   **Resolution:** Devices with built-in battery backup (UPS) continue for 30 minutes. Beyond that, the security guard maintains a manual log. Once power restores, IT enters the manual log into the system via a "Bulk Manual Entry" interface.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `bio_rfid_duplicate_scan_cooldown_sec` | 300 | Seconds to ignore duplicate scans |
| `bio_face_match_confidence_min` | 85% | Minimum confidence for facial recognition |
| `bio_device_heartbeat_interval_sec` | 60 | How often devices report health status |
| `bio_device_offline_alert_after_sec` | 180 | Seconds of silence before alerting IT |
| `bio_fallback_on_failure` | `RFID` | Alternative method if primary fails |
| `bio_auto_mark_attendance` | `true` | Auto-create daily attendance from scan? |
| `bio_entry_exit_tracking` | `true` | Track both entry and exit timestamps? |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

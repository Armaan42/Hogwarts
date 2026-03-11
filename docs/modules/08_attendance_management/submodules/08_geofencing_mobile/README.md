# GEOFENCING & MOBILE ATTENDANCE

**Module:** Attendance Management  
**Submodule Code:** ATT-GEO-008  
**Category:** Advanced / Mobile  
**Priority:** Medium (P2)  
**Owners:** IT Administrator, Transport Coordinator, Field Trip Coordinators

---

## OVERVIEW

The Geofencing & Mobile Attendance submodule extends the attendance system beyond the school's physical gates. It uses GPS-based geofencing to verify that attendance is being marked from a valid location, supports mobile-based attendance for field trips and sports events, and enables transport attendance tracking (tracking students on the school bus). This is particularly useful for off-campus activities where traditional biometric devices are not available.

### Purpose

To ensure attendance integrity in mobile and off-campus scenarios. A teacher on a field trip can mark attendance from their phone, but ONLY if their GPS confirms they are at the designated location (museum, stadium, etc.). This prevents fake attendance from remote locations.

### Scope

-   **Geofence Definition:** Drawing virtual boundaries around the school campus and off-campus venues.
-   **GPS-Verified Mobile Attendance:** Teacher marks from app, GPS validates location.
-   **Bus Attendance:** Tracking students boarding and de-boarding the school bus.
-   **Field Trip Check-In:** Roll calls at off-campus locations.
-   **Sports Event Attendance:** Marking for inter-school competitions off-campus.
-   **Parent Pickup Tracking:** Logging when parents pick up students early.

---

## KEY FEATURES

### 1. Campus Geofence

**Feature Description:**
A virtual boundary around the school.
*   Admin draws a polygon around the school campus on a map interface.
*   Radius: Configurable (default: 200 meters from school center point).
*   Any attendance marked via the mobile app MUST originate from within this geofence.
*   If a teacher tries to mark attendance from home (outside the geofence), the system blocks: "You are outside the school campus. Attendance cannot be marked from this location."

### 2. Field Trip Mobile Attendance

**Feature Description:**
Off-campus check-ins with location verification.
*   Before a field trip, the coordinator creates a "Temporary Geofence" around the destination (e.g., National Museum, 500m radius).
*   On arrival, the teacher opens the app and marks attendance. GPS verifies they are at the museum.
*   Multiple check-ins throughout the day: "10:00 AM - Museum (40/40 present). 1:00 PM - Lunch Spot (39/40 - Rohan missing)."

### 3. School Bus Attendance

**Feature Description:**
Tracking students on transport.
*   Each school bus has an RFID reader or an app on the bus attendant's phone.
*   Student taps RFID at boarding. System logs: "Student 1001 boarded Bus 7 at Stop 3 at 7:15 AM."
*   At de-boarding (school gate), another tap confirms arrival.
*   If a student boards but doesn't de-board (missed their stop?), an alert is sent.
*   Parent notification: "Aarav boarded Bus 7 at 7:15 AM. Arrived at school at 7:45 AM."

### 4. Early Pickup Tracking

**Feature Description:**
Logging when parents collect students mid-day.
*   Parent arrives at school at 12:00 PM for early pickup (doctor's appointment).
*   Front desk logs the pickup: Student, Time, Reason, Parent ID verified.
*   Attendance changes from "Present" to "Half-Day (Early Departure at 12:00 PM)."
*   Subsequent period-wise slots are auto-marked "Absent."

---

## DATABASE SCHEMA

### 1. Geofence Definitions (`att_geofences`)

```sql
CREATE TABLE att_geofences (
    geofence_id INT PRIMARY KEY AUTO_INCREMENT,
    geofence_name VARCHAR(100), -- 'School Campus', 'National Museum Trip'
    
    geofence_type ENUM('PERMANENT', 'TEMPORARY'),
    
    center_latitude DECIMAL(10, 7),
    center_longitude DECIMAL(10, 7),
    radius_meters INT DEFAULT 200,
    polygon_coordinates JSON, -- For complex shapes
    
    active_from DATETIME,
    active_until DATETIME, -- NULL for permanent
    
    created_by INT,
    is_active BOOLEAN DEFAULT TRUE
);
```

### 2. GPS Verification Logs (`att_gps_verification`)

```sql
CREATE TABLE att_gps_verification (
    verification_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    teacher_id INT NOT NULL,
    
    latitude DECIMAL(10, 7),
    longitude DECIMAL(10, 7),
    accuracy_meters DECIMAL(6,1),
    
    geofence_id INT,
    is_within_geofence BOOLEAN,
    
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    device_id VARCHAR(100)
);
```

### 3. Bus Attendance (`att_bus_attendance`)

```sql
CREATE TABLE att_bus_attendance (
    bus_att_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    bus_route_id INT NOT NULL,
    
    attendance_date DATE,
    
    boarding_time DATETIME,
    boarding_stop VARCHAR(100),
    
    deboarding_time DATETIME,
    deboarding_stop VARCHAR(100),
    
    status ENUM('BOARDED', 'ARRIVED', 'MISSED_DEBOARD', 'DID_NOT_BOARD'),
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## BUSINESS RULES

### Rule 1: GPS Accuracy Threshold
*   The GPS reading must have an accuracy of <= 50 meters for the geofence check to be valid. If GPS accuracy is > 50m (common indoors), the system shows: "GPS accuracy too low. Please step outside or use Wi-Fi-based location."

### Rule 2: Temporary Geofence Auto-Expiry
*   Geofences created for field trips automatically expire at the `active_until` timestamp. No manual cleanup is needed.

### Rule 3: Bus Attendance Cannot Replace School Attendance
*   Boarding the school bus confirms the student is en route to school. It does NOT mark "Present" in the daily roll. The daily attendance is marked separately (via biometric at the gate or teacher marking). Bus attendance is a supplementary tracking layer.

### Rule 4: Mock Location Detection
*   The mobile app detects GPS spoofing (mock location apps). If detected, the attendance attempt is blocked and an alert is sent to IT: "Teacher [Name] attempted attendance with mock GPS location."

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Daily Attendance (01):** GPS-verified attendance contributes to the daily record.
*   **To Transport Module:** Bus boarding/deboarding data integrated with route management.
*   **To Parent Portal:** Real-time bus tracking and pickup logs.

### Inbound Relationships
*   **From Timetable/Trip Planner:** Provides field trip venue details for temporary geofence creation.
*   **From Transport Module:** Supplies bus routes and stop lists.

---

## USER WORKFLOWS

### Workflow 1: Field Trip Attendance with GPS
**Actor:** Trip Coordinator (Teacher)

1.  **Pre-Trip:** Creates a temporary geofence around "Science City" (radius 300m).
2.  **At Venue:** Opens app at 10:00 AM. GPS confirms: "You are at Science City."
3.  **Mark:** Takes roll call. 38 present out of 40. 2 students were on the bus but not yet inside.
4.  **Re-Check:** At 10:15 AM, re-marks. 40/40 present.
5.  **Departure:** Marks "All students on bus" at 3:00 PM. Geofence expires at 5:00 PM automatically.

### Workflow 2: Parent Tracks Bus in Real-Time
**Actor:** Parent

1.  **Morning:** App notification: "Aarav boarded Bus 7 at Stop 3 (7:15 AM)."
2.  **Track:** Opens map. Sees Bus 7 icon moving toward school.
3.  **Arrival:** Notification: "Bus 7 reached school at 7:45 AM. Aarav is at school."
4.  **Evening:** "Aarav boarded Bus 7 at school (3:30 PM). Estimated arrival at Stop 3: 4:15 PM."

---

## EDGE CASES

### Edge Case 1: GPS Not Available (Underground Location)
*   **Scenario:** Field trip to a cave/museum basement. No GPS signal.
*   **Resolution:** The teacher switches to "Manual Mode" which allows attendance without GPS verification but logs: "GPS Unavailable - Manual Override." A justification text is required: "Underground venue, no GPS."

### Edge Case 2: Student Left on the Bus
*   **Scenario:** A kindergarten student falls asleep and the bus attendant doesn't notice. The bus returns to the depot with the child still on board.
*   **Resolution:** The system detects: "Student 1001 has BOARDED status but no DEBOARDING log at school." An emergency alert is sent to the Transport Coordinator and parent immediately after the bus arrives at school and the student hasn't deboarded.

### Edge Case 3: Two Schools Share a Campus
*   **Scenario:** Junior school and Senior school share the same campus. Geofences overlap.
*   **Resolution:** Each school defines its own geofence with a slightly different boundary (e.g., junior uses Block A polygon, senior uses Block B polygon). The app identifies which school the teacher belongs to and validates against the correct geofence.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `geo_campus_radius_meters` | 200 | Default campus geofence radius |
| `geo_gps_accuracy_max_meters` | 50 | Max acceptable GPS accuracy |
| `geo_mock_location_detection` | `true` | Detect and block GPS spoofing? |
| `geo_temp_geofence_auto_expiry` | `true` | Auto-expire trip geofences? |
| `geo_bus_missed_deboard_alert` | `true` | Alert if student doesn't deboard? |
| `geo_bus_parent_tracking` | `true` | Enable real-time bus tracking for parents? |
| `geo_manual_mode_allowed` | `true` | Allow attendance without GPS in emergencies? |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

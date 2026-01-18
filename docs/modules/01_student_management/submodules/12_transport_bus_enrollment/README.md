# TRANSPORT & BUS ENROLLMENT - COMPLETE DOCUMENTATION

**Module:** Student Management  
**Submodule Code:** STD-TRANSPORT-012  
**Category:** Student Services  
**Priority:** High (P1)  
**Owner:** Transport Department

---

## 📋 OVERVIEW

The Transport & Bus Enrollment submodule manages school transport services including bus route assignments, pickup/drop points, GPS tracking, transport fee management, bus pass issuance, driver/conductor details, and ensures student safety during commute with real-time parent notifications.

### Purpose

Enroll students in school transport, assign optimal bus routes, manage pickup/drop locations, track bus movements via GPS, issue bus passes, collect transport fees, ensure student safety, communicate with parents, and maintain transport records.

### Scope

- **Route Assignment**: Optimal bus route allocation
- **Pickup/Drop Points**: Location management
- **GPS Tracking**: Real-time bus location
- **Bus Pass**: Digital and physical passes
- **Transport Fee**: Route-based fee calculation
- **Safety**: Attendance, GPS alerts
- **Parent Communication**: SMS notifications

---

## 🎯 KEY FEATURES

### 1. Bus Route Management

#### 1.1 Route Assignment

**Route Details:**
```yaml
Route ID: ROUTE-01
Route Name: Noida Sector 15-62 Route
Bus Number: DL-1AB-1234
Capacity: 40 students
Current Enrollment: 35 students
Available Seats: 5

Route Path:
  Start: School (Sector 62, Noida)
  End: Sector 15, Noida
  Distance: 18 km
  Duration: 45 minutes (approx)
  
Stops (Morning Pickup):
  1. Sector 62 (School) - 7:00 AM
  2. Sector 58 - 7:05 AM
  3. Sector 52 - 7:12 AM
  4. Sector 50 - 7:18 AM
  5. Sector 44 - 7:25 AM
  6. Sector 37 - 7:32 AM
  7. Sector 29 - 7:38 AM
  8. Sector 18 - 7:43 AM
  9. Sector 15 - 7:48 AM
  10. Return to School - 8:00 AM

Stops (Afternoon Drop):
  1. School - 3:30 PM
  2. Sector 15 - 3:42 PM
  3. Sector 18 - 3:47 PM
  4. Sector 29 - 3:52 PM
  5. Sector 37 - 3:58 PM
  6. Sector 44 - 4:05 PM
  7. Sector 50 - 4:12 PM
  8. Sector 52 - 4:18 PM
  9. Sector 58 - 4:25 PM
  10. Sector 62 - 4:30 PM

Staff:
  Driver: Mr. Ramesh Kumar
    License: DL-123456789
    Experience: 15 years
    Contact: +91-9876543210
    
  Conductor: Mrs. Priya Sharma
    ID: COND-001
    Experience: 8 years
    Contact: +91-9876543211

Transport Fee (Annual):
  0-5 km: ₹15,000
  5-10 km: ₹20,000
  10-15 km: ₹25,000
  15-20 km: ₹30,000
```

#### 1.2 Student Enrollment

**Enrollment Record:**
```yaml
Student: Aarav Patel (2024/06/0456)
Grade: 6-A
Academic Year: 2024-25

Transport Details:
  Route: ROUTE-01 (Noida Sector 15-62)
  Bus Number: DL-1AB-1234
  
Pickup Details:
  Stop: Sector 52, Noida
  Address: A-123, Sector 52, Noida
  Landmark: Near Metro Station
  Pickup Time: 7:12 AM
  Distance from School: 12 km
  
Drop Details:
  Stop: Sector 52, Noida
  Address: A-123, Sector 52, Noida
  Drop Time: 4:18 PM

Transport Fee:
  Category: 10-15 km
  Annual Fee: ₹25,000
  Payment Mode: Term-wise
  Term 1: ₹8,334 (Paid)
  Term 2: ₹8,333 (Paid)
  Term 3: ₹8,333 (Pending)

Bus Pass:
  Pass Number: BP-2024-0456
  Issue Date: 01/04/2024
  Valid Till: 31/03/2025
  Type: Digital + Physical
  QR Code: Generated
  Photo: Uploaded

Emergency Contacts:
  Primary: Mr. Rajesh Patel (Father) - +91-9876543210
  Secondary: Mrs. Priya Patel (Mother) - +91-9876543211
  Alternate: Mr. Suresh Patel (Uncle) - +91-9876543212

Authorized Pickup Persons:
  1. Mr. Rajesh Patel (Father)
  2. Mrs. Priya Patel (Mother)
  3. Mr. Suresh Patel (Uncle)
  4. Mrs. Anjali Patel (Grandmother)

GPS Tracking:
  Parent App Access: Yes
  Real-time Tracking: Enabled
  Arrival Alerts: SMS + App Notification
  
Enrollment Status: ACTIVE
Enrollment Date: 01/04/2024
```

---

### 2. GPS Tracking & Safety

#### 2.1 Real-time Bus Tracking

**GPS System:**
```yaml
Bus: DL-1AB-1234 (ROUTE-01)
GPS Device: GPS-TRACK-001
Status: ACTIVE

Current Location (Live):
  Latitude: 28.5355° N
  Longitude: 77.3910° E
  Location: Sector 52, Noida
  Speed: 25 km/h
  Last Updated: 2 seconds ago

Route Progress:
  Current Stop: Sector 52 (Stop 3/10)
  Next Stop: Sector 50 (ETA: 6 minutes)
  Students Onboard: 15/35
  
  Completed Stops:
    ✓ Sector 62 (School) - 7:00 AM
    ✓ Sector 58 - 7:05 AM
    ✓ Sector 52 - 7:12 AM (Current)
    
  Upcoming Stops:
    ○ Sector 50 - 7:18 AM (ETA: 6 min)
    ○ Sector 44 - 7:25 AM (ETA: 13 min)
    ○ Sector 37 - 7:32 AM (ETA: 20 min)

Alerts:
  - No alerts
  - On schedule
  - Speed within limits (40 km/h max)

Parent Notifications Sent:
  7:05 AM: Bus departed from Sector 58
  7:12 AM: Bus arrived at Sector 52
  7:12 AM: Aarav Patel boarded the bus
  7:18 AM: Bus will arrive at Sector 50 in 6 minutes
```

#### 2.2 Student Attendance on Bus

**Bus Attendance:**
```yaml
Date: 17/01/2024 (Morning Trip)
Route: ROUTE-01
Bus: DL-1AB-1234

Student Attendance:

Stop 1 - Sector 58 (7:05 AM):
  Boarded:
    - Rohan Kumar (2024/06/0457) ✓
    - Priya Sharma (2024/06/0458) ✓
    - Diya Patel (2018/08/0123) ✓
  Absent:
    - Arjun Verma (2024/06/0459) ❌
      Reason: On leave (sick)
      Parent Notified: Yes

Stop 2 - Sector 52 (7:12 AM):
  Boarded:
    - Aarav Patel (2024/06/0456) ✓
    - Amit Kumar (2024/06/0461) ✓
  Absent: None

[... continues for all stops]

Total Students:
  Expected: 35
  Boarded: 34
  Absent: 1 (On leave)
  Attendance: 97%

Conductor Notes:
  - All students boarded safely
  - Seat belts checked
  - No issues reported
  - Reached school on time (8:00 AM)

Parent Notifications:
  - Boarding SMS sent to all parents
  - Absent student parent notified
  - Arrival at school SMS sent
```

---

### 3. Bus Pass Management

#### 3.1 Digital Bus Pass

**Bus Pass Details:**
```yaml
Pass Type: DIGITAL + PHYSICAL
Student: Aarav Patel (2024/06/0456)

Digital Pass:
  Pass Number: BP-2024-0456
  QR Code: [QR Code Image]
  Barcode: 2024045600001
  
  Student Details:
    Name: Aarav Patel
    Admission No: 2024/06/0456
    Grade: 6-A
    Photo: [Student Photo]
    
  Route Details:
    Route: ROUTE-01
    Bus: DL-1AB-1234
    Pickup: Sector 52, Noida
    Drop: Sector 52, Noida
    
  Validity:
    Issue Date: 01/04/2024
    Expiry Date: 31/03/2025
    Status: ACTIVE
    
  Access:
    Mobile App: Yes
    Digital Wallet: Added
    Offline Access: Yes

Physical Pass:
  Card Type: PVC Card
  Card Number: BP-2024-0456
  Issue Date: 05/04/2024
  Collected By: Parent
  
  Security Features:
    - Hologram
    - School logo
    - QR code
    - Student photo
    - Signature

Usage:
  - Scan at bus entry/exit
  - Automatic attendance marking
  - Parent notification trigger
  - Access control

Renewal:
  Auto-renewal: Yes (if fee paid)
  Renewal Date: 01/04/2025
  Renewal Fee: Included in transport fee
```

---

### 4. Transport Fee Management

#### 4.1 Fee Structure

**Distance-based Fee:**
```yaml
Fee Structure (2024-25):

Slab 1: 0-5 km
  Annual Fee: ₹15,000
  Per Month: ₹1,250
  Term-wise: ₹5,000 per term
  
Slab 2: 5-10 km
  Annual Fee: ₹20,000
  Per Month: ₹1,667
  Term-wise: ₹6,667 per term
  
Slab 3: 10-15 km
  Annual Fee: ₹25,000
  Per Month: ₹2,083
  Term-wise: ₹8,333 per term
  
Slab 4: 15-20 km
  Annual Fee: ₹30,000
  Per Month: ₹2,500
  Term-wise: ₹10,000 per term

Additional Charges:
  Bus Pass (Physical): ₹500 (one-time)
  GPS Tracking: Included
  Insurance: Included
  
Discounts:
  Sibling Discount: 10% on 2nd child
  Annual Payment: 5% discount
  Staff Ward: 50% discount

Payment Options:
  - Annual (5% discount)
  - Term-wise (3 installments)
  - Monthly (12 installments)
```

---

## 📊 DATABASE SCHEMA

### Transport Enrollment Table

```sql
CREATE TABLE transport_enrollments (
  enrollment_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Route Details
  route_id INT NOT NULL,
  bus_id INT NOT NULL,
  
  -- Pickup/Drop
  pickup_stop_id INT NOT NULL,
  pickup_address TEXT,
  pickup_landmark VARCHAR(200),
  pickup_time TIME,
  
  drop_stop_id INT NOT NULL,
  drop_address TEXT,
  drop_time TIME,
  
  -- Distance
  distance_km DECIMAL(5,2),
  
  -- Fee
  transport_fee DECIMAL(10,2) NOT NULL,
  fee_slab VARCHAR(20),
  
  -- Bus Pass
  bus_pass_number VARCHAR(50) UNIQUE,
  bus_pass_issue_date DATE,
  bus_pass_expiry_date DATE,
  bus_pass_status ENUM('ACTIVE', 'EXPIRED', 'SUSPENDED', 'CANCELLED') DEFAULT 'ACTIVE',
  
  -- Emergency Contacts
  emergency_contact_1 VARCHAR(15),
  emergency_contact_2 VARCHAR(15),
  
  -- Authorized Pickup
  authorized_persons TEXT,
  
  -- GPS Tracking
  gps_tracking_enabled BOOLEAN DEFAULT TRUE,
  parent_app_access BOOLEAN DEFAULT TRUE,
  
  -- Status
  enrollment_status ENUM('ACTIVE', 'SUSPENDED', 'CANCELLED') DEFAULT 'ACTIVE',
  
  -- Dates
  enrollment_date DATE NOT NULL,
  start_date DATE NOT NULL,
  end_date DATE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  notes TEXT,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  FOREIGN KEY (route_id) REFERENCES bus_routes(route_id),
  FOREIGN KEY (bus_id) REFERENCES buses(bus_id),
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_route_id (route_id),
  INDEX idx_bus_id (bus_id),
  INDEX idx_status (enrollment_status)
);
```

### Bus Routes Table

```sql
CREATE TABLE bus_routes (
  route_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Route Details
  route_name VARCHAR(200) NOT NULL,
  route_code VARCHAR(50) UNIQUE NOT NULL,
  
  -- Path
  start_location VARCHAR(200),
  end_location VARCHAR(200),
  total_distance_km DECIMAL(5,2),
  estimated_duration_minutes INT,
  
  -- Capacity
  total_capacity INT NOT NULL,
  current_enrollment INT DEFAULT 0,
  available_seats INT,
  
  -- Timing
  morning_start_time TIME,
  morning_end_time TIME,
  afternoon_start_time TIME,
  afternoon_end_time TIME,
  
  -- Status
  is_active BOOLEAN DEFAULT TRUE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Indexes
  INDEX idx_route_code (route_code),
  INDEX idx_active (is_active)
);
```

### Bus Attendance Table

```sql
CREATE TABLE bus_attendance (
  attendance_id BIGINT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Date & Trip
  attendance_date DATE NOT NULL,
  trip_type ENUM('MORNING', 'AFTERNOON') NOT NULL,
  
  -- Route & Bus
  route_id INT NOT NULL,
  bus_id INT NOT NULL,
  
  -- Attendance
  status ENUM('PRESENT', 'ABSENT', 'ON_LEAVE') NOT NULL,
  boarding_time TIME,
  alighting_time TIME,
  
  -- Stop
  pickup_stop_id INT,
  drop_stop_id INT,
  
  -- Marked By
  marked_by INT,
  marking_method ENUM('MANUAL', 'QR_SCAN', 'RFID', 'GPS') DEFAULT 'MANUAL',
  
  -- Parent Notification
  parent_notified BOOLEAN DEFAULT FALSE,
  notification_time TIMESTAMP,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  FOREIGN KEY (route_id) REFERENCES bus_routes(route_id),
  FOREIGN KEY (bus_id) REFERENCES buses(bus_id),
  
  -- Indexes
  INDEX idx_student_date (student_id, attendance_date),
  INDEX idx_date (attendance_date),
  INDEX idx_route (route_id, attendance_date)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Route Capacity Check

```javascript
FUNCTION check_route_capacity(route_id) {
  route = GET_ROUTE(route_id)
  
  current_enrollment = QUERY("
    SELECT COUNT(*) as count
    FROM transport_enrollments
    WHERE route_id = ?
    AND enrollment_status = 'ACTIVE'
  ", [route_id])
  
  available_seats = route.total_capacity - current_enrollment.count
  
  IF available_seats <= 0 {
    RETURN {
      available: FALSE,
      message: "Route at full capacity",
      capacity: route.total_capacity,
      enrolled: current_enrollment.count
    }
  }
  
  IF available_seats <= 5 {
    RETURN {
      available: TRUE,
      warning: "Limited seats available",
      available_seats: available_seats
    }
  }
  
  RETURN {
    available: TRUE,
    available_seats: available_seats
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Fee Management**: Transport fee collection
2. **Communication Module**: Parent SMS notifications
3. **GPS System**: Real-time tracking
4. **Mobile App**: Parent tracking interface

### Inbound Integrations

1. **Student Registration**: Transport enrollment during admission
2. **Parent Portal**: Transport application
3. **Payment Gateway**: Fee payment

---

## 👥 USER WORKFLOWS

### Workflow: Enroll Student in Transport

**Actor:** Transport Coordinator  
**Duration:** 15-20 minutes

**Steps:**

1. Login to Admin Portal
2. Navigate to: Transport → Enrollments
3. Select student
4. Choose route based on address
5. Assign pickup/drop stop
6. Calculate transport fee
7. Generate bus pass
8. Collect fee
9. Activate enrollment
10. Send confirmation to parents

**Expected Outcome:** Student enrolled in transport with active bus pass

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 17, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

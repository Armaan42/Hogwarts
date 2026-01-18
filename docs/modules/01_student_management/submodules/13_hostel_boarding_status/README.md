# HOSTEL & BOARDING STATUS - COMPLETE DOCUMENTATION

**Module:** Student Management  
**Submodule Code:** STD-HOSTEL-013  
**Category:** Student Services  
**Priority:** High (P1)  
**Owner:** Hostel Administration

---

## 📋 OVERVIEW

The Hostel & Boarding Status submodule manages residential student accommodations including room assignments, hostel fee management, mess services, warden supervision, visitor management, leave tracking, and ensures student safety and well-being in hostel facilities.

### Purpose

Manage hostel admissions, assign rooms, track hostel fees, manage mess services, monitor student welfare, handle visitor permissions, track hostel leave, ensure safety and security, and maintain hostel records.

### Scope

- **Room Allocation**: Hostel and room assignment
- **Hostel Fee**: Boarding charges management
- **Mess Services**: Meal planning and tracking
- **Warden Supervision**: Student monitoring
- **Visitor Management**: Guest permissions
- **Leave Tracking**: Hostel exit/entry
- **Safety & Security**: 24x7 monitoring

---

## 🎯 KEY FEATURES

### 1. Hostel Admission & Room Allocation

#### 1.1 Hostel Assignment

**Hostel Details:**
```yaml
Student: Aarav Patel (2024/06/0456)
Grade: 6-A
Academic Year: 2024-25

Hostel Assignment:
  Hostel Name: Boys Hostel - Block A
  Room Number: A-205
  Floor: 2nd Floor
  Room Type: Triple Sharing
  Bed Number: Bed 2
  
Room Capacity: 3 students
Current Occupancy: 3 students
Roommates:
  1. Rohan Kumar (2024/06/0457) - Bed 1
  2. Aarav Patel (2024/06/0456) - Bed 2
  3. Arjun Verma (2024/06/0459) - Bed 3

Room Facilities:
  - 3 Study tables with chairs
  - 3 Wardrobes
  - 3 Beds with mattresses
  - Attached bathroom
  - Fan (2 units)
  - Tube lights (2 units)
  - Window with curtains
  - Notice board

Hostel Facilities:
  - Common room with TV
  - Study hall
  - Dining hall
  - Recreation room
  - Gym
  - Medical room
  - Laundry service
  - Wi-Fi
  - 24x7 security
  - CCTV surveillance

Warden Details:
  Name: Mr. Suresh Kumar
  Contact: +91-9876543210
  Room: Ground Floor, Room 101
  Available: 24x7

Hostel Fee (Annual):
  Boarding Charges: ₹80,000
  Mess Charges: ₹40,000
  Laundry: ₹5,000
  Maintenance: ₹5,000
  Total: ₹1,30,000
  
  Payment Mode: Term-wise
  Term 1: ₹43,334 (Paid)
  Term 2: ₹43,333 (Paid)
  Term 3: ₹43,333 (Pending)

Admission Date: 01/04/2024
Check-in Date: 05/04/2024
Check-in Time: 10:00 AM
```

---

### 2. Mess & Food Services

#### 2.1 Meal Planning

**Weekly Menu:**
```yaml
Week: 15-21 January 2024
Hostel: Boys Hostel - Block A

Monday:
  Breakfast (7:00-8:00 AM):
    - Aloo Paratha
    - Curd
    - Pickle
    - Tea/Milk
    
  Lunch (12:30-1:30 PM):
    - Roti/Rice
    - Dal Tadka
    - Mixed Vegetable
    - Salad
    - Buttermilk
    
  Evening Snack (4:30-5:00 PM):
    - Samosa
    - Tea
    
  Dinner (7:30-8:30 PM):
    - Roti/Rice
    - Paneer Butter Masala
    - Dal
    - Raita
    - Sweet (Gulab Jamun)

[... continues for all days]

Special Dietary Requirements:
  Student: Aarav Patel
  Requirement: Vegetarian (Jain)
  Restrictions: No onion, no garlic
  Alternate Menu: Provided

Mess Feedback:
  Rating System: 1-5 stars
  Weekly Feedback: Collected
  Average Rating: 4.2/5
  Improvements: Based on feedback
```

---

### 3. Hostel Leave Management

#### 3.1 Leave Application

**Leave Request:**
```yaml
Leave ID: HL-2024-0456
Student: Aarav Patel (2024/06/0456)
Hostel: Boys Hostel - Block A
Room: A-205

Leave Details:
  Type: Home Visit
  From: 20/01/2024 (Saturday) 10:00 AM
  To: 21/01/2024 (Sunday) 6:00 PM
  Duration: 1 day 8 hours
  Reason: Family function
  
Applied By: Student
Applied On: 18/01/2024 at 5:00 PM
Applied Via: Student Portal

Parent Consent:
  Consent Given: Yes
  Consent By: Mr. Rajesh Patel (Father)
  Consent Date: 18/01/2024
  Consent Method: Digital signature

Approval Workflow:
  Step 1: Warden Approval
    Approved By: Mr. Suresh Kumar
    Date: 18/01/2024 at 6:00 PM
    Status: APPROVED
    
  Step 2: Principal Approval (>2 days)
    Status: NOT REQUIRED (1 day leave)

Pickup Details:
  Picked By: Mr. Rajesh Patel (Father)
  Pickup Time: 20/01/2024 at 10:15 AM
  ID Verified: Yes (Aadhaar)
  Signature: Obtained
  Gate Pass: GP-2024-0456

Return Details:
  Expected Return: 21/01/2024 at 6:00 PM
  Actual Return: 21/01/2024 at 5:45 PM
  Checked In By: Warden
  Status: RETURNED ON TIME ✓

Leave Status: COMPLETED
```

---

### 4. Visitor Management

#### 4.1 Visitor Entry

**Visitor Record:**
```yaml
Visit ID: VIS-2024-0123
Date: 17/01/2024
Student: Aarav Patel (2024/06/0456)

Visitor Details:
  Name: Mr. Rajesh Patel
  Relation: Father
  ID Proof: Aadhaar Card
  ID Number: 1234-5678-9012
  Contact: +91-9876543210
  
Entry Details:
  Entry Time: 2:00 PM
  Entry Gate: Main Gate
  Security Check: Completed ✓
  Visitor Pass: VP-2024-0123
  
Meeting Location: Visitor Room (Ground Floor)
Purpose: Parent-Teacher Meeting

Exit Details:
  Exit Time: 4:30 PM
  Duration: 2 hours 30 minutes
  Visitor Pass Returned: Yes
  
Visitor Rules:
  - Visiting hours: 2:00 PM - 5:00 PM (Weekends)
  - ID proof mandatory
  - Security check required
  - Meeting in designated areas only
  - No entry to student rooms
  - Maximum 2 visitors per student
```

---

## 📊 DATABASE SCHEMA

### Hostel Enrollment Table

```sql
CREATE TABLE hostel_enrollments (
  enrollment_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Hostel Details
  hostel_id INT NOT NULL,
  room_id INT NOT NULL,
  bed_number VARCHAR(10),
  
  -- Dates
  admission_date DATE NOT NULL,
  check_in_date DATE NOT NULL,
  check_out_date DATE,
  
  -- Fee
  boarding_fee DECIMAL(10,2) NOT NULL,
  mess_fee DECIMAL(10,2) NOT NULL,
  other_charges DECIMAL(10,2) DEFAULT 0,
  total_fee DECIMAL(10,2) NOT NULL,
  
  -- Status
  enrollment_status ENUM('ACTIVE', 'CHECKED_OUT', 'SUSPENDED', 'CANCELLED') DEFAULT 'ACTIVE',
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  notes TEXT,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  FOREIGN KEY (hostel_id) REFERENCES hostels(hostel_id),
  FOREIGN KEY (room_id) REFERENCES hostel_rooms(room_id),
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_hostel_id (hostel_id),
  INDEX idx_room_id (room_id),
  INDEX idx_status (enrollment_status)
);
```

### Hostel Leave Table

```sql
CREATE TABLE hostel_leave (
  leave_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Leave Details
  leave_type ENUM('HOME_VISIT', 'MEDICAL', 'EMERGENCY', 'OTHER') NOT NULL,
  from_datetime DATETIME NOT NULL,
  to_datetime DATETIME NOT NULL,
  duration_hours INT,
  reason TEXT NOT NULL,
  
  -- Application
  applied_by INT NOT NULL,
  applied_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Parent Consent
  parent_consent BOOLEAN DEFAULT FALSE,
  parent_consent_date TIMESTAMP,
  
  -- Approval
  approval_status ENUM('PENDING', 'APPROVED', 'REJECTED') DEFAULT 'PENDING',
  approved_by INT,
  approval_date TIMESTAMP,
  
  -- Pickup/Return
  picked_by VARCHAR(200),
  pickup_time DATETIME,
  return_time DATETIME,
  gate_pass_number VARCHAR(50),
  
  -- Status
  leave_status ENUM('PENDING', 'APPROVED', 'IN_PROGRESS', 'COMPLETED', 'CANCELLED') DEFAULT 'PENDING',
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_status (leave_status),
  INDEX idx_dates (from_datetime, to_datetime)
);
```

### Visitor Log Table

```sql
CREATE TABLE hostel_visitor_log (
  visitor_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Visitor Details
  visitor_name VARCHAR(200) NOT NULL,
  relation VARCHAR(100),
  id_proof_type VARCHAR(50),
  id_proof_number VARCHAR(100),
  contact_number VARCHAR(15),
  
  -- Visit Details
  visit_date DATE NOT NULL,
  entry_time TIME NOT NULL,
  exit_time TIME,
  duration_minutes INT,
  purpose VARCHAR(500),
  
  -- Security
  security_check BOOLEAN DEFAULT TRUE,
  visitor_pass_number VARCHAR(50),
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_visit_date (visit_date)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Room Capacity Check

```javascript
FUNCTION check_room_capacity(room_id) {
  room = GET_ROOM(room_id)
  
  current_occupancy = QUERY("
    SELECT COUNT(*) as count
    FROM hostel_enrollments
    WHERE room_id = ?
    AND enrollment_status = 'ACTIVE'
  ", [room_id])
  
  available_beds = room.capacity - current_occupancy.count
  
  IF available_beds <= 0 {
    RETURN {
      available: FALSE,
      message: "Room at full capacity"
    }
  }
  
  RETURN {
    available: TRUE,
    available_beds: available_beds
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Fee Management**: Hostel fee collection
2. **Communication Module**: Parent notifications
3. **Security System**: Entry/exit tracking
4. **Mess Management**: Meal planning

### Inbound Integrations

1. **Student Registration**: Hostel admission
2. **Parent Portal**: Leave applications
3. **Medical Module**: Health records

---

## 👥 USER WORKFLOWS

### Workflow: Hostel Admission

**Actor:** Hostel Warden  
**Duration:** 30 minutes

**Steps:**

1. Receive hostel application
2. Check room availability
3. Assign room and bed
4. Calculate hostel fee
5. Collect fee and documents
6. Issue hostel ID card
7. Conduct orientation
8. Check-in student
9. Notify parents
10. Update records

**Expected Outcome:** Student admitted to hostel with room assignment

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 17, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

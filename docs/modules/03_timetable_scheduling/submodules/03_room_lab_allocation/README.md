# ROOM & LAB ALLOCATION - COMPLETE DOCUMENTATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-ROOM-003  
**Category:** Resource Allocation  
**Priority:** Critical (P0)  
**Owner:** Facilities & Academic Operations

---

## 📋 OVERVIEW

The Room & Lab Allocation submodule manages the assignment of classrooms, laboratories, and special facilities to classes and activities, ensuring optimal utilization, preventing conflicts, accommodating special requirements, and maintaining flexibility for maintenance and events.

### Purpose

Allocate appropriate rooms based on class size and subject requirements, optimize room utilization across the school, prevent double-booking conflicts, manage lab scheduling for science and computer classes, accommodate special room needs (auditorium, music room, art studio), and handle room maintenance schedules.

### Scope

- **Classroom Assignment**: Regular classroom allocation by section
- **Laboratory Scheduling**: Science, computer, and language labs
- **Special Room Booking**: Auditorium, music room, art studio, library
- **Capacity Management**: Match room size to class strength
- **Utilization Optimization**: Maximize facility usage
- **Maintenance Coordination**: Schedule around repairs and cleaning
- **Equipment Tracking**: Lab equipment and AV systems
- **Multi-Purpose Room Management**: Shared facility scheduling

---

## 🎯 KEY FEATURES

### 1. Room Inventory & Classification

#### 1.1 Room Categories

**Complete Facility Inventory:**
```yaml
School: Hogwarts International
Total Rooms: 75

Regular Classrooms (40):
  Primary Wing (Grades 1-5):
    - Rooms 101-120 (20 rooms)
    - Capacity: 40 students each
    - Features: Whiteboard, projector, AC
    - Total Capacity: 800 students
  
  Middle School Wing (Grades 6-8):
    - Rooms 201-215 (15 rooms)
    - Capacity: 45 students each
    - Features: Smart board, projector, AC
    - Total Capacity: 675 students
  
  Senior Wing (Grades 9-12):
    - Rooms 301-305 (5 rooms)
    - Capacity: 35 students each
    - Features: Smart board, projector, AC, WiFi
    - Total Capacity: 175 students

Science Laboratories (6):
  Physics Labs:
    - Lab P1 (Room 401): Capacity 30, Equipment: Excellent
    - Lab P2 (Room 402): Capacity 30, Equipment: Good
  
  Chemistry Labs:
    - Lab C1 (Room 403): Capacity 30, Equipment: Excellent
    - Lab C2 (Room 404): Capacity 30, Equipment: Good
  
  Biology Labs:
    - Lab B1 (Room 405): Capacity 30, Equipment: Excellent
    - Lab B2 (Room 406): Capacity 30, Equipment: Good
  
  Safety Features:
    - Fire extinguishers, eye wash stations
    - Ventilation systems, fume hoods
    - Emergency exits, first aid kits

Computer Labs (4):
  - Comp Lab 1 (Room 501): 40 computers, Windows
  - Comp Lab 2 (Room 502): 40 computers, Windows
  - Mac Lab (Room 503): 30 iMacs, macOS
  - Robotics Lab (Room 504): 20 stations, specialized equipment

Special Purpose Rooms (15):
  - Auditorium: Capacity 500, Stage, AV system
  - Music Room 1: Capacity 30, Instruments
  - Music Room 2: Capacity 30, Recording studio
  - Art Studio 1: Capacity 25, Easels, supplies
  - Art Studio 2: Capacity 25, Pottery wheel
  - Dance Studio: Capacity 40, Mirrors, sound system
  - Library: Capacity 100, 10,000+ books
  - Conference Room 1: Capacity 20
  - Conference Room 2: Capacity 15
  - Counseling Room 1: Capacity 5
  - Counseling Room 2: Capacity 5
  - Medical Room: Capacity 10
  - Sports Equipment Room
  - Cafeteria: Capacity 300
  - Multipurpose Hall: Capacity 200

Outdoor Facilities (10):
  - Sports Ground: Football, Cricket
  - Basketball Courts (2)
  - Volleyball Court
  - Tennis Courts (2)
  - Swimming Pool
  - Primary Playground
  - Middle School Playground
  - Senior Recreation Area
  - Amphitheater: Capacity 150

Total Capacity: 2,650+ students (current enrollment: 1,200)
Utilization Target: 75-85%
```

#### 1.2 Room Specifications Database

**Detailed Room Attributes:**
```yaml
Room: 205 (Middle School Wing)

Basic Information:
  Room Number: 205
  Building: Main Building
  Floor: 2nd Floor
  Wing: Middle School
  Room Type: Regular Classroom
  
Physical Specifications:
  Area: 600 sq ft
  Capacity: 45 students
  Seating Arrangement: Rows (5×9)
  Windows: 4 (natural light)
  Doors: 2 (main + emergency exit)
  
Equipment & Facilities:
  Whiteboard: Yes (8ft × 4ft)
  Smart Board: Yes (Interactive)
  Projector: Yes (HD, 3000 lumens)
  Screen: Motorized, 100 inches
  Audio System: Yes (speakers + mic)
  Air Conditioning: Yes (2-ton split AC)
  Fans: 4 ceiling fans
  Lighting: LED panels (energy efficient)
  Power Outlets: 8 (student use)
  WiFi: Yes (high-speed)
  
Furniture:
  Student Desks: 45 (individual)
  Teacher Desk: 1
  Teacher Chair: 1
  Storage Cabinets: 2
  Bookshelf: 1
  Notice Board: 1
  
Accessibility:
  Wheelchair Accessible: Yes
  Ramp: Available
  Accessible Washroom: Nearby
  
Safety Features:
  Fire Extinguisher: Yes
  Smoke Detector: Yes
  Emergency Exit: Yes
  First Aid Kit: Yes
  
Maintenance:
  Last Cleaned: Today, 6:00 AM
  Last Painted: March 2025
  AC Servicing: Monthly (last: Jan 10, 2026)
  Equipment Check: Weekly
  
Current Assignment:
  Primary Section: Grade 9A
  Periods Allocated: 42/48 (88% utilization)
  Available Periods: 6
  
Booking Status:
  Monday-Friday: Grade 9A (homeroom)
  Saturday: Available for events
  After School: Available for activities
  
Restrictions:
  No food/drinks (except water)
  No outdoor shoes (carpeted)
  Max occupancy: 50 (safety limit)
```

---

### 2. Room Allocation Algorithm

#### 2.1 Capacity-Based Assignment

**Matching Room Size to Class Strength:**
```javascript
FUNCTION allocate_rooms_to_sections(sections, rooms) {
  allocations = {}
  
  // Sort sections by student count (descending)
  sections = SORT(sections, by: student_count, desc: TRUE)
  
  // Sort rooms by capacity (descending)
  rooms = SORT(rooms, by: capacity, desc: TRUE)
  
  FOR each section IN sections {
    student_count = section.student_count
    grade = section.grade
    
    // Find suitable rooms
    suitable_rooms = FILTER(rooms, room => {
      // Capacity check (80-100% utilization optimal)
      capacity_match = (student_count >= room.capacity * 0.8) AND 
                       (student_count <= room.capacity)
      
      // Wing preference (Primary in Primary wing, etc.)
      wing_match = MATCHES_GRADE_WING(room, grade)
      
      // Availability
      is_available = NOT room.is_allocated
      
      RETURN capacity_match AND wing_match AND is_available
    })
    
    IF suitable_rooms.length == 0 {
      // Relax constraints: Try any available room with sufficient capacity
      suitable_rooms = FILTER(rooms, room => {
        RETURN room.capacity >= student_count AND NOT room.is_allocated
      })
    }
    
    IF suitable_rooms.length > 0 {
      // Select best match (closest capacity)
      best_room = SELECT_CLOSEST_CAPACITY(suitable_rooms, student_count)
      
      allocations[section] = best_room
      best_room.is_allocated = TRUE
      best_room.utilization = (student_count / best_room.capacity) * 100
    } ELSE {
      RETURN ERROR(`No suitable room for ${section.name} (${student_count} students)`)
    }
  }
  
  RETURN allocations
}

Example Allocation:

Grade 9A (36 students):
  Suitable Rooms:
    - Room 205 (Capacity 45): Utilization 80% ✓ Optimal
    - Room 301 (Capacity 35): Utilization 103% ✗ Overcapacity
    - Room 210 (Capacity 50): Utilization 72% △ Underutilized
  
  Selected: Room 205 (best match)
  
Grade 12A (28 students):
  Suitable Rooms:
    - Room 301 (Capacity 35): Utilization 80% ✓ Optimal
    - Room 302 (Capacity 35): Utilization 80% ✓ Optimal
  
  Selected: Room 301 (first available)
```

---

### 3. Laboratory Scheduling

#### 3.1 Lab Period Allocation

**Science Lab Scheduling:**
```yaml
Lab: Physics Lab 1 (Room 401)
Capacity: 30 students
Equipment: Excellent
Weekly Availability: 48 periods

Lab Schedule (Week of Jan 20-25):

Monday:
  P1-P2 (8:15-9:45): Grade 11A Physics Lab (30 students)
  P3-P4 (9:45-11:15): Grade 11B Physics Lab (28 students)
  P5-P6 (11:30-1:00): Grade 12A Physics Lab (25 students)
  P7-P8 (1:30-3:00): Grade 12B Physics Lab (27 students)
  
Tuesday:
  P1-P2: Grade 10A Physics Lab (32 students) ⚠️ Over capacity
  P3-P4: Grade 10B Physics Lab (30 students)
  P5-P6: Maintenance (equipment calibration)
  P7-P8: Available
  
Wednesday:
  P1-P2: Grade 9A Physics Lab (36 students) ⚠️ Over capacity
  P3-P4: Grade 9B Physics Lab (35 students) ⚠️ Over capacity
  P5-P6: Grade 9C Physics Lab (34 students) ⚠️ Over capacity
  P7-P8: Available
  
Thursday:
  P1-P2: Grade 11A Physics Lab (makeup)
  P3-P4: Available
  P5-P6: Available
  P7-P8: Science Club Activity
  
Friday:
  P1-P2: Grade 10C Physics Lab (31 students) ⚠️ Over capacity
  P3-P4: Available
  P5-P6: Available
  P7-P8: Available
  
Saturday:
  P1-P2: Grade 12C Physics Lab (26 students)
  P3-P4: Available
  P5-P6: Available
  P7-P8: Deep cleaning

Utilization: 28/48 periods = 58%
Issues:
  ⚠️ Grade 9 sections exceed lab capacity (need to split into batches)
  ⚠️ Grade 10A exceeds capacity
  
Recommendations:
  - Split large sections into 2 batches (15-18 students each)
  - Use Physics Lab 2 for overflow
  - Increase utilization (target: 75%)
```

---

## 📊 DATABASE SCHEMA

### Room Allocation Table

```sql
CREATE TABLE room_allocation (
  allocation_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Room & Section
  room_id INT NOT NULL,
  section_id INT,
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Allocation Type
  allocation_type ENUM('HOMEROOM', 'LAB', 'SPECIAL', 'TEMPORARY') NOT NULL,
  
  -- Utilization
  periods_allocated INT,
  utilization_percentage DECIMAL(5,2),
  
  -- Capacity Match
  section_strength INT,
  room_capacity INT,
  capacity_utilization DECIMAL(5,2),
  
  -- Status
  allocation_status ENUM('DRAFT', 'APPROVED', 'ACTIVE') DEFAULT 'DRAFT',
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (room_id) REFERENCES rooms(room_id),
  FOREIGN KEY (section_id) REFERENCES sections(section_id),
  
  -- Indexes
  INDEX idx_room (room_id),
  INDEX idx_section (section_id),
  INDEX idx_academic_year (academic_year)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Capacity Validation

```javascript
FUNCTION validate_room_capacity(room, section) {
  IF section.student_count > room.capacity {
    RETURN {
      valid: FALSE,
      reason: `Section strength (${section.student_count}) exceeds room capacity (${room.capacity})`
    }
  }
  
  utilization = (section.student_count / room.capacity) * 100
  
  IF utilization < 60 {
    RETURN {
      valid: TRUE,
      warning: `Low utilization: ${utilization}% (consider smaller room)`
    }
  }
  
  RETURN {valid: TRUE, utilization: utilization}
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations
1. **Master Timetable**: Provide room assignments
2. **Facilities Management**: Room maintenance schedules
3. **Student Management**: Section strength data

### Inbound Integrations
1. **Facilities Module**: Room availability, capacity
2. **Maintenance Module**: Repair schedules
3. **Events Module**: Special room bookings

---

## 👥 USER WORKFLOWS

### Workflow: Allocate Rooms for New Academic Year

**Actor:** Facilities Coordinator  
**Duration:** 1-2 hours  
**Frequency:** Once per year

**Steps:**
1. Login to Timetable Portal
2. Navigate to "Room Allocation"
3. Select Academic Year: 2026-27
4. Review room inventory (75 rooms)
5. Review section list (50 sections)
6. Click "Auto-Allocate Rooms"
7. System matches rooms to sections
8. Review allocation report
9. Adjust 3-5 allocations manually
10. Approve allocation
11. Publish to coordinators

**Expected Outcome:** All sections assigned appropriate rooms with optimal utilization.

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 1.0  
**Documentation Standard:** Enterprise Grade

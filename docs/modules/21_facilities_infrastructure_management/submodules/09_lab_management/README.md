# LAB MANAGEMENT SYSTEM

**Module:** Facilities & Infrastructure Management  
**Submodule Code:** FAC-LAB-009  
**Category:** Academic Infrastructure  
**Priority:** High (P1)  
**Owner:** Facilities & Science Department

---

## 📋 OVERVIEW

The Lab Management System submodule provides end-to-end management of all laboratory facilities in the institution — including science labs (Physics, Chemistry, Biology), computer labs, language labs, robotics labs, and maker spaces. It handles lab booking and scheduling, equipment inventory and maintenance, experiment management, safety compliance, chemical inventory with MSDS tracking, and student experiment records linked to academic assessments.

### Purpose

Ensure safe, efficient, and well-equipped laboratory operations by digitizing lab booking, tracking equipment and consumables in real-time, enforcing safety protocols, scheduling preventive maintenance, and maintaining comprehensive student experiment logs for academic evaluation and accreditation purposes.

### Scope

- **Lab Booking & Scheduling**: Calendar-based slot booking for classes and independent use
- **Equipment Inventory**: Complete lab equipment register with condition tracking
- **Chemical Management**: Chemical inventory, MSDS records, usage tracking, expiry alerts
- **Experiment Management**: Experiment catalog, procedure documents, student observation logs
- **Safety Compliance**: Safety audit checklists, incident reporting, equipment certification
- **Maintenance**: Preventive maintenance scheduling, breakdown tracking, calibration records
- **Integration**: Links to Timetable, Academic Curriculum, Procurement, and Assessment modules

---

## 🎯 KEY FEATURES

### 9.1 Lab Inventory & Classification

**Lab Types:**
```
╔══════════════════════════════════════════════════════════════╗
║                  LABORATORY INVENTORY                        ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║ SCIENCE LABS:                                                ║
║  1. Physics Lab (2 rooms) - Capacity: 40 students each      ║
║  2. Chemistry Lab (2 rooms) - Capacity: 30 students each    ║
║  3. Biology Lab (1 room) - Capacity: 40 students            ║
║  4. Combined Science Lab (1 room) - Grade 6-8               ║
║                                                              ║
║ TECHNOLOGY LABS:                                             ║
║  5. Computer Lab (3 rooms) - 40 PCs each                    ║
║  6. Robotics/STEM Lab (1 room) - 20 workstations            ║
║  7. Maker Space (1 room) - 3D printers, laser cutters       ║
║                                                              ║
║ SPECIALIZED LABS:                                            ║
║  8. Language Lab (1 room) - 30 audio stations                ║
║  9. Geography/GIS Lab (1 room) - 25 workstations            ║
║  10. Home Science Lab (1 room) - 20 workstations            ║
║                                                              ║
║ Total Labs: 13 | Total Capacity: 435 students               ║
╚══════════════════════════════════════════════════════════════╝
```

### 9.2 Lab Booking & Scheduling

**Booking Workflow:**
```
Step 1: Teacher logs in → Lab Booking Portal
Step 2: Selects lab type: "Chemistry Lab 1"
Step 3: Selects date: March 15, 2025
Step 4: Selects time slot: Period 5 (11:30 AM - 12:15 PM)
Step 5: System checks:
        ✅ Lab available (not booked)
        ✅ Equipment ready (last maintenance: Feb 28)
        ✅ Required chemicals in stock (HCl, NaOH, phenolphthalein)
        ⚠️ Alert: HCl stock low (500 mL remaining, needs 200 mL)
Step 6: Enters experiment: "Acid-Base Titration" (Grade 11 Chemistry)
Step 7: System auto-assigns: Lab assistant (Mr. Ramesh)
Step 8: Booking confirmed → Calendar updated
Step 9: Pre-lab checklist generated for lab assistant:
        □ Set up 30 titration apparatus
        □ Prepare 0.1M NaOH solution (3L)
        □ Prepare 0.1M HCl solution (3L)
        □ Place phenolphthalein indicator at each station
        □ Ensure safety goggles at each station
        □ Check first aid kit
        □ Ensure eye wash station functional
```

**Timetable Integration:**
```
Automatic Lab Slot Allocation:
  Grade 11 Science (CBSE):
    Physics Practical: 2 periods/week (Tuesday P5-P6)
    Chemistry Practical: 2 periods/week (Thursday P5-P6)
    Biology Practical: 2 periods/week (Friday P5-P6)
    Computer Science: 2 periods/week (Wednesday P5-P6)
  
  Grade 9-10 Science:
    Combined Science Lab: 2 periods/week (rotating)
  
  System prevents:
    - Double-booking of same lab
    - More than capacity students in a slot
    - Lab booking during maintenance window
    - Booking without required chemicals in stock
```

### 9.3 Equipment Tracking

**Equipment Register:**
```
Equipment: Compound Microscope (Olympus CX23)
  Asset ID: LAB-BIO-MIC-001
  Location: Biology Lab, Station 12
  Purchase Date: 15-Jul-2022
  Purchase Price: ₹45,000
  Vendor: Olympus India Pvt. Ltd.
  Warranty: Until 14-Jul-2025
  
  Condition: GOOD
  Last Maintenance: 10-Jan-2025
  Next Maintenance Due: 10-Jul-2025
  Calibration Status: CALIBRATED (Jan 2025)
  
  Usage Log:
    Jan 2025: 28 sessions (Grade 9, 10, 11)
    Feb 2025: 22 sessions
    Mar 2025: 18 sessions (so far)
    
  Issues:
    #1: Eyepiece lens scratched (reported 05-Feb-2025) → Replaced 12-Feb-2025
    #2: Focus knob stiff (reported 20-Mar-2025) → PENDING

Equipment: Digital Balance (Shimadzu ATX224)
  Asset ID: LAB-CHEM-BAL-003
  Location: Chemistry Lab 1, Station 5
  Purchase Price: ₹1,85,000
  Calibration: Monthly (last: 01-Mar-2025)
  Accuracy: ±0.0001g
  Status: OPERATIONAL
```

### 9.4 Chemical Inventory & Safety

**Chemical Management:**
```
Chemical: Hydrochloric Acid (HCl)
  CAS Number: 7647-01-0
  Location: Chemistry Lab 1, Safety Cabinet A
  Current Stock: 2.5 Liters (Concentration: 37%)
  Minimum Stock Level: 1 Liter
  Reorder Point: 1.5 Liters
  
  MSDS Reference: MSDS-HCL-001
  Hazard Class: Corrosive (GHS05)
  Storage: Acid cabinet, temperature 15-25°C
  PPE Required: Goggles, gloves, lab coat, fume hood
  
  Usage This Month:
    05-Mar: 200 mL (Grade 11 Titration, Mrs. Gupta)
    12-Mar: 150 mL (Grade 12 Salt Analysis, Mr. Kumar)
    15-Mar: 200 mL (scheduled, Grade 11 Titration)
  
  Expiry Date: 30-Sep-2025
  Status: ⚠️ APPROACHING REORDER POINT
  
  Auto-Alert: "HCl stock at 2.5L. Reorder point: 1.5L. 
  Estimated depletion: April 2025. Place order by March 25."
```

**Safety Compliance Checklist:**
```
╔══════════════════════════════════════════════════════════════╗
║             DAILY LAB SAFETY CHECKLIST                        ║
║        Chemistry Lab 1 | Date: 15-Mar-2025                   ║
║        Checked by: Mr. Ramesh (Lab Assistant)                ║
╠══════════════════════════════════════════════════════════════╣
║ [✓] Fire extinguisher accessible & charged                   ║
║ [✓] Eye wash station functional                              ║
║ [✓] First aid kit stocked (burn gel, bandages)               ║
║ [✓] Fume hood operational (airflow: 0.5 m/s)                ║
║ [✓] Gas supply valves closed (overnight)                     ║
║ [✓] Chemical cabinets locked                                 ║
║ [✓] Emergency exit clear and marked                          ║
║ [✓] Safety goggles: 35 available (need 30)                   ║
║ [✓] Lab coats: 32 available (need 30)                        ║
║ [✓] Waste disposal bins labeled and not full                 ║
║ [✗] Broken glass disposal bin replaced ⚠️                    ║
║     Action: Requested new bin from procurement               ║
╚══════════════════════════════════════════════════════════════╝
```

### 9.5 Experiment Management

**Experiment Catalog:**
```
Experiment: Acid-Base Titration (CBSE Grade 11 Chemistry)

  Experiment Code: CHEM-11-EXP-005
  CBSE Practical No.: Experiment 5 (Chemistry Practical Syllabus)
  Duration: 45 minutes
  Difficulty: Moderate
  
  Objectives:
  1. Determine the strength of a given acid by titrating against standard NaOH
  2. Learn the use of burette, pipette, and indicators
  3. Calculate molarity from titration data
  
  Apparatus Required (per student):
  - Burette (50 mL) × 1
  - Pipette (20 mL) × 1
  - Conical flask (250 mL) × 3
  - Burette stand × 1
  - Wash bottle × 1
  
  Chemicals Required (per student):
  - 0.1M NaOH: 100 mL
  - Unknown HCl solution: 50 mL
  - Phenolphthalein indicator: 5 mL
  
  Total for 30 students:
  - NaOH: 3,000 mL (3L)
  - HCl: 1,500 mL (1.5L)
  - Phenolphthalein: 150 mL
  
  Safety Notes:
  - Wear goggles and gloves at all times
  - Do not pipette by mouth
  - Report any spills immediately
  - Neutralize acid/base spills with sodium bicarbonate/dilute acetic acid
```

**Student Experiment Record:**
```
Student: Priya Sharma (Grade 11A, Roll No. 15)
Experiment: Acid-Base Titration
Date: 15-Mar-2025

Observations:
  | Trial | Initial Reading (mL) | Final Reading (mL) | Volume of NaOH (mL) |
  |-------|---------------------|--------------------|--------------------|
  | 1     | 0.0                 | 20.5               | 20.5 (rough)       |
  | 2     | 0.5                 | 20.3               | 19.8               |
  | 3     | 0.0                 | 19.9               | 19.9               |

Concordant Readings: Trial 2 & 3 (19.8 mL, 19.9 mL)
Mean Volume: 19.85 mL

Calculation:
  N1 × V1 = N2 × V2
  0.1 × 19.85 = N2 × 20
  N2 = 0.0993 M
  
  Strength of HCl = 0.0993 M ≈ 0.1 M

Result: The strength of given HCl solution is 0.0993 M
Teacher Grade: A (Accurate result, neat observation, correct calculation)
Marks: 8/10

Teacher Feedback: "Excellent technique. Concordant readings within 0.1 mL. 
Remember to record temperature of solution for accuracy."
```

---

## 📊 DATA FIELDS

### Lab Record

```sql
CREATE TABLE fac_lab_management (
    lab_id INT PRIMARY KEY AUTO_INCREMENT,
    lab_name VARCHAR(100) NOT NULL,
    lab_type ENUM('PHYSICS', 'CHEMISTRY', 'BIOLOGY', 'COMPUTER', 'ROBOTICS', 'LANGUAGE', 'MAKER_SPACE', 'OTHER'),
    building VARCHAR(50),
    room_number VARCHAR(20),
    floor INT,
    capacity INT,
    
    -- Equipment
    total_equipment INT DEFAULT 0,
    operational_equipment INT DEFAULT 0,
    under_maintenance INT DEFAULT 0,
    
    -- Status
    status ENUM('OPERATIONAL', 'UNDER_MAINTENANCE', 'CLOSED', 'RENOVATION'),
    last_safety_audit DATE,
    next_safety_audit DATE,
    safety_score DECIMAL(5,2),
    
    -- Booking
    booking_enabled BOOLEAN DEFAULT TRUE,
    max_daily_slots INT DEFAULT 8,
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME ON UPDATE CURRENT_TIMESTAMP
);

CREATE TABLE fac_lab_chemical_inventory (
    chemical_id INT PRIMARY KEY AUTO_INCREMENT,
    chemical_name VARCHAR(100) NOT NULL,
    cas_number VARCHAR(20),
    lab_id INT NOT NULL,
    
    -- Stock
    current_stock_ml DECIMAL(10,2),
    minimum_stock_ml DECIMAL(10,2),
    reorder_point_ml DECIMAL(10,2),
    unit ENUM('ML', 'L', 'G', 'KG'),
    concentration VARCHAR(20),
    
    -- Safety
    hazard_class ENUM('FLAMMABLE', 'CORROSIVE', 'TOXIC', 'OXIDIZER', 'EXPLOSIVE', 'IRRITANT', 'NON_HAZARDOUS'),
    msds_reference VARCHAR(50),
    storage_requirement VARCHAR(200),
    ppe_required VARCHAR(200),
    
    -- Lifecycle
    purchase_date DATE,
    expiry_date DATE,
    vendor VARCHAR(100),
    batch_number VARCHAR(50),
    
    status ENUM('IN_STOCK', 'LOW_STOCK', 'OUT_OF_STOCK', 'EXPIRED', 'DISPOSED'),
    
    FOREIGN KEY (lab_id) REFERENCES fac_lab_management(lab_id)
);

CREATE TABLE fac_lab_experiment_log (
    log_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    experiment_code VARCHAR(30) NOT NULL,
    lab_id INT NOT NULL,
    
    experiment_date DATE,
    teacher_id INT,
    
    -- Observation Data
    observations JSON,
    calculations TEXT,
    result TEXT,
    
    -- Assessment
    marks_obtained DECIMAL(5,2),
    max_marks DECIMAL(5,2),
    grade CHAR(2),
    teacher_feedback TEXT,
    
    -- Safety
    safety_incident BOOLEAN DEFAULT FALSE,
    incident_description TEXT,
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (lab_id) REFERENCES fac_lab_management(lab_id)
);
```

---

## 📋 BUSINESS RULES

### Rule 1: Chemical Reorder Alert
```
FUNCTION check_chemical_stock():
  FOR EACH chemical IN lab_chemicals:
    IF chemical.current_stock <= chemical.reorder_point:
      ALERT lab_incharge: "Reorder {chemical.name}. Stock: {chemical.current_stock}mL"
      CREATE_PURCHASE_REQUEST(chemical, procurement_module)
    END IF
    
    IF chemical.expiry_date <= TODAY + 30_DAYS:
      ALERT lab_incharge: "{chemical.name} expiring on {chemical.expiry_date}. Dispose safely."
    END IF
  END FOR
END FUNCTION
```

### Rule 2: Safety Compliance Enforcement
```
FUNCTION verify_lab_safety(lab_id, booking):
  lab = GET_LAB(lab_id)
  
  IF lab.last_safety_audit < TODAY - 90_DAYS:
    BLOCK_BOOKING: "Safety audit overdue. Lab cannot be used until audit completed."
  END IF
  
  IF lab.safety_score < 70:
    BLOCK_BOOKING: "Safety score below minimum (70%). Issues must be resolved."
  END IF
  
  IF booking.experiment.hazard_level == "HIGH":
    REQUIRE: lab_assistant_present = TRUE
    REQUIRE: teacher_present = TRUE
    VERIFY: all_ppe_available(booking.student_count)
  END IF
END FUNCTION
```

---

## 🔄 INTEGRATION POINTS

| Connected Module | Data Exchanged | Direction |
|---|---|---|
| Timetable & Scheduling (03) | Lab slot allocation, class schedule integration | Inbound |
| Academic Curriculum (02) | Experiment syllabus mapping, practical curriculum | Inbound |
| Assessment & Exams (05) | Practical exam marks, experiment grades | Outbound |
| Procurement (17) | Chemical/equipment purchase requests, vendor orders | Outbound |
| Inventory & Asset (16) | Lab equipment asset register, depreciation | Both |
| Incident & Crisis (20) | Lab safety incidents, chemical spills | Outbound |
| Health & Wellness (09) | Lab injury reports, first aid records | Outbound |

---

## ⚠️ EDGE CASES

### Edge Case 1: Chemical Spill During Experiment
- **Scenario:** Grade 11 student accidentally spills concentrated HCl on the lab bench during titration.
- **Resolution:** 1) Lab assistant activates spill protocol, 2) Students evacuated from immediate area, 3) Spill neutralized with sodium bicarbonate, 4) Incident logged in system with photos, 5) Auto-notification to Health & Wellness module, 6) Safety incident triggers lab safety review.

### Edge Case 2: Equipment Breakdown During Board Practical Exam
- **Scenario:** 3 microscopes malfunction during CBSE Biology practical examination.
- **Resolution:** System alerts lab incharge immediately. Spare equipment pool activated (system tracks 10% buffer inventory). If insufficient spares, exam controller notified for time extension. Post-exam: breakdown logged, warranty claim initiated if applicable.

### Edge Case 3: Hazardous Chemical Ordered Without Authorization
- **Scenario:** Teacher requests concentrated Sulfuric Acid (H2SO4) without proper authorization.
- **Resolution:** System requires HOD approval for Hazard Class "CORROSIVE" or higher. Purchase request auto-routed to Safety Officer for sign-off. Chemical received only after MSDS is uploaded and storage location verified.

---

## ⚙️ CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `safety_audit_frequency_days` | 90 | Days between mandatory safety audits |
| `minimum_safety_score` | 70 | Minimum safety score to allow lab booking |
| `chemical_reorder_auto_pr` | `true` | Auto-create purchase request on low stock |
| `experiment_log_mandatory` | `true` | Students must log observations digitally |
| `ppe_verification_required` | `true` | Verify PPE before high-hazard experiments |
| `max_students_per_workstation` | 2 | Maximum students sharing one workstation |
| `buffer_equipment_percentage` | 10 | Percentage of spare equipment to maintain |
| `chemical_expiry_alert_days` | 30 | Days before expiry to send alerts |
| `lab_assistant_required_hazard` | `HIGH` | Hazard level requiring lab assistant presence |

---

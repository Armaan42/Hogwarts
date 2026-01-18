# PRACTICAL/LAB CURRICULUM MANAGEMENT - COMPLETE DOCUMENTATION

**Module:** Academic Curriculum  
**Submodule Code:** CURR-LAB-012  
**Category:** Practical Education  
**Priority:** Critical (P0)  
**Owner:** Lab Coordinator & Subject Teachers

---

## 📋 OVERVIEW

The Practical/Lab Curriculum Management submodule manages laboratory curriculum, mandatory experiments, lab equipment requirements, safety protocols, practical assessment criteria, lab manual design, and ensures hands-on learning aligned with theoretical curriculum.

### Purpose

Design lab curriculum, manage mandatory experiments list, define equipment requirements, implement safety protocols, create practical assessment rubrics, distribute lab manuals, conduct practical exams, and ensure lab-based learning outcomes.

### Scope

- **Mandatory Experiments**: Subject-wise, grade-wise experiment lists
- **Lab Manuals**: Experiment procedures and guidelines
- **Equipment Management**: Apparatus and chemical requirements
- **Safety Protocols**: Lab safety rules and procedures
- **Practical Assessment**: Rubrics and evaluation criteria
- **Virtual Labs**: Digital lab simulations

---

## 🎯 KEY FEATURES

### 1. Mandatory Experiments List

#### 1.1 CBSE Grade 9 Science - Mandatory Experiments

**Physics Experiments (10):**
```yaml
Experiment 1: Verify laws of reflection of light
  Apparatus: Plane mirror, protractor, pins, drawing board
  Duration: 1 period (40 minutes)
  Learning Outcome: Understand reflection principles
  Safety: Handle glass mirror carefully
  
Experiment 2: Determine density of solid (denser than water)
  Apparatus: Spring balance, measuring cylinder, water, solid object
  Duration: 1 period
  Learning Outcome: Apply density formula
  
[... 8 more physics experiments]

Chemistry Experiments (5):
Experiment 1: Identify acidic/basic nature using indicators
  Chemicals: HCl, NaOH, litmus paper, phenolphthalein
  Duration: 1 period
  Safety: Wear gloves, handle acids/bases carefully
  
[... 4 more chemistry experiments]

Biology Experiments (5):
Experiment 1: Study plant and animal cells under microscope
  Apparatus: Microscope, slides, onion peel, cheek cells
  Duration: 1 period
  Learning Outcome: Identify cell structures
  
[... 4 more biology experiments]

Total: 20 mandatory experiments
Lab Periods Required: 20 periods (2 per week × 10 weeks)
```

---

### 2. Lab Manual Design

#### 2.1 Experiment Format

**Sample Lab Manual Entry:**
```yaml
Experiment No: 5
Title: Verify Ohm's Law

Aim: To verify Ohm's Law by studying the relationship between voltage and current

Theory:
  Ohm's Law states that the current flowing through a conductor is directly 
  proportional to the voltage across it, provided temperature remains constant.
  V = IR, where V = Voltage, I = Current, R = Resistance

Apparatus Required:
  - Resistor (known resistance)
  - Ammeter (0-1A range)
  - Voltmeter (0-5V range)
  - Battery (1.5V cells)
  - Rheostat
  - Connecting wires
  - Plug key

Circuit Diagram:
  [Diagram showing circuit with battery, rheostat, ammeter, resistor, voltmeter]

Procedure:
  1. Connect circuit as per diagram
  2. Note ammeter and voltmeter readings with key open (should be zero)
  3. Close key, adjust rheostat to get minimum current
  4. Note ammeter reading (I) and voltmeter reading (V)
  5. Increase current using rheostat, take 5-6 readings
  6. Calculate V/I ratio for each reading
  7. Plot V-I graph

Observations:
  Table:
  | S.No | Voltage (V) | Current (A) | V/I (Ω) |
  |------|-------------|-------------|---------|
  | 1    | 0.5         | 0.1         | 5.0     |
  | 2    | 1.0         | 0.2         | 5.0     |
  | 3    | 1.5         | 0.3         | 5.0     |
  | 4    | 2.0         | 0.4         | 5.0     |
  | 5    | 2.5         | 0.5         | 5.0     |

Calculations:
  Average resistance = (5.0 + 5.0 + 5.0 + 5.0 + 5.0) / 5 = 5.0 Ω

Graph:
  Plot V (Y-axis) vs I (X-axis)
  Graph should be a straight line passing through origin
  Slope = Resistance = 5.0 Ω

Result:
  Ohm's Law is verified. The V-I graph is a straight line, confirming V ∝ I.
  Resistance of given resistor = 5.0 Ω

Precautions:
  1. Connections should be tight
  2. Ammeter should be connected in series
  3. Voltmeter should be connected in parallel
  4. Key should be closed only while taking readings
  5. Rheostat should be at maximum resistance initially

Sources of Error:
  1. Loose connections
  2. Heating of resistor
  3. Parallax error in reading meters
  4. Instrument errors

Viva Questions:
  1. State Ohm's Law
  2. What is the SI unit of resistance?
  3. Why should ammeter be connected in series?
  4. What happens if voltmeter is connected in series?
  5. Define resistance
```

---

### 3. Equipment & Chemical Requirements

#### 3.1 Lab Inventory Management

**Grade 9 Science Lab Requirements:**
```yaml
Physics Lab Equipment:
  Apparatus:
    - Plane mirrors: 30 pieces
    - Spring balances (0-500g): 15 pieces
    - Measuring cylinders (100ml): 30 pieces
    - Ammeters (0-1A): 15 pieces
    - Voltmeters (0-5V): 15 pieces
    - Rheostats: 15 pieces
    - Batteries (1.5V): 50 cells
    - Connecting wires: 100 pieces
    
  Cost Estimate: ₹50,000

Chemistry Lab Equipment:
  Apparatus:
    - Beakers (100ml, 250ml): 50 each
    - Test tubes: 200 pieces
    - Test tube stands: 30 pieces
    - Burners: 15 pieces
    - Droppers: 100 pieces
    
  Chemicals:
    - HCl (dilute): 500ml
    - NaOH (dilute): 500ml
    - Litmus paper: 10 books
    - Phenolphthalein: 100ml
    - Methyl orange: 100ml
    
  Safety Equipment:
    - Lab coats: 40 pieces
    - Safety goggles: 40 pieces
    - First aid kit: 2 kits
    - Fire extinguisher: 2 units
    
  Cost Estimate: ₹75,000

Biology Lab Equipment:
  Apparatus:
    - Microscopes: 20 pieces
    - Slides & cover slips: 500 each
    - Petri dishes: 50 pieces
    - Dissection kits: 20 sets
    
  Specimens:
    - Onion bulbs: Fresh supply
    - Prepared slides: 100 pieces
    
  Cost Estimate: ₹1,50,000

Total Lab Setup Cost: ₹2,75,000
Annual Maintenance: ₹50,000
```

---

### 4. Lab Safety Protocols

#### 4.1 Safety Rules

**General Lab Safety:**
```yaml
Before Entering Lab:
  1. Wear lab coat and safety goggles
  2. Tie long hair
  3. Remove jewelry
  4. Wash hands

During Experiment:
  1. Follow instructions carefully
  2. Never taste chemicals
  3. Handle glassware carefully
  4. Report breakage immediately
  5. Keep workspace clean
  6. No eating/drinking in lab
  7. Use equipment properly
  8. Dispose waste correctly

Chemical Safety:
  1. Read labels before use
  2. Use minimum quantities
  3. Never mix chemicals randomly
  4. Handle acids/bases with care
  5. Use fume hood for volatile chemicals
  6. Wear gloves when required

Electrical Safety:
  1. Check connections before switching on
  2. Keep hands dry
  3. Don't overload circuits
  4. Report damaged equipment
  5. Switch off after use

Emergency Procedures:
  Fire:
    - Alert teacher immediately
    - Use fire extinguisher
    - Evacuate if necessary
    
  Chemical Spill:
    - Alert teacher
    - Neutralize if possible
    - Clean up properly
    
  Injury:
    - Report immediately
    - Apply first aid
    - Seek medical help if needed
```

---

### 5. Practical Assessment

#### 5.1 Practical Exam Format

**CBSE Grade 10 Science Practical Exam:**
```yaml
Total Marks: 20
Duration: 2 hours

Distribution:
  Experiment: 10 marks
    - Aim & Theory: 1 mark
    - Procedure: 2 marks
    - Observations: 3 marks
    - Calculations: 2 marks
    - Result: 1 mark
    - Precautions: 1 mark
    
  Viva Voce: 5 marks
    - 5 questions × 1 mark each
    - Based on experiment performed
    - General lab safety questions
    
  Practical File: 5 marks
    - All experiments completed: 2 marks
    - Neatness & presentation: 1 mark
    - Diagrams & graphs: 1 mark
    - Regularity: 1 mark

Assessment Rubric:
  Experiment Performance:
    Excellent (9-10): All steps correct, accurate results
    Good (7-8): Minor errors, mostly correct
    Satisfactory (5-6): Some errors, basic understanding
    Needs Improvement (<5): Major errors, poor understanding
    
  Viva Performance:
    Excellent (5): All questions answered correctly
    Good (4): 4 questions correct
    Satisfactory (3): 3 questions correct
    Needs Improvement (<3): Less than 3 correct
```

---

## 📊 DATABASE SCHEMA

### Lab Experiments Table

```sql
CREATE TABLE lab_experiments (
  experiment_id INT PRIMARY KEY AUTO_INCREMENT,
  subject_id INT NOT NULL,
  grade VARCHAR(10) NOT NULL,
  
  -- Experiment Details
  experiment_number INT NOT NULL,
  experiment_title VARCHAR(300) NOT NULL,
  aim TEXT NOT NULL,
  theory TEXT,
  
  -- Requirements
  apparatus_required TEXT,
  chemicals_required TEXT,
  
  -- Procedure
  procedure TEXT NOT NULL,
  circuit_diagram_path VARCHAR(500),
  
  -- Assessment
  marks INT DEFAULT 10,
  duration_minutes INT DEFAULT 40,
  
  -- Safety
  safety_precautions TEXT,
  
  -- Learning Outcome
  learning_outcome TEXT,
  
  -- Status
  is_mandatory BOOLEAN DEFAULT TRUE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (subject_id) REFERENCES subjects(subject_id),
  
  -- Indexes
  INDEX idx_subject_grade (subject_id, grade),
  INDEX idx_mandatory (is_mandatory)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Lab Capacity Check

```javascript
FUNCTION check_lab_capacity(lab_id, date, period) {
  lab = GET_LAB(lab_id)
  
  // Check if lab is already booked
  existing_booking = QUERY("
    SELECT * FROM lab_bookings
    WHERE lab_id = ?
    AND booking_date = ?
    AND period = ?
  ", [lab_id, date, period])
  
  IF existing_booking {
    RETURN {
      available: FALSE,
      message: "Lab already booked for this period"
    }
  }
  
  // Check lab capacity
  students_count = GET_CLASS_STRENGTH(class_id)
  
  IF students_count > lab.capacity {
    RETURN {
      available: FALSE,
      message: `Lab capacity (${lab.capacity}) insufficient for class (${students_count})`
    }
  }
  
  RETURN {available: TRUE}
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Timetable Module**: Lab period allocation
2. **Inventory Module**: Equipment tracking
3. **Assessment Module**: Practical exams

### Inbound Integrations

1. **Curriculum Design**: Subject requirements
2. **Subject Management**: Lab subjects

---

## 👥 USER WORKFLOWS

### Workflow: Conduct Practical Exam

**Actor:** Lab Teacher  
**Duration:** 2 hours per batch

**Steps:**

1. Prepare lab with required apparatus
2. Students enter lab
3. Distribute experiment slips
4. Students perform experiments
5. Teacher observes and evaluates
6. Conduct viva voce
7. Collect practical files
8. Mark and record scores

**Expected Outcome:** Practical exam completed, marks recorded

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

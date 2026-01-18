# MASTER TIMETABLE GENERATION - COMPLETE DOCUMENTATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-MASTER-001  
**Category:** Core Operations  
**Priority:** Critical (P0)  
**Owner:** Academic Operations Team

---

## 📋 OVERVIEW

The Master Timetable Generation submodule is the foundational engine for creating optimized, conflict-free timetables for the entire school. It employs advanced algorithms including genetic algorithms, constraint satisfaction, and AI-based optimization to generate balanced schedules that maximize resource utilization while respecting all hard and soft constraints.

### Purpose

Automate the complex process of timetable generation for all grades and sections, ensure zero conflicts in teacher/room/student assignments, optimize for educational effectiveness (heavy subjects in morning), balance teacher workload fairly, maximize facility utilization, and provide flexibility for manual adjustments.

### Scope

- **Automated Generation**: AI-powered timetable creation
- **Constraint Management**: Hard and soft constraint handling
- **Optimization Algorithms**: Genetic algorithm, simulated annealing
- **Conflict Resolution**: Automatic conflict detection and resolution
- **Resource Allocation**: Teacher, room, and period assignment
- **Quality Metrics**: Timetable quality scoring
- **Manual Override**: Coordinator adjustments
- **Template Management**: Reusable timetable templates
- **Multi-Year Support**: Generate for multiple academic years

---

## 🎯 KEY FEATURES

### 1. Timetable Structure & Configuration

#### 1.1 School Day Structure

**Standard School Day Configuration:**
```yaml
School Timings: 8:00 AM - 3:30 PM (7.5 hours)

Period Structure (Grades 6-12):
  Assembly (Monday only): 8:00-8:15 AM (15 min)
  
  Period 1: 8:15-9:00 AM (45 min)
  Period 2: 9:00-9:45 AM (45 min)
  Period 3: 9:45-10:30 AM (45 min)
  
  Short Break: 10:30-10:45 AM (15 min)
  
  Period 4: 10:45-11:30 AM (45 min)
  Period 5: 11:30-12:15 PM (45 min)
  
  Lunch Break: 12:15-12:45 PM (30 min)
  
  Period 6: 12:45-1:30 PM (45 min)
  Period 7: 1:30-2:15 PM (45 min)
  Period 8: 2:15-3:00 PM (45 min)
  Period 9: 3:00-3:30 PM (30 min) [Optional/Activity]

Weekly Structure:
  Working Days: 6 (Monday-Saturday)
  Periods per Day: 8-9
  Total Periods per Week: 48-54
  
  Saturday: Half day (6 periods, ends at 1:00 PM)

Period Duration Variations:
  Regular Period: 45 minutes
  Lab Period (Double): 90 minutes (2 consecutive periods)
  Activity Period: 30-45 minutes
  Assembly: 15 minutes

Break Timing (Staggered by Grade):
  Primary (1-5): 10:00-10:20 AM, Lunch 12:00-12:40 PM
  Middle (6-8): 10:20-10:40 AM, Lunch 12:40-1:20 PM
  Senior (9-12): 10:40-11:00 AM, Lunch 1:20-2:00 PM
```

#### 1.2 Subject Allocation by Grade

**Grade-wise Period Distribution:**
```yaml
Grade 6 - Weekly Period Allocation:
  Subject                Periods/Week    Period Type
  English                6               Theory
  Hindi                  5               Theory
  Mathematics            6               Theory
  Science                6               Theory (4) + Lab (2)
  Social Studies         5               Theory
  Computer Science       2               Lab
  Physical Education     2               Activity
  Art/Music              2               Activity
  Library                1               Study
  Moral Science          1               Theory
  Value Education        1               Theory
  ─────────────────────────────────────────────────
  Total:                 37 periods
  Remaining:             11 periods (study halls, extra classes)

Grade 9 - Weekly Period Allocation:
  Subject                Periods/Week    Period Type
  English                5               Theory
  Hindi                  4               Theory
  Mathematics            6               Theory
  Science:
    - Physics            2               Theory + 1 Lab
    - Chemistry          2               Theory + 1 Lab
    - Biology            2               Theory + 1 Lab
  Social Studies         5               Theory
  Computer Science       2               Lab
  Physical Education     2               Activity
  Art/Music              1               Activity
  Library                1               Study
  ─────────────────────────────────────────────────
  Total:                 36 periods
  Remaining:             12 periods

Grade 11 (Science Stream):
  Subject                Periods/Week    Period Type
  English                4               Theory
  Physics                5               Theory (3) + Lab (2)
  Chemistry              5               Theory (3) + Lab (2)
  Mathematics            6               Theory
  Biology/Computer       5               Theory (3) + Lab (2)
  Physical Education     2               Activity
  ─────────────────────────────────────────────────
  Total:                 27 periods
  Remaining:             21 periods (study, electives)

Grade 12 (Commerce Stream):
  Subject                Periods/Week    Period Type
  English                4               Theory
  Accountancy            6               Theory
  Business Studies       6               Theory
  Economics              6               Theory
  Mathematics/Informatics 5              Theory/Lab
  Physical Education     2               Activity
  ─────────────────────────────────────────────────
  Total:                 29 periods
  Remaining:             19 periods
```

---

### 2. Constraints & Rules Engine

#### 2.1 Hard Constraints (Must be Satisfied)

**Mandatory Constraints - Zero Tolerance:**
```yaml
Hard Constraints (Cannot be Violated):

HC1: Teacher Clash Prevention
  Rule: A teacher cannot be assigned to two different classes at the same time
  Validation:
    FOR each time slot (day, period):
      teacher_assignments = GET_ASSIGNMENTS(day, period)
      FOR each teacher IN teacher_assignments:
        IF teacher.count > 1:
          RETURN CONFLICT("Teacher ${teacher.name} has ${teacher.count} classes at ${day} Period ${period}")
  
  Example Violation:
    Monday, Period 1:
      - Grade 9A Mathematics - Mrs. Verma
      - Grade 10B Mathematics - Mrs. Verma
    ERROR: Mrs. Verma cannot teach two classes simultaneously

HC2: Room Clash Prevention
  Rule: A room cannot host two different classes at the same time
  Validation:
    FOR each time slot (day, period):
      room_bookings = GET_ROOM_BOOKINGS(day, period)
      FOR each room IN room_bookings:
        IF room.count > 1:
          RETURN CONFLICT("Room ${room.number} double-booked at ${day} Period ${period}")
  
  Example Violation:
    Tuesday, Period 3:
      - Grade 8A Science - Lab 1
      - Grade 8B Science - Lab 1
    ERROR: Lab 1 cannot accommodate two classes

HC3: Student Section Clash Prevention
  Rule: A section cannot have two subjects scheduled at the same time
  Validation:
    FOR each section:
      FOR each time slot (day, period):
        subject_count = COUNT_SUBJECTS(section, day, period)
        IF subject_count > 1:
          RETURN CONFLICT("Section ${section.name} has ${subject_count} subjects at ${day} Period ${period}")
  
  Example Violation:
    Wednesday, Period 5:
      - Grade 7A English
      - Grade 7A Mathematics
    ERROR: Section 7A cannot attend two classes simultaneously

HC4: Subject Period Requirement
  Rule: Each subject must receive exactly the required number of periods per week
  Validation:
    FOR each section:
      FOR each subject IN section.subjects:
        allocated_periods = COUNT_PERIODS(section, subject)
        required_periods = subject.periods_per_week
        IF allocated_periods != required_periods:
          RETURN CONFLICT("${subject.name} for ${section.name}: ${allocated_periods}/${required_periods} periods")
  
  Example Violation:
    Grade 9A Mathematics:
      Required: 6 periods/week
      Allocated: 5 periods/week
    ERROR: Insufficient periods allocated

HC5: Teacher Workload Limit
  Rule: Teacher cannot exceed maximum periods per week
  Validation:
    FOR each teacher:
      total_periods = COUNT_TEACHER_PERIODS(teacher)
      max_capacity = teacher.max_periods_per_week
      IF total_periods > max_capacity:
        RETURN CONFLICT("${teacher.name} overloaded: ${total_periods}/${max_capacity} periods")
  
  Example Violation:
    Mrs. Sharma:
      Max Capacity: 30 periods/week
      Allocated: 32 periods/week
    ERROR: Teacher overload

HC6: Lab Period Continuity
  Rule: Lab periods must be consecutive (double period)
  Validation:
    FOR each lab_period:
      IF lab_period.duration == 90_minutes:
        next_period = lab_period.period_number + 1
        IF NOT IS_CONSECUTIVE(lab_period, next_period):
          RETURN CONFLICT("Lab period for ${lab_period.subject} not consecutive")
  
  Example Violation:
    Grade 9A Physics Lab:
      Period 3 (10:30-11:15) - Physics Lab
      Period 4 (11:15-12:00) - Mathematics (different subject)
    ERROR: Lab period interrupted

HC7: Working Hours Compliance
  Rule: All periods must fall within school working hours
  Validation:
    school_start = 8:00 AM
    school_end = 3:30 PM
    FOR each period:
      IF period.start_time < school_start OR period.end_time > school_end:
        RETURN CONFLICT("Period ${period.number} outside working hours")

HC8: Minimum Free Periods for Teachers
  Rule: Teachers must have minimum free periods for preparation
  Validation:
    FOR each teacher:
      teaching_periods = COUNT_TEACHING_PERIODS(teacher)
      total_periods = 48 (6 days × 8 periods)
      free_periods = total_periods - teaching_periods
      min_free = 6
      IF free_periods < min_free:
        RETURN CONFLICT("${teacher.name} has only ${free_periods} free periods (min: ${min_free})")
```

#### 2.2 Soft Constraints (Preferred but Flexible)

**Optimization Constraints - Improve Quality:**
```yaml
Soft Constraints (Desirable but not Mandatory):

SC1: No Consecutive Same-Subject Periods
  Rule: Avoid scheduling same subject in consecutive periods
  Weight: 20%
  Rationale: Student attention span, variety
  
  Penalty Calculation:
    FOR each section:
      FOR each day:
        consecutive_count = 0
        FOR period IN 1 to 8:
          IF subject[period] == subject[period-1]:
            consecutive_count++
            penalty += consecutive_count * 10
  
  Example:
    Grade 9A Monday:
      P1: Mathematics
      P2: Mathematics (consecutive - penalty: 10)
      P3: Mathematics (3 consecutive - penalty: 20)
    Total Penalty: 30 points

SC2: Heavy Subjects in Morning
  Rule: Schedule Math, Science in morning periods (P1-P4)
  Weight: 15%
  Rationale: Student alertness higher in morning
  
  Penalty Calculation:
    heavy_subjects = ['Mathematics', 'Science', 'Physics', 'Chemistry']
    FOR each heavy_subject_period:
      IF period_number > 4:
        penalty += (period_number - 4) * 5
  
  Example:
    Mathematics in Period 7 (afternoon):
      Penalty: (7 - 4) * 5 = 15 points

SC3: Balanced Daily Workload
  Rule: Distribute subjects evenly across days
  Weight: 15%
  Rationale: Avoid overloading specific days
  
  Penalty Calculation:
    avg_periods_per_day = total_periods / 6
    FOR each day:
      deviation = ABS(day_periods - avg_periods_per_day)
      penalty += deviation * 8
  
  Example:
    Average: 6 periods/day
    Monday: 8 periods (deviation: 2, penalty: 16)
    Tuesday: 4 periods (deviation: 2, penalty: 16)
    Total Penalty: 32 points

SC4: Teacher Preference Accommodation
  Rule: Assign teachers to preferred time slots when possible
  Weight: 10%
  Rationale: Teacher satisfaction, work-life balance
  
  Penalty Calculation:
    FOR each teacher_assignment:
      IF NOT IN teacher.preferred_slots:
        penalty += 5
  
  Example:
    Mrs. Sharma prefers morning slots (P1-P4)
    Assigned to Period 7: Penalty: 5 points

SC5: Lab Period Timing
  Rule: Schedule labs in middle periods (P3-P6)
  Weight: 10%
  Rationale: Adequate preparation time, cleanup time
  
  Penalty Calculation:
    FOR each lab_period:
      IF period_number < 3 OR period_number > 6:
        penalty += 8
  
  Example:
    Physics Lab in Period 1-2:
      Penalty: 8 points (too early)

SC6: Minimize Teacher Idle Time
  Rule: Reduce gaps between teacher's periods
  Weight: 12%
  Rationale: Efficient teacher utilization
  
  Penalty Calculation:
    FOR each teacher:
      FOR each day:
        gaps = COUNT_GAPS_BETWEEN_PERIODS(teacher, day)
        penalty += gaps * 6
  
  Example:
    Mrs. Verma Monday:
      P1: Teaching
      P2: Free (gap)
      P3: Teaching
      P4: Free (gap)
      P5: Teaching
    Gaps: 2, Penalty: 12 points

SC7: Avoid Last Period Heavy Subjects
  Rule: Don't schedule Math/Science in last period (P8)
  Weight: 8%
  Rationale: Student fatigue, reduced effectiveness
  
  Penalty Calculation:
    IF heavy_subject IN period_8:
      penalty += 10
  
  Example:
    Mathematics in Period 8:
      Penalty: 10 points

SC8: Break Distribution
  Rule: Ensure breaks are evenly distributed
  Weight: 10%
  Rationale: Student rest, energy management
  
  Penalty Calculation:
    ideal_break_position = [after_period_3, after_period_5]
    FOR each break:
      IF break.position NOT IN ideal_break_position:
        penalty += 7

Total Soft Constraint Weight: 100%
Optimization Goal: Minimize Total Penalty Score
```

---

### 3. Automated Generation Algorithms

#### 3.1 Genetic Algorithm Approach

**Evolutionary Timetable Optimization:**
```yaml
Genetic Algorithm Configuration:

Population Size: 100 timetables
Generations: 5,000 iterations
Mutation Rate: 15%
Crossover Rate: 70%
Elitism: Top 10% preserved

Algorithm Steps:

STEP 1: Initialize Population
  Generate 100 random timetables
  Each timetable satisfies hard constraints (or marked invalid)
  
  Initial Population:
    Timetable 1: Fitness Score = 450
    Timetable 2: Fitness Score = 520
    Timetable 3: Fitness Score = 380
    ...
    Timetable 100: Fitness Score = 410

STEP 2: Fitness Evaluation
  Calculate fitness score for each timetable
  
  Fitness Function:
    fitness_score = 1000 - total_penalty_score
    
    Where total_penalty_score = SUM of:
      - Hard constraint violations × 1000 (fatal)
      - Soft constraint penalties (weighted)
    
    Higher fitness = Better timetable
  
  Example:
    Timetable 1:
      Hard Constraint Violations: 0
      Soft Constraint Penalties: 450
      Fitness Score: 1000 - 450 = 550

STEP 3: Selection
  Select parents for next generation using tournament selection
  
  Tournament Selection:
    - Randomly pick 5 timetables
    - Select the 2 with highest fitness
    - These become parents
  
  Example:
    Tournament: [T1(550), T5(480), T12(620), T23(390), T45(510)]
    Selected Parents: T12(620), T1(550)

STEP 4: Crossover (Breeding)
  Combine two parent timetables to create offspring
  
  Crossover Method: Single-Point Crossover
    - Split timetables at random day (e.g., Wednesday)
    - Child 1: Mon-Wed from Parent 1, Thu-Sat from Parent 2
    - Child 2: Mon-Wed from Parent 2, Thu-Sat from Parent 1
  
  Example:
    Parent 1: [Mon_P1, Tue_P1, Wed_P1, Thu_P1, Fri_P1, Sat_P1]
    Parent 2: [Mon_P2, Tue_P2, Wed_P2, Thu_P2, Fri_P2, Sat_P2]
    
    Crossover Point: After Wednesday
    
    Child 1: [Mon_P1, Tue_P1, Wed_P1, Thu_P2, Fri_P2, Sat_P2]
    Child 2: [Mon_P2, Tue_P2, Wed_P2, Thu_P1, Fri_P1, Sat_P1]

STEP 5: Mutation
  Randomly modify offspring to introduce variation
  
  Mutation Operations:
    - Swap two periods within same day
    - Swap two periods across days
    - Change teacher for a period (if qualified)
    - Change room for a period (if available)
  
  Mutation Rate: 15%
  
  Example:
    Original: Mon P1 = Math, Mon P2 = English
    Mutated: Mon P1 = English, Mon P2 = Math

STEP 6: Validation
  Check if offspring satisfy hard constraints
  If invalid, discard and generate new offspring
  
  Validation Checks:
    ✓ No teacher clashes
    ✓ No room clashes
    ✓ No student clashes
    ✓ All subjects allocated
    ✓ Workload within limits

STEP 7: Replacement
  Replace old population with new generation
  Keep top 10% (elitism) from previous generation
  
  New Population:
    - Top 10 from previous generation (elitism)
    - 90 new offspring

STEP 8: Termination
  Stop when:
    - Generation limit reached (5,000)
    - OR fitness plateau (no improvement for 500 generations)
    - OR perfect score achieved (fitness = 1000)
  
  Best Timetable Selected:
    Generation 3,245: Fitness Score = 920
    Hard Constraint Violations: 0
    Soft Constraint Penalties: 80
    Quality: Excellent

Performance Metrics:
  Execution Time: 8-12 minutes
  Success Rate: 98%
  Average Fitness: 850-920
  Convergence: ~3,000 generations
```

#### 3.2 Constraint Satisfaction Problem (CSP) Approach

**Backtracking with Forward Checking:**
```javascript
FUNCTION generate_timetable_csp() {
  // Initialize empty timetable
  timetable = INITIALIZE_EMPTY_TIMETABLE()
  
  // Get all variables (slots to fill)
  variables = GET_ALL_SLOTS() // 6 days × 8 periods × 50 sections = 2,400 slots
  
  // Sort variables by most constrained first (MRV heuristic)
  variables = SORT_BY_CONSTRAINTS(variables)
  
  // Start backtracking
  result = BACKTRACK(timetable, variables, 0)
  
  RETURN result
}

FUNCTION backtrack(timetable, variables, index) {
  // Base case: All variables assigned
  IF index == variables.length {
    RETURN timetable
  }
  
  // Get current variable (slot)
  slot = variables[index]
  section = slot.section
  day = slot.day
  period = slot.period
  
  // Get domain (possible subjects for this slot)
  domain = GET_POSSIBLE_SUBJECTS(section, day, period)
  
  // Try each value in domain
  FOR each subject IN domain {
    // Check if assignment is consistent
    IF IS_CONSISTENT(timetable, slot, subject) {
      // Assign subject to slot
      timetable[section][day][period] = subject
      
      // Forward checking: Update domains of future variables
      saved_domains = FORWARD_CHECK(timetable, variables, index)
      
      // Recursively assign remaining variables
      result = BACKTRACK(timetable, variables, index + 1)
      
      // If successful, return
      IF result != NULL {
        RETURN result
      }
      
      // Backtrack: Undo assignment
      timetable[section][day][period] = NULL
      RESTORE_DOMAINS(saved_domains)
    }
  }
  
  // No valid assignment found
  RETURN NULL
}

FUNCTION is_consistent(timetable, slot, subject) {
  section = slot.section
  day = slot.day
  period = slot.period
  
  // Get teacher and room for this subject
  teacher = GET_TEACHER(section, subject)
  room = GET_ROOM(section, subject)
  
  // Check teacher clash
  IF TEACHER_BUSY(timetable, teacher, day, period) {
    RETURN FALSE
  }
  
  // Check room clash
  IF ROOM_OCCUPIED(timetable, room, day, period) {
    RETURN FALSE
  }
  
  // Check subject period limit
  current_periods = COUNT_SUBJECT_PERIODS(timetable, section, subject)
  required_periods = subject.periods_per_week
  IF current_periods >= required_periods {
    RETURN FALSE
  }
  
  // Check soft constraints (optional)
  penalty = CALCULATE_SOFT_PENALTY(timetable, slot, subject)
  IF penalty > THRESHOLD {
    RETURN FALSE
  }
  
  RETURN TRUE
}

Performance:
  Execution Time: 15-30 minutes (slower than GA)
  Success Rate: 100% (always finds solution if exists)
  Quality: Good (satisfies hard constraints, may not optimize soft)
  Use Case: Small schools (<500 students)
```

---

### 4. Timetable Quality Metrics

#### 4.1 Quality Scoring System

**Comprehensive Quality Assessment:**
```yaml
Timetable Quality Score (0-100):

Component 1: Hard Constraint Compliance (40 points)
  Perfect Score: 40 (zero violations)
  Deduction: -40 per violation (fatal)
  
  Checks:
    ✓ No teacher clashes (10 points)
    ✓ No room clashes (10 points)
    ✓ No student clashes (10 points)
    ✓ All subjects allocated (10 points)

Component 2: Soft Constraint Optimization (30 points)
  Scoring:
    - No consecutive same-subject: 6 points
    - Heavy subjects in morning: 5 points
    - Balanced daily workload: 5 points
    - Teacher preferences: 3 points
    - Lab timing optimization: 3 points
    - Minimal teacher idle time: 4 points
    - Avoid last period heavy: 2 points
    - Break distribution: 2 points

Component 3: Resource Utilization (15 points)
  Metrics:
    - Teacher utilization: 85-95% optimal (5 points)
    - Room utilization: 80-90% optimal (5 points)
    - Lab utilization: 70-85% optimal (5 points)

Component 4: Balance & Fairness (15 points)
  Metrics:
    - Teacher workload variance: <10% (5 points)
    - Daily period distribution: Std dev <1.5 (5 points)
    - Subject distribution: Even across week (5 points)

Quality Grades:
  90-100: Excellent (Production-ready)
  80-89: Good (Minor adjustments needed)
  70-79: Acceptable (Moderate adjustments)
  60-69: Poor (Significant rework)
  <60: Unacceptable (Regenerate)

Example Quality Report:

Timetable: 2025-26_v3
Generated: January 18, 2026, 10:45 AM
Algorithm: Genetic Algorithm (Generation 3,245)

Quality Score: 92/100 (Excellent)

Breakdown:
  Hard Constraints: 40/40 ✓
    - Teacher Clashes: 0
    - Room Clashes: 0
    - Student Clashes: 0
    - Subject Allocation: 100%
  
  Soft Constraints: 28/30
    - No Consecutive Same-Subject: 5/6 (2 violations)
    - Heavy Subjects Morning: 5/5 ✓
    - Balanced Daily Workload: 4/5 (slight imbalance)
    - Teacher Preferences: 3/3 ✓
    - Lab Timing: 3/3 ✓
    - Minimal Idle Time: 4/4 ✓
    - Last Period Heavy: 2/2 ✓
    - Break Distribution: 2/2 ✓
  
  Resource Utilization: 14/15
    - Teacher Utilization: 87% (5/5) ✓
    - Room Utilization: 84% (5/5) ✓
    - Lab Utilization: 68% (4/5) (slightly low)
  
  Balance & Fairness: 10/15
    - Workload Variance: 8% (5/5) ✓
    - Daily Distribution: Std dev 1.8 (3/5)
    - Subject Distribution: 2/5 (uneven)

Recommendations:
  ⚠️ Improve subject distribution across week
  ⚠️ Reduce 2 consecutive same-subject instances
  ✓ Overall excellent quality, ready for deployment
```

---

### 5. Manual Adjustments & Overrides

#### 5.1 Coordinator Manual Editing

**Post-Generation Refinements:**
```yaml
Manual Editing Features:

1. Period Swap:
   Action: Swap two periods within same section
   Use Case: Teacher preference, optimization
   
   Example:
     Before:
       Monday P1: Mathematics
       Monday P2: English
     
     After Swap:
       Monday P1: English
       Monday P2: Mathematics
   
   Validation:
     - Check teacher availability
     - Check room availability
     - Verify no new conflicts

2. Teacher Change:
   Action: Assign different teacher to period
   Use Case: Teacher expertise, workload balancing
   
   Example:
     Before:
       Grade 9A Math - Mrs. Sharma
     
     After:
       Grade 9A Math - Mr. Verma
   
   Validation:
     - Verify teacher qualification
     - Check workload limits
     - Ensure no clashes

3. Room Change:
   Action: Assign different room to period
   Use Case: Room maintenance, capacity adjustment
   
   Example:
     Before:
       Grade 8A Science - Lab 1
     
     After:
       Grade 8A Science - Lab 2
   
   Validation:
     - Check room availability
     - Verify room type (lab/classroom)
     - Ensure capacity adequate

4. Add/Remove Period:
   Action: Add extra period or remove existing
   Use Case: Curriculum change, event scheduling
   
   Example:
     Add: Extra Math period on Saturday
     Remove: Library period (event day)
   
   Validation:
     - Maintain subject period requirements
     - Check teacher/room availability

5. Lock Period:
   Action: Prevent period from being modified
   Use Case: Fixed commitments, special arrangements
   
   Example:
     Lock: Grade 12 Physics Lab (Tuesday P3-P4)
     Reason: Shared equipment with college
   
   Effect:
     - Period excluded from future optimizations
     - Cannot be swapped or changed

Audit Trail:
  All manual changes logged with:
    - Change type
    - Changed by (user)
    - Timestamp
    - Reason
    - Before/after state
```

---

## 📊 DATABASE SCHEMA

### Master Timetable Table

```sql
CREATE TABLE master_timetable (
  timetable_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Section & Grade
  section_id INT NOT NULL,
  grade VARCHAR(10) NOT NULL,
  
  -- Day & Period
  day_of_week ENUM('MONDAY', 'TUESDAY', 'WEDNESDAY', 'THURSDAY', 'FRIDAY', 'SATURDAY') NOT NULL,
  period_number INT NOT NULL,
  
  -- Timing
  start_time TIME NOT NULL,
  end_time TIME NOT NULL,
  duration_minutes INT NOT NULL,
  
  -- Subject Assignment
  subject_id INT NOT NULL,
  teacher_id INT NOT NULL,
  room_id INT,
  
  -- Period Type
  period_type ENUM('THEORY', 'LAB', 'PRACTICAL', 'ACTIVITY', 'LIBRARY', 'STUDY') NOT NULL,
  is_double_period BOOLEAN DEFAULT FALSE,
  
  -- Generation Info
  generation_algorithm VARCHAR(50), -- 'GENETIC_ALGORITHM', 'CSP', 'MANUAL'
  generation_timestamp TIMESTAMP,
  quality_score DECIMAL(5,2),
  
  -- Manual Overrides
  is_manually_edited BOOLEAN DEFAULT FALSE,
  edited_by INT,
  edit_timestamp TIMESTAMP,
  edit_reason TEXT,
  is_locked BOOLEAN DEFAULT FALSE,
  
  -- Status
  timetable_status ENUM('DRAFT', 'APPROVED', 'ACTIVE', 'ARCHIVED') DEFAULT 'DRAFT',
  approved_by INT,
  approval_date DATE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (section_id) REFERENCES sections(section_id),
  FOREIGN KEY (subject_id) REFERENCES subjects(subject_id),
  FOREIGN KEY (teacher_id) REFERENCES teachers(teacher_id),
  FOREIGN KEY (room_id) REFERENCES rooms(room_id),
  
  -- Indexes
  INDEX idx_section_day_period (section_id, day_of_week, period_number),
  INDEX idx_teacher_day_period (teacher_id, day_of_week, period_number),
  INDEX idx_room_day_period (room_id, day_of_week, period_number),
  INDEX idx_academic_year (academic_year),
  INDEX idx_status (timetable_status),
  
  -- Unique Constraint
  UNIQUE KEY uk_section_day_period (section_id, day_of_week, period_number, academic_year)
);
```

### Timetable Generation Log Table

```sql
CREATE TABLE timetable_generation_log (
  generation_log_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Generation Details
  academic_year VARCHAR(9) NOT NULL,
  generation_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Algorithm
  algorithm_used VARCHAR(50) NOT NULL,
  algorithm_parameters JSON,
  
  -- Performance
  execution_time_seconds INT,
  iterations_completed INT,
  
  -- Quality
  final_quality_score DECIMAL(5,2),
  hard_constraint_violations INT,
  soft_constraint_penalty INT,
  
  -- Statistics
  total_periods_generated INT,
  total_sections INT,
  total_teachers INT,
  total_rooms INT,
  
  -- Status
  generation_status ENUM('SUCCESS', 'FAILED', 'PARTIAL') NOT NULL,
  error_message TEXT,
  
  -- User
  generated_by INT NOT NULL,
  
  -- Indexes
  INDEX idx_academic_year (academic_year),
  INDEX idx_timestamp (generation_timestamp),
  INDEX idx_status (generation_status)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Timetable Generation Validation

```javascript
FUNCTION validate_generated_timetable(timetable) {
  validation_report = {
    is_valid: TRUE,
    errors: [],
    warnings: [],
    statistics: {}
  }
  
  // Check 1: All subjects allocated
  FOR each section IN sections {
    FOR each subject IN section.subjects {
      allocated_periods = COUNT_PERIODS(timetable, section, subject)
      required_periods = subject.periods_per_week
      
      IF allocated_periods < required_periods {
        validation_report.errors.ADD({
          type: 'INSUFFICIENT_PERIODS',
          section: section.name,
          subject: subject.name,
          allocated: allocated_periods,
          required: required_periods
        })
        validation_report.is_valid = FALSE
      } ELSE IF allocated_periods > required_periods {
        validation_report.errors.ADD({
          type: 'EXCESS_PERIODS',
          section: section.name,
          subject: subject.name,
          allocated: allocated_periods,
          required: required_periods
        })
        validation_report.is_valid = FALSE
      }
    }
  }
  
  // Check 2: No conflicts
  conflicts = DETECT_ALL_CONFLICTS(timetable)
  IF conflicts.count > 0 {
    validation_report.errors.ADD_ALL(conflicts)
    validation_report.is_valid = FALSE
  }
  
  // Check 3: Teacher workload
  FOR each teacher IN teachers {
    workload = COUNT_TEACHER_PERIODS(timetable, teacher)
    max_capacity = teacher.max_periods_per_week
    
    IF workload > max_capacity {
      validation_report.errors.ADD({
        type: 'TEACHER_OVERLOAD',
        teacher: teacher.name,
        workload: workload,
        max_capacity: max_capacity
      })
      validation_report.is_valid = FALSE
    } ELSE IF workload < max_capacity * 0.5 {
      validation_report.warnings.ADD({
        type: 'TEACHER_UNDERUTILIZED',
        teacher: teacher.name,
        workload: workload,
        max_capacity: max_capacity
      })
    }
  }
  
  // Calculate statistics
  validation_report.statistics = {
    total_periods: COUNT_ALL_PERIODS(timetable),
    total_conflicts: conflicts.count,
    quality_score: CALCULATE_QUALITY_SCORE(timetable),
    teacher_utilization: CALCULATE_TEACHER_UTILIZATION(timetable),
    room_utilization: CALCULATE_ROOM_UTILIZATION(timetable)
  }
  
  RETURN validation_report
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Teacher Allocation Module**: Send teacher assignments
2. **Room Allocation Module**: Send room bookings
3. **Student Timetable View**: Provide student schedules
4. **Attendance Module**: Period-wise attendance slots
5. **Substitution Module**: Teacher availability data

### Inbound Integrations

1. **Curriculum Module**: Subject period requirements
2. **Teacher Management**: Teacher availability, qualifications
3. **Room Management**: Room availability, capacity
4. **Student Management**: Section details, enrollment

---

## 👥 USER WORKFLOWS

### Workflow: Generate Master Timetable for New Academic Year

**Actor:** Timetable Coordinator  
**Duration:** 30-45 minutes  
**Frequency:** Once per year

**Steps:**

1. **Login to Timetable Portal**
   - Access admin dashboard
   - Navigate to "Timetable Generation"

2. **Select Academic Year**
   - Choose: "2026-27"
   - Confirm year details

3. **Configure Parameters**
   - School timings: 8:00 AM - 3:30 PM
   - Working days: Monday-Saturday
   - Periods per day: 8
   - Break timings: Configure staggered breaks

4. **Load Curriculum Data**
   - System imports subject period requirements
   - Grades 1-12 curriculum loaded
   - Total subjects: 150+

5. **Load Teacher Data**
   - System imports 120 teachers
   - Qualifications verified
   - Workload limits set (30 periods/week max)

6. **Load Room Data**
   - System imports 60 rooms
   - 30 classrooms, 5 labs, 25 special rooms
   - Capacity data loaded

7. **Select Generation Algorithm**
   - Choose: "Genetic Algorithm"
   - Set parameters:
     - Population: 100
     - Generations: 5,000
     - Mutation rate: 15%

8. **Start Generation**
   - Click "Generate Timetable"
   - System displays progress:
     - Generation 1,000/5,000 (20%)
     - Current best fitness: 720
     - Estimated time: 8 minutes

9. **Monitor Progress**
   - Real-time fitness graph
   - Conflict count: 0
   - Quality score: Improving

10. **Generation Complete**
    - Time taken: 10 minutes
    - Final fitness: 920
    - Quality score: 92/100 (Excellent)

11. **Review Generated Timetable**
    - View quality report
    - Check conflict report (0 conflicts)
    - Review resource utilization:
      - Teachers: 87%
      - Rooms: 84%
      - Labs: 72%

12. **Manual Adjustments** (Optional)
    - Swap 2 periods for teacher preference
    - Lock 3 periods (fixed commitments)
    - Adjust 1 room assignment

13. **Validate Final Timetable**
    - Run validation checks
    - All checks passed ✓
    - Quality score: 93/100

14. **Approve Timetable**
    - Click "Approve for 2026-27"
    - Principal approval required
    - Approval granted

15. **Publish Timetable**
    - Click "Publish"
    - System distributes to:
      - 120 teachers (email + portal)
      - 1,200 students (portal + app)
      - 1,200 parents (email + portal)
    - Notifications sent: 2,520

16. **Confirmation**
    - Success message displayed
    - Timetable activated for 2026-27
    - Backup created

**Expected Outcome:** Master timetable generated, approved, and published for new academic year.

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

# TEACHER ALLOCATION - COMPLETE DOCUMENTATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-TEACHER-002  
**Category:** Resource Allocation  
**Priority:** Critical (P0)  
**Owner:** Academic Operations Team

---

## 📋 OVERVIEW

The Teacher Allocation submodule manages the assignment of teachers to subjects, sections, and periods while ensuring workload balance, qualification matching, preference accommodation, and optimal utilization of teaching resources across the entire school.

### Purpose

Allocate teachers to appropriate subjects based on qualifications, balance workload fairly across all teachers, accommodate teacher preferences when possible, optimize free period distribution, ensure adequate preparation time, and maintain flexibility for substitutions.

### Scope

- **Qualification Matching**: Assign teachers to subjects they're qualified for
- **Workload Balancing**: Fair distribution of teaching periods
- **Preference Management**: Accommodate teacher time preferences
- **Free Period Optimization**: Strategic distribution of non-teaching periods
- **Subject Expertise Mapping**: Match teacher specialization to grade levels
- **Multi-Subject Assignment**: Handle teachers teaching multiple subjects
- **Part-Time Teacher Support**: Proportional allocation for part-time staff
- **Substitute Pool Management**: Maintain available teachers for coverage

---

## 🎯 KEY FEATURES

### 1. Teacher-Subject Qualification Matching

#### 1.1 Qualification Matrix

**Subject Expertise Mapping:**
```yaml
Teacher: Mrs. Verma
Employee ID: T-2015-045
Qualification: M.Sc. Mathematics, B.Ed.
Experience: 12 years
Specialization: Secondary Mathematics

Qualified Subjects:
  Primary Level:
    - Mathematics (Grades 1-5): ✓ Qualified
    - General Science (Grades 3-5): ✓ Qualified
    
  Middle School:
    - Mathematics (Grades 6-8): ✓✓ Highly Qualified
    - Science (Grades 6-8): ✓ Qualified
    
  Secondary:
    - Mathematics (Grades 9-10): ✓✓✓ Expert
    - Physics (Grades 9-10): ✓ Qualified
    
  Senior Secondary:
    - Mathematics (Grades 11-12): ✓✓ Highly Qualified
    - Physics (Grades 11-12): ✗ Not Qualified

Preferred Teaching Level: Grades 9-10 (Secondary)
Current Assignment: Grade 9A, 9B, 10A Mathematics

Qualification Levels:
  ✓✓✓ Expert: 10+ years experience at this level
  ✓✓ Highly Qualified: 5+ years experience
  ✓ Qualified: Meets minimum requirements
  ✗ Not Qualified: Lacks required qualifications
```

#### 1.2 Automatic Qualification Validation

**Pre-Assignment Checks:**
```javascript
FUNCTION validate_teacher_assignment(teacher, subject, grade) {
  // Check 1: Subject qualification
  qualifications = GET_TEACHER_QUALIFICATIONS(teacher)
  required_qualification = GET_SUBJECT_REQUIREMENT(subject, grade)
  
  IF NOT HAS_QUALIFICATION(qualifications, required_qualification) {
    RETURN {
      valid: FALSE,
      reason: `${teacher.name} not qualified for ${subject.name} Grade ${grade}`,
      required: required_qualification,
      has: qualifications
    }
  }
  
  // Check 2: Grade level expertise
  expertise_level = GET_EXPERTISE_LEVEL(teacher, subject, grade)
  
  IF expertise_level == 'NOT_QUALIFIED' {
    RETURN {
      valid: FALSE,
      reason: `${teacher.name} lacks expertise for Grade ${grade} ${subject.name}`
    }
  }
  
  // Check 3: Board-specific requirements (CBSE, ICSE, IB)
  board = GET_SECTION_BOARD(grade, section)
  IF board == 'IB' AND NOT teacher.ib_certified {
    RETURN {
      valid: FALSE,
      reason: `IB certification required for ${subject.name}`
    }
  }
  
  // Check 4: Special certifications (Lab safety, etc.)
  IF subject.requires_lab AND NOT teacher.lab_certified {
    RETURN {
      valid: FALSE,
      reason: `Lab safety certification required`
    }
  }
  
  RETURN {
    valid: TRUE,
    expertise_level: expertise_level,
    confidence: CALCULATE_MATCH_SCORE(teacher, subject, grade)
  }
}
```

---

### 2. Workload Balancing Algorithm

#### 2.1 Fair Distribution Strategy

**Workload Calculation:**
```yaml
School: Hogwarts International
Total Teachers: 120
Total Periods/Week: 2,400 (50 sections × 48 periods)
Average Load: 20 periods/teacher

Workload Categories:

Full-Time Teachers (100):
  Minimum: 18 periods/week
  Target: 24-28 periods/week
  Maximum: 30 periods/week
  Free Periods: Minimum 6/week
  
Part-Time Teachers (20):
  Minimum: 10 periods/week
  Target: 12-15 periods/week
  Maximum: 18 periods/week
  Free Periods: Minimum 3/week

Workload Distribution Example:

Mathematics Department (10 teachers):
  Mrs. Verma (Full-time):
    Sections: 9A, 9B, 10A, 10B
    Periods: 6 per section × 4 = 24 periods
    Free Periods: 6
    Invigilation: 2 periods
    Total: 26/30 (87% utilization)
    Status: Optimal
    
  Mr. Saxena (Full-time):
    Sections: 8A, 8B, 8C, 9C
    Periods: 6 per section × 4 = 24 periods
    Free Periods: 6
    Total: 24/30 (80% utilization)
    Status: Optimal
    
  Mrs. Gupta (Part-time, 60%):
    Sections: 7A, 7B
    Periods: 6 per section × 2 = 12 periods
    Free Periods: 3
    Total: 12/18 (67% utilization)
    Status: Optimal
    
  Mr. Singh (Full-time):
    Sections: 11A, 11B, 12A, 12B
    Periods: 6 per section × 4 = 24 periods
    Free Periods: 4 (below minimum)
    Invigilation: 4 periods
    Total: 28/30 (93% utilization)
    Status: ⚠️ High Load, Reduce invigilation

Department Statistics:
  Average Load: 24.5 periods
  Std Deviation: 2.1 periods
  Overloaded: 0 teachers
  Underutilized: 1 teacher (Mr. Patel, 18 periods)
  Balance Score: 92/100 (Excellent)
```

#### 2.2 Workload Balancing Algorithm

**Automated Load Distribution:**
```javascript
FUNCTION balance_teacher_workload(teachers, sections, subjects) {
  // Step 1: Calculate total required periods
  total_required = 0
  FOR each section IN sections {
    FOR each subject IN section.subjects {
      total_required += subject.periods_per_week
    }
  }
  
  // Step 2: Calculate available teacher capacity
  total_capacity = 0
  FOR each teacher IN teachers {
    total_capacity += teacher.max_periods_per_week
  }
  
  IF total_required > total_capacity {
    RETURN ERROR("Insufficient teacher capacity")
  }
  
  // Step 3: Initial allocation (greedy approach)
  allocations = {}
  FOR each section IN sections {
    FOR each subject IN section.subjects {
      // Find qualified teachers
      qualified = FILTER(teachers, t => CAN_TEACH(t, subject, section.grade))
      
      // Sort by current load (ascending)
      qualified = SORT(qualified, by: current_load)
      
      // Assign to teacher with lowest load
      teacher = qualified[0]
      ALLOCATE(teacher, section, subject)
      allocations[teacher] += subject.periods_per_week
    }
  }
  
  // Step 4: Optimization (reduce variance)
  max_iterations = 1000
  FOR iteration IN 1 TO max_iterations {
    variance = CALCULATE_VARIANCE(allocations)
    
    // Find overloaded and underutilized teachers
    overloaded = FILTER(teachers, t => allocations[t] > target_load + 3)
    underutilized = FILTER(teachers, t => allocations[t] < target_load - 3)
    
    IF overloaded.length == 0 AND underutilized.length == 0 {
      BREAK // Balanced
    }
    
    // Transfer sections from overloaded to underutilized
    FOR each over_teacher IN overloaded {
      FOR each under_teacher IN underutilized {
        // Find transferable sections
        transferable = FIND_TRANSFERABLE_SECTIONS(
          over_teacher,
          under_teacher
        )
        
        IF transferable.exists {
          TRANSFER_SECTION(transferable, over_teacher, under_teacher)
          allocations[over_teacher] -= transferable.periods
          allocations[under_teacher] += transferable.periods
          BREAK
        }
      }
    }
  }
  
  // Step 5: Validate final allocation
  FOR each teacher IN teachers {
    IF allocations[teacher] > teacher.max_periods_per_week {
      RETURN ERROR(`${teacher.name} overloaded: ${allocations[teacher]}`)
    }
    
    free_periods = 48 - allocations[teacher]
    IF free_periods < teacher.min_free_periods {
      RETURN WARNING(`${teacher.name} has only ${free_periods} free periods`)
    }
  }
  
  RETURN {
    allocations: allocations,
    variance: CALCULATE_VARIANCE(allocations),
    balance_score: CALCULATE_BALANCE_SCORE(allocations)
  }
}
```

---

### 3. Teacher Preference Management

#### 3.1 Preference Types

**Configurable Teacher Preferences:**
```yaml
Teacher: Mrs. Sharma (English)

Time Preferences:
  Preferred Slots:
    - Morning periods (P1-P4): High preference
    - Reason: "Peak energy levels, better student engagement"
  
  Avoided Slots:
    - Last period (P8): Avoid if possible
    - Saturday afternoon: Avoid
    - Reason: "Personal commitments"
  
  Preferred Days:
    - Monday, Tuesday, Wednesday: No preference
    - Thursday: Prefer lighter load
    - Friday: Prefer lighter load
    - Saturday: Prefer morning only

Grade Preferences:
  Preferred Grades: 9-10 (Secondary)
  Reason: "Specialization in board exam preparation"
  
  Acceptable Grades: 6-8 (Middle school)
  
  Avoid: Grades 1-5 (Primary)
  Reason: "Prefer advanced literature teaching"

Section Preferences:
  Preferred Sections: Top sections (A, B)
  Reason: "Enjoy working with high-performing students"
  
  Acceptable: All sections
  
Subject Preferences:
  Primary: English Literature
  Secondary: English Language
  Avoid: General English (Primary)

Workload Preferences:
  Target Load: 24-26 periods/week
  Maximum: 28 periods/week
  Consecutive Periods: Max 3
  Free Periods: Prefer clustered (for planning)

Special Requests:
  - No classes immediately after lunch (digestion time)
  - Prefer same sections across week (continuity)
  - Avoid split sections (e.g., 9A + 11B, prefer 9A + 9B)

Preference Priority: MEDIUM
Accommodation Rate: 75% (try to accommodate but not mandatory)
```

#### 3.2 Preference Accommodation Algorithm

**Balancing Preferences with Constraints:**
```javascript
FUNCTION accommodate_preferences(teacher, available_slots, subject, section) {
  scored_slots = []
  
  FOR each slot IN available_slots {
    score = 100 // Start with perfect score
    
    // Time preference scoring
    IF slot.period IN teacher.preferred_periods {
      score += 20 // Bonus for preferred time
    } ELSE IF slot.period IN teacher.avoided_periods {
      score -= 30 // Penalty for avoided time
    }
    
    // Day preference scoring
    IF slot.day IN teacher.preferred_days {
      score += 10
    } ELSE IF slot.day IN teacher.avoided_days {
      score -= 20
    }
    
    // Consecutive period check
    consecutive = COUNT_CONSECUTIVE_PERIODS(teacher, slot)
    IF consecutive > teacher.max_consecutive {
      score -= 40 // Heavy penalty
    }
    
    // Free period clustering
    IF teacher.prefers_clustered_free_periods {
      free_period_cluster_score = CALCULATE_CLUSTER_SCORE(teacher, slot)
      score += free_period_cluster_score
    }
    
    // Grade preference
    IF section.grade IN teacher.preferred_grades {
      score += 15
    } ELSE IF section.grade IN teacher.avoided_grades {
      score -= 25
    }
    
    scored_slots.ADD({slot: slot, score: score})
  }
  
  // Sort by score (descending)
  scored_slots = SORT(scored_slots, by: score, desc: TRUE)
  
  // Return top-scored slot
  RETURN scored_slots[0].slot
}
```

---

### 4. Free Period Optimization

#### 4.1 Strategic Free Period Distribution

**Free Period Allocation:**
```yaml
Purpose of Free Periods:
  1. Lesson Planning (30%)
  2. Assessment Grading (25%)
  3. Student Doubt Clearing (20%)
  4. Professional Development (15%)
  5. Administrative Tasks (10%)

Optimal Distribution:

Mrs. Verma's Free Periods (6 per week):
  Monday:
    - Period 4 (11:00-11:45 AM): Planning
    - Period 7 (1:45-2:30 PM): Grading
  
  Tuesday:
    - Period 2 (9:00-9:45 AM): Doubt clearing (students can visit)
  
  Wednesday:
    - Period 6 (12:45-1:30 PM): Planning
  
  Thursday:
    - Period 3 (9:45-10:30 AM): Doubt clearing
  
  Friday:
    - Period 8 (2:15-3:00 PM): Administrative

Distribution Strategy: Spread across week
Clustering: 2 consecutive free periods on Monday (deep work)
Availability: Tuesday P2, Thursday P3 (student access)

Benefits:
  ✓ Daily planning time
  ✓ Regular student interaction slots
  ✓ Adequate grading time
  ✓ No excessive idle time
  ✓ Work-life balance maintained
```

---

## 📊 DATABASE SCHEMA

### Teacher Allocation Table

```sql
CREATE TABLE teacher_allocation (
  allocation_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Teacher & Subject
  teacher_id INT NOT NULL,
  subject_id INT NOT NULL,
  section_id INT NOT NULL,
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  
  -- Allocation Details
  periods_per_week INT NOT NULL,
  allocation_type ENUM('PRIMARY', 'SECONDARY', 'SUBSTITUTE') DEFAULT 'PRIMARY',
  
  -- Workload
  total_periods_allocated INT,
  free_periods INT,
  workload_percentage DECIMAL(5,2),
  
  -- Qualification Match
  qualification_level ENUM('EXPERT', 'HIGHLY_QUALIFIED', 'QUALIFIED', 'PROVISIONAL') NOT NULL,
  match_score DECIMAL(5,2),
  
  -- Preferences
  preference_score DECIMAL(5,2),
  is_preferred_assignment BOOLEAN DEFAULT FALSE,
  
  -- Status
  allocation_status ENUM('DRAFT', 'APPROVED', 'ACTIVE', 'ARCHIVED') DEFAULT 'DRAFT',
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  approved_by INT,
  approval_date DATE,
  
  -- Foreign Keys
  FOREIGN KEY (teacher_id) REFERENCES teachers(teacher_id),
  FOREIGN KEY (subject_id) REFERENCES subjects(subject_id),
  FOREIGN KEY (section_id) REFERENCES sections(section_id),
  
  -- Indexes
  INDEX idx_teacher (teacher_id),
  INDEX idx_section (section_id),
  INDEX idx_academic_year (academic_year),
  
  -- Unique Constraint
  UNIQUE KEY uk_teacher_section_subject (teacher_id, section_id, subject_id, academic_year)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Workload Limit Enforcement

```javascript
FUNCTION enforce_workload_limits(teacher, new_allocation) {
  current_load = GET_CURRENT_WORKLOAD(teacher)
  new_load = current_load + new_allocation.periods_per_week
  
  IF new_load > teacher.max_periods_per_week {
    RETURN {
      allowed: FALSE,
      reason: `Exceeds maximum workload: ${new_load}/${teacher.max_periods_per_week}`
    }
  }
  
  free_periods = 48 - new_load
  IF free_periods < teacher.min_free_periods {
    RETURN {
      allowed: FALSE,
      reason: `Insufficient free periods: ${free_periods}/${teacher.min_free_periods}`
    }
  }
  
  RETURN {allowed: TRUE}
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations
1. **Master Timetable**: Provide teacher assignments
2. **Workload Analytics**: Teacher utilization data
3. **Payroll**: Teaching hours for salary calculation

### Inbound Integrations
1. **HR Module**: Teacher qualifications, employment type
2. **Curriculum Module**: Subject requirements
3. **Student Management**: Section details

---

## 👥 USER WORKFLOWS

### Workflow: Allocate Teachers for New Academic Year

**Actor:** Academic Coordinator  
**Duration:** 2-3 hours  
**Frequency:** Once per year

**Steps:**
1. Login to Timetable Portal
2. Navigate to "Teacher Allocation"
3. Select Academic Year: 2026-27
4. Click "Auto-Allocate Teachers"
5. System analyzes:
   - 120 teachers
   - 50 sections
   - 15 subjects per section
6. Algorithm runs (10 minutes)
7. Review allocation report
8. Make manual adjustments (5-10 changes)
9. Approve allocation
10. Publish to teachers

**Expected Outcome:** Teachers allocated to subjects with balanced workload.

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 1.0  
**Documentation Standard:** Enterprise Grade

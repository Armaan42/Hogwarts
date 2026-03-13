# ADAPTIVE TESTING (COMPUTER ADAPTIVE TESTING - CAT)

**Module:** Assessment & Examinations  
**Submodule Code:** ASSESS-CAT-012  
**Category:** Advanced Assessment  
**Priority:** High (P1)  
**Owner:** Assessment & EdTech Team

---

## 📋 OVERVIEW

The Adaptive Testing submodule implements Computer Adaptive Testing (CAT) — a sophisticated assessment methodology where the test dynamically adjusts question difficulty based on the student's responses in real-time. Using Item Response Theory (IRT) and machine learning algorithms, it delivers personalized assessments that provide more accurate ability estimates with fewer questions, reducing test fatigue while increasing measurement precision.

### Purpose

Transform traditional one-size-fits-all assessments into personalized, precise evaluations that adapt to each student's ability level in real-time — enabling accurate diagnostic placement, identifying knowledge gaps at granular levels, reducing testing time by 30-50%, and providing detailed competency maps across Bloom's Taxonomy levels.

### Scope

- **Adaptive Algorithm Engine**: IRT-based item selection with ability estimation
- **Question Bank Management**: Calibrated item pool with difficulty parameters
- **Test Configuration**: Customizable stopping rules, content balancing, exposure control
- **Real-Time Adaptation**: Dynamic difficulty adjustment during test
- **Ability Estimation**: Theta (θ) scoring with standard error measurement
- **Diagnostic Reporting**: Competency maps, learning gap analysis
- **Integration**: Connected to Question Paper Generation, Student Progress Tracking

---

## 🎯 KEY FEATURES

### 12.1 How CAT Works

**Adaptive Test Flow:**
```
Traditional Test:
  All students get same 50 questions (easy + medium + hard)
  Duration: 90 minutes
  Accuracy: Moderate (many questions too easy or too hard for individual student)

Computer Adaptive Test:
  Each student gets personalized 20-30 questions at their level
  Duration: 30-45 minutes
  Accuracy: HIGH (every question calibrated to student's ability)

Flow:
  ┌──────────────────────────────────────────────────────┐
  │ 1. Start with MEDIUM difficulty question              │
  │    ↓                                                  │
  │ 2. Student answers CORRECTLY                          │
  │    → Next question: HARDER                            │
  │    ↓                                                  │
  │ 3. Student answers CORRECTLY again                    │
  │    → Next question: EVEN HARDER                       │
  │    ↓                                                  │
  │ 4. Student answers INCORRECTLY                        │
  │    → Next question: SLIGHTLY EASIER                   │
  │    ↓                                                  │
  │ 5. Continue until ability estimate stabilizes         │
  │    (Standard Error < 0.3)                             │
  │    ↓                                                  │
  │ 6. Test ends → Precise ability score generated        │
  └──────────────────────────────────────────────────────┘
```

### 12.2 Item Response Theory (IRT) Parameters

**Question Calibration:**
```
Every question in the item bank has IRT parameters:

Parameter a (Discrimination): How well the question differentiates between students
  Range: 0.5 - 2.5
  High a (2.0+): Question sharply distinguishes good from weak students
  Low a (0.5-0.8): Question doesn't differentiate well

Parameter b (Difficulty): The ability level at which 50% of students answer correctly
  Range: -3.0 to +3.0
  b = -2.0: Very easy (95% students answer correctly)
  b = 0.0: Medium difficulty
  b = +2.0: Very hard (only top 5% answer correctly)

Parameter c (Guessing): Probability of answering correctly by chance
  Range: 0 to 0.35
  MCQ with 4 options: c ≈ 0.25
  Open-ended: c ≈ 0

Example Calibrated Question:
  Question: "Solve: 3x² - 12x + 9 = 0"
  Subject: Mathematics (Grade 10)
  Topic: Quadratic Equations
  
  IRT Parameters:
    a = 1.8 (high discrimination — good question)
    b = 0.5 (slightly above average difficulty)
    c = 0.20 (MCQ with 5 options)
  
  Bloom's Level: Application (Level 3)
  Time Expected: 2.5 minutes
  Used: 145 times | Pass Rate: 52%
```

### 12.3 Adaptive Algorithm

**Item Selection Algorithm (Maximum Fisher Information):**
```
FUNCTION select_next_item(student_theta, item_bank, administered_items):
  // Filter available items
  available = item_bank - administered_items
  
  // Apply content balancing (ensure topic coverage)
  IF topic_quota_not_met(current_test):
    available = FILTER(available, WHERE topic IN needed_topics)
  END IF
  
  // Apply exposure control (prevent over-used items)
  available = FILTER(available, WHERE exposure_count < max_exposure)
  
  // Calculate Fisher Information for each item at current theta
  FOR EACH item IN available:
    P = probability_correct(student_theta, item.a, item.b, item.c)
    Q = 1 - P
    
    // Fisher Information: higher = more informative at this ability level
    item.information = (item.a² * (P - item.c)² * Q) / ((1 - item.c)² * P)
  END FOR
  
  // Select item with maximum information
  best_item = MAX(available, BY information)
  
  RETURN best_item
END FUNCTION

FUNCTION update_ability_estimate(student_theta, response, item):
  // Maximum Likelihood Estimation (MLE) / Expected A Posteriori (EAP)
  P = probability_correct(student_theta, item.a, item.b, item.c)
  
  IF response == CORRECT:
    // Increase theta estimate
    adjustment = LEARNING_RATE * item.a * (1 - P)
    new_theta = student_theta + adjustment
  ELSE:
    // Decrease theta estimate
    adjustment = LEARNING_RATE * item.a * P
    new_theta = student_theta - adjustment
  END IF
  
  // Calculate Standard Error
  total_information = SUM(fisher_information for all administered items at new_theta)
  standard_error = 1 / SQRT(total_information)
  
  RETURN {theta: new_theta, se: standard_error}
END FUNCTION
```

### 12.4 Test Configuration

**Sample CAT Test Setup:**
```
Test: Mathematics Diagnostic Assessment (Grade 9)

Configuration:
  Item Pool Size: 500 calibrated questions
  
  Topics to Cover:
    - Number Systems (20%)
    - Algebra (25%)
    - Geometry (20%)
    - Statistics (15%)
    - Trigonometry (20%)
  
  Difficulty Distribution:
    - Easy (b < -1.0): 150 items
    - Medium (-1.0 ≤ b ≤ 1.0): 200 items
    - Hard (b > 1.0): 150 items
  
  Stopping Rules:
    - Maximum items: 30
    - Minimum items: 15
    - Standard Error threshold: 0.30 (stop when SE < 0.30)
    - Maximum time: 45 minutes
  
  Starting Point:
    - First item difficulty: b = 0.0 (medium)
    - OR use previous test theta as starting point
  
  Content Balancing:
    - No more than 3 consecutive questions from same topic
    - Each topic must have at least 3 questions
  
  Exposure Control:
    - Maximum exposure rate per item: 30% of test-takers
    - Randomized selection among top-5 most informative items
```

### 12.5 Sample CAT Session

**Live Test Example:**
```
Student: Rohan Verma (Grade 9A) | Test: Math Diagnostic CAT

Starting Theta (θ): 0.0 (average)

Q1: "Simplify: 3(2x + 5) - 4x"        [b = 0.0, Medium]
    Rohan's answer: 2x + 15 ✅ CORRECT
    Updated θ: 0.45 | SE: 1.20

Q2: "Solve: 2x² - 8 = 0"               [b = 0.5, Medium-Hard]
    Rohan's answer: x = ±2 ✅ CORRECT
    Updated θ: 0.82 | SE: 0.85

Q3: "Find area of triangle: vertices (0,0), (4,0), (0,3)" [b = 1.0, Hard]
    Rohan's answer: 6 sq units ✅ CORRECT
    Updated θ: 1.15 | SE: 0.65

Q4: "If sin θ = 3/5, find cos θ"        [b = 1.3, Hard]
    Rohan's answer: 5/4 ❌ INCORRECT (correct: 4/5)
    Updated θ: 0.88 | SE: 0.55

Q5: "Find median of: 12, 7, 3, 9, 15"   [b = 0.8, Medium-Hard]
    Rohan's answer: 9 ✅ CORRECT
    Updated θ: 1.02 | SE: 0.48

... (continues until SE < 0.30) ...

Q18: Final question answered
    Final θ: 0.95 | SE: 0.28 ✅ (below 0.30 threshold)
    Test COMPLETE: 18 questions in 28 minutes

Result:
  Theta Score: 0.95 (Above Average)
  Percentile: 72nd
  
  Competency Map:
    Number Systems: PROFICIENT (θ = 1.2)
    Algebra: PROFICIENT (θ = 1.0)
    Geometry: DEVELOPING (θ = 0.6) ⚠️
    Statistics: PROFICIENT (θ = 1.1)
    Trigonometry: BEGINNING (θ = 0.3) ⚠️
  
  Recommendation:
    "Rohan is strong in Algebra and Number Systems. Focus areas: 
    Geometry (coordinate geometry) and Trigonometry (basic ratios).
    Suggested resources: LMS Module → Geometry Unit 3, Trig Unit 1."
```

### 12.6 Competency Mapping & Bloom's Taxonomy

**Bloom's Level Assessment:**
```
CAT assesses across Bloom's Taxonomy levels:

Level 1 - Remember: "What is the formula for area of circle?"
  Difficulty: Easy (b ≈ -1.5)
  Tests: Recall of facts, definitions, formulas

Level 2 - Understand: "Explain why π is irrational."
  Difficulty: Medium (b ≈ -0.5)
  Tests: Comprehension, interpretation

Level 3 - Apply: "Calculate the area of a circle with radius 7 cm."
  Difficulty: Medium (b ≈ 0.0)
  Tests: Using knowledge in new situations

Level 4 - Analyze: "Compare the areas of a circle and square with equal perimeter."
  Difficulty: Hard (b ≈ 1.0)
  Tests: Breaking down, identifying relationships

Level 5 - Evaluate: "Which shape gives maximum area for a fixed perimeter? Justify."
  Difficulty: Very Hard (b ≈ 1.5)
  Tests: Justification, critical thinking

Level 6 - Create: "Design a garden layout maximizing area with 100m fencing."
  Difficulty: Very Hard (b ≈ 2.0)
  Tests: Original design, synthesis
```

---

## 📊 DATA FIELDS

### CAT Session Record

```sql
CREATE TABLE assess_cat_sessions (
    session_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    test_id INT NOT NULL,
    student_id INT NOT NULL,
    
    -- Session Details
    start_time DATETIME,
    end_time DATETIME,
    duration_minutes INT,
    
    -- Adaptive Parameters
    starting_theta DECIMAL(5,3) DEFAULT 0.000,
    final_theta DECIMAL(5,3),
    standard_error DECIMAL(5,4),
    
    -- Items Administered
    total_items INT,
    correct_answers INT,
    incorrect_answers INT,
    
    -- Results
    percentile INT,
    competency_map JSON,        -- {"algebra": 1.2, "geometry": 0.6, ...}
    blooms_assessment JSON,     -- {"remember": 0.9, "apply": 0.7, ...}
    
    -- Stopping Reason
    stop_reason ENUM('SE_THRESHOLD', 'MAX_ITEMS', 'MAX_TIME', 'STUDENT_QUIT'),
    
    -- Metadata
    status ENUM('IN_PROGRESS', 'COMPLETED', 'ABANDONED', 'INVALIDATED'),
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_student (student_id),
    INDEX idx_test (test_id)
);

CREATE TABLE assess_cat_item_responses (
    response_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    session_id BIGINT NOT NULL,
    item_id BIGINT NOT NULL,
    sequence_number INT NOT NULL,
    
    -- Item Parameters
    item_difficulty DECIMAL(5,3),
    item_discrimination DECIMAL(5,3),
    item_guessing DECIMAL(5,4),
    
    -- Response
    student_response VARCHAR(500),
    correct_response VARCHAR(500),
    is_correct BOOLEAN,
    response_time_seconds INT,
    
    -- Ability Update
    theta_before DECIMAL(5,3),
    theta_after DECIMAL(5,3),
    se_after DECIMAL(5,4),
    fisher_information DECIMAL(8,4),
    
    FOREIGN KEY (session_id) REFERENCES assess_cat_sessions(session_id)
);

CREATE TABLE assess_cat_item_bank (
    item_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    subject VARCHAR(50) NOT NULL,
    topic VARCHAR(100) NOT NULL,
    subtopic VARCHAR(100),
    grade VARCHAR(10),
    
    -- Item Content
    question_text TEXT NOT NULL,
    question_type ENUM('MCQ', 'MSQ', 'NUMERICAL', 'SHORT_ANSWER'),
    options JSON,
    correct_answer VARCHAR(500),
    
    -- IRT Parameters (calibrated)
    discrimination_a DECIMAL(5,3),
    difficulty_b DECIMAL(5,3),
    guessing_c DECIMAL(5,4) DEFAULT 0,
    
    -- Metadata
    blooms_level ENUM('REMEMBER', 'UNDERSTAND', 'APPLY', 'ANALYZE', 'EVALUATE', 'CREATE'),
    expected_time_seconds INT,
    times_administered INT DEFAULT 0,
    exposure_rate DECIMAL(5,4) DEFAULT 0,
    
    -- Calibration
    calibration_sample_size INT,
    last_calibrated DATE,
    calibration_status ENUM('UNCALIBRATED', 'PILOT', 'OPERATIONAL', 'RETIRED'),
    
    status ENUM('ACTIVE', 'INACTIVE', 'RETIRED'),
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

---

## 📋 BUSINESS RULES

### Rule 1: Item Calibration Requirement
```
FUNCTION validate_item_for_cat(item):
  IF item.calibration_status != 'OPERATIONAL':
    REJECT: "Only calibrated items can be used in CAT"
  END IF
  
  IF item.calibration_sample_size < 200:
    FLAG: "Insufficient calibration data. Minimum 200 responses required."
  END IF
  
  IF item.discrimination_a < 0.5:
    RETIRE_ITEM: "Discrimination too low. Item does not differentiate students."
  END IF
END FUNCTION
```

### Rule 2: Test Validity Check
```
FUNCTION validate_cat_session(session):
  // Check for random answering pattern
  avg_response_time = AVERAGE(session.response_times)
  IF avg_response_time < 5_SECONDS:
    FLAG_SESSION: "Possible random answering (avg < 5 sec)"
    INVALIDATE if > 80% responses < 3 seconds
  END IF
  
  // Check for ability estimate stability
  IF session.standard_error > 0.50 AND session.total_items >= MAX_ITEMS:
    FLAG: "Unstable ability estimate. Consider retesting."
  END IF
END FUNCTION
```

---

## 🔄 INTEGRATION POINTS

| Connected Module | Data Exchanged | Direction |
|---|---|---|
| Question Paper Generation (02) | Calibrated item bank, IRT parameters | Inbound |
| Student Progress Tracking (06) | Theta scores, competency maps, learning gaps | Outbound |
| AI & Predictive Analytics (25) | Ability trends, performance predictions | Outbound |
| LMS - Adaptive Learning (11) | Learning recommendations based on CAT results | Outbound |
| Report Cards (06) | CAT-derived grades for formative assessment | Outbound |
| Online Proctoring (09) | Proctoring during adaptive tests | Inbound |

---

## ⚠️ EDGE CASES

### Edge Case 1: Student Gets First 5 Questions Wrong
- **Scenario:** Theta drops to -2.5 after 5 consecutive incorrect answers. Remaining item pool at that difficulty is very small.
- **Resolution:** System switches to "Floor Mode" — selects easiest available items to provide a confidence-building experience. If 3 consecutive correct at easy level → resume normal adaptation. Minimum score floor ensures student doesn't receive a demoralizing result.

### Edge Case 2: Network Disconnection During Test
- **Scenario:** Student's internet drops at Question 12 (of estimated 25).
- **Resolution:** Session saved server-side with current theta and SE. Student can resume within 30 minutes from Q13. If > 30 minutes → session marked "INTERRUPTED". If SE already < 0.35 → accept current estimate. If SE > 0.35 → offer retest.

### Edge Case 3: Item Exposure Overflow
- **Scenario:** Top 20 items are selected so frequently that they become "known" among students.
- **Resolution:** Exposure control limits each item to 30% exposure rate. When limit reached, item temporarily locked. System rotates through parallel items (same topic, difficulty). Quarterly item rotation recommended.

### Edge Case 4: Misfit Student (Erratic Response Pattern)
- **Scenario:** Student answers very hard questions correctly but easy questions incorrectly — suggesting test anxiety or cheating on hard items.
- **Resolution:** System calculates "person-fit" statistic (lz*). If lz* < -2.0 → flag as "ABERRANT_PATTERN". Alert teacher for manual review. Possible causes: test anxiety, peeking at neighbor's screen, technical glitch.

---

## ⚙️ CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `cat_min_items` | 15 | Minimum questions before test can end |
| `cat_max_items` | 30 | Maximum questions in a CAT session |
| `cat_se_threshold` | 0.30 | Standard error threshold to stop test |
| `cat_max_time_minutes` | 45 | Maximum time allowed for CAT session |
| `cat_starting_theta` | 0.0 | Default starting ability estimate |
| `cat_exposure_limit` | 0.30 | Maximum exposure rate per item |
| `cat_content_balance` | `true` | Enforce topic coverage in item selection |
| `cat_min_topic_items` | 3 | Minimum items per topic for content balance |
| `cat_max_consecutive_topic` | 3 | Max consecutive items from same topic |
| `cat_item_selection` | `MAX_INFO` | Algorithm: MAX_INFO, RANDOMIZED_TOP5, BAYESIAN |
| `cat_ability_estimator` | `EAP` | Estimator: MLE, EAP, MAP |
| `cat_resume_timeout_min` | 30 | Minutes to allow session resume after disconnect |
| `cat_person_fit_threshold` | -2.0 | lz* threshold for aberrant pattern detection |

---

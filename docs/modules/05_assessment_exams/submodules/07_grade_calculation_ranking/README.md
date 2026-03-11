# GRADE CALCULATION & RANKING

**Module:** Assessment & Exams  
**Submodule Code:** EXAM-GRADE-007  
**Category:** Output & Computation  
**Priority:** High (P1)  
**Owners:** Exam Controller, Academic Coordinator

---

## OVERVIEW

The Grade Calculation & Ranking submodule is the mathematical brain behind result computation. It transforms raw numerical marks into standardized grades (A1, B2, C1, etc.), calculates GPAs, determines class and section ranks, identifies subject toppers, and generates merit lists. It supports multiple grading scales simultaneously (CBSE, ICSE, international IB scales) and handles the nuanced rules around weightage, internal assessment integration, and best-of-N policies.

### Purpose

To provide a flexible, configurable grading engine that can adapt to any Indian or international education board's grading policy. It ensures consistent, error-free grade assignment and ranking that can withstand scrutiny from parents, auditors, and governing boards.

### Scope

-   **Grading Scale Management:** Defining and managing multiple mark-to-grade mapping tables.
-   **GPA / CGPA Calculation:** Credit-hour-weighted grade point averages.
-   **Ranking Algorithms:** Class rank, Section rank, Subject rank with tie-breaking.
-   **Merit List Generation:** Top-N students across configurable dimensions.
-   **Normalization & Scaling:** Statistical moderation of marks when required.
-   **Best-of-N Policies:** Selecting the best scores from multiple assessments.

---

## KEY FEATURES

### 1. Multi-Scale Grading Engine

**Feature Description:**
Supporting different grading systems within the same school.

**CBSE 9-Point Scale:**
| Grade | Marks Range | Grade Points |
|---|---|---|
| A1 | 91-100 | 10 |
| A2 | 81-90 | 9 |
| B1 | 71-80 | 8 |
| B2 | 61-70 | 7 |
| C1 | 51-60 | 6 |
| C2 | 41-50 | 5 |
| D | 33-40 | 4 |
| E1 | 21-32 | 3 (Grace) |
| E2 | 0-20 | 2 (Fail) |

**ICSE Percentage-Based:**
| Grade | Marks Range |
|---|---|
| 1 | 80-100 |
| 2 | 60-79 |
| 3 | 40-59 |
| 4 | 33-39 |
| 5 | Below 33 (Fail) |

### 2. Weighted GPA Calculation

**Feature Description:**
Computing Grade Point Average factoring in credit hours.

**Formula:**
`GPA = Sum(Grade_Points_i * Credit_Hours_i) / Sum(Credit_Hours_i)`

**Example:**
| Subject | Credits | Grade | Points | Weighted |
|---|---|---|---|---|
| Mathematics | 5 | A1 | 10 | 50 |
| English | 4 | B1 | 8 | 32 |
| Science | 5 | A2 | 9 | 45 |
| Hindi | 3 | B2 | 7 | 21 |
| SST | 3 | C1 | 6 | 18 |
| **Total** | **20** | | | **166** |

`GPA = 166 / 20 = 8.30`

### 3. Intelligent Ranking with Tie-Breaking

**Feature Description:**
Assigning ranks fairly across sections and grades.

**Ranking Levels:**
*   **Section Rank:** Within Grade 10-A (40 students).
*   **Grade Rank:** Across all sections of Grade 10 (160 students).
*   **School Rank:** Across all grades (for ceremonies like "School Topper Award").

**Tie-Breaking Cascade:**
1.  Higher aggregate percentage.
2.  Higher marks in Primary Language (English / Hindi).
3.  Higher marks in Mathematics.
4.  Fewer absent subjects.
5.  If still tied: same rank assigned, next rank skipped.

### 4. Best-of-N Assessment Policy

**Feature Description:**
Flexibility in computing final internal assessment marks.

**Example Policy:** "IA marks = Best 3 out of 4 Unit Tests."
*   Student's UT scores: 8, 6, 9, 7.
*   System selects: 8, 9, 7 (drops lowest = 6).
*   Best-of-3 total: 24/30.

---

## DATABASE SCHEMA

### 1. Grading Scales (`exam_grading_scales`)

```sql
CREATE TABLE exam_grading_scales (
    scale_id INT PRIMARY KEY AUTO_INCREMENT,
    scale_name VARCHAR(50), -- 'CBSE_9_POINT', 'ICSE_PCT', 'IB_7_POINT'
    board_affiliation VARCHAR(50),
    
    grade_map JSON, -- [{"grade":"A1","min":91,"max":100,"points":10}, ...]
    
    is_default BOOLEAN DEFAULT FALSE,
    applicable_grades JSON, -- [9, 10, 11, 12]
    
    academic_year_id INT
);
```

### 2. Subject Credit Hours (`exam_subject_credits`)

```sql
CREATE TABLE exam_subject_credits (
    credit_id INT PRIMARY KEY AUTO_INCREMENT,
    subject_id INT NOT NULL,
    grade_id INT NOT NULL,
    
    credit_hours DECIMAL(3,1), -- 5.0, 3.5
    is_elective BOOLEAN DEFAULT FALSE,
    
    weightage_in_aggregate DECIMAL(5,2) DEFAULT 100.00 -- 100% = full weight
);
```

### 3. Ranking Results (`exam_rankings`)

```sql
CREATE TABLE exam_rankings (
    ranking_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    exam_id INT NOT NULL,
    student_id INT NOT NULL,
    
    aggregate_percentage DECIMAL(5,2),
    gpa DECIMAL(4,2),
    
    section_rank INT,
    grade_rank INT,
    school_rank INT,
    
    is_subject_topper BOOLEAN DEFAULT FALSE,
    topper_subjects JSON, -- [3, 7] (subject IDs)
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## BUSINESS RULES

### Rule 1: Board-Specific Grade Assignment
*   The system MUST use the grading scale that matches the student's board affiliation. A CBSE student in Grade 10 uses the 9-point scale. An ICSE student in the same school (if applicable) uses the percentage-based scale.

### Rule 2: Elective Subject Handling in Aggregate
*   CBSE allows "Best 5 of 6" subjects for aggregate calculation. If a student takes 6 subjects, the lowest-scoring subject is dropped from the aggregate.
*   The system identifies the lowest subject automatically and recalculates the aggregate excluding it.

### Rule 3: Grace Marks Impact on Grade
*   If a student's raw marks are 31% (Grade E2 = Fail) but 2 grace marks are applied (becoming 33% = Grade D = Pass), the system uses the grace-adjusted marks for grade assignment, not the raw marks.

### Rule 4: Rank Suppression Policy
*   Some schools choose NOT to display ranks on report cards (to reduce competitive pressure on young children). The system supports `rank_display_policy`:
    *   `ALWAYS`: Show ranks on all report cards.
    *   `GRADE_9_AND_ABOVE`: Only show for senior classes.
    *   `NEVER`: Ranks are computed internally but never printed on report cards.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Report Card (06):** Provides grade letters, GPA, and rank for printing on the card.
*   **To Result Processing (05):** Supplies the grade scale for pass/fail determination.
*   **To Awards Module:** Sends "Subject Topper" and "Overall Topper" data for prize distribution.

### Inbound Relationships
*   **From Marks Entry (04):** Reads finalized marks to compute grades.
*   **From Academic Curriculum (02):** Reads credit hour assignments per subject.

---

## USER WORKFLOWS

### Workflow 1: Configuring a New Grading Scale
**Actor:** Academic Coordinator

1.  **Navigate:** Admin Panel -> Assessment Config -> Grading Scales.
2.  **Create:** Clicks "Add New Scale". Names it "CBSE_2026_Updated".
3.  **Define:** Enters the grade map: A1 (91-100, 10 pts), A2 (81-90, 9 pts), ..., E2 (0-20, Fail).
4.  **Assign:** Maps it to Grades 9, 10, 11, 12 for Academic Year 2026-27.
5.  **Activate:** Sets as default. All future result computations for those grades use this scale.

### Workflow 2: Generating the Annual Merit List
**Actor:** Exam Controller

1.  **Trigger:** After Final Exam results are locked, clicks "Generate Merit List".
2.  **Parameters:** School-wide, Top 10 per Grade, Top 3 Subject Toppers.
3.  **Output:** System produces a formatted PDF:
    *   "Grade 10 Topper: Priya Sharma (96.4%) -- Section A."
    *   "School Topper: Arjun Reddy (97.2%) -- Grade 12 Science."
4.  **Usage:** List is shared at Annual Day Ceremony for prize distribution.

---

## EDGE CASES

### Edge Case 1: Student Exempt from a Subject
*   **Scenario:** A student with hearing impairment is exempt from the Language 2 exam (as per CBSE policy). They have marks in only 4 of 5 main subjects.
*   **Resolution:** The system excludes the exempt subject from both the aggregate calculation and the GPA denominator. The report card shows "EXEMPT" in the Language 2 column.

### Edge Case 2: Mid-Year Grading Scale Change
*   **Scenario:** The school moves from percentage-based grading to CBSE 9-point grading effective January.
*   **Resolution:** Mid-Term results retain the old scale. Final results use the new scale. The annual report card displays both scales with a footnote: "Grading scale updated as per Board Circular No. XYZ."

### Edge Case 3: All Students Score Identically
*   **Scenario:** In a 10-student class, all 10 students score exactly 75% in Mathematics.
*   **Resolution:** All 10 receive Rank 1 in Mathematics. No rank 2 through 10 exists. The next rank (for a lower scorer) would be Rank 11.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `grade_default_scale` | `CBSE_9_POINT` | Default grading scale applied |
| `grade_best_of_n_enabled` | `true` | Use best-of-N for aggregate? |
| `grade_best_of_n_value` | 5 | Best N out of total subjects |
| `grade_rank_display_policy` | `GRADE_9_AND_ABOVE` | When to show ranks on report cards |
| `grade_tie_breaking_order` | `['PCT','LANG1','MATH']` | Tie-breaking priority |
| `grade_gpa_decimal_places` | 2 | GPA rounding precision |
| `grade_merit_list_top_n` | 10 | Number of students on merit list per grade |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

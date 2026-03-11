# PSYCHOMETRIC ANALYSIS

**Module:** Assessment & Exams  
**Submodule Code:** EXAM-PSYCH-010  
**Category:** Advanced / AI-Powered  
**Priority:** Medium (P2)  
**Owners:** School Counselor, Career Guidance Cell, Academic Coordinator

---

## OVERVIEW

The Psychometric Analysis submodule goes beyond traditional academic grading to assess a student's cognitive abilities, personality traits, learning styles, aptitudes, and emotional intelligence. It integrates standardized psychometric instruments into the exam framework, providing schools with data-driven insights to support career counseling, stream selection (Science/Commerce/Arts), and personalized learning interventions.

### Purpose

To provide a scientific, objective basis for guiding students' academic and career choices. Instead of relying solely on marks (a student scoring 85% in Science doesn't necessarily have the aptitude for Engineering), psychometric tests reveal hidden strengths, learning preferences, and personality traits that influence long-term success.

### Scope

-   **Aptitude Testing:** Verbal, Numerical, Spatial, Logical reasoning assessments.
-   **Interest Inventory:** Holland's RIASEC model (Realistic, Investigative, Artistic, Social, Enterprising, Conventional).
-   **Learning Style Assessment:** Visual, Auditory, Kinesthetic preference identification.
-   **Personality Profiling:** Big Five (OCEAN) or MBTI-style assessment adapted for students.
-   **Emotional Intelligence (EQ):** Self-awareness, empathy, social skills evaluation.
-   **Career Mapping:** Matching test results to potential career paths and higher education streams.

---

## KEY FEATURES

### 1. Standardized Test Library

**Feature Description:**
A curated set of age-appropriate psychometric instruments.

**Available Instruments:**
| Test | Target Grade | Duration | Purpose |
|---|---|---|---|
| Junior Aptitude Battery | 6-8 | 60 min | Basic cognitive ability assessment |
| Differential Aptitude Test (DAT) | 9-10 | 120 min | Stream selection guidance |
| Strong Interest Inventory | 9-12 | 45 min | Career interest mapping |
| VARK Learning Style Quiz | All | 15 min | Identifying learning preference |
| Student EQ Assessment | 8-12 | 30 min | Emotional intelligence baseline |

### 2. Adaptive Testing Engine

**Feature Description:**
Tests that adjust difficulty based on student responses.
*   If a student answers 5 "Medium" questions correctly, the engine serves "Hard" questions next.
*   If they struggle, it gracefully drops to "Easy" to get a more accurate baseline.
*   This Computer Adaptive Testing (CAT) approach reduces test length while improving accuracy.

### 3. Comprehensive Profile Report

**Feature Description:**
A multi-page, visual report generated per student.

**Report Sections:**
*   **Aptitude Radar Chart:** Visual map of strengths across 6 aptitude dimensions.
*   **Interest Profile:** Top 3 Holland types with career clusters.
*   **Learning Style Summary:** "Aarav is primarily a Visual Learner (65%) with secondary Kinesthetic (25%) preference."
*   **Personality Snapshot:** Key traits and how they influence classroom behavior.
*   **Career Recommendations:** "Based on your aptitude (High Numerical + Spatial) and interests (Investigative), consider: Engineering, Architecture, Data Science."

### 4. Counselor Dashboard

**Feature Description:**
Aggregated views for the school counselor.
*   **Section Heatmap:** "Grade 10A: 40% Investigative, 30% Social, 20% Artistic, 10% Conventional."
*   **At-Risk Students:** Students whose aptitude scores diverge significantly from their academic performance (e.g., high aptitude but low grades = possible disengagement).
*   **Stream Recommendation Report:** For Grade 10 students choosing Science/Commerce/Arts, a bulk report recommending streams based on psychometric data.

---

## DATABASE SCHEMA

### 1. Psychometric Tests (`exam_psych_tests`)

```sql
CREATE TABLE exam_psych_tests (
    test_id INT PRIMARY KEY AUTO_INCREMENT,
    test_name VARCHAR(150),
    test_type ENUM('APTITUDE', 'INTEREST', 'LEARNING_STYLE', 'PERSONALITY', 'EQ'),
    
    target_grade_range VARCHAR(20), -- '9-12'
    duration_minutes INT,
    total_items INT,
    
    is_adaptive BOOLEAN DEFAULT FALSE,
    
    instrument_source VARCHAR(100), -- 'Holland RIASEC', 'VARK', 'Big Five'
    normative_data_year INT -- Year the norms were established
);
```

### 2. Student Test Results (`exam_psych_results`)

```sql
CREATE TABLE exam_psych_results (
    result_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    test_id INT NOT NULL,
    
    taken_at DATETIME,
    time_spent_minutes INT,
    
    raw_scores JSON, -- {"verbal": 78, "numerical": 92, "spatial": 65}
    percentile_scores JSON, -- {"verbal": 72, "numerical": 95, "spatial": 50}
    
    profile_type VARCHAR(50), -- 'ISA' (Holland code)
    
    learning_style_primary ENUM('VISUAL', 'AUDITORY', 'KINESTHETIC', 'READ_WRITE'),
    
    report_pdf_path VARCHAR(255),
    
    counselor_notes TEXT,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 3. Career Mapping (`exam_psych_career_map`)

```sql
CREATE TABLE exam_psych_career_map (
    map_id INT PRIMARY KEY AUTO_INCREMENT,
    holland_code VARCHAR(3), -- 'ISA', 'RCE'
    aptitude_profile VARCHAR(100), -- 'High Numerical + Spatial'
    
    recommended_streams JSON, -- ['Science-Math', 'Engineering']
    recommended_careers JSON, -- ['Software Engineer', 'Architect', 'Data Analyst']
    
    min_aptitude_percentile INT DEFAULT 50
);
```

---

## BUSINESS RULES

### Rule 1: Parental Consent Required
*   Psychometric testing CANNOT be administered without explicit written or digital consent from the parent/guardian. The system sends a consent form via the Parent Portal. Only students whose parents have clicked "I Consent" are included in the test session.

### Rule 2: Results Confidentiality
*   Psychometric results are classified as "Sensitive Personal Data." They are accessible only to: the student, the parent, the school counselor, and the class teacher (read-only). Subject teachers do NOT have access.

### Rule 3: No Pass/Fail in Psychometric Tests
*   These tests are diagnostic, not evaluative. There is no "pass" or "fail." The system explicitly labels reports: "This assessment is a guidance tool. There are no right or wrong profiles."

### Rule 4: Re-Test Interval
*   To ensure validity, a student cannot retake the same psychometric test within 6 months. The system enforces a cooldown period.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Career Guidance Module (13):** Sends aptitude and interest profiles for career counseling sessions.
*   **To Parent Portal:** Publishes the profile report for parent review and discussion.
*   **To Teacher Dashboard:** Provides learning style data so teachers can adapt pedagogy.

### Inbound Relationships
*   **From Student Profile:** Reads academic performance trends to correlate with aptitude scores.
*   **From Attendance Module:** Cross-references engagement data with psychometric profiles.

---

## USER WORKFLOWS

### Workflow 1: Conducting a Batch Aptitude Test for Grade 10
**Actor:** School Counselor

1.  **Schedule:** Counselor schedules "DAT Test" for all Grade 10 students on March 5th, Period 5-7.
2.  **Consent:** System sends consent forms to 160 parents. 152 consent. 8 opt out.
3.  **Administration:** On March 5th, 152 students log into the Exam Portal -> Psychometric Section.
4.  **Test:** Adaptive test serves questions. Average completion time: 90 minutes.
5.  **Results:** System generates 152 individual reports within 24 hours.
6.  **Counseling:** Counselor reviews the reports. Identifies 15 students whose aptitude strongly suggests "Arts" stream but whose parents have pre-enrolled them in "Science." Schedules parent meetings.

### Workflow 2: Learning Style Integration with Teaching
**Actor:** Class Teacher

1.  **View:** Teacher opens the class dashboard. Sees a "Learning Style Distribution" pie chart.
2.  **Insight:** "10A: 45% Visual, 30% Auditory, 25% Kinesthetic."
3.  **Adaptation:** For the next lesson on Photosynthesis, the teacher prepares a video animation (Visual), a class discussion (Auditory), and a hands-on leaf experiment (Kinesthetic) to cater to all styles.

---

## EDGE CASES

### Edge Case 1: Student Answers Randomly
*   **Scenario:** A disengaged student clicks random answers to finish quickly.
*   **Resolution:** The system detects a "Response Time Too Low" pattern (average < 3 seconds per question for items that should take 15-30 seconds). The report is flagged as "Potentially Invalid" and excluded from aggregate analysis.

### Edge Case 2: Cultural Bias in Test Items
*   **Scenario:** An aptitude question references a cricket analogy that a student from a non-cricket-playing background doesn't understand.
*   **Resolution:** Test items undergo periodic "Bias Review" by a panel. Culturally specific items are tagged and replaced with universal alternatives. The system supports locale-specific item banks.

### Edge Case 3: Parent Disagrees with Career Recommendation
*   **Scenario:** Test recommends "Arts/Humanities" but the parent insists on "Science." The counselor is caught in the middle.
*   **Resolution:** The system generates a "Comparison Report" showing: "Aptitude alignment with Science: 35th percentile. Aptitude alignment with Arts: 88th percentile." This data-driven evidence supports the counselor's recommendation without being prescriptive. The final choice remains with the parent.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `psych_parental_consent_required` | `true` | Block test without parent consent? |
| `psych_retest_cooldown_months` | 6 | Minimum months between re-tests |
| `psych_min_response_time_secs` | 3 | Flag responses below this threshold |
| `psych_results_access_roles` | `['COUNSELOR','CLASS_TEACHER','PARENT']` | Who can view results |
| `psych_adaptive_testing_enabled` | `true` | Use CAT for supported tests? |
| `psych_report_auto_generate` | `true` | Auto-generate PDF report after test? |
| `psych_career_map_min_percentile` | 50 | Min percentile to include a career recommendation |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

# COMPARATIVE ANALYTICS

**Module:** Assessment & Exams  
**Submodule Code:** EXAM-ANAL-011  
**Category:** Advanced / Data Intelligence  
**Priority:** Medium (P2)  
**Owners:** Academic Coordinator, Principal, Data Analytics Team

---

## OVERVIEW

The Comparative Analytics submodule transforms raw exam data into actionable insights by enabling multi-dimensional comparisons. It enables the school leadership to ask and answer questions like: "Is Section A performing better than Section B?", "Has the average Math score improved compared to last year?", "Which teacher's students consistently outperform?", and "How do our Grade 10 results compare to the national CBSE average?" This is the intelligence layer that drives academic decision-making.

### Purpose

To move school leadership from "gut feel" decisions to data-driven academic governance. By comparing performance across sections, years, teachers, subjects, genders, and demographics, the school can identify strengths, weaknesses, and trends that inform teacher training, curriculum changes, and resource allocation.

### Scope

-   **Cross-Section Analysis:** Comparing performance of the same grade across different sections (10A vs 10B vs 10C).
-   **Year-over-Year Trends:** Tracking subject averages, pass percentages, and toppers across 3-5 years.
-   **Teacher Effectiveness Index:** Correlating teacher assignments with student outcomes.
-   **Subject Difficulty Index:** Identifying which subjects are consistently harder based on score distributions.
-   **Gender & Demographic Analysis:** Comparing performance across gender, scholarship status, and fee categories.
-   **Benchmarking:** Comparing school results against board/national averages (if data is available).

---

## KEY FEATURES

### 1. Cross-Section Comparison Dashboard

**Feature Description:**
Answering: "Are all sections of Grade 10 performing equally?"

**Metrics Displayed:**
*   Average marks per section per subject.
*   Pass percentage per section.
*   Standard deviation (spread of scores -- a high SD means inconsistent performance).
*   Box-and-whisker plots showing median, quartiles, and outliers.

**Use Case:** If 10A averages 72% in Physics and 10C averages 55%, the Principal investigates: Is Teacher C (who teaches 10C) struggling? Is the student mix different? Should remedial classes be arranged for 10C?

### 2. Year-over-Year Trend Analysis

**Feature Description:**
Longitudinal tracking of academic performance.

**Visualizations:**
*   Line chart: "Grade 10 Math Average: 2022 (65%), 2023 (68%), 2024 (64%), 2025 (72%)."
*   Bar chart: "Pass Percentage in Science: 2023 (88%) vs 2024 (91%) vs 2025 (94%)."
*   Identification of statistically significant improvements or declines using standard deviation thresholds.

### 3. Teacher Effectiveness Index (TEI)

**Feature Description:**
A sensitive but powerful metric.

**Methodology:**
*   Compares the performance of students taught by Teacher A vs Teacher B for the same subject and same grade.
*   Controls for student intake quality (using prior year's marks as a baseline).
*   TEI = `(Average Score of Teacher's Students - Expected Score based on Prior Performance) / Standard Deviation`.
*   A positive TEI means the teacher's students outperformed expectations. Negative means underperformance.
*   This data is CONFIDENTIAL; accessible only to the Principal and Academic Director.

### 4. Subject Difficulty Index (SDI)

**Feature Description:**
Quantifying how "hard" each subject is based on actual outcomes.

**Calculation:**
*   SDI = `1 - (Average Pass Percentage / 100)`.
*   If Math has a 70% pass rate, SDI = 0.30 (Moderate).
*   If English has a 95% pass rate, SDI = 0.05 (Easy).
*   SDI is used to calibrate question paper difficulty (Submodule 02) for the next cycle.

---

## DATABASE SCHEMA

### 1. Comparative Report Snapshots (`exam_analytics_snapshots`)

```sql
CREATE TABLE exam_analytics_snapshots (
    snapshot_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    exam_id INT NOT NULL,
    academic_year_id INT NOT NULL,
    
    report_type ENUM('SECTION_COMPARISON', 'YOY_TREND', 'TEACHER_EFFECTIVENESS', 
                     'SUBJECT_DIFFICULTY', 'GENDER_ANALYSIS', 'BENCHMARK'),
    
    generated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    generated_by INT,
    
    data_payload JSON, -- Full computed analytics data
    report_pdf_path VARCHAR(255),
    
    is_archived BOOLEAN DEFAULT FALSE
);
```

### 2. Teacher Effectiveness Records (`exam_teacher_effectiveness`)

```sql
CREATE TABLE exam_teacher_effectiveness (
    tei_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    teacher_id INT NOT NULL,
    subject_id INT NOT NULL,
    exam_id INT NOT NULL,
    
    sections_taught JSON, -- ['10A', '10B']
    student_count INT,
    
    avg_marks_obtained DECIMAL(5,2),
    expected_avg_marks DECIMAL(5,2), -- Based on prior year performance
    standard_deviation DECIMAL(5,2),
    
    tei_score DECIMAL(5,2), -- Positive = above expectation
    
    access_restricted_to JSON DEFAULT '["PRINCIPAL","ACADEMIC_DIRECTOR"]'
);
```

### 3. Benchmarking Data (`exam_benchmark_data`)

```sql
CREATE TABLE exam_benchmark_data (
    benchmark_id INT PRIMARY KEY AUTO_INCREMENT,
    board VARCHAR(50), -- 'CBSE'
    academic_year_id INT,
    grade_id INT,
    subject_id INT,
    
    national_average DECIMAL(5,2),
    state_average DECIMAL(5,2),
    school_average DECIMAL(5,2),
    
    data_source VARCHAR(100), -- 'CBSE Board Results 2025'
    imported_at DATETIME
);
```

---

## BUSINESS RULES

### Rule 1: TEI Confidentiality
*   Teacher Effectiveness Index data is classified as "Strictly Confidential." It is NOT visible to the teacher themselves (to prevent demotivation or gaming). Only the Principal and Academic Director can view individual TEI scores. The Academic Coordinator sees only aggregate department-level data.

### Rule 2: Minimum Sample Size
*   Analytics are suppressed if the sample size is too small to be statistically meaningful. If a subject has only 5 students (e.g., an obscure elective), comparisons are not generated. Minimum threshold: 20 students per analysis group.

### Rule 3: Demographic Analysis Ethics
*   Gender and socioeconomic comparisons are permissible ONLY for identifying gaps that need intervention (e.g., "Girls' Math scores dropped -- let's investigate why"). They must NEVER be used for discriminatory purposes. Reports include a disclaimer: "This data is for equity analysis only."

### Rule 4: Benchmark Data Freshness
*   National/State averages are imported annually after board results are published. The system warns if benchmark data is older than 1 year: "Benchmark data is from 2024. Current year data not yet available."

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Principal's Dashboard:** Feeds the "Academic Health" widget showing key indicators.
*   **To HR/Teacher Module:** TEI data (anonymized) informs professional development planning.
*   **To Curriculum Module:** Subject Difficulty Index feeds back into curriculum review and question paper calibration.

### Inbound Relationships
*   **From Result Processing (05):** Reads all finalized results for analysis.
*   **From Historical Data:** Accesses archived results from previous years for trend computation.
*   **From Board Results (External):** Manually imported national/state average data.

---

## USER WORKFLOWS

### Workflow 1: Principal's Monthly Academic Review
**Actor:** Principal

1.  **Open:** Logs into the Admin Dashboard -> Analytics -> Academic Performance.
2.  **View:** Sees the "Cross-Section Comparison" for the recent Mid-Term across Grades 6-12.
3.  **Drill Down:** Notices Grade 9C has a significantly lower average (52%) than 9A (71%) and 9B (68%).
4.  **Investigate:** Clicks on 9C. Sees that Math (avg 38%) is pulling the overall average down.
5.  **Correlate:** Checks TEI for the Math teacher assigned to 9C. TEI = -1.8 (below expectation).
6.  **Action:** Schedules a meeting with the Math HOD to discuss remedial strategies and potential peer observation for the teacher.

### Workflow 2: Board Result Benchmarking
**Actor:** Academic Coordinator

1.  **Import:** After CBSE Board results are published in May, the Coordinator uploads the national/state averages into the benchmark table.
2.  **Compare:** Runs "School vs National" report for Grade 10.
3.  **Highlight:** "School Math Average: 78%. National Average: 65%. School outperforms by +13%."
4.  **Gap:** "School English Average: 62%. National Average: 70%. School underperforms by -8%."
5.  **Report:** Generates a summary for the Trust Board meeting, highlighting areas of strength and weakness.

---

## EDGE CASES

### Edge Case 1: New Teacher with No Historical Data
*   **Scenario:** A newly hired teacher has no prior year data. TEI cannot compute an "Expected Score."
*   **Resolution:** For the first year, the teacher is excluded from TEI ranking. Their students' scores are baselined against the section average (not the teacher's historical performance). This becomes their "Year 0" baseline for future comparisons.

### Edge Case 2: Streaming Bias in Comparisons
*   **Scenario:** Comparing Grade 11 Science (high-aptitude students) against Grade 11 Commerce (mixed aptitude) is misleading because the intake quality differs.
*   **Resolution:** The system supports "Cohort-Controlled Comparisons" where students are grouped by prior year performance bands (Top 25%, Middle 50%, Bottom 25%). This ensures apples-to-apples comparisons within performance tiers.

### Edge Case 3: Sudden Score Drop Due to Paper Difficulty
*   **Scenario:** Grade 8 Math average drops from 72% to 48% year-over-year. Alarm bells ring.
*   **Resolution:** Before concluding "students got worse," the analytics engine checks the Subject Difficulty Index (SDI) for that exam. If SDI spiked from 0.28 to 0.52, the drop is attributable to paper difficulty, not student decline. The system flags: "Adjusted for difficulty, estimated true performance: 68% (within normal range)."

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `analytics_min_sample_size` | 20 | Minimum students per group for valid analysis |
| `analytics_tei_access_roles` | `['PRINCIPAL']` | Who can see individual TEI scores |
| `analytics_yoy_years_lookback` | 5 | Number of years for trend analysis |
| `analytics_sdi_recalculation` | `AFTER_EVERY_EXAM` | When to recompute Subject Difficulty Index |
| `analytics_benchmark_stale_months` | 12 | Months before benchmark data is flagged as old |
| `analytics_gender_analysis_enabled` | `true` | Enable demographic comparisons? |
| `analytics_auto_report_schedule` | `POST_EXAM` | When to auto-generate analytics reports |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

# ATTENDANCE PREDICTION

**Module:** Attendance Management  
**Submodule Code:** ATT-PRED-009  
**Category:** Advanced / AI-Powered  
**Priority:** Medium (P2)  
**Owners:** Data Analytics Team, Academic Coordinator, Counselor

---

## OVERVIEW

The Attendance Prediction submodule uses machine learning models to forecast future attendance patterns for individual students and the school as a whole. By analyzing historical data (past attendance records, seasonal trends, day-of-week patterns, weather data, festival calendar) and correlating with academic performance, it predicts which students are likely to drop below attendance thresholds and which days will see abnormally low turnout.

### Purpose

To enable PROACTIVE intervention rather than reactive firefighting. If the system predicts that a student will hit 74% attendance by March (below the 75% eligibility threshold) based on their current trajectory, the school can intervene in January -- before the problem becomes irreversible.

### Scope

-   **Individual Student Trajectory Prediction:** "At current rate, Student X will reach Y% by end of term."
-   **Cohort-Level Forecasting:** "Grade 9C is trending 5% below last year's attendance at this point."
-   **School-Wide Daily Prediction:** "Tomorrow (Monday after a long weekend) is predicted to have 85% attendance (normal: 93%)."
-   **At-Risk Student Identification:** Proactively flagging students likely to fall below compliance thresholds.
-   **Weather & Event Correlation:** "Heavy rain predicted for Thursday: expect 8% attendance drop."
-   **Intervention Impact Tracking:** Measuring whether counseling sessions improved attendance for flagged students.

---

## KEY FEATURES

### 1. Student Trajectory Forecasting

**Feature Description:**
Predicting each student's end-of-year attendance percentage.

**Model Inputs:**
*   Historical attendance (past 6-12 months).
*   Current year attendance.
*   Day-of-week absence patterns (does this student tend to skip Mondays?).
*   Medical leave history.
*   Correlation with academic performance (students who disengage academically often disengage physically).

**Output:**
*   "Predicted End-of-Year Attendance: 76.2% (within 2% of the 75% threshold). Risk Level: HIGH."
*   Prediction updates weekly as new data comes in.

### 2. Daily Attendance Prediction

**Feature Description:**
Forecasting school-wide attendance for the next 5 days.
*   Uses: Day-of-week trends, weather forecast API, proximity to holidays/festivals, historical patterns for the same date last year.
*   Output: "Tomorrow (March 12, Wednesday): Predicted attendance 91%. Friday (March 14, pre-Holi): Predicted attendance 72%."
*   Admin uses this to plan: cancel important events on predicted low-attendance days, arrange extra transport on bad weather days.

### 3. At-Risk Dashboard

**Feature Description:**
A priority-sorted list of students needing intervention.
*   Sorted by: "Probability of falling below 75% by term end."
*   Columns: Student Name, Current %, Predicted End %, Risk Level, Days of Buffer (how many more days they can miss before hitting 75%).
*   Color-coded: Red (will definitely hit 75%), Orange (likely), Yellow (possible).
*   Click on a student to see their predicted trajectory graph.

### 4. Intervention Impact Tracker

**Feature Description:**
Measuring whether interventions work.
*   After a counselor meets with a student flagged as "at-risk," the system tracks: "Pre-intervention attendance: 78%. Post-intervention (30 days): 88%. Improvement: +10%."
*   Aggregated data tells the school: "Counselor meetings improved attendance by an average of 7% for flagged students."

---

## DATABASE SCHEMA

### 1. Prediction Results (`att_predictions`)

```sql
CREATE TABLE att_predictions (
    prediction_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    academic_year_id INT NOT NULL,
    
    current_attendance_pct DECIMAL(5,2),
    predicted_eoy_pct DECIMAL(5,2), -- End of Year prediction
    confidence_interval_low DECIMAL(5,2),
    confidence_interval_high DECIMAL(5,2),
    
    risk_level ENUM('LOW', 'MEDIUM', 'HIGH', 'CRITICAL'),
    days_of_buffer INT, -- Number of absent days before hitting 75%
    
    predicted_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    model_version VARCHAR(20),
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Daily School Prediction (`att_daily_predictions`)

```sql
CREATE TABLE att_daily_predictions (
    prediction_id INT PRIMARY KEY AUTO_INCREMENT,
    prediction_date DATE NOT NULL,
    
    predicted_pct DECIMAL(5,2),
    actual_pct DECIMAL(5,2), -- Filled after the day passes
    prediction_error DECIMAL(5,2), -- |predicted - actual|
    
    factors JSON, -- {"day_of_week": "Monday", "weather": "Rain", "pre_holiday": true}
    
    model_version VARCHAR(20)
);
```

### 3. Intervention Log (`att_intervention_log`)

```sql
CREATE TABLE att_intervention_log (
    intervention_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    
    intervention_type ENUM('COUNSELOR_MEETING', 'PARENT_CALL', 'VP_MEETING', 'HOME_VISIT', 'PEER_SUPPORT'),
    intervention_date DATE,
    conducted_by INT,
    
    pre_intervention_pct DECIMAL(5,2),
    post_intervention_pct DECIMAL(5,2), -- Measured 30 days after intervention
    improvement_pct DECIMAL(5,2),
    
    notes TEXT,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## BUSINESS RULES

### Rule 1: Prediction Recalculation Frequency
*   Individual student predictions are recalculated weekly (every Sunday night). Daily school predictions are generated nightly for the next 5 days.

### Rule 2: Minimum Data Requirement
*   The ML model requires at least 30 days of attendance data before making predictions for an individual student. New students or start-of-year predictions use historical cohort averages as a proxy.

### Rule 3: Prediction Accuracy Monitoring
*   The system tracks `prediction_error` for daily predictions. If average error exceeds 5% over 30 days, the model is flagged for retraining. Model accuracy is reported monthly to the IT team.

### Rule 4: Non-Punitive Use
*   Predictions are to be used for SUPPORTIVE interventions only, not punitive measures. A student predicted to fall below 75% cannot be pre-emptively barred from exams. Only actual attendance (not predicted) determines eligibility.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Counselor Dashboard:** Routes at-risk students for intervention scheduling.
*   **To Admin Dashboard:** Daily prediction feeds into school planning.
*   **To Compliance Tracking (07):** Provides early warning data aligned with compliance thresholds.

### Inbound Relationships
*   **From Daily Attendance (01):** Historical and current attendance data for model training.
*   **From Weather API:** External weather forecast data.
*   **From Academic Calendar:** Holiday proximity data.
*   **From Academic Performance:** Grades correlation for behavioral modeling.

---

## USER WORKFLOWS

### Workflow 1: Counselor Uses At-Risk Dashboard
**Actor:** School Counselor

1.  **Weekly Review:** Opens the At-Risk Dashboard every Monday.
2.  **Top Priority:** "5 students at HIGH risk of falling below 75% by term end."
3.  **Profile:** Clicks on Rahul (Current: 79%, Predicted EOY: 73%, Buffer: 4 days).
4.  **History:** Sees Rahul's pattern: absent every Monday and Friday. Academic grades also declining.
5.  **Intervene:** Schedules a counseling session. Discovers: parents separating, child is stressed.
6.  **Support:** Initiates a support plan. Tracks improvement over 30 days.
7.  **Result:** Rahul's attendance improves to 85% over the next 2 months.

### Workflow 2: Admin Plans for a Predicted Low-Attendance Day
**Actor:** Admin Office

1.  **Alert:** System predicts: "Next Friday (pre-Holi): 72% attendance expected."
2.  **Action:** Admin decides not to schedule any major tests or guest lectures on that day.
3.  **Transport:** Notifies transport coordinator to reduce buses (save fuel).
4.  **Kitchen:** Reduces mess/canteen orders by 30%.
5.  **Actual:** Friday arrives: 68% attendance (prediction was close). Resources were optimally allocated.

---

## EDGE CASES

### Edge Case 1: Pandemic / Extended Closure
*   **Scenario:** School closes for 2 months due to a pandemic. The ML model has no data for that period and its predictions become unreliable.
*   **Resolution:** The system detects "Extended No-Data Period" and resets predictions. It uses a "Cold Start" mode based on cohort averages until individual data accumulates for 30+ days post-reopening.

### Edge Case 2: Model Predicts Zero Risk, But Student Drops Out
*   **Scenario:** A student with 95% attendance suddenly stops coming due to a sudden family relocation. The model didn't predict this.
*   **Resolution:** Predictions are probabilistic, not deterministic. The system acknowledges limitations: "This model predicts based on historical patterns. Sudden life events cannot be foreseen." The escalation system (Sub 05) handles the actual rapid absence detection.

### Edge Case 3: Over-Reliance on Predictions
*   **Scenario:** A teacher uses prediction data to prejudge a student: "The AI says you're going to fail attendance, so I am putting you on notice already."
*   **Resolution:** Policy training for teachers emphasizes: Predictions are supportive tools, not labels. The system's disclaimer reads: "These predictions are guidance for early intervention, not deterministic outcomes."

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `prediction_student_recalc_schedule` | `WEEKLY` | How often to recalculate student predictions |
| `prediction_daily_forecast_days` | 5 | Days ahead for daily school prediction |
| `prediction_min_data_days` | 30 | Min attendance days before individual prediction |
| `prediction_model_retrain_trigger_error_pct` | 5% | Error threshold for model retraining |
| `prediction_risk_high_threshold_pct` | 78% | Predicted % below which risk = HIGH |
| `prediction_risk_critical_threshold_pct` | 75% | Predicted % below which risk = CRITICAL |
| `prediction_weather_api_enabled` | `true` | Use weather data in predictions? |
| `prediction_intervention_track_days` | 30 | Days after intervention to measure impact |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

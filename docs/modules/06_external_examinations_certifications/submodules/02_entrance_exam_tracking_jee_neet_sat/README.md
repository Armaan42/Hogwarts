# ENTRANCE EXAM TRACKING (JEE/NEET/SAT)

**Module:** External Examinations & Certifications  
**Submodule Code:** EXTEXAM-002  
**Category:** External Examinations & Certifications  
**Priority:** Critical (P0)

## Overview

This submodule handles entrance exam tracking (jee/neet/sat) functionality within the External Examinations & Certifications module.

## Purpose

Track student preparation and registration for competitive entrance examinations (JEE, NEET, SAT, CLAT, etc.), manage coaching schedules, monitor mock test performance, and provide analytics on student readiness for these high-stakes exams.

## Key Features

### 2.1 Exam Registration Tracking

**Entrance Exam Dashboard:**
```
Student: Amit Kumar (Grade 12, Science Stream)
Academic Year: 2024-25

Registered Exams:
1. JEE Main 2025 (Session 1)
   - Registration: ✓ Complete
   - Admit Card: Downloaded
   - Exam Date: 22-Jan-2025
   - Center: Delhi Public School, Sector 24

2. JEE Main 2025 (Session 2)
   - Registration: ✓ Complete
   - Admit Card: Pending
   - Exam Date: 06-Apr-2025

3. NEET UG 2025
   - Registration: ✓ Complete
   - Exam Date: 04-May-2025

4. BITSAT 2025
   - Registration: In Progress
   - Deadline: 15-Mar-2025
```

### 2.2 Mock Test Performance Tracking

**Performance Analytics:**
```
Student: Amit Kumar
Exam: JEE Main

Mock Test History (Last 5):
Test      Date        Physics  Chemistry  Maths   Total   Percentile
Mock 1    01-Nov      52/100   48/100     60/100  160/300  85.2
Mock 2    15-Nov      58/100   55/100     65/100  178/300  89.1
Mock 3    01-Dec      62/100   52/100     70/100  184/300  91.3
Mock 4    15-Dec      65/100   60/100     72/100  197/300  93.8
Mock 5    01-Jan      70/100   65/100     75/100  210/300  95.5

Trend: ↑ Consistent improvement
Predicted Score: 215-225/300
Predicted Percentile: 96-97
```

### 2.3 Preparation Planning

**Study Plan Integration:**
```
Subject: Physics (JEE Main)
Weak Topics (based on mock analysis):
1. Electromagnetic Induction - Avg Score: 40%
2. Modern Physics - Avg Score: 45%
3. Thermodynamics - Avg Score: 50%

Recommended Actions:
- 3 extra coaching sessions on Electromagnetic Induction
- Practice set: 50 problems on Modern Physics
- Revision video: Thermodynamics concepts
```

## Data Fields

```sql
CREATE TABLE entrance_exam_tracking (
    tracking_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id BIGINT NOT NULL,
    exam_name VARCHAR(100) NOT NULL,
    exam_type ENUM('ENGINEERING', 'MEDICAL', 'LAW', 'MANAGEMENT', 'ABROAD', 'OTHER'),
    exam_year INT NOT NULL,
    session VARCHAR(20),
    
    -- Registration
    registration_number VARCHAR(50),
    registration_date DATE,
    registration_status ENUM('NOT_STARTED', 'IN_PROGRESS', 'COMPLETED', 'CANCELLED'),
    admit_card_url VARCHAR(500),
    
    -- Exam Details
    exam_date DATE,
    exam_center VARCHAR(200),
    exam_city VARCHAR(100),
    
    -- Results
    score_obtained DECIMAL(10,2),
    max_score DECIMAL(10,2),
    percentile DECIMAL(5,2),
    rank INT,
    result_status ENUM('AWAITED', 'DECLARED', 'QUALIFIED', 'NOT_QUALIFIED'),
    
    -- Mock Tests
    mock_tests_attempted INT DEFAULT 0,
    avg_mock_score DECIMAL(10,2),
    best_mock_score DECIMAL(10,2),
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

## Business Rules

1. **Registration Deadline Alerts:** System sends reminders at 30, 15, 7, and 1 day before registration deadlines.
2. **Eligibility Check:** Validates student eligibility based on age, grade, and subject combination before allowing registration tracking.
3. **Mock Test Scheduling:** Mock tests must not conflict with regular school timetable; scheduling checked against Module 03.
4. **Performance Threshold Alerts:** If mock test scores drop below 40th percentile, counselor is automatically notified.
5. **Result Auto-Import:** Where APIs are available, results are automatically fetched from exam authority websites.
6. **Coaching Attendance:** Students enrolled in coaching must maintain minimum 80% coaching attendance.
7. **Parent Visibility:** Parents can view registration status and mock test performance through the parent portal.

## Integration Points

- **Student Management (Module 01):** Student profiles and eligibility data
- **Career Guidance (Module 13):** Shares exam performance data for college admission counseling
- **Timetable & Scheduling (Module 03):** Coordinates mock test scheduling with regular classes
- **Assessment Module (Module 05):** Imports internal exam scores for comparative analysis
- **Communication Engine (Module 30):** Sends deadline reminders and result notifications
- **Parent Engagement (Module 31):** Provides exam tracking visibility to parents
- **Analytics & Insights (Module 46):** Feeds performance data for predictive analytics

## User Workflows

**Entrance Exam Registration Tracking:**
```
1. Career Counselor → Identifies students eligible for entrance exams
2. System → Creates exam tracking entries for each student-exam pair
3. Student → Updates registration status as they complete registration
4. System → Sends reminders for pending registrations
5. Student → Uploads admit card upon receipt
6. System → Tracks exam dates and sends day-before reminders
7. Post-exam → Student/System enters scores when results declared
8. System → Updates performance analytics dashboard
9. Career Counselor → Uses results for college admission guidance
```

**Mock Test Cycle:**
```
1. Exam Coordinator → Schedules mock test (checks timetable conflicts)
2. System → Notifies enrolled students
3. Students → Attempt mock test (online/offline)
4. System → Auto-grades objective sections
5. Teachers → Grade subjective sections (if any)
6. System → Generates performance report with topic-wise analysis
7. System → Updates trend charts and predicted scores
8. Counselor → Reviews at-risk students and plans interventions
```

---

## Edge Cases

### Edge Case 1: Incomplete Data Submission
- **Scenario:** A user attempts to submit a record with missing mandatory fields.
- **Resolution:** System validates all required fields before submission. Clear error messages indicate which fields need completion. Partial data can be saved as a draft for later completion.

### Edge Case 2: Concurrent Modification
- **Scenario:** Two users attempt to modify the same record simultaneously.
- **Resolution:** System implements optimistic locking. The second user receives a notification that the record has been modified and must refresh before making changes. Change history preserves both users' intended modifications.

### Edge Case 3: Data Migration from Legacy System
- **Scenario:** Historical records need to be imported from a previous system with different data formats.
- **Resolution:** System provides a data import wizard with field mapping capabilities. Validation rules are applied during import, and records that fail validation are flagged for manual review.

---

## Configuration Parameters

| Parameter | Default | Description |
|---|---|---|
| `auto_archive_after_days` | 365 | Days after which inactive records are auto-archived |
| `approval_required` | `true` | Whether supervisor approval is required for new records |
| `max_attachments` | 10 | Maximum number of file attachments per record |
| `notification_enabled` | `true` | Enable/disable automated notifications |
| `duplicate_check_enabled` | `true` | Enable/disable duplicate detection on creation |
| `bulk_operation_limit` | 500 | Maximum records per bulk operation |
| `data_export_formats` | `CSV,PDF,XLSX` | Supported export formats |

---

**Status:** Production-Ready Documentation  
**Version:** 1.0  
**Last Updated:** March 2026

# INTERNATIONAL CERTIFICATIONS (IELTS/TOEFL)

**Module:** External Examinations & Certifications  
**Submodule Code:** EXTEXAM-003  
**Category:** External Examinations & Certifications  
**Priority:** Critical (P0)

## Overview

This submodule handles international certifications (ielts/toefl) functionality within the External Examinations & Certifications module.

## Purpose

Manage student registrations and preparation for international English proficiency and standardized tests including IELTS, TOEFL, Cambridge English (PET/FCE/CAE), and other international certifications required for overseas education.

## Key Features

### 3.1 International Exam Registry

**Student Certification Dashboard:**
```
Student: Sneha Patel (Grade 11)
Target: Study abroad (UK/USA)

Registered Certifications:
1. IELTS Academic
   - Registration: ✓ Complete
   - Test Date: 15-Mar-2025
   - Center: British Council, Mumbai
   - Target Score: 7.0+
   
2. TOEFL iBT
   - Registration: Planned
   - Preferred Date: May 2025
   - Target Score: 100+

3. SAT
   - Registration: ✓ Complete  
   - Test Date: 08-Jun-2025
   - Target Score: 1400+

Current Preparation Status:
- IELTS Practice Tests Completed: 8/12
- Average Practice Score: 6.5 (Listening: 7.0, Reading: 7.0, Writing: 6.0, Speaking: 6.0)
- Weak Areas: Writing Task 2, Speaking Part 3
```

### 3.2 Practice Test Tracking

**IELTS Practice Performance:**
```
Test    Date        Listening  Reading  Writing  Speaking  Overall
PT-1    01-Jan      6.5        6.0      5.5      6.0       6.0
PT-2    15-Jan      7.0        6.5      5.5      6.0       6.25
PT-3    01-Feb      7.0        7.0      6.0      6.0       6.5
PT-4    15-Feb      7.0        7.0      6.0      6.5       6.625
PT-5    01-Mar      7.5        7.0      6.5      6.5       6.875

Trend: ↑ Steady improvement
Predicted Score: 7.0 (meets target)
```

### 3.3 Score Reporting

**Score Distribution to Universities:**
```
Student: Sneha Patel
IELTS Score: 7.0 (Official)

Score Reports Sent To:
1. University of Edinburgh - Sent 20-Apr-2025
2. University of Manchester - Sent 20-Apr-2025
3. King's College London - Sent 22-Apr-2025
4. University of Toronto - Sent 25-Apr-2025

Reports Remaining: 1 (5 free reports included)
Additional Report Fee: USD 20 each
```

## Data Fields

```sql
CREATE TABLE international_certification (
    cert_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id BIGINT NOT NULL,
    exam_type ENUM('IELTS', 'TOEFL', 'SAT', 'GRE', 'GMAT', 'CAMBRIDGE_PET', 'CAMBRIDGE_FCE', 'CAMBRIDGE_CAE', 'AP', 'OTHER'),
    exam_subtype VARCHAR(50),
    
    -- Registration
    registration_number VARCHAR(100),
    registration_date DATE,
    test_date DATE,
    test_center VARCHAR(200),
    target_score VARCHAR(20),
    
    -- Scores (section-wise)
    section_scores JSON,
    overall_score DECIMAL(5,2),
    
    -- Score Reporting
    score_reports_sent JSON,
    reports_remaining INT,
    
    -- Practice Tests
    practice_tests_completed INT DEFAULT 0,
    avg_practice_score DECIMAL(5,2),
    
    -- Status
    status ENUM('PLANNED', 'REGISTERED', 'COMPLETED', 'SCORE_RECEIVED', 'CANCELLED'),
    result_valid_until DATE,
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

## Business Rules

1. **Score Validity:** IELTS and TOEFL scores are valid for 2 years from test date; system tracks expiry and alerts students if scores may expire before university admission deadlines.
2. **Retake Policy:** System allows tracking multiple attempts; best score is highlighted for university applications.
3. **Target Score Alerts:** If practice test average is more than 1 band/10 points below target, counselor is notified for intervention.
4. **Score Report Tracking:** System tracks how many free score reports are used and calculates additional costs.
5. **Preparation Milestones:** Students must complete minimum 6 practice tests before the actual exam date.
6. **Budget Tracking:** Total examination costs (registration + score reports) are tracked per student for financial planning.
7. **Confidentiality:** Scores are visible only to the student, parents, and assigned career counselor.

## Integration Points

- **Career Guidance (Module 13):** Shares scores for university matching and application management
- **Student Management (Module 01):** Retrieves student profiles and academic records
- **Fee Management (Module 22):** Tracks examination fees and score report costs
- **Communication Engine (Module 30):** Sends test date reminders and score notifications
- **Document & Certificate (Module 28):** Stores official score reports in student document vault
- **Parent Engagement (Module 31):** Provides exam preparation progress visibility to parents

## User Workflows

**International Exam Registration & Tracking:**
```
1. Career Counselor → Identifies students targeting international education
2. Counselor → Recommends appropriate exams based on target countries/universities
3. Student → Registers for exam on official website
4. Student → Updates registration details in school system
5. System → Creates preparation timeline with milestones
6. Student → Completes practice tests on schedule
7. System → Tracks scores and generates improvement recommendations
8. Student → Takes actual exam
9. Student → Updates actual scores when received
10. Counselor → Reviews scores and advises on university applications
11. Student → Requests score reports to be sent to universities
12. System → Tracks score report delivery status
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

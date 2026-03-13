# OLYMPIAD & COMPETITION MANAGEMENT

**Module:** External Examinations & Certifications  
**Submodule Code:** EXTEXAM-004  
**Category:** External Examinations & Certifications  
**Priority:** High (P1)

## Overview

This submodule handles olympiad & competition management functionality within the External Examinations & Certifications module.

## Purpose

Manage student participation in academic olympiads (Science, Mathematics, Cyber, English), inter-school competitions, national talent search examinations (NTSE), and other academic contests, including registration, preparation tracking, and achievement recording.

## Key Features

### 4.1 Competition Registry

**Available Competitions:**
```
Academic Year: 2024-25

Science Olympiads:
1. National Science Olympiad (NSO)
   - Organizer: SOF
   - Levels: School → Regional → National
   - Registration Deadline: 15-Sep-2024
   - Exam Date (Level 1): 15-Nov-2024
   - Fee: INR 125 per student
   - Eligible: Grades 1-12

2. International Science Olympiad (ISO)
   - Organizer: SilverZone
   - Registration Deadline: 30-Sep-2024

Math Olympiads:
3. International Mathematics Olympiad (IMO)
   - Organizer: SOF
   - Fee: INR 125 per student

4. ARYABHATTA National Maths Competition
   - Organizer: State Education Board

Talent Search:
5. NTSE (National Talent Search Exam)
   - Organizer: NCERT
   - Eligible: Grade 10
   - Scholarship: INR 1,250/month (up to PhD)
```

### 4.2 Student Participation Tracking

**Class-wise Registration:**
```
Competition: National Science Olympiad (NSO) 2024
Grade 8:
  Section A: 28/35 students registered (80%)
  Section B: 25/33 students registered (76%)
  Section C: 30/34 students registered (88%)
  
Total Registered: 83 students
Fee Collected: INR 10,375

Top Performers (Previous Year):
- Arjun Mehta (Gold Medal, National Rank 15)
- Priya Sharma (Silver Medal, National Rank 42)
```

### 4.3 Multi-Level Progression

**Competition Progression Tracking:**
```
Student: Arjun Mehta (Grade 9)
Competition: NSO 2024

Level 1 (School Level):
  Date: 15-Nov-2024
  Score: 42/50 (84%)
  Result: QUALIFIED for Level 2
  School Rank: 1
  
Level 2 (Regional):
  Date: 15-Feb-2025
  Score: 38/50 (76%)
  Result: QUALIFIED for National
  Regional Rank: 8

Level 3 (National):
  Date: Awaited
  Status: Preparing
```

## Data Fields

```sql
CREATE TABLE olympiad_competition (
    competition_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    competition_name VARCHAR(200) NOT NULL,
    organizer VARCHAR(200),
    subject_area VARCHAR(100),
    competition_type ENUM('OLYMPIAD', 'TALENT_SEARCH', 'QUIZ', 'DEBATE', 'SCIENCE_FAIR', 'OTHER'),
    eligible_grades JSON,
    registration_deadline DATE,
    fee_per_student DECIMAL(10,2),
    levels INT DEFAULT 1,
    academic_year VARCHAR(9),
    status ENUM('UPCOMING', 'REGISTRATION_OPEN', 'ONGOING', 'COMPLETED'),
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE student_competition_entry (
    entry_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id BIGINT NOT NULL,
    competition_id BIGINT NOT NULL,
    registration_date DATE,
    payment_status ENUM('PENDING', 'PAID', 'WAIVED') DEFAULT 'PENDING',
    current_level INT DEFAULT 1,
    level_scores JSON,
    overall_rank INT,
    award ENUM('GOLD', 'SILVER', 'BRONZE', 'MERIT', 'PARTICIPATION', 'NONE') DEFAULT 'NONE',
    certificate_url VARCHAR(500),
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

## Business Rules

1. **Bulk Registration:** Schools register students in bulk; minimum 10 students required per competition for SOF olympiads.
2. **Fee Collection:** Competition fees are collected along with school fees or separately; tracked via Fee Management module.
3. **Level Progression:** Students must qualify at each level to progress; qualification criteria set by organizing body.
4. **Award Recognition:** Medal winners are automatically added to the school's achievement board and student portfolio.
5. **Teacher Incentive:** Teachers whose students win national-level awards receive recognition points in HR module.
6. **Practice Material:** System distributes previous year question papers to registered students via LMS.
7. **Participation Certificate:** All participants receive participation certificates; stored in Document Vault.

## Integration Points

- **Student Management (Module 01):** Student enrollment and grade data for eligibility
- **Fee Management (Module 22):** Competition fee collection and tracking
- **Advanced LMS (Module 04):** Distributes practice materials and previous year papers
- **Events & Activities (Module 32):** Schedules competition dates on school calendar
- **Communication Engine (Module 30):** Sends registration reminders and result notifications
- **Gamification & Rewards (Module 44):** Awards achievement points for competition participation and wins
- **Document & Certificate (Module 28):** Stores competition certificates in student vault

## User Workflows

**Olympiad Registration & Tracking Workflow:**
```
1. Academic Coordinator → Identifies relevant competitions for the year
2. System → Creates competition entries with deadlines and eligibility
3. Class Teachers → Encourage student participation
4. Students/Parents → Opt-in for competitions via portal
5. System → Validates eligibility and collects fees
6. School → Submits bulk registration to organizing body
7. System → Distributes practice materials via LMS
8. Students → Appear for Level 1 exam at school center
9. System → Records scores and identifies qualifiers
10. Qualifiers → Prepare for next level with additional coaching
11. System → Tracks progression through all levels
12. System → Records awards and updates student portfolios
13. System → Generates achievement reports for school newsletter
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

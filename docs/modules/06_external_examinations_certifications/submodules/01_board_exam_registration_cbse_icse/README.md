# BOARD EXAM REGISTRATION (CBSE/ICSE)

**Module:** External Examinations & Certifications  
**Submodule Code:** EXTEXAM-001  
**Category:** External Examinations & Certifications  
**Priority:** Critical (P0)

## Overview

This submodule handles board exam registration (cbse/icse) functionality within the External Examinations & Certifications module.

## Purpose

Register students for board examinations (CBSE, ICSE, State Boards), manage exam forms, track registration status, handle fee payments, and ensure compliance with board-specific deadlines and requirements.

## Key Features

### 1.1 Board Registration Management

**Registration Workflow:**
```
Step 1: Academic Office selects eligible students (Grade 10/12)
Step 2: System auto-populates student data from Student Management
Step 3: Students/parents verify personal details
Step 4: Upload required documents (photo, signature, ID proof)
Step 5: Select exam subjects based on stream
Step 6: Pay registration fee via integrated payment gateway
Step 7: Submit form to board portal (API/manual)
Step 8: Track registration status
```

**Board-Specific Configuration:**
```yaml
CBSE:
  Registration Window: August 1 - October 31
  Eligible Grades: 10, 12
  Fee: INR 1,500 (General), INR 1,200 (SC/ST)
  Photo Specs: 3.5x4.5 cm, white background
  Subjects: Min 5, Max 6 (Grade 10), Min 5 Max 6 (Grade 12)

ICSE:
  Registration Window: July 15 - September 30
  Eligible Grades: 10, 12 (ISC)
  Fee: INR 2,000
  Photo Specs: Passport size, color
  Subjects: Min 6, Max 7 (Grade 10)

State Board (Maharashtra):
  Registration Window: September 1 - November 15
  Fee: INR 800
```

### 1.2 Document Verification

**Required Documents Checklist:**
```
Student: Rohan Sharma (Grade 12, CBSE)

Document                    Status      Uploaded
Passport Photo              ✓ Verified  15-Aug-2024
Student Signature           ✓ Verified  15-Aug-2024
Aadhaar Card                ✓ Verified  15-Aug-2024
Grade 10 Mark Sheet         ✓ Verified  15-Aug-2024
Migration Certificate       ✗ Pending   -
Caste Certificate (if SC/ST) N/A        -

Overall Status: INCOMPLETE (1 document pending)
Deadline: 31-Oct-2024 (47 days remaining)
```

### 1.3 Subject Selection & Validation

**Subject Combination Validation:**
```
Student: Priya Singh (Grade 12, Science Stream)

Selected Subjects:
1. English (Core) - Mandatory
2. Physics - Elective
3. Chemistry - Elective
4. Mathematics - Elective
5. Computer Science - Elective

Validation: ✓ VALID
- Minimum subjects: 5 ✓
- Maximum subjects: 5 ✓
- Mandatory subjects included: ✓
- Stream compatibility: ✓
```

## Data Fields

```sql
CREATE TABLE board_exam_registration (
    registration_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id BIGINT NOT NULL,
    academic_year VARCHAR(9) NOT NULL,
    board ENUM('CBSE', 'ICSE', 'ISC', 'STATE_BOARD') NOT NULL,
    grade ENUM('10', '12') NOT NULL,
    
    -- Personal Details
    student_name VARCHAR(100) NOT NULL,
    father_name VARCHAR(100),
    mother_name VARCHAR(100),
    date_of_birth DATE NOT NULL,
    gender ENUM('MALE', 'FEMALE', 'OTHER'),
    category ENUM('GENERAL', 'OBC', 'SC', 'ST', 'EWS'),
    
    -- Documents
    photo_url VARCHAR(500),
    signature_url VARCHAR(500),
    id_proof_url VARCHAR(500),
    
    -- Subjects
    subjects_selected JSON,
    stream VARCHAR(50),
    
    -- Registration
    board_registration_number VARCHAR(50),
    registration_fee DECIMAL(10,2),
    payment_status ENUM('PENDING', 'PAID', 'REFUNDED') DEFAULT 'PENDING',
    registration_status ENUM('DRAFT', 'SUBMITTED', 'VERIFIED', 'APPROVED', 'REJECTED') DEFAULT 'DRAFT',
    
    -- Timestamps
    submitted_at DATETIME,
    verified_at DATETIME,
    approved_at DATETIME,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

## Business Rules

1. **Eligibility Validation:** Only students with minimum 75% attendance and no pending disciplinary actions can register for board exams.
2. **Subject Combination Rules:** System validates subject combinations against board-specific rules (e.g., CBSE requires minimum 5 subjects including 1 language).
3. **Document Completeness:** Registration cannot be submitted until all mandatory documents are uploaded and verified.
4. **Deadline Enforcement:** System blocks new registrations after the board-specified deadline; late registrations require principal approval and late fee.
5. **Fee Concession:** SC/ST/EWS students automatically receive fee concession based on category stored in Student Management module.
6. **Duplicate Prevention:** System prevents duplicate registration for the same board and academic year.
7. **Data Freeze:** Once submitted to the board, student details are frozen and cannot be modified without a formal correction request.

## Integration Points

- **Student Management (Module 01):** Pulls student personal details, enrollment data, and category information
- **Fee Management (Module 22):** Processes registration fee payments and tracks payment status
- **Attendance Management (Module 08):** Validates minimum attendance eligibility criteria
- **Document & Certificate (Module 28):** Retrieves uploaded documents for board submission
- **Assessment & Examination (Module 05):** Shares subject enrollment data for exam scheduling
- **Communication Engine (Module 30):** Sends registration status notifications to students and parents
- **Discipline & Behavior (Module 10):** Checks for pending disciplinary actions that may block registration

## User Workflows

**Board Exam Registration Workflow:**
```
1. Academic Office → Initiates registration cycle
2. System → Identifies eligible students based on grade and attendance
3. System → Auto-populates registration forms with student data
4. Student/Parent → Reviews and confirms personal details via portal
5. Student/Parent → Uploads required documents (photo, signature)
6. Student → Selects exam subjects based on stream
7. System → Validates subject combination against board rules
8. Parent → Pays registration fee via online payment
9. Class Teacher → Verifies student details and documents
10. Exam Controller → Reviews and approves all registrations
11. System → Submits batch registration to board portal
12. System → Receives and stores board registration numbers
13. System → Generates registration confirmation for students
```

**Late Registration Workflow:**
```
1. Student → Requests late registration with reason
2. Class Teacher → Forwards request with recommendation
3. Principal → Approves/rejects late registration
4. If approved → Student pays registration fee + late fee
5. System → Submits individual late registration to board
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

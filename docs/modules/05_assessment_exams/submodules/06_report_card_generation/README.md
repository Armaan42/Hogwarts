# REPORT CARD GENERATION

**Module:** Assessment & Exams  
**Submodule Code:** EXAM-RC-006  
**Category:** Output & Communication  
**Priority:** High (P1)  
**Owners:** Exam Controller, Class Teachers, IT Administrator

---

## OVERVIEW

The Report Card Generation submodule transforms raw result data into professionally formatted, board-compliant student report cards. It handles the complex layout requirements of Indian education boards (CBSE, ICSE, State Boards) which mandate specific formats for scholastic grades, co-scholastic assessments, teacher remarks, attendance summaries, and behavioral evaluations -- all on a single document.

### Purpose

To automate the production of hundreds or thousands of report cards per exam cycle, ensuring accuracy, consistency, and compliance with the governing board's prescribed format. It eliminates manual template filling and handwriting, provides digital distribution options, and maintains an archive through the student's entire school career.

### Scope

-   **Template Engine:** Board-specific and school-custom report card layouts.
-   **Multi-Term Consolidation:** Combining Unit Test, Mid-Term, and Final marks on a single annual card.
-   **Co-Scholastic Grading:** Life Skills, Work Education, Art, Health & Physical Education.
-   **Teacher Remarks:** Personalized qualitative feedback per student.
-   **Digital Signatures:** Electronically signed by Class Teacher, Exam Controller, and Principal.
-   **Bulk Generation & Printing:** PDF batch generation for physical distribution.

---

## KEY FEATURES

### 1. Template-Driven Report Card Engine

**Feature Description:**
Supporting multiple layouts for different boards and school preferences.

**Supported Formats:**
*   **CBSE Format:** Scholastic (5-point grading A1-E) + Co-Scholastic (3-point grading A-C) + Discipline Grade + Teacher Remarks.
*   **ICSE Format:** Marks-based with percentages and class averages.
*   **State Board:** Varies by state. Configurable template.
*   **Custom:** School's own proprietary format with logos, mottos, and unique sections.

### 2. Multi-Term Consolidation

**Feature Description:**
A single report card showing the student's progression across the year.

**Layout Example:**
| Subject | UT1 (10) | Mid-Term (80) | UT2 (10) | Final (80) | IA (20) | Total (200) | Grade |
|---|---|---|---|---|---|---|---|
| Mathematics | 8 | 62 | 7 | 71 | 18 | 166 | A2 |
| Science | 9 | 55 | 8 | 60 | 17 | 149 | B1 |

### 3. Co-Scholastic Assessment Integration

**Feature Description:**
Capturing the holistic development of the student beyond academics.

**CBSE Co-Scholastic Areas:**
*   **Work Education:** Project work quality, teamwork, initiative.
*   **Art Education:** Creativity, participation in cultural activities.
*   **Health & Physical Education:** Sports performance, fitness levels.
*   Graded on a 3-point scale: A (Outstanding), B (Very Good), C (Fair).

### 4. Personalized Teacher Remarks

**Feature Description:**
Qualitative feedback that complements numerical data.
*   Class Teacher enters a 2-3 line personalized remark per student: "Aarav shows excellent analytical skills in Mathematics. Needs to participate more actively in group discussions."
*   System provides "Remark Templates" to speed up entry: "Shows consistent improvement in [Subject]. Should focus on [Area]."
*   Principal adds a general remark or stamp for the entire batch.

---

## DATABASE SCHEMA

### 1. Report Card Templates (`exam_rc_templates`)

```sql
CREATE TABLE exam_rc_templates (
    template_id INT PRIMARY KEY AUTO_INCREMENT,
    template_name VARCHAR(100), -- 'CBSE_2025_Standard'
    board_affiliation VARCHAR(50),
    
    layout_html TEXT, -- HTML/CSS template with placeholders
    header_logo_path VARCHAR(255),
    
    sections JSON, -- ['SCHOLASTIC', 'CO_SCHOLASTIC', 'ATTENDANCE', 'REMARKS', 'SIGNATURE']
    
    is_active BOOLEAN DEFAULT TRUE,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### 2. Report Card Records (`exam_report_cards`)

```sql
CREATE TABLE exam_report_cards (
    rc_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    academic_year_id INT NOT NULL,
    exam_id INT, -- Null if cumulative annual card
    
    template_id INT NOT NULL,
    
    scholastic_data JSON, -- All subject marks, grades, totals
    co_scholastic_data JSON, -- Art, PE, Work Ed grades
    
    attendance_summary JSON, -- {total_days: 220, present: 198, percentage: 90}
    
    class_teacher_remarks TEXT,
    principal_remarks TEXT,
    
    pdf_path VARCHAR(255),
    
    generation_status ENUM('PENDING', 'GENERATED', 'SIGNED', 'DISTRIBUTED'),
    generated_at DATETIME,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 3. Teacher Remarks Bank (`exam_remarks_bank`)

```sql
CREATE TABLE exam_remarks_bank (
    remark_id INT PRIMARY KEY AUTO_INCREMENT,
    category ENUM('POSITIVE', 'IMPROVEMENT', 'GENERAL', 'BEHAVIORAL'),
    
    remark_template TEXT, -- 'Shows excellent aptitude in {subject}. Recommended for advanced study.'
    
    applicable_grade_range VARCHAR(20), -- '1-5', '6-8', '9-12'
    created_by INT
);
```

---

## BUSINESS RULES

### Rule 1: Co-Scholastic Completeness
*   A report card CANNOT be generated until all co-scholastic grades are entered. If the Art teacher has not submitted grades for 10B, the system blocks report card generation for the entire section with the message: "Art Education grades missing for 10B."

### Rule 2: Attendance Threshold Warning
*   If a student's attendance is below 75%, the report card includes a red-highlighted note: "Attendance below mandatory threshold. Student is at risk of detention as per school policy."

### Rule 3: Digital Signature Cascade
*   The report card PDF is digitally signed in sequence: Class Teacher -> Exam Controller -> Principal. Each signature is a cryptographic stamp. If any signer has not signed, the PDF shows "DRAFT - NOT OFFICIAL" watermark.

### Rule 4: Historical Immutability
*   Once a report card is marked as `DISTRIBUTED`, no fields can be altered. Any correction requires issuing a "Revised Report Card" with a new `rc_id` and a footnote: "This report card supersedes RC-ID XXXX."

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Parent Portal:** Published report card PDFs available for download.
*   **To Print Vendor:** Bulk PDF export for professional printing on card stock.
*   **To Student Profile:** Archived as part of the student's permanent academic record.

### Inbound Relationships
*   **From Result Processing (05):** Receives finalized marks, grades, percentages, and ranks.
*   **From Attendance Module:** Provides the attendance summary (Present/Absent/Late days).
*   **From Co-Scholastic Module:** Provides activity and behavior grades.

---

## USER WORKFLOWS

### Workflow 1: Annual Report Card Generation
**Actor:** Exam Controller

1.  **Pre-Check:** Dashboard shows "Grade 8: All Marks Locked, Co-Scholastic: 100% Complete, Teacher Remarks: 85% Complete." The 15% pending remarks are flagged -- Controller nudges those class teachers.
2.  **Generate:** Once 100% data is available, clicks "Generate Report Cards for Grade 8".
3.  **Preview:** System generates a sample for the first 5 students. Controller checks layout, data placement, and signature blocks.
4.  **Bulk Generate:** Confirms. System generates 200 report cards in ~3 minutes (PDF batch).
5.  **Sign:** Routes to Class Teachers for digital signature, then to Exam Controller, then Principal.
6.  **Distribute:** Cards are marked `DISTRIBUTED`. PDFs appear on the Parent Portal. Physical copies are printed and given during PTM.

### Workflow 2: Entering Co-Scholastic Grades
**Actor:** PE Teacher

1.  **Navigate:** Opens Assessment Portal -> Co-Scholastic -> Health & PE -> Grade 7A.
2.  **Grid:** Sees 40 students. Columns: Fitness Test Grade, Sports Participation, Discipline.
3.  **Enter:** Assigns A/B/C to each student based on term observations.
4.  **Save:** Clicks "Submit". Data flows into the Report Card engine for that section.

---

## EDGE CASES

### Edge Case 1: Student Joined Mid-Year (Transfer Case)
*   **Scenario:** Student transferred from another school in October. Has no UT1 or Mid-Term marks in this school.
*   **Resolution:** The report card shows "N/A" or "Transfer" for the missing terms. The percentage/grade calculation is based ONLY on the exams the student appeared for. A footnote reads: "Student joined on [Date]. Previous school records available separately."

### Edge Case 2: Board Format Changes Mid-Year
*   **Scenario:** CBSE releases a circular in January changing the Report Card format (e.g., adding a new section for "21st Century Skills").
*   **Resolution:** The admin creates a new template version. Report cards generated before the change retain the old format. Report cards for the Final exam use the updated template. Both are valid.

### Edge Case 3: Student with All-Absent Record
*   **Scenario:** A student was absent for ALL exams in the year (long medical leave).
*   **Resolution:** Report card is generated with status `ABSENT_ALL`. All marks columns show "AB". The result status is "Year Not Assessed". A medical certificate can be attached. The student is eligible for a "Special Exam" in the following session.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `rc_template_board` | `CBSE` | Default board format |
| `rc_co_scholastic_mandatory` | `true` | Block RC generation if co-scholastic incomplete? |
| `rc_digital_signature_required` | `true` | Require all 3 digital signatures? |
| `rc_bulk_generation_batch_size` | 50 | PDFs generated per batch to manage server load |
| `rc_remarks_max_chars` | 500 | Character limit for teacher remarks |
| `rc_attendance_warning_threshold` | 75% | Below this, attendance warning appears on card |
| `rc_archive_retention_years` | 10 | How long generated PDFs are stored |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

# RESULT PROCESSING & PUBLISHING

**Module:** Assessment & Exams  
**Submodule Code:** EXAM-RES-005  
**Category:** Core Exam Pipeline  
**Priority:** Critical (P0)  
**Owners:** Exam Controller, Principal, IT Administrator

---

## OVERVIEW

The Result Processing & Publishing submodule is the culmination of the exam pipeline. It takes the verified, locked marks from all subjects, applies pass/fail rules, computes aggregates (totals, percentages, grades, ranks), and publishes the final results to students, parents, and teachers through multiple channels. This is the single most anticipated event in any school's calendar from a parent perspective.

### Purpose

To automate the complex tabulation of results across multiple subjects, apply board-specific pass/fail criteria, generate merit lists, and provide a controlled, secure mechanism for publishing results -- ensuring no premature leaks and enabling simultaneous access for all stakeholders.

### Scope

-   **Result Tabulation:** Aggregating marks across subjects and computing totals/percentages.
-   **Pass/Fail Determination:** Applying minimum pass marks rules (per-subject and aggregate).
-   **Compartmental / Supplementary Logic:** Identifying students who fail in 1-2 subjects and are eligible for re-exam.
-   **Rank & Merit Generation:** Class rank, Section rank, Subject toppers.
-   **Result Publication:** Timed release on Portal, SMS, and physical notice boards.
-   **Result Verification Board:** Final human review before publication.

---

## KEY FEATURES

### 1. Tabulation Engine

**Feature Description:**
The core computation that transforms raw marks into structured results.

**Process:**
*   Reads all `CONTROLLER_LOCKED` marks entries for a given exam.
*   Computes: Total Marks, Percentage, Grade (based on grade scale rules from Submodule 07).
*   Handles special cases: Absent (AB), Malpractice (MP), Exempted (EX).
*   Outputs a "Result Sheet" per section showing all students' performance at a glance.

### 2. Pass / Fail / Compartment Logic

**Feature Description:**
Board-specific rules that determine a student's outcome.

**CBSE Example Rules:**
*   Pass: Minimum 33% in each subject AND 33% aggregate.
*   Compartment: Fail in exactly 1 subject -> eligible for supplementary exam.
*   Fail: Fail in 2+ subjects -> must repeat the year (for Board classes) or promoted with remedial (for lower classes).
*   "No Detention" Policy (RTE Act for Grades 1-8): No student can be held back regardless of marks.

### 3. Merit List & Topper Generation

**Feature Description:**
Recognizing academic excellence.
*   **Class Topper:** Student with highest aggregate percentage in the class.
*   **Subject Topper:** Highest marks per subject per grade.
*   **Merit List:** Top 10 students per section, Top 10 per grade, Top 10 overall school.
*   **Tie-Breaking Rule:** If two students have identical aggregates, the one with higher marks in the primary subject (e.g., Mathematics for Science stream) ranks higher.

### 4. Controlled Publication Pipeline

**Feature Description:**
Ensuring results are released securely and simultaneously.
*   **Step 1 (Draft):** Tabulation engine generates draft results.
*   **Step 2 (Review):** Result Verification Board (Principal, VP, Exam Controller) reviews for anomalies.
*   **Step 3 (Approve):** Principal gives final digital sign-off.
*   **Step 4 (Schedule):** IT schedules publication at an exact time (e.g., "March 15, 10:00 AM").
*   **Step 5 (Go Live):** At the scheduled moment, Portal, SMS, and App all show results simultaneously.

---

## DATABASE SCHEMA

### 1. Result Summary (`exam_results`)

```sql
CREATE TABLE exam_results (
    result_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    exam_id INT NOT NULL,
    student_id INT NOT NULL,
    
    total_marks_obtained DECIMAL(8,2),
    total_max_marks INT,
    percentage DECIMAL(5,2),
    
    overall_grade VARCHAR(5), -- 'A1', 'B2', etc.
    
    rank_in_section INT,
    rank_in_grade INT,
    
    result_status ENUM('PASS', 'FAIL', 'COMPARTMENT', 'WITHHELD', 'ABSENT_ALL'),
    compartment_subject_ids JSON, -- [4] (Subject ID where student failed)
    
    is_published BOOLEAN DEFAULT FALSE,
    published_at DATETIME,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Result Publication Control (`exam_result_publication`)

```sql
CREATE TABLE exam_result_publication (
    pub_id INT PRIMARY KEY AUTO_INCREMENT,
    exam_id INT NOT NULL,
    
    scheduled_publish_time DATETIME,
    actual_publish_time DATETIME,
    
    approved_by INT, -- Principal ID
    approved_at DATETIME,
    
    publication_channels JSON, -- ['PORTAL', 'SMS', 'APP', 'EMAIL']
    
    status ENUM('DRAFT', 'APPROVED', 'SCHEDULED', 'PUBLISHED', 'REVOKED')
);
```

---

## BUSINESS RULES

### Rule 1: No Partial Publication
*   Results for a grade are either fully published or not published at all. You cannot publish Section A's results while Section B is still pending.

### Rule 2: Withheld Results
*   If a student has a pending disciplinary case or a fee dispute flagged by the Accounts department, their individual result status is set to `WITHHELD`. The portal shows "Result Withheld - Contact School Office" instead of marks.

### Rule 3: Promotion Policy (Non-Board Classes)
*   For Grades 1-8 (under RTE Act No Detention Policy), even students who score below 33% are promoted. However, they are tagged as "Promoted with Remedial" and their Report Card includes a "Remedial Requirement" note.

### Rule 4: Re-Tabulation Freeze Window
*   After results are published, a 7-day freeze window applies. No marks corrections are processed during this window. Revaluation applications (Submodule 08) are accepted only after Day 7.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Report Card Module (06):** Provides the finalized marks, grades, and ranks for report card generation.
*   **To Parent Portal & App:** Pushes published results for real-time viewing.
*   **To SMS Gateway:** Sends "Your ward's result: Percentage X%, Rank Y" to all parents at publication time.

### Inbound Relationships
*   **From Marks Entry (04):** Reads all `CONTROLLER_LOCKED` marks.
*   **From Grade Calculation (07):** Uses the grade scale to map percentage to grade letters.
*   **From Fee Module:** Checks `defaulter_status` for result withholding decisions.

---

## USER WORKFLOWS

### Workflow 1: End-of-Year Result Processing
**Actor:** Exam Controller

1.  **Verify Completeness:** Dashboard shows "All 12 subjects for Grade 10: GREEN (100% marks locked)". If any subject shows YELLOW, the Controller nudges that HOD.
2.  **Run Tabulation:** Clicks "Generate Results". System computes in ~30 seconds for 500 students.
3.  **Review:** Opens the draft result sheet. Sorts by "Percentage Desc" to see toppers. Sorts by "Status = FAIL" to check failures.
4.  **Anomaly Check:** Notices that Student Roll 42 has 95% in 4 subjects but 12% in Hindi. Flags for HOD review (possible marks entry error vs. genuine poor performance).
5.  **Board Presentation:** Exports the summary to a PDF for the Result Verification Board meeting.
6.  **Approval:** Principal reviews and digitally signs off.
7.  **Schedule:** Controller sets publication to "March 20, 11:00 AM".
8.  **Go Live:** At 11:00 AM, 1500 parents receive simultaneous SMS notifications and portal access opens.

### Workflow 2: Handling a Compartment Student
**Actor:** Vice Principal

1.  **Identification:** System reports: "23 students of Grade 9 have COMPARTMENT status (failed in 1 subject)."
2.  **Notification:** Parents receive: "Your child has been placed in Compartment in [Subject]. Supplementary exam date: April 15."
3.  **Re-Exam:** Student appears for the supplementary exam. New marks are entered via the standard Marks Entry workflow.
4.  **Result Update:** If the student passes the supplementary, their result status changes from `COMPARTMENT` to `PASS`. The published result is updated.

---

## EDGE CASES

### Edge Case 1: Server Crash During Publication
*   **Scenario:** 2000 parents try to access the portal at exactly 11:00 AM. The server crashes under load.
*   **Resolution:** Results are pre-rendered as static HTML/PDF files and served via a CDN (Content Delivery Network). The portal's "Result" page is a static page during the first hour (no database queries), then switches to dynamic mode.

### Edge Case 2: Same Marks, Same Rank
*   **Scenario:** 4 students score exactly 87.5%. How to rank them?
*   **Resolution:** Configurable tie-breaking rules:
    *   Step 1: Higher marks in primary subject (e.g., Main Language).
    *   Step 2: If still tied, higher marks in Mathematics.
    *   Step 3: If still tied, all receive the same rank. Next rank is skipped (e.g., Rank 3, 3, 3, 6).

### Edge Case 3: Parent Claims "Result Not Received"
*   **Scenario:** SMS delivery fails for a parent (phone off, DND active).
*   **Resolution:** The system logs SMS delivery status. If `FAILED`, the parent can access results via the Portal or App. The school office can also generate a printed result slip on demand.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `result_pass_mark_percentage` | 33% | Minimum per-subject pass mark |
| `result_aggregate_pass_percentage` | 33% | Minimum overall aggregate to pass |
| `result_compartment_max_subjects` | 1 | Max subjects failed to still get compartment |
| `result_no_detention_grades` | `[1-8]` | Grades where RTE No Detention applies |
| `result_publish_channels` | `['PORTAL','SMS','APP']` | Where results are published |
| `result_recount_freeze_days` | 7 | Days after publish before corrections accepted |
| `result_tie_breaking_order` | `['LANG1','MATH','SCIENCE']` | Subject priority for tie-breaking |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

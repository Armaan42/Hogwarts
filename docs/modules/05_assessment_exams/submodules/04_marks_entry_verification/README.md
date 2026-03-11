# MARKS ENTRY & VERIFICATION

**Module:** Assessment & Exams  
**Submodule Code:** EXAM-MARKS-004  
**Category:** Core Exam Pipeline  
**Priority:** Critical (P0)  
**Owners:** Subject Teachers, Exam Controller, Vice Principal

---

## OVERVIEW

The Marks Entry & Verification submodule is the bridge between evaluation and results. It provides a structured, error-resistant interface for teachers to enter marks (whether from physical or digital evaluation), validates the data against expected ranges, and implements a multi-step locking workflow that ensures no marks can be tampered with after verification. This submodule directly feeds the Result Processing engine.

### Purpose

To digitize the traditionally error-prone process of transcribing marks from answer sheets into a register. It eliminates common mistakes such as wrong totals, misplaced decimal points, and data entry into the wrong student's row through automated validation, double-entry verification, and digital audit trails.

### Scope

-   **Teacher Marks Entry Interface:** Subject-wise, section-wise data entry screens.
-   **Validation Engine:** Range checks, absentee marking, grace marks application.
-   **Double-Entry Verification:** Two independent entries compared for discrepancy detection.
-   **Grace Marks & Moderation:** Bulk adjustments applied by the Exam Board.
-   **Locking Workflow:** Multi-level lock (Teacher -> HOD -> Exam Controller) preventing post-hoc changes.
-   **Internal Assessment (IA) / Co-Scholastic Entry:** Marks for practicals, projects, and behavioral assessments.

---

## KEY FEATURES

### 1. Streamlined Entry Interface

**Feature Description:**
A purpose-built UI for rapid marks entry.
*   Grid layout: Student Names (rows) x Questions/Sections (columns).
*   Tab-to-next-cell navigation for keyboard-only operation (no mouse required for speed).
*   Real-time row total calculation: as teacher enters per-question marks, the total updates instantly.
*   Color-coded cells: Green (entered), Yellow (saved but not locked), Red (validation error).

### 2. Automated Validation

**Feature Description:**
Catching errors before they propagate to results.
*   **Range Check:** If Max Marks for Q1 = 5, entering "6" shows error: "Exceeds maximum marks."
*   **Absent Logic:** If a student is marked "Absent" in the Attendance for that exam slot, their marks row auto-fills with "AB" (Absent). Teacher cannot enter marks for an absent student without overriding.
*   **Total Verification:** `Sum of individual question marks == Total marks entered`. Mismatch triggers a warning.

### 3. Double-Entry Verification (DEV)

**Feature Description:**
Used for high-stakes exams (Board Pre-Boards, Finals).
*   **Step 1:** Teacher A (the evaluator) enters subject marks.
*   **Step 2:** A Data Entry Operator (or Teacher B) enters the same marks independently from the physical mark sheet.
*   **Step 3:** System compares Entry 1 vs Entry 2 cell-by-cell.
*   **Outcome:** If 100% match, marks are auto-verified. If discrepancies exist (e.g., Teacher A entered 72, Operator entered 78), the system flags the specific cells for the HOD to adjudicate by checking the source document.

### 4. Grace Marks & Moderation

**Feature Description:**
Policy-driven bulk adjustments.
*   **Grace Marks:** If the school board decides "All students get +3 marks in Mathematics" (due to a very hard paper), the system applies `+3` across all students' Math totals in one click.
*   **Best-of Policy:** "For Internal Assessment, take the best 3 out of 4 Unit Test scores." The system auto-selects the highest 3 for each student.
*   **Scaling/Moderation:** If the average score in a subject is unexpectedly low (e.g., Class Average = 35%), the Exam Board may apply a normalization formula.

---

## DATABASE SCHEMA

### 1. Marks Entry (`exam_marks_entries`)

```sql
CREATE TABLE exam_marks_entries (
    entry_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    exam_id INT NOT NULL,
    subject_id INT NOT NULL,
    student_id INT NOT NULL,
    
    marks_obtained DECIMAL(6,2),
    max_marks INT,
    
    is_absent BOOLEAN DEFAULT FALSE,
    absent_reason ENUM('ABSENT', 'MEDICAL', 'MALPRACTICE_DEBARRED'),
    
    grace_marks DECIMAL(4,1) DEFAULT 0,
    moderated_marks DECIMAL(6,2), -- Final marks after moderation
    
    entered_by INT NOT NULL, -- Teacher ID
    entered_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    lock_status ENUM('OPEN', 'TEACHER_LOCKED', 'HOD_VERIFIED', 'CONTROLLER_LOCKED'),
    locked_at DATETIME,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Question-Level Marks (`exam_marks_detail`)
For per-question granularity (especially useful for on-screen evaluation data).

```sql
CREATE TABLE exam_marks_detail (
    detail_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    entry_id BIGINT NOT NULL,
    question_number VARCHAR(10), -- 'Q1', 'Q2a', 'Q2b'
    
    marks_awarded DECIMAL(4,1),
    max_marks DECIMAL(4,1),
    
    evaluator_remarks TEXT, -- "Partial credit: method correct, calculation error"
    
    FOREIGN KEY (entry_id) REFERENCES exam_marks_entries(entry_id)
);
```

### 3. Double Entry Verification (`exam_marks_dev`)

```sql
CREATE TABLE exam_marks_dev (
    dev_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    entry_id BIGINT NOT NULL,
    
    second_entry_marks DECIMAL(6,2), -- Independent second entry
    second_entry_by INT,
    
    is_matched BOOLEAN,
    discrepancy_amount DECIMAL(4,1),
    resolution_remarks TEXT,
    resolved_by INT,
    
    FOREIGN KEY (entry_id) REFERENCES exam_marks_entries(entry_id)
);
```

---

## BUSINESS RULES

### Rule 1: Sequential Locking Hierarchy
*   Marks flow through a strict lock chain: `OPEN -> TEACHER_LOCKED -> HOD_VERIFIED -> CONTROLLER_LOCKED`.
*   Once at `CONTROLLER_LOCKED`, marks can ONLY be changed via a formal "Marks Correction Request" which requires the Principal's digital signature.

### Rule 2: Malpractice Flag
*   If a student is caught cheating during an exam, the invigilator files an "Unfair Means Report". The marks entry screen for that student shows a red banner: "MALPRACTICE CASE PENDING". The teacher cannot enter marks until the Discipline Committee provides a verdict (e.g., "Zero Marks" or "Re-exam Granted").

### Rule 3: Deadline Enforcement
*   The system enforces a "Marks Entry Deadline" (e.g., 5 days after the exam). After the deadline, the entry screen for that subject is auto-locked. Late entries require Exam Controller override.

### Rule 4: Internal Assessment Weightage
*   If the exam pattern is "80 Marks Theory + 20 Marks IA", the system validates that both components are entered and that `Theory + IA = 100`. The IA marks may come from a separate entry workflow (practical exams, project submissions).

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Result Processing (05):** Sends finalized, locked marks for tabulation and pass/fail logic.
*   **To Report Card (06):** Provides the marks data for printing on report cards.

### Inbound Relationships
*   **From Answer Sheet Module (03):** Receives auto-graded OMR scores and on-screen evaluation marks.
*   **From Attendance Module:** Confirms student absence for auto-populating "AB" status.
*   **From Discipline Module:** Provides malpractice case verdicts.

---

## USER WORKFLOWS

### Workflow 1: Standard Marks Entry
**Actor:** Subject Teacher

1.  **Navigate:** Teacher opens Exam Portal -> Marks Entry -> Selects "Mid-Term, Physics, Grade 10A".
2.  **Grid:** Sees 40 student names in rows, Q1-Q10 in columns.
3.  **Enter:** Types marks for each question. Tab key moves to the next cell. Total auto-calculates.
4.  **Absent:** For Roll 15 (absent), clicks "Mark Absent". Row turns grey.
5.  **Review:** Checks totals visually. Clicks "Save Draft" (can return later).
6.  **Lock:** After completing all 40 students, clicks "Submit & Lock". Status: `TEACHER_LOCKED`.

### Workflow 2: HOD Verification
**Actor:** Subject HOD

1.  **Dashboard:** HOD sees "3 sections: 10A, 10B, 10C marks pending verification for Physics".
2.  **Review:** Opens 10A. Checks if any anomalies (e.g., 15 students scored 0 on Q7 -- possible printing error?).
3.  **Spot Check:** Randomly selects 5 students and cross-checks against the physical answer script.
4.  **Approve:** Clicks "Verify & Lock". Status: `HOD_VERIFIED`.
5.  **Escalate:** If HOD finds an error (Teacher entered 67 instead of 76), clicks "Unlock for Correction" with a note.

---

## EDGE CASES

### Edge Case 1: Student Attended Exam but Name Missing from List
*   **Scenario:** Student transferred to this section mid-term. Their name doesn't appear in the marks entry grid because the Section Change was processed after the exam roster was frozen.
*   **Resolution:** Teacher clicks "Add Student Manually" which searches the school database. The student is added to the grid with a "Late Addition" flag, and the system logs the reason.

### Edge Case 2: Power Cut During Bulk Entry
*   **Scenario:** Teacher has entered 35 out of 40 students' marks, and the school loses power. Browser closes.
*   **Resolution:** The system auto-saves every cell change to the server (debounced at 3-second intervals). When power returns, the teacher sees 35 students' marks intact. Only the last active cell might be lost.

### Edge Case 3: Marks Correction After Controller Lock
*   **Scenario:** A parent produces evidence that their child's answer for Q5 was incorrectly marked as zero (evaluator missed the page).
*   **Resolution:** The Principal files a "Post-Lock Correction Order" via the system. The Exam Controller unlocks ONLY that specific student's entry for that specific question. After correction, the system re-locks and generates an audit trail documenting the before/after values and the authorization chain.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `marks_entry_deadline_days` | 5 | Days after exam to complete entry |
| `marks_double_entry_required` | `false` | Enable DEV for all exams? (Usually only Finals) |
| `marks_auto_save_interval_seconds` | 3 | Debounced auto-save frequency |
| `marks_grace_marks_max` | 5 | Maximum grace marks awardable per subject |
| `marks_absent_override_requires_reason` | `true` | Must provide reason to enter marks for absent student |
| `marks_lock_hierarchy` | `TEACHER,HOD,CONTROLLER` | Lock chain sequence |
| `marks_best_of_policy_enabled` | `true` | Use best-of-N for IA calculations? |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

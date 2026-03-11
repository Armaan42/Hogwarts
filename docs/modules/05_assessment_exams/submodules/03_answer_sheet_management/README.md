# ANSWER SHEET MANAGEMENT

**Module:** Assessment & Exams  
**Submodule Code:** EXAM-ASM-003  
**Category:** Core Exam Pipeline  
**Priority:** Critical (P0)  
**Owners:** Exam Controller, Evaluators, IT Administrator

---

## OVERVIEW

The Answer Sheet Management submodule handles the physical and digital lifecycle of student answer sheets -- from pre-exam distribution of blank booklets through post-exam collection, evaluation, and archival. In the Indian school context, this encompasses the management of Main Answer Booklets, Supplementary Sheets, OMR (Optical Mark Recognition) sheets for MCQ-based exams, and increasingly, digitized answer scripts for on-screen evaluation.

### Purpose

To maintain a tamper-proof chain of custody for every answer sheet, enable anonymous evaluation (so the evaluator does not know whose paper they are marking), support both physical and digital evaluation workflows, and provide a robust audit trail from collection to result publication.

### Scope

-   **Blank Booklet Inventory:** Tracking stock of pre-printed answer booklets with serial numbers.
-   **Bar Code / QR Masking:** Assigning unique codes to mask student identity during evaluation.
-   **Bundle Management:** Grouping answer sheets into evaluator bundles and tracking their movement.
-   **OMR Scanning:** Auto-grading MCQ responses via optical scanning.
-   **Digital On-Screen Evaluation:** Scanning physical sheets and enabling teachers to mark them on-screen.
-   **Archival & Retrieval:** Storing evaluated sheets for revaluation requests.

---

## KEY FEATURES

### 1. Pre-Exam Booklet Distribution

**Feature Description:**
Tracking blank answer sheets from inventory to student hands.
*   Each blank booklet has a pre-printed serial number (e.g., BKL-2025-00001).
*   Before the exam, the Exam Controller issues bundles of booklets (50 per hall) to invigilators.
*   Post-exam, the count of unused booklets is reconciled: Issued - Used - Returned = 0 (no missing booklets).

### 2. Student Identity Masking (Coding)

**Feature Description:**
Ensuring anonymous, unbiased evaluation.
*   When a student receives their booklet, the invigilator affixes a bar-coded sticker.
*   The sticker maps `Booklet Serial -> Student ID`, but this mapping is stored in an encrypted database accessible only to the Exam Controller AFTER evaluation is complete.
*   The evaluator sees only the barcode ID, not the student's name or roll number.

### 3. OMR Auto-Grading

**Feature Description:**
Machine-based evaluation for MCQ-heavy exams.
*   Students fill in bubbles on a standardized OMR sheet.
*   Scanned via a dedicated OMR scanner or a high-resolution document scanner.
*   System compares filled bubbles against the Answer Key and auto-grades.
*   Supports negative marking (configurable: -0.25 per wrong MCQ).

### 4. On-Screen Digital Evaluation

**Feature Description:**
Replacing physical red-pen marking with on-screen annotation.
*   Physical answer sheets are scanned (both sides, color) into high-resolution images.
*   Assigned to evaluator's digital queue.
*   Evaluator marks each answer on-screen: tick marks, cross marks, awarding marks per question.
*   Advantages: No physical transport of bundles, faster turnaround, centralized monitoring of evaluation progress.

---

## DATABASE SCHEMA

### 1. Booklet Inventory (`exam_booklet_inventory`)

```sql
CREATE TABLE exam_booklet_inventory (
    booklet_serial VARCHAR(20) PRIMARY KEY,
    batch_id VARCHAR(30), -- Print batch
    
    status ENUM('IN_STOCK', 'ISSUED_TO_HALL', 'USED', 'RETURNED_UNUSED', 'DAMAGED', 'MISSING'),
    
    issued_to_hall_id INT,
    issued_at DATETIME,
    
    student_id INT, -- Populated after exam
    exam_slot_id BIGINT,
    
    barcode_sticker_id VARCHAR(30)
);
```

### 2. Identity Masking Map (`exam_coding_map`)

```sql
CREATE TABLE exam_coding_map (
    code_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    exam_id INT NOT NULL,
    
    barcode_id VARCHAR(30) UNIQUE NOT NULL, -- What the evaluator sees
    student_id INT NOT NULL, -- Hidden until decoding
    booklet_serial VARCHAR(20),
    
    is_decoded BOOLEAN DEFAULT FALSE, -- Set to TRUE after marks are finalized
    decoded_at DATETIME,
    decoded_by INT
);
```

### 3. Evaluation Queue (`exam_evaluation_queue`)

```sql
CREATE TABLE exam_evaluation_queue (
    queue_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    exam_id INT NOT NULL,
    subject_id INT NOT NULL,
    
    barcode_id VARCHAR(30) NOT NULL,
    scanned_pdf_path VARCHAR(255), -- For on-screen evaluation
    
    assigned_evaluator_id INT,
    assigned_at DATETIME,
    
    total_marks_awarded DECIMAL(5,1),
    evaluation_status ENUM('PENDING', 'IN_PROGRESS', 'EVALUATED', 'MODERATED'),
    evaluated_at DATETIME
);
```

---

## BUSINESS RULES

### Rule 1: Strict Booklet Reconciliation
*   After every exam, the invigilator submits: `Used Booklets + Unused Booklets = Total Issued`. Any discrepancy triggers a "Missing Booklet Alert" escalated to the Principal within 1 hour.

### Rule 2: Evaluator Cannot Decode Identity
*   The `exam_coding_map` table is access-restricted. Only the Exam Controller (with OTP verification) can run the "Decode" function AFTER all marks are entered and locked.

### Rule 3: OMR Multiple Mark Detection
*   If a student fills two bubbles for the same MCQ, the system marks it as "Invalid Response" (zero marks, no negative marking applied). The scanned image is flagged for manual review.

### Rule 4: Evaluation Time Tracking
*   For on-screen evaluation, the system logs `time_per_paper`. If an evaluator marks a 3-hour paper's sheet in under 2 minutes (statistically impossible for thorough checking), the sheet is auto-flagged for "Cursory Evaluation Review".

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Marks Entry Module (04):** Sends the evaluated marks (per question) for tabulation.
*   **To Revaluation Module (08):** Provides the original scanned images for re-checking.

### Inbound Relationships
*   **From Exam Schedule (01):** Knows which exam and subject to prepare booklets for.
*   **From Question Paper Module (02):** Receives the Answer Key for OMR auto-grading.

---

## USER WORKFLOWS

### Workflow 1: Post-Exam Collection & Coding
**Actor:** Invigilator & Exam Office Staff

1.  **Collection:** Invigilator collects 55 answer booklets from Hall 3 after the Physics exam.
2.  **Count Verify:** Scans each booklet's serial barcode using a handheld scanner. System confirms: "55 of 55 booklets collected."
3.  **Bundling:** Booklets are grouped into bundles of 25. Each bundle gets a "Bundle ID" sticker.
4.  **Handover:** Invigilator signs the digital handover form. Exam Office staff counter-signs.
5.  **Queue Assignment:** System assigns Bundle 1 to Physics Evaluator A, Bundle 2 to Evaluator B.

### Workflow 2: On-Screen Evaluation
**Actor:** Subject Teacher (Evaluator)

1.  **Login:** Teacher logs into the "Evaluation Portal".
2.  **Queue:** Sees "25 papers pending evaluation for Physics Grade 11".
3.  **Open Paper:** Clicks first paper. Scanned image of the answer booklet appears (page-by-page viewer).
4.  **Mark:** For each question, teacher enters marks awarded. Adds annotation comments if needed.
5.  **Submit:** After marking all questions, clicks "Submit". System auto-calculates total.
6.  **Next:** Moves to the next paper. Dashboard shows "1/25 complete".

---

## EDGE CASES

### Edge Case 1: Student Uses Wrong Booklet
*   **Scenario:** Student accidentally writes the Physics exam in a booklet pre-assigned (via barcode) to Chemistry.
*   **Resolution:** The invigilator manually notes the correction. Post-exam, the Exam Office remaps the barcode in the system to the correct exam slot. An audit entry is created documenting the swap.

### Edge Case 2: Scanner Jam / Incomplete Scan
*   **Scenario:** During OMR scanning, 3 sheets get jammed and are partially scanned.
*   **Resolution:** The system detects incomplete scans (page count mismatch) and flags them as "Rescan Required". The operator re-feeds the damaged sheets manually.

### Edge Case 3: Evaluator Falls Sick Mid-Bundle
*   **Scenario:** Evaluator A has marked 10 out of 25 papers and then goes on medical leave.
*   **Resolution:** The Exam Controller reassigns the remaining 15 papers to Evaluator C. The system preserves Evaluator A's marks for the first 10 and merges the final tally seamlessly.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `booklet_reconciliation_deadline_hours` | 2 | Hours after exam to complete booklet count |
| `omr_negative_marking_value` | -0.25 | Marks deducted per wrong MCQ |
| `omr_multi_bubble_policy` | `ZERO_MARKS` | Options: `ZERO_MARKS`, `FIRST_FILLED` |
| `onscreen_eval_min_time_minutes` | 5 | Min time per paper before flagging cursory evaluation |
| `coding_decode_requires_otp` | `true` | OTP verification for identity decode? |
| `scan_resolution_dpi` | 300 | Minimum scan resolution for on-screen eval |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

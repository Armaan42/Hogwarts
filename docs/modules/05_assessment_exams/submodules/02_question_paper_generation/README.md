# QUESTION PAPER GENERATION

**Module:** Assessment & Exams  
**Submodule Code:** EXAM-QPG-002  
**Category:** Core Exam Pipeline  
**Priority:** Critical (P0)  
**Owners:** Subject HOD, Exam Controller, Paper Setter

---

## OVERVIEW

The Question Paper Generation submodule manages the entire lifecycle of creating, reviewing, and securing examination question papers. It moves away from ad-hoc paper setting (where a single teacher writes questions at home) to a structured, blueprint-driven process with question banks, difficulty balancing, moderation panels, and secure digital distribution -- eliminating human bias and paper leaks.

### Purpose

To ensure every question paper is academically rigorous, syllabus-aligned, difficulty-balanced, and securely handled from creation to printing. It provides a repository of vetted questions that can be reused across years while preventing repetition.

### Scope

-   **Question Bank Management:** Building a searchable repository of questions tagged by subject, chapter, difficulty, Bloom's taxonomy level, and marks.
-   **Blueprint/Pattern Engine:** Defining the structure of a paper (e.g., "Section A: 10 MCQs x 1 mark, Section B: 5 Short Answers x 3 marks").
-   **Auto-Paper Generation:** Algorithmically picking questions from the bank to match the blueprint.
-   **Moderation Workflow:** Routing the draft paper through a review panel before finalization.
-   **Secure Printing & Distribution:** Digital watermarking, encrypted transmission to the print vendor, and chain-of-custody tracking.

---

## KEY FEATURES

### 1. Structured Question Bank

**Feature Description:**
A living repository of examination questions.

**Attributes per Question:**
*   Subject, Grade, Chapter/Topic.
*   Question Type: MCQ, Short Answer, Long Answer, Case Study, Diagram-Based.
*   Difficulty: Easy, Medium, Hard.
*   Bloom's Taxonomy Level: Remember, Understand, Apply, Analyze, Evaluate, Create.
*   Marks, Expected Answer Time, Model Answer/Rubric.
*   Usage History: "Last used in Mid-Term Oct 2024". Prevents repetition.

### 2. Blueprint-Driven Paper Assembly

**Feature Description:**
The structural template that every paper must conform to.

**Example Blueprint (Grade 10 Science - 80 Marks):**
| Section | Type | Questions | Marks Each | Total | Difficulty Mix |
|---|---|---|---|---|---|
| A | MCQ | 16 | 1 | 16 | 50% Easy, 50% Medium |
| B | Short Answer | 5 (out of 7) | 3 | 15 | 40% Easy, 60% Medium |
| C | Long Answer | 5 (out of 7) | 5 | 25 | 30% Medium, 70% Hard |
| D | Case Study | 3 | 4 | 12 | 100% Apply/Analyze |
| E | Map/Diagram | 1 | 5 | 5 | Medium |
|   |  |  |  | **80** |  |

### 3. Auto-Generation Algorithm

**Feature Description:**
One-click paper creation based on the blueprint.
*   The system queries the Question Bank using the blueprint constraints.
*   It ensures: No question from the same chapter appears twice in the same section. No question used in the last 2 exams is selected.
*   Output: A formatted, ready-to-print Question Paper PDF with proper headers (School Logo, Exam Name, Date, Time, Instructions).

### 4. Moderation & Approval Pipeline

**Feature Description:**
Quality control before the paper reaches students.
*   **Step 1 (Setter):** Teacher A creates the paper.
*   **Step 2 (Reviewer):** Teacher B (same subject, different section) reviews for errors, ambiguity, and syllabus coverage.
*   **Step 3 (HOD Approval):** Subject HOD gives final sign-off.
*   **Step 4 (Exam Controller):** Locks the paper. No further edits allowed.

---

## DATABASE SCHEMA

### 1. Question Bank (`exam_question_bank`)

```sql
CREATE TABLE exam_question_bank (
    question_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    subject_id INT NOT NULL,
    grade_id INT NOT NULL,
    chapter_id INT,
    
    question_text TEXT NOT NULL,
    question_type ENUM('MCQ', 'SHORT_ANSWER', 'LONG_ANSWER', 'CASE_STUDY', 'DIAGRAM', 'PRACTICAL'),
    
    difficulty ENUM('EASY', 'MEDIUM', 'HARD'),
    blooms_level ENUM('REMEMBER', 'UNDERSTAND', 'APPLY', 'ANALYZE', 'EVALUATE', 'CREATE'),
    marks INT NOT NULL,
    expected_time_minutes INT,
    
    model_answer TEXT,
    rubric TEXT, -- Marking scheme
    
    last_used_exam_id INT, -- Prevents repetition
    usage_count INT DEFAULT 0,
    
    created_by INT, -- Teacher who contributed
    is_approved BOOLEAN DEFAULT FALSE,
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### 2. Paper Blueprints (`exam_paper_blueprints`)

```sql
CREATE TABLE exam_paper_blueprints (
    blueprint_id INT PRIMARY KEY AUTO_INCREMENT,
    subject_id INT NOT NULL,
    grade_id INT NOT NULL,
    exam_type ENUM('UNIT_TEST', 'MID_TERM', 'FINAL'),
    
    total_marks INT,
    total_duration_minutes INT,
    
    sections JSON, -- [{"name":"A", "type":"MCQ", "count":16, "marks_each":1, "difficulty_mix":{"EASY":50,"MEDIUM":50}}]
    
    board_compliance VARCHAR(50) -- 'CBSE_2025', 'ICSE_2025'
);
```

### 3. Generated Papers (`exam_generated_papers`)

```sql
CREATE TABLE exam_generated_papers (
    paper_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    exam_id INT NOT NULL,
    blueprint_id INT NOT NULL,
    
    selected_question_ids JSON, -- [101, 205, 310, ...]
    paper_pdf_path VARCHAR(255),
    answer_key_pdf_path VARCHAR(255),
    
    setter_id INT,
    reviewer_id INT,
    approver_id INT,
    
    status ENUM('DRAFT', 'UNDER_REVIEW', 'APPROVED', 'LOCKED', 'PRINTED'),
    locked_at DATETIME,
    
    digital_watermark_hash VARCHAR(64) -- SHA256 for leak detection
);
```

---

## BUSINESS RULES

### Rule 1: Repetition Prevention
*   A question used in the Final Exam of 2024 CANNOT be auto-selected for any exam in 2025. Manual override requires HOD approval with a documented reason.

### Rule 2: Syllabus Coverage Validation
*   Before locking a paper, the system validates that every chapter in the syllabus has at least 1 question. If Chapter 8 (Heredity) has 0 questions, the system flags: "Warning: Chapter 8 has no representation."

### Rule 3: Secure Paper Handling (Chain of Custody)
*   Once a paper is `LOCKED`, the PDF is encrypted with AES-256.
*   Only the Exam Controller or Principal can decrypt and send to the press.
*   Every download/print action is logged with User ID, IP, and timestamp.
*   If the paper is leaked (detected via digital watermark), the audit trail identifies exactly who accessed the file.

### Rule 4: Parallel Paper Sets
*   For competitive-style exams, the system supports generating 2-4 "Sets" (Set A, B, C, D) with the same questions in a different order, to prevent copying between adjacent students.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Answer Sheet Module (03):** Sends the marking scheme/rubric for the evaluator's reference.
*   **To Print Vendor API:** Secure encrypted transmission of the final PDF for bulk printing.

### Inbound Relationships
*   **From Academic Curriculum Module (02):** Reads the chapter/topic list to validate syllabus coverage.
*   **From Exam Schedule (01):** Knows the exam date to enforce the "Paper must be locked 48 hours before exam" deadline.

---

## USER WORKFLOWS

### Workflow 1: Creating a Final Exam Paper
**Actor:** Subject Teacher (Setter)

1.  **Select:** Teacher opens "Paper Generator" -> Selects Subject: Physics, Grade: 11, Exam: Final.
2.  **Blueprint:** System loads the pre-configured blueprint (80 marks, 3 hours, 5 sections).
3.  **Auto-Fill:** Teacher clicks "Auto-Generate from Bank". System picks 35 questions matching the blueprint.
4.  **Review:** Teacher reviews each question. Swaps Question 12 (too easy) with an alternative from the bank (same chapter, higher difficulty).
5.  **Submit:** Clicks "Submit for Review". Status changes to `UNDER_REVIEW`.
6.  **Peer Review:** Reviewer (Physics Teacher B) checks for errors and approves.
7.  **HOD Sign-off:** Physics HOD validates and clicks "Approve".
8.  **Lock:** Exam Controller clicks "Lock Paper". PDF is encrypted and timestamped.

### Workflow 2: Emergency Paper Replacement (Leak Scenario)
**Actor:** Exam Controller

1.  **Alert:** The digital watermark of Paper Set A for Grade 10 Maths is detected circulating on WhatsApp 12 hours before the exam.
2.  **Investigation:** Audit trail shows the paper was accessed by 2 users. One is flagged as suspicious.
3.  **Action:** Controller selects "Generate Emergency Replacement" which pulls a completely new set of questions from the bank using the same blueprint.
4.  **Approval:** Principal reviews the new paper in 30 minutes.
5.  **Distribution:** New paper is encrypted and dispatched to the secure print facility.

---

## EDGE CASES

### Edge Case 1: Insufficient Questions in Bank
*   **Scenario:** The blueprint requires 7 "Hard" Long Answer questions from Chapter 5, but the bank only has 4.
*   **Resolution:** The system shows a "Bank Deficit Alert" and suggests: (a) Relax difficulty to include "Medium" questions, or (b) Reduce the number of choices (offer 5 out of 6 instead of 5 out of 7), or (c) Teacher adds new questions on the spot.

### Edge Case 2: Bilingual Paper Requirement
*   **Scenario:** As per CBSE norms, Science papers must be printed in both English and Hindi.
*   **Resolution:** Each question in the bank has an optional `question_text_regional` field. The PDF generator creates a side-by-side or alternating layout with both languages.

### Edge Case 3: Practical Exam Paper
*   **Scenario:** The Chemistry practical exam doesn't have traditional "questions" but rather "Experiment Assignments" (e.g., "Perform salt analysis of given sample").
*   **Resolution:** The system supports a `PRACTICAL` question type where the "question" is the experiment description and the "model answer" is the expected procedure + observations table. Blueprint sections can be of type `PRACTICAL` with different formatting rules.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `qp_repetition_block_exams` | 2 | Number of past exams to check for repetition |
| `qp_lock_deadline_hours` | 48 | Hours before exam that paper must be locked |
| `qp_max_sets` | 4 | Maximum paper sets (A, B, C, D) |
| `qp_encryption_algorithm` | `AES-256` | Encryption for locked papers |
| `qp_syllabus_coverage_check` | `true` | Validate all chapters are represented? |
| `qp_bilingual_enabled` | `false` | Generate bilingual papers? |
| `qp_auto_generate_answer_key` | `true` | Auto-create answer key PDF alongside paper? |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

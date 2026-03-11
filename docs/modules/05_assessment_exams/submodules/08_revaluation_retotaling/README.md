# REVALUATION & RETOTALING

**Module:** Assessment & Exams  
**Submodule Code:** EXAM-REVAL-008  
**Category:** Post-Result Operations  
**Priority:** High (P1)  
**Owners:** Exam Controller, Subject HOD, Accounts Officer

---

## OVERVIEW

The Revaluation & Retotaling submodule handles the post-result dispute resolution process. After results are published, students (or parents) who believe their answer sheets were incorrectly evaluated can formally request a re-check (retotaling of marks) or a full re-evaluation (re-marking by a different evaluator). This submodule manages the application pipeline, fee collection, evaluator assignment, result revision, and communication of outcomes.

### Purpose

To provide a transparent, fee-based, and auditable mechanism for challenging exam results. It ensures that genuine evaluation errors are corrected while preventing frivolous requests through a non-refundable application fee. It protects both student rights and evaluator integrity.

### Scope

-   **Application Pipeline:** Online form submission with subject selection and fee payment.
-   **Retotaling:** Verifying that all answered questions were marked and totals are arithmetically correct.
-   **Revaluation:** Complete re-marking by a different (senior) evaluator.
-   **Photocopy Request:** Providing the student with a copy of their evaluated answer sheet for reference.
-   **Result Revision:** Updating marks, grades, and ranks if changes are warranted.
-   **Refund Logic:** Refunding the fee if marks change by a specified threshold.

---

## KEY FEATURES

### 1. Online Application Portal

**Feature Description:**
Self-service application for parents via the school portal.
*   Parent selects: Exam -> Subject(s) -> Service Type (Retotaling / Revaluation / Photocopy).
*   Fee auto-calculated: Rs. 200 per subject for Retotaling, Rs. 500 per subject for Revaluation.
*   Payment via integrated gateway. Application is `SUBMITTED` only after payment confirmation.

### 2. Retotaling Engine (Automated)

**Feature Description:**
Quick arithmetic verification.
*   If on-screen evaluation was used, the system can auto-retotal by summing all per-question marks stored in `exam_marks_detail` and comparing with the entry in `exam_marks_entries`.
*   If physical evaluation: The original evaluator's marks on the physical sheet are re-summed by a clerk.
*   Turnaround: 2-3 working days.

### 3. Revaluation Panel Assignment

**Feature Description:**
Fair re-evaluation by an independent evaluator.
*   The system selects a "Revaluation Panel Evaluator" who is:
    *   A senior teacher of the same subject.
    *   NOT the original evaluator (conflict prevention).
    *   Preferably from a different section or campus.
*   The evaluator marks the paper independently without seeing the original marks.
*   If the new evaluation differs by more than a configurable threshold (e.g., +/- 15%), a third evaluator ("Moderator") reviews.

### 4. Result Revision & Notification

**Feature Description:**
Handling the outcome of revaluation.
*   **Marks Increased:** Student's record is updated. Revised report card is generated. Parent receives SMS: "Revaluation complete. Marks changed from 52 to 61 in Physics. Updated report card available."
*   **Marks Unchanged:** Parent notified: "Revaluation complete. No change in marks." Fee is non-refundable.
*   **Marks Decreased:** Rare but possible. School policy determines if the lower mark is applied or the original stands. Most schools follow "Take the Higher" policy.

---

## DATABASE SCHEMA

### 1. Revaluation Applications (`exam_reval_applications`)

```sql
CREATE TABLE exam_reval_applications (
    app_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    exam_id INT NOT NULL,
    subject_id INT NOT NULL,
    
    service_type ENUM('RETOTALING', 'REVALUATION', 'PHOTOCOPY'),
    
    fee_amount DECIMAL(8,2),
    payment_id BIGINT, -- FK to fee_payments
    
    original_marks DECIMAL(6,2),
    revised_marks DECIMAL(6,2), -- Populated after completion
    
    status ENUM('SUBMITTED', 'FEE_PENDING', 'IN_PROGRESS', 'COMPLETED', 'REJECTED'),
    
    assigned_evaluator_id INT,
    moderator_id INT, -- If needed
    
    submitted_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    completed_at DATETIME,
    
    refund_eligible BOOLEAN DEFAULT FALSE,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Revaluation Audit Trail (`exam_reval_audit`)

```sql
CREATE TABLE exam_reval_audit (
    audit_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    app_id BIGINT NOT NULL,
    
    action VARCHAR(50), -- 'ORIGINAL_MARKS_SEALED', 'EVALUATOR_ASSIGNED', 'NEW_MARKS_ENTERED'
    performed_by INT,
    performed_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    old_value TEXT,
    new_value TEXT,
    remarks TEXT,
    
    FOREIGN KEY (app_id) REFERENCES exam_reval_applications(app_id)
);
```

---

## BUSINESS RULES

### Rule 1: Application Window
*   Revaluation applications are accepted only between Day 7 and Day 21 after result publication. Before Day 7, the "freeze window" applies. After Day 21, the window closes permanently.

### Rule 2: Fee Refund on Significant Change
*   If the revaluation results in a mark change of >= 10% of the max marks (e.g., >= 8 marks change on an 80-mark paper), the application fee is refunded as a Credit Note on the student's fee account.
*   If the change is < 10%, the fee is non-refundable regardless of direction.

### Rule 3: "Take the Higher" Policy
*   If the revaluation results in LOWER marks than the original, the school applies the "Take the Higher" policy: the original (higher) marks are retained. This protects the student from being penalized for exercising their right to revaluation.

### Rule 4: Original Evaluator Anonymity
*   The revaluation panel evaluator must NOT know who the original evaluator was. The system strips all evaluator metadata from the answer sheet before presenting it for revaluation.

### Rule 5: Cascading Result Update
*   If marks change, the system must automatically re-run: Grade Calculation -> Rank Recalculation -> Result Status Update -> Report Card Regeneration. All downstream artifacts are updated in a single transaction.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Fee Module:** Creates a demand for the revaluation fee and processes refunds.
*   **To Grade Calculation (07):** Triggers grade and rank recalculation upon mark revision.
*   **To Report Card (06):** Triggers revised report card generation if marks change.

### Inbound Relationships
*   **From Result Processing (05):** Reads the published marks that the student is disputing.
*   **From Answer Sheet Module (03):** Retrieves the scanned/physical answer sheet for re-evaluation or photocopy.

---

## USER WORKFLOWS

### Workflow 1: Parent Applies for Revaluation
**Actor:** Parent

1.  **Review:** Parent sees Physics result: 48/80. Expected higher. Downloads the Photocopy (Rs. 100).
2.  **Analyze:** Compares answer sheet with model answers provided by the school.
3.  **Apply:** Navigates to Portal -> Revaluation -> Selects "Physics" -> "Full Revaluation" (Rs. 500).
4.  **Pay:** Completes payment. Status: `SUBMITTED`.
5.  **Wait:** System assigns a senior Physics teacher from another section.
6.  **Result:** After 10 days, parent receives notification: "Revised marks: 56/80 (+8). Fee refunded."
7.  **Update:** Portal shows updated percentage, grade (C1 -> B2), and revised rank.

### Workflow 2: Exam Controller Processes Batch Retotaling
**Actor:** Exam Controller

1.  **Dashboard:** Sees "42 Retotaling Applications for Mid-Term Physics".
2.  **Auto-Process:** For on-screen evaluated papers, clicks "Run Auto-Retotal". System checks `SUM(question_marks) == total_marks` for all 42 students.
3.  **Results:** 39 students: no change. 3 students: totaling error found (marks changed by 2-5 marks).
4.  **Notify:** System auto-sends results to all 42 applicants. 3 students receive mark corrections.

---

## EDGE CASES

### Edge Case 1: Revaluation Changes Pass/Fail Status
*   **Scenario:** Student had 31% (Fail). Revaluation gives 35% (Pass). This changes the entire result outcome.
*   **Resolution:** System recalculates everything: Result status changes from FAIL to PASS. Compartment status is removed. Updated report card is issued. Student is cleared for promotion to the next grade.

### Edge Case 2: Multiple Subjects Revaluation
*   **Scenario:** Student applies for revaluation in 3 subjects. Physics is done first (+8 marks). Chemistry is done second (-2 marks, original retained). Math is still pending.
*   **Resolution:** System applies changes incrementally. After each subject's revaluation completes, a partial update runs. The final aggregate/rank is recalculated only after ALL applied subjects are processed to avoid multiple rank shuffles.

### Edge Case 3: Original Answer Sheet Lost
*   **Scenario:** The physical answer sheet is missing from storage. The student has applied for revaluation.
*   **Resolution:** If on-screen evaluation was used, the scanned images are sufficient for re-evaluation. If only physical copies existed and they are lost, the school issues a formal apology and grants "Benefit of Doubt" marks (typically average of other subjects or the class average for that subject, whichever is higher).

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `reval_window_start_day` | 7 | Days after result publication to start accepting |
| `reval_window_end_day` | 21 | Last day to accept applications |
| `reval_retotaling_fee` | Rs. 200 | Fee per subject for retotaling |
| `reval_full_reval_fee` | Rs. 500 | Fee per subject for full revaluation |
| `reval_photocopy_fee` | Rs. 100 | Fee per subject for answer sheet photocopy |
| `reval_refund_threshold_pct` | 10% | Min % change in marks for fee refund |
| `reval_take_higher_policy` | `true` | Retain higher marks if reval gives lower? |
| `reval_third_evaluator_trigger_pct` | 15% | Discrepancy % between original and reval to trigger moderator |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

# OUTSTANDING DUES & DEFAULTER TRACKING

**Module:** Fee Management  
**Submodule Code:** FEE-DEBT-005  
**Category:** Financial Control  
**Priority:** Critical (P0)  
**Owners:** Accounts Manager, Principal

---

## OVERVIEW

The Outstanding Dues & Defaulter Tracking submodule is the enforcement arm of the Fee Management system. It monitors student ledgers to identify overdue payments, categorizes defaulters based on severity, and triggers automated follow-up actions. It also manages service blocking (e.g., withholding admit cards, blocking portal access) to incentivize timely payment.

### Purpose

To maximize collection efficiency and minimize bad debts. It provides the administration with a real-time "Risk View" of the school's finances and automates the uncomfortable process of following up with parents for payments.

### Scope

-   **Defaulter Classification:** Automatic categorization (e.g., Soft Defaulter vs. Hard Defaulter).
-   **Aging Analysis:** Report showing dues by age (0-30 days, 30-60 days, >90 days).
-   **Automated Reminders:** SMS/Email/IVR blasts to parents of defaulters.
-   **Service Restriction:** Auto-blocking of non-essential services (Library, Transport) for chronic non-payers.
-   **Promise-to-Pay Tracking:** Recording parent commitments and following up.
-   **Late Fee Calculation:** Daily/Monthly penalty application.

---

## KEY FEATURES

### 1. Dynamic Defaulter Lists

**Feature Description:**
Real-time generation of defaulter lists based on configurable thresholds.

**Logic:**
*   **Threshold:** "Show students with > ₹500 outstanding for > 7 days".
*   **Risk Scores:** Assigns a risk score to parents based on payment history (e.g., "Frequent Late Payer").
*   **Drill-Down:** Accounts team can click on a "Grade 5" summary to see the list of 12 students owing ₹1.2 Lakhs.

### 2. Automated Dunning (Reminders)

**Feature Description:**
A multi-stage communication workflow to nudge parents.

**Workflow:**
1.  **D-3 (Due Date minus 3):** Gentle Reminder ("Your fee is due soon").
2.  **D+1 (Day after Due Date):** Missed Payment Notification.
3.  **D+7:** Warning: "Late fees will apply".
4.  **D+15:** Escalation: "Administrative action may be initiated".
5.  **D+30:** Notice: "Service suspension warning".

### 3. Service Blocking Integration

**Feature Description:**
Integrates with other ERP modules to enforce compliance digitally.
*   **LMS/Portal:** Restrict access to Report Cards or Online Classes.
*   **Transport:** Remove student name from the "Bus Boarding List" manifest.
*   **Exam:** Block generation of "Hall Ticket / Admit Card" until 'No Dues' certificate is generated.

### 4. Late Fine Engine

**Feature Description:**
Mathematically calculates penalties to discourage delay.
*   **Flat Fee:** ₹50 per day.
*   **Percentage:** 2% interest per month on outstanding principal.
*   **Capping:** "Maximum fine cannot exceed ₹2,000".
*   **Grace Period:** "No fine if paid within 5 days of due date".

---

## DATABASE SCHEMA

### 1. Defaulter Records (`fee_defaulter_snapshots`)
Daily/Weekly snapshots to track trends in outstanding dues.

```sql
CREATE TABLE fee_defaulter_snapshots (
    snapshot_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    snapshot_date DATE DEFAULT CURRENT_DATE,
    
    student_id INT NOT NULL,
    total_due DECIMAL(12,2),
    days_overdue INT,
    
    risk_category ENUM('LOW', 'MEDIUM', 'HIGH', 'CRITICAL'),
    last_payment_date DATE,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 2. Follow-Up Logs (`fee_followup_logs`)
CRM-style history of interactions with parents regarding dues.

```sql
CREATE TABLE fee_followup_logs (
    log_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    
    interaction_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    interaction_type ENUM('SMS', 'EMAIL', 'CALL', 'MEETING'),
    
    staff_id INT, -- Who made the call
    
    outcome ENUM('NO_ANSWER', 'PROMISE_TO_PAY', 'DISPUTE_RAISED', 'REFUSAL'),
    promise_date DATE, -- "Will pay by Monday"
    remarks TEXT,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 3. Service Blocks (`fee_service_blocks`)
Active restrictions on students.

```sql
CREATE TABLE fee_service_blocks (
    block_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    
    service_type ENUM('PORTAL_ACCESS', 'EXAM_ADMIT_CARD', 'LIBRARY', 'TRANSPORT', 'RERORT_CARD'),
    
    blocked_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    blocked_by INT, -- System or Admin
    reason TEXT,
    
    is_active BOOLEAN DEFAULT TRUE,
    auto_release_on_payment BOOLEAN DEFAULT TRUE,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## BUSINESS RULES

### Rule 1: Allocation of Payment to Fine
*   Any payment received from a defaulter is **first** adjusted against "Late Fine" and **then** against "Tuition Fee".
*   This prevents parents from paying only the tuition and ignoring the penalty.

### Rule 2: Force Majeure / Waiver
*   Principal has the authority to waive late fees for genuine cases (Medical emergency, etc.).
*   This requires a "Waive Off" transaction with a mandatory comment explaining the reason for audit purposes.

### Rule 3: Admit Card Hold
*   System creates a "Hold List" for exams 15 days prior.
*   If Balance > ₹500, Student is added to Hold List.
*   If Paid, student is automatically removed from Hold List within 15 minutes.

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Examination Module:** API `check_no_dues(student_id)` returns boolean. If False, Exam Hall Ticket download is disabled.
*   **To Parent Portal:** Shows a red banner "Outstanding Dues: ₹XYZ" on the dashboard.

### Inbound Relationships
*   **From Payment Collection:** When a Receipt is generated, the system checks if the student was in the "Defaulter List". If yes, it re-evaluates and potentially clears the Service Block.

---

## USER WORKFLOWS

### Workflow 1: Managing the "Call List"
**Actor:** Fee Clerk

1.  **Morning Routine:** Log in and open "Follow-up Dashboard".
2.  **Filter:** Select "Due > ₹10,000" and "Days Overdue > 30".
3.  **List:** System shows 15 parents to call.
4.  **Action:** Call Parent A. Parent says "Will pay on Saturday".
5.  **Log:** Click "Log Interaction" -> Type "Promise to Pay" -> Set Date "Saturday".
6.  **Snooze:** Alert snoozed until Sunday. If no payment by Sunday, alert reappears with "Promise Broken" tag.

### Workflow 2: Bulk Late Fee Application
**Actor:** System Scheduler (Cron)

1.  **Time:** 00:01 AM on 11th of the Month (Grace period ended 10th).
2.  **Scan:** Identify 400 invoices that are unpaid.
3.  **Calculate:** Apply ₹100 Fine to each.
4.  **Debit:** Post ₹100 debit entry to 400 student ledgers.
5.  **Notify:** Send SMS: "Late fee of ₹100 applied due to non-payment."

---

## REAL-WORLD SCENARIOS

### Scenario A: The "Disputed" Bill
*   **Context:** Parent refuses to pay Transport fee saying "My child didn't use the bus in January".
*   **Action:** Clerk marks invoice as "Disputed".
*   **Logic:**
    *   System **pauses** automated reminders/harassment for this specific item.
    *   Admin investigates.
    *   If valid: Credit Note issued.
    *   If invalid: Dispute rejected, payment demanded.

### Scenario B: End of Year Clearance
*   **Context:** Finals are approaching.
*   **Action:** School runs "Zero Dues" campaign.
*   **Tool:** "Year-End Defaulter Report".
*   **Result:** 98% collection efficiency achieved by blocking Report Card access for the remaining 2%.

---

**Status:** Production-Ready Documentation  

## EDGE CASES

### Edge Case 1: Student with Partial Scholarship Defaulting
*   **Scenario:** Student has a 50% scholarship and is supposed to pay the remaining 50%. The parent defaults on the 50%.
*   **Resolution:** The system correctly identifies the defaulter as owing only the net amount (not the gross). The dunning message reads "Outstanding Rs. 15,000" (not Rs. 30,000). The scholarship is not revoked due to the default; it's the parent's financial obligation that is overdue, not the student's academic entitlement.

### Edge Case 2: Deceased Parent / Guardian
*   **Scenario:** The primary fee-paying parent passes away. The student's account shows large outstanding dues.
*   **Resolution:** The system supports a compassionate "Hold" status. The Admin marks the account as `COMPASSIONATE_HOLD` which:
    1.  Freezes all automated dunning SMS/Emails.
    2.  Suspends late fee accrual.
    3.  Flags the case for the Trust Board's "Financial Aid Committee" to decide on a waiver or alternative arrangement.

### Edge Case 3: Sibling Cross-Blocking
*   **Scenario:** Parent has 2 children. Child A's fee is fully paid. Child B's fee is overdue. Should Child A's report card also be blocked?
*   **Resolution:** Configurable policy (`sibling_cross_block = true/false`).
    *   If `true`: Both children's non-essential services are blocked until ALL sibling dues are cleared.
    *   If `false`: Only the specific child with dues is affected.

### Edge Case 4: Legal Notice Scenarios
*   **Scenario:** Parent owes Rs. 2 Lakhs for over 6 months. All reminders exhausted.
*   **Resolution:** System generates a "Final Legal Notice Draft" (pre-approved template by school's lawyer) with all financial details auto-populated. The notice is sent via Registered Post (tracked). The `student_dues` record is flagged as `LEGAL_ESCALATION`. A separate `legal_cases` table tracks the outcome.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `defaulter_grace_period_days` | 15 | Days after due date before marking as defaulter |
| `late_fee_type` | `FLAT` | Options: `FLAT`, `PERCENTAGE`, `COMPOUND` |
| `late_fee_amount` | Rs. 50/day | Daily late fee (if type is FLAT) |
| `late_fee_max_cap` | Rs. 5,000 | Maximum late fee per invoice |
| `dunning_reminder_schedule` | `[7, 15, 30]` | Days after due date to send reminders |
| `service_block_threshold_days` | 30 | Days overdue before blocking report card/TC |
| `sibling_cross_block` | `false` | Block one sibling for another's dues? |
| `legal_escalation_threshold` | Rs. 50,000 | Minimum outstanding for legal notice generation |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

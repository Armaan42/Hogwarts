# OUTSTANDING DUES & DEFAULTER TRACKING

**Module:** Fee Management  
**Submodule Code:** FEE-DUES-005  
**Category:** Financial Core  
**Priority:** Critical (P0)

## Overview

Monitors unpaid fees, tracks due dates, categorizes defaulters by severity, automates reminders, and manages consequences of non-payment.

## Purpose

Ensure timely fee collection by identifying defaulters early, automating escalation, and implementing appropriate measures to recover outstanding dues.

## Key Features

### 5.1 Outstanding Calculation

**Real-time Balance:**
```
Total Invoiced (YTD): ₹1,20,000
Total Paid: ₹80,000
Advance: ₹5,000
───────────────────────────────
Current Outstanding: ₹35,000
```

### 5.2 Due Date Tracking

**Invoice Aging:**
- 0-15 days: Not overdue (grace period)
- 16-30 days: Overdue (reminder sent)
- 31-60 days: Seriously overdue (escalation)
- 60+ days: Chronic defaulter (final warning)

### 5.3 Defaulter Categories

**Green (Current):** No outstanding, 1,500 students (75%)
**Yellow (Warning):** ₹1-20K outstanding, 1-30 days overdue, 350 students (17.5%)
**Orange (Escalation):** ₹20-50K outstanding, 31-60 days overdue, 100 students (5%)
**Red (Critical):** >₹50K outstanding, >60 days overdue, 50 students (2.5%)

### 5.4 Defaulter Actions

**Automated Reminders:**
- Due date -3 days: "Reminder: Fee due in 3 days"
- Due date: "Fee due today"
- Due date +7 days: "Fee overdue by 7 days"
- Due date +15 days: "Urgent: Fee overdue by 15 days"
- Due date +30 days: "Final reminder"

**Manual Interventions:**
- Phone call from accounts
- Physical reminder letter
- Parent-principal meeting
- Fee payment plan offered

**Penalties:**
- Late fee: ₹100 per week after 15 days
- Maximum late fee: ₹1,000

### 5.5 Consequences of Default

**Soft:** Defaulter list (internal), Reminder messages
**Medium:** Library suspended, Optional activities barred, Bus service suspended
**Strict:** Barred from exams, Name struck off rolls, TC withheld

### 5.6 Fee Payment Plans

**For Financial Hardship:**
- Outstanding: ₹50,000
- Plan: ₹10,000/month for 5 months
- Interest: Waived
- Agreement signed

## Data Fields

```
outstanding_id (PK)
student_id (FK)
total_invoiced
total_paid
advance_amount
current_outstanding
oldest_unpaid_invoice_date
days_overdue
defaulter_category (GREEN/YELLOW/ORANGE/RED)
last_reminder_sent
reminder_count
payment_plan_id (FK - if applicable)
status
```

## Business Logic

```
FUNCTION calculate_outstanding(student):
  total_invoiced = SUM(invoices.total_amount WHERE student_id = student.id)
  total_paid = SUM(payments.amount WHERE student_id = student.id)
  advance = student.advance_payment
  
  outstanding = total_invoiced - total_paid - advance
  
  // Categorize defaulter
  oldest_invoice = GET oldest_unpaid_invoice(student)
  days_overdue = DAYS_BETWEEN(oldest_invoice.due_date, TODAY)
  
  IF outstanding = 0:
    category = "GREEN"
  ELSE IF outstanding <= 20000 AND days_overdue <= 30:
    category = "YELLOW"
  ELSE IF outstanding <= 50000 AND days_overdue <= 60:
    category = "ORANGE"
  ELSE:
    category = "RED"
  END IF
  
  RETURN {outstanding, days_overdue, category}
END FUNCTION
```

## Integration Points

### Connects TO:
1. **Communication Module** - Send reminders
2. **Library Module** - Suspend library access
3. **Transport Module** - Suspend bus service
4. **Exam Module** - Bar from exams

### Receives FROM:
1. **Invoice Module** - Total invoiced amount
2. **Payment Module** - Total paid amount

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

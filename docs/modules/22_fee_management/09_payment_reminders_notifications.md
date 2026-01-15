# PAYMENT REMINDERS & NOTIFICATIONS

**Module:** Fee Management  
**Submodule Code:** FEE-NOTIFY-009  
**Category:** Communication  
**Priority:** High (P1)

## Overview

Automated communication for fee payments including pre-due reminders, post-due escalations, payment confirmations, and multi-channel delivery.

## Key Features

### Reminder Schedule

**Pre-due:**
- 7 days before: "Fee due on July 15, amount ₹28,500"
- 3 days before: "Reminder: Fee due in 3 days"
- 1 day before: "Final reminder: Fee due tomorrow"

**Post-due:**
- Due date +1: "Fee was due yesterday"
- Due date +7: "Fee overdue by 1 week"
- Due date +15: "Urgent: Grace period ending"
- Due date +30: "Serious: Fee overdue by 1 month"

### Communication Channels

**SMS:** Short message + payment link, 98% delivery
**Email:** Detailed with invoice PDF, 95% delivery
**WhatsApp:** Interactive with payment button
**Push Notification:** Parent app, real-time
**In-app Alert:** Banner when parent logs in

### Notification Preferences
- Parent chooses channels
- Frequency settings
- Quiet hours (no SMS after 9 PM)
- Opt-out option (with acknowledgment)

### Payment Confirmation
- Instant notification on payment
- "Payment ₹28,500 received. Receipt: RCT/2024/001234"
- Receipt PDF attached

### Escalation Notifications
- To class teacher: "Student fee overdue 30 days"
- To principal: "50 students overdue >60 days"
- To management: Monthly summary

## Data Fields
```
notification_id (PK)
student_id (FK)
notification_type (REMINDER/CONFIRMATION/ESCALATION)
channel (SMS/EMAIL/WHATSAPP/PUSH)
message_content
sent_date
delivery_status
read_status
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

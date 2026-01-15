# FEE MANAGEMENT MODULE - COMPLETE DEPENDENCY ANALYSIS

## 🎯 MODULE OVERVIEW

**Name:** Fee Management Module  
**Role:** Financial Operations Hub - Revenue & Payment Control Center  
**Type:** Core Financial Module  
**Dependencies:** 30+ modules depend on fee status for service delivery  

**Primary Functions:**
- Fee Structure Definition (grade-wise, category-wise)
- Invoice Generation & Payment Processing
- Scholarship & Discount Management
- Payment Tracking & Reconciliation
- Defaulter Management & Collections
- Financial Reporting & Analytics
- Online Payment Gateway Integration
- Fee Receipts & Refunds
- Installment Plans & Payment Schedules
- Late Fee Calculation & Penalties

---

## 📤 OUTBOUND CONNECTIONS (Fee Management → Other Modules)

### 1. TO STUDENT MANAGEMENT MODULE

**WHY This Connection Exists:**
Every student has a unique fee structure based on their profile (grade, category, scholarships, siblings). Fee status determines access to various student services (exams, certificates, transport).

**DATA FLOW:**
- **Fee Structure Assignment:**
  - Student ID → Individualized fee calculation
  - Grade/Class → Base fee amount
  - Category (Regular/Scholarship/Staff Ward/EWS/NRI) → Discount/premium
  - Sibling count → Sibling discount percentage
  - Enrollment date → Pro-rata calculation for mid-year admissions
- **Payment Status Updates:**
  - Total fees due
  - Amount paid
  - Outstanding balance
  - Payment history (all transactions)
  - Defaulter flag (if overdue > 30 days)
- **Service Restriction Flags:**
  - Exam admit card blocked (if dues > ₹50,000)
  - Transfer certificate blocked (if any outstanding)
  - Library borrowing blocked (if dues > ₹20,000)
  - Transport service suspended (if transport fee unpaid for 2 months)

**TRIGGER EVENT:**
- **Admission:** First fee invoice generated
- **Payment Received:** Outstanding balance updated, services unblocked
- **Payment Overdue:** Defaulter flag set, reminder sent
- **Grade Promotion:** Fee structure updated for new grade
- **Scholarship Awarded:** Discount applied, invoice regenerated
- **Sibling Admission:** Existing sibling's fee recalculated with discount

**IMPACT:**
- **Individualized Billing:**
  - Student A (Grade 6, Regular, No siblings): ₹1,20,000/year
  - Student B (Grade 6, Regular, 2nd sibling): ₹1,08,000/year (10% discount)
  - Student C (Grade 6, 50% scholarship): ₹60,000/year
  - Student D (Grade 6, Staff ward): ₹0/year (100% waiver)
- **Service Access Control:**
  - Rohan (₹60,000 dues, overdue 45 days): Exam admit card blocked, parent receives notice
  - After payment: Services restored within 2 hours
- **Real-time Status Sync:**
  - Payment made at 2 PM → Student record updated by 2:05 PM
  - Exam department sees updated status, unblocks admit card

**BUSINESS LOGIC:**
```
FUNCTION calculate_student_fee(student):
  base_fee = GET fee_structure WHERE grade = student.grade
  
  // Apply scholarship
  IF student.scholarship_percentage > 0:
    base_fee = base_fee * (1 - student.scholarship_percentage/100)
  END IF
  
  // Apply sibling discount
  siblings = COUNT active_siblings(student)
  IF siblings = 1:
    base_fee = base_fee * 0.90  // 10% off for 2nd child
  ELSE IF siblings = 2:
    base_fee = base_fee * 0.85  // 15% off for 3rd child
  ELSE IF siblings >= 3:
    base_fee = base_fee * 0.80  // 20% off for 4th+ child
  END IF
  
  // Apply category adjustments
  IF student.category = "STAFF_WARD":
    base_fee = base_fee * 0.5  // 50% discount
  ELSE IF student.category = "NRI":
    base_fee = base_fee * 1.5  // 50% premium
  ELSE IF student.category = "EWS":
    base_fee = base_fee * 0.25  // 75% discount (govt quota)
  END IF
  
  // Pro-rata for mid-year admission
  IF student.admission_month > 4:  // After April (new session starts April)
    months_remaining = 12 - student.admission_month
    base_fee = (base_fee / 12) * months_remaining
  END IF
  
  RETURN base_fee
END FUNCTION

FUNCTION check_service_eligibility(student, service):
  outstanding = GET student.total_outstanding
  overdue_days = GET student.longest_overdue_invoice.days
  
  IF service = "EXAM_ADMIT_CARD":
    IF outstanding > 50000:
      RETURN "BLOCKED: Clear dues above ₹50,000"
    END IF
  ELSE IF service = "TRANSFER_CERTIFICATE":
    IF outstanding > 0:
      RETURN "BLOCKED: Clear all outstanding dues"
    END IF
  ELSE IF service = "LIBRARY_BORROWING":
    IF outstanding > 20000:
      RETURN "BLOCKED: Clear dues above ₹20,000"
    END IF
  ELSE IF service = "TRANSPORT":
    IF overdue_days > 60:
      RETURN "SUSPENDED: Transport fee overdue 60+ days"
    END IF
  END IF
  
  RETURN "ALLOWED"
END FUNCTION
```

**EXAMPLE:**
- **Sharma Family (3 children):**
  - Aarav (Grade 10): Base ₹1,50,000 → Final ₹1,50,000 (1st child, no discount)
  - Ananya (Grade 7): Base ₹1,20,000 → Final ₹1,08,000 (2nd child, 10% off)
  - Arnav (Grade 4): Base ₹1,00,000 → Final ₹85,000 (3rd child, 15% off)
  - **Total Family Fee:** ₹3,43,000/year (saved ₹27,000 via sibling discounts)
- **Mid-Year Admission:**
  - Riya joins Grade 8 in September (5 months into session)
  - Base fee: ₹1,30,000/year
  - Pro-rata: (₹1,30,000 / 12) × 7 months = ₹75,833
- **Service Blocking:**
  - Rohan (Grade 9) has ₹65,000 outstanding, overdue 50 days
  - Tries to download exam admit card → System blocks: "Outstanding dues ₹65,000. Pay minimum ₹15,000 to unblock."
  - Father pays ₹20,000 online at 3 PM
  - By 3:10 PM: Outstanding reduced to ₹45,000, admit card unblocked automatically

---

### 2. TO PARENT ENGAGEMENT PORTAL

**WHY This Connection Exists:**
Parents need complete transparency on fee structure, payment history, outstanding dues, and ability to pay online. Fee-related queries are the #1 reason parents contact school office.

**DATA FLOW:**
- **Fee Dashboard:**
  - Total annual fee breakdown (tuition, transport, activities, books, uniform)
  - Payment schedule (quarterly/monthly installments)
  - Amount paid to date
  - Outstanding balance
  - Next payment due date
  - Late fee penalties (if any)
- **Payment History:**
  - All receipts downloadable (PDF)
  - Payment mode (cash/cheque/online/UPI)
  - Transaction IDs for online payments
  - Date and time of each payment
- **Online Payment:**
  - Pay full/partial amount
  - Multiple payment modes (credit card, debit card, UPI, net banking, wallets)
  - Instant receipt generation
  - Auto-update to student record
- **Alerts & Reminders:**
  - Payment due in 7 days
  - Payment overdue
  - Late fee added
  - Scholarship awarded

**TRIGGER EVENT:**
- **Parent Login:** Fee dashboard displayed
- **Payment Due Date Approaching:** Reminder sent 7 days, 3 days, 1 day before
- **Payment Overdue:** Alert sent on due date + 1 day, + 7 days, + 15 days
- **Payment Received:** Instant confirmation notification
- **Fee Structure Change:** Notification sent (scholarship awarded, sibling discount applied)

**IMPACT:**
- **24/7 Fee Transparency:**
  - Parent logs in at 11 PM, sees exact outstanding: ₹45,000
  - Breakdown: Tuition ₹30,000, Transport ₹10,000, Activities ₹5,000
  - Next due date: March 31
- **Instant Online Payment:**
  - Parent pays ₹45,000 via UPI at 11:15 PM
  - Receipt generated immediately with transaction ID
  - Outstanding updated to ₹0 by 11:16 PM
  - No need to visit school office
- **Reduced Office Queries:**
  - Before portal: 50 fee queries/day to office
  - After portal: 5 queries/day (95% reduction)
- **Payment Compliance:**
  - Automated reminders increase on-time payments by 40%
  - Late payment rate drops from 25% to 8%

**BUSINESS LOGIC:**
```
FUNCTION parent_fee_dashboard(parent):
  children = GET students WHERE parent_id = parent.id
  
  FOR each child:
    fee_summary = {
      student: child.name,
      grade: child.grade,
      annual_fee: child.total_annual_fee,
      fee_breakdown: {
        tuition: child.tuition_fee,
        transport: child.transport_fee,
        activities: child.activity_fee,
        books: child.book_fee,
        uniform: child.uniform_fee,
        exam: child.exam_fee
      },
      payment_schedule: GET installments WHERE student = child,
      paid_to_date: SUM(payments WHERE student = child),
      outstanding: child.total_fee - paid_to_date,
      next_due_date: GET next_installment.due_date,
      next_due_amount: GET next_installment.amount,
      late_fees: SUM(late_fees WHERE student = child),
      payment_history: GET all_payments(child, last_12_months)
    }
    
    dashboard.add(fee_summary)
  END FOR
  
  RETURN dashboard
END FUNCTION

FUNCTION process_online_payment(parent, student, amount, payment_mode):
  // Initiate payment gateway
  transaction = PAYMENT_GATEWAY.initiate(amount, payment_mode)
  
  IF transaction.status = "SUCCESS":
    // Record payment
    payment = CREATE_PAYMENT_RECORD
    payment.student = student
    payment.amount = amount
    payment.mode = payment_mode
    payment.transaction_id = transaction.id
    payment.timestamp = NOW
    payment.received_by = "ONLINE_PORTAL"
    
    // Update outstanding
    student.outstanding -= amount
    
    // Allocate to pending invoices (FIFO)
    pending_invoices = GET invoices WHERE student = student AND status = "UNPAID" ORDER BY due_date
    remaining_amount = amount
    
    FOR each invoice IN pending_invoices:
      IF remaining_amount >= invoice.amount:
        invoice.status = "PAID"
        invoice.paid_date = NOW
        remaining_amount -= invoice.amount
      ELSE:
        invoice.partial_payment += remaining_amount
        remaining_amount = 0
        BREAK
      END IF
    END FOR
    
    // Generate receipt
    receipt = GENERATE_RECEIPT(payment)
    
    // Send confirmation
    SEND EMAIL(parent.email, receipt.pdf, "Payment successful: ₹{amount}")
    SEND SMS(parent.mobile, "Fee payment ₹{amount} received. Receipt: {receipt.number}")
    
    // Unblock services if applicable
    IF student.outstanding < service_block_threshold:
      UNBLOCK_SERVICES(student)
    END IF
    
    RETURN {status: "SUCCESS", receipt: receipt}
  ELSE:
    RETURN {status: "FAILED", error: transaction.error}
  END IF
END FUNCTION
```

**EXAMPLE:**
- **Mr. Sharma logs into parent portal at 8 PM:**
  - Sees 2 children: Aarav (Grade 10), Ananya (Grade 7)
  - **Aarav's Fee Status:**
    - Annual Fee: ₹1,50,000
    - Paid: ₹1,00,000 (April ₹50,000, July ₹50,000)
    - Outstanding: ₹50,000
    - Next Due: October 15 (₹50,000)
    - Days Until Due: 5 days
  - **Ananya's Fee Status:**
    - Annual Fee: ₹1,08,000 (with 10% sibling discount)
    - Paid: ₹1,08,000 (Fully paid)
    - Outstanding: ₹0
    - Status: ✅ Paid in Full
- **Mr. Sharma pays Aarav's ₹50,000 via UPI:**
  - Clicks "Pay Now" → Selects UPI → Enters amount ₹50,000
  - UPI app opens → Confirms payment
  - Transaction successful at 8:12 PM
  - Receipt #FEE/2024/10234 generated instantly
  - Email + SMS confirmation sent
  - Outstanding updated to ₹0
  - Aarav's exam admit card (previously blocked) auto-unblocked

---

### 3. TO ADMISSIONS & CRM MODULE

**WHY This Connection Exists:**
Admission fee is the first financial transaction. Fee payment confirms seat acceptance. Admission deposits and registration fees must be tracked and adjusted against annual fees.

**DATA FLOW:**
- **Admission Fee Structure:**
  - Registration fee (non-refundable): ₹5,000
  - Admission fee (one-time): ₹25,000
  - Security deposit (refundable on leaving): ₹15,000
  - First installment (tuition): ₹30,000
  - **Total at admission:** ₹75,000
- **Payment Status to CRM:**
  - Prospect → Applicant (after registration fee paid)
  - Applicant → Admitted (after admission fee paid)
  - Admitted → Enrolled (after first installment paid)
- **Fee Waiver for Admission:**
  - Scholarship students: Admission fee waived
  - Staff wards: 50% admission fee waived
  - Sibling: ₹5,000 discount on admission fee

**TRIGGER EVENT:**
- **Registration Fee Paid:** Prospect becomes Applicant, admission test scheduled
- **Admission Fee Paid:** Seat confirmed, student record created
- **First Installment Paid:** Enrollment complete, section assigned
- **Payment Deadline Missed:** Seat released, offered to waitlist

**IMPACT:**
- **Seat Confirmation:**
  - Priya's parents pay ₹75,000 admission package on June 1
  - Seat confirmed in Grade 6
  - Student ID generated: 2024/06/0234
  - Welcome email sent with fee schedule
- **Waitlist Management:**
  - Rohan offered seat, deadline June 10
  - Payment not received by June 10
  - Seat released at 11:59 PM June 10
  - Next waitlist candidate (Kavya) offered seat on June 11
- **Sibling Benefit:**
  - Aarav already studying in Grade 8
  - Sister Ananya gets admission to Grade 6
  - Admission fee: ₹25,000 - ₹5,000 (sibling discount) = ₹20,000

**BUSINESS LOGIC:**
```
FUNCTION process_admission_payment(applicant, payment_type, amount):
  IF payment_type = "REGISTRATION_FEE":
    IF amount >= 5000:
      applicant.status = "REGISTERED"
      SCHEDULE admission_test(applicant)
      SEND confirmation("Registration successful, test on {date}")
    END IF
    
  ELSE IF payment_type = "ADMISSION_FEE":
    required_amount = 25000
    
    // Apply sibling discount
    IF has_sibling_in_school(applicant):
      required_amount -= 5000
    END IF
    
    // Apply scholarship waiver
    IF applicant.scholarship_awarded:
      required_amount = 0
    END IF
    
    IF amount >= required_amount:
      applicant.status = "ADMITTED"
      CREATE student_record(applicant)
      SEND welcome_package
      GENERATE fee_schedule
    END IF
    
  ELSE IF payment_type = "FIRST_INSTALLMENT":
    IF amount >= 30000:
      student.status = "ENROLLED"
      ASSIGN section(student)
      ACTIVATE parent_portal
      SEND "Enrollment complete, classes start {date}"
    END IF
  END IF
END FUNCTION
```

**EXAMPLE:**
- **Admission Timeline for Priya:**
  - **March 15:** Enquiry, registration fee ₹5,000 paid → Status: Registered
  - **March 20:** Admission test (scored 85%)
  - **March 25:** Admission offered, deadline April 5
  - **April 2:** Admission fee ₹25,000 + Security deposit ₹15,000 + First installment ₹30,000 = ₹70,000 paid
  - **April 2 (same day):** Student ID created, section assigned (6A), parent portal activated
  - **April 3:** Welcome email with fee schedule: Remaining ₹90,000 in 3 installments (July, Oct, Jan)

---

### 4. TO TRANSPORT MANAGEMENT MODULE

**WHY This Connection Exists:**
Transport fee is separate from tuition fee. Students enrolled in transport must pay transport charges. Non-payment leads to transport service suspension.

**DATA FLOW:**
- **Transport Fee Calculation:**
  - Based on distance/route
  - Route 1 (0-5 km): ₹12,000/year
  - Route 2 (5-10 km): ₹18,000/year
  - Route 3 (10-15 km): ₹24,000/year
  - Route 4 (15+ km): ₹30,000/year
- **Transport Enrollment Status:**
  - Enrolled → Transport fee added to invoice
  - Withdrawn → Pro-rata refund calculated
  - Temporary (occasional use): Per-trip charges
- **Payment Status:**
  - Paid → Bus access granted
  - Overdue 30 days → Warning sent
  - Overdue 60 days → Transport suspended

**TRIGGER EVENT:**
- **Transport Enrollment:** Transport fee added to student invoice
- **Transport Withdrawal:** Pro-rata refund processed
- **Payment Overdue 60 days:** Transport access suspended
- **Payment Received:** Transport access restored

**IMPACT:**
- **Route-based Billing:**
  - Aarav (Route 2, 8 km): ₹18,000/year transport fee
  - Ananya (Route 3, 12 km): ₹24,000/year transport fee
- **Service Suspension:**
  - Rohan's transport fee overdue 65 days
  - System blocks RFID card, cannot board bus
  - Parent receives SMS: "Transport suspended due to non-payment. Clear ₹12,000 to restore."
  - Payment made → RFID reactivated within 1 hour
- **Mid-Year Changes:**
  - Priya withdraws from transport in October (6 months used)
  - Transport fee: ₹18,000/year
  - Used: 6 months = ₹9,000
  - Refund: ₹9,000 (credited to fee account)

**BUSINESS LOGIC:**
```
FUNCTION calculate_transport_fee(student):
  IF student.transport_enrolled = FALSE:
    RETURN 0
  END IF
  
  route = GET student.transport_route
  annual_fee = GET route.annual_fee
  
  // Pro-rata if enrolled mid-year
  IF student.transport_enrollment_month > 4:
    months_remaining = 12 - student.transport_enrollment_month
    annual_fee = (annual_fee / 12) * months_remaining
  END IF
  
  RETURN annual_fee
END FUNCTION

FUNCTION check_transport_access(student):
  IF student.transport_enrolled = FALSE:
    RETURN "NOT_ENROLLED"
  END IF
  
  transport_dues = GET outstanding WHERE fee_type = "TRANSPORT"
  overdue_days = GET transport_invoice.overdue_days
  
  IF overdue_days > 60:
    RETURN "SUSPENDED: Payment overdue 60+ days"
  ELSE IF overdue_days > 30:
    RETURN "WARNING: Payment overdue, suspension in {60 - overdue_days} days"
  ELSE:
    RETURN "ACTIVE"
  END IF
END FUNCTION
```

**EXAMPLE:**
- **Aarav enrolls in transport (Route 2, 8 km):**
  - Annual transport fee: ₹18,000
  - Added to fee invoice (quarterly: ₹4,500 each)
  - Q1 (April): ₹4,500 paid ✓
  - Q2 (July): ₹4,500 paid ✓
  - Q3 (October): Not paid, overdue 35 days
  - System sends warning: "Transport fee overdue, pay within 25 days to avoid suspension"
  - Day 60: Still unpaid
  - Transport suspended, RFID card blocked
  - Aarav cannot board bus on Day 61
  - Parent pays ₹9,000 (Q3 + Q4) on Day 62
  - RFID reactivated same day, can board bus from next day

---

### 5. TO HOSTEL & MESS MANAGEMENT MODULE

**WHY This Connection Exists:**
Hostel and mess fees are significant additional charges for boarding students. Non-payment can lead to hostel eviction or mess service suspension.

**DATA FLOW:**
- **Hostel Fee:**
  - Single room: ₹1,20,000/year
  - Shared room (2 students): ₹80,000/year
  - Shared room (3 students): ₹60,000/year
- **Mess Fee:**
  - Vegetarian: ₹48,000/year
  - Non-vegetarian: ₹60,000/year
  - Special diet (medical): ₹72,000/year
- **Combined Boarding Package:**
  - Hostel (shared) + Mess (veg) = ₹1,08,000/year
  - Discount: 10% on package = ₹97,200/year
- **Payment Status:**
  - Paid → Full hostel/mess access
  - Overdue 30 days → Warning
  - Overdue 60 days → Mess suspended (hostel continues)
  - Overdue 90 days → Hostel eviction notice

**TRIGGER EVENT:**
- **Boarding Enrollment:** Hostel + mess fees added
- **Room Change:** Fee adjusted (single ↔ shared)
- **Dietary Change:** Mess fee adjusted (veg ↔ non-veg)
- **Payment Overdue:** Service suspension warnings
- **Hostel Withdrawal:** Pro-rata refund

**IMPACT:**
- **Boarding Package Billing:**
  - Rohan (Grade 9, boarder): Shared room + Veg mess
  - Annual: ₹80,000 (hostel) + ₹48,000 (mess) = ₹1,28,000
  - With 10% package discount: ₹1,15,200
  - Quarterly: ₹28,800 each
- **Service Suspension:**
  - Priya's mess fee overdue 65 days
  - Mess card blocked, cannot swipe for meals
  - Hostel access continues (separate fee, paid)
  - Must eat outside or pay cash per meal
  - After payment: Mess card reactivated
- **Mid-Year Withdrawal:**
  - Kavya withdraws from hostel in November (7 months used)
  - Hostel fee: ₹80,000/year
  - Used: 7 months = ₹46,667
  - Refund: ₹33,333 (processed in 15 days)

**BUSINESS LOGIC:**
```
FUNCTION calculate_boarding_fee(student):
  IF student.boarding_status != "BOARDER":
    RETURN 0
  END IF
  
  // Hostel fee based on room type
  hostel_fee = GET room_type_fee(student.room_type)
  
  // Mess fee based on dietary preference
  mess_fee = GET mess_fee(student.dietary_preference)
  
  total = hostel_fee + mess_fee
  
  // Package discount
  IF student.enrolled_in_both:
    total = total * 0.90  // 10% discount
  END IF
  
  // Pro-rata if enrolled mid-year
  IF student.boarding_enrollment_month > 4:
    months_remaining = 12 - student.boarding_enrollment_month
    total = (total / 12) * months_remaining
  END IF
  
  RETURN total
END FUNCTION

FUNCTION check_boarding_services(student):
  hostel_dues = GET outstanding WHERE fee_type = "HOSTEL"
  mess_dues = GET outstanding WHERE fee_type = "MESS"
  
  hostel_overdue = hostel_dues.overdue_days
  mess_overdue = mess_dues.overdue_days
  
  services = {hostel: "ACTIVE", mess: "ACTIVE"}
  
  IF mess_overdue > 60:
    services.mess = "SUSPENDED"
  ELSE IF mess_overdue > 30:
    services.mess = "WARNING"
  END IF
  
  IF hostel_overdue > 90:
    services.hostel = "EVICTION_NOTICE"
    NOTIFY warden, principal, parent
  ELSE IF hostel_overdue > 60:
    services.hostel = "FINAL_WARNING"
  END IF
  
  RETURN services
END FUNCTION
```

**EXAMPLE:**
- **Arjun (Grade 8, boarder):**
  - Room: Shared (3 students) = ₹60,000/year
  - Mess: Vegetarian = ₹48,000/year
  - Package total: ₹1,08,000 - 10% = ₹97,200/year
  - Quarterly: ₹24,300
  - **Payment History:**
    - Q1 (April): ₹24,300 paid ✓
    - Q2 (July): ₹24,300 paid ✓
    - Q3 (October): Not paid, overdue 40 days
    - Day 60: Mess card suspended
    - Arjun must pay cash for meals (₹200/day)
    - Day 65: Father pays ₹48,600 (Q3 + Q4)
    - Mess card reactivated same day

---

### 6. TO LIBRARY MANAGEMENT MODULE

**WHY This Connection Exists:**
Library fines (overdue books, lost books) are added to student fee account. Outstanding library fines can block borrowing privileges and other services.

**DATA FLOW:**
- **Library Fine Types:**
  - Overdue fine: ₹2/day per book
  - Lost book: Replacement cost + ₹50 processing fee
  - Damaged book: Repair cost or replacement cost
- **Fine Integration:**
  - Library fine added to fee account
  - Appears in fee invoice as "Library Charges"
  - Must be cleared like any other fee
- **Service Blocking:**
  - Library fine > ₹100: Cannot borrow new books
  - Library fine > ₹500: Added to defaulter list
  - Transfer Certificate: Blocked if any library fine pending

**TRIGGER EVENT:**
- **Book Overdue:** Fine calculated daily, added to account
- **Book Lost:** Replacement cost added to account
- **Fine Paid:** Borrowing privileges restored
- **TC Request:** System checks for pending library fines

**IMPACT:**
- **Overdue Fines:**
  - Aarav borrows book, due March 1
  - Returns March 15 (14 days late)
  - Fine: ₹2 × 14 = ₹28
  - Added to fee account automatically
  - Next fee invoice shows: "Library Fine: ₹28"
- **Lost Book:**
  - Priya loses "Harry Potter" (cost ₹500)
  - System adds: ₹500 + ₹50 processing = ₹550 to fee account
  - Cannot borrow new books until paid
  - After payment: Borrowing restored
- **TC Blocking:**
  - Rohan's family relocating, requests TC
  - System checks: ₹150 library fine pending (3 overdue books)
  - TC blocked: "Clear library dues first"
  - After payment: TC issued

**BUSINESS LOGIC:**
```
FUNCTION calculate_library_fine(student, book, return_date):
  due_date = book.due_date
  days_overdue = return_date - due_date
  
  IF days_overdue > 0:
    fine = days_overdue * 2  // ₹2 per day
    
    // Add to fee account
    ADD_CHARGE(student, "LIBRARY_FINE", fine, "Overdue: {book.title}")
    
    // Block borrowing if total fines > ₹100
    total_fines = SUM library_fines WHERE student = student AND paid = FALSE
    IF total_fines > 100:
      BLOCK_BORROWING(student)
    END IF
  END IF
END FUNCTION

FUNCTION process_lost_book(student, book):
  replacement_cost = book.price
  processing_fee = 50
  total = replacement_cost + processing_fee
  
  ADD_CHARGE(student, "LOST_BOOK", total, "Lost: {book.title}")
  BLOCK_BORROWING(student)
  
  NOTIFY parent("Book lost: {book.title}, charge: ₹{total}")
END FUNCTION
```

**EXAMPLE:**
- **Kavya's Library Fine Journey:**
  - **Week 1:** Borrows 3 books, due in 14 days (March 15)
  - **March 15:** Returns 2 books on time, forgets 1 book
  - **March 20:** Realizes book overdue (5 days), fine = ₹10
  - **March 25:** Still not returned (10 days), fine = ₹20
  - **March 30:** Returns book (15 days late), fine = ₹30
  - Fine added to fee account
  - Next quarterly invoice shows: "Library Fine: ₹30"
  - Kavya pays with regular fee payment
  - Can continue borrowing books

---

### 7. TO EXAM & ASSESSMENT MODULE

**WHY This Connection Exists:**
Exam fees (board exams, competitive exams) are charged separately. Admit cards are blocked for students with outstanding dues above threshold.

**DATA FLOW:**
- **Exam Fee Types:**
  - Internal exams: Included in tuition
  - Board exams (Grade 10, 12): ₹5,000 - ₹10,000
  - Competitive exams (Olympiads): ₹500 - ₹2,000 per exam
  - Revaluation fee: ₹1,000 per subject
- **Admit Card Blocking:**
  - Outstanding > ₹50,000: Admit card blocked
  - Outstanding > ₹1,00,000: Cannot appear for exams
  - Must clear minimum 50% dues to unblock
- **Exam Registration:**
  - Board exam registration requires fee clearance
  - Olympiad registration: Separate payment

**TRIGGER EVENT:**
- **Exam Registration:** Exam fee added to invoice
- **Admit Card Generation:** Fee clearance check
- **Outstanding > Threshold:** Admit card blocked
- **Payment Received:** Admit card unblocked

**IMPACT:**
- **Board Exam Fee:**
  - Priya (Grade 10) registers for CBSE board exams
  - Board exam fee: ₹8,500
  - Added to January invoice
  - Must be paid by February 15 for admit card
- **Admit Card Blocking:**
  - Rohan (Grade 12) has ₹75,000 outstanding
  - Board exams in March
  - Admit card blocked in February
  - Parent pays ₹40,000 (outstanding now ₹35,000)
  - Admit card unblocked, can appear for exams
- **Olympiad Registration:**
  - Aarav wants to register for Math Olympiad
  - Fee: ₹1,500
  - Parent pays online via portal
  - Registration confirmed, study material sent

**BUSINESS LOGIC:**
```
FUNCTION check_admit_card_eligibility(student, exam):
  outstanding = GET student.total_outstanding
  
  IF exam.type = "BOARD_EXAM":
    threshold = 50000
  ELSE IF exam.type = "INTERNAL":
    threshold = 100000
  ELSE:
    threshold = 20000
  END IF
  
  IF outstanding > threshold:
    RETURN {
      eligible: FALSE,
      reason: "Outstanding dues ₹{outstanding}. Clear minimum ₹{outstanding - threshold} to unblock."
    }
  ELSE:
    RETURN {eligible: TRUE}
  END IF
END FUNCTION

FUNCTION register_for_exam(student, exam, exam_fee):
  // Check fee clearance for board exams
  IF exam.type = "BOARD_EXAM":
    IF student.outstanding > 50000:
      RETURN "Error: Clear dues before board exam registration"
    END IF
  END IF
  
  // Add exam fee to invoice
  ADD_CHARGE(student, "EXAM_FEE", exam_fee, exam.name)
  
  // Register student
  REGISTER student FOR exam
  
  NOTIFY parent("Registered for {exam.name}, fee: ₹{exam_fee}")
END FUNCTION
```

**EXAMPLE:**
- **Ananya (Grade 10) - Board Exam Timeline:**
  - **November:** CBSE board exam registration opens
  - **November 15:** School registers Ananya, exam fee ₹8,500 added
  - **December:** Ananya's outstanding: ₹65,000 (tuition + exam fee)
  - **January 15:** Admit card generation starts
  - **January 15:** System blocks Ananya's admit card (outstanding > ₹50,000)
  - **January 20:** Parent receives alert: "Clear ₹15,000 minimum to unblock admit card"
  - **January 25:** Parent pays ₹20,000
  - **January 25:** Outstanding now ₹45,000, admit card auto-unblocked
  - **February 1:** Admit card downloaded, exams in March

---

### 8. TO SCHOLARSHIP & FINANCIAL AID MODULE

**WHY This Connection Exists:**
Scholarships reduce fee burden. Scholarship awards must be reflected in fee invoices. Scholarship compliance (attendance, grades) affects fee calculations.

**DATA FLOW:**
- **Scholarship Types:**
  - Merit scholarship: 25%, 50%, 75%, 100% tuition waiver
  - Sports scholarship: 50% tuition waiver
  - Need-based: Variable (₹10,000 - ₹50,000/year)
  - Government quota (EWS): 75% fee reduction
  - Staff ward: 50-100% waiver
- **Scholarship Application:**
  - Student applies → Reviewed → Approved/Rejected
  - If approved: Discount applied from next invoice
- **Compliance Monitoring:**
  - Merit scholarship: Requires 75%+ attendance, 80%+ grades
  - Sports scholarship: Requires active participation in school team
  - Non-compliance: Scholarship revoked, full fee charged

**TRIGGER EVENT:**
- **Scholarship Awarded:** Fee structure updated, discount applied
- **Scholarship Renewed:** Annual review, continued for next year
- **Scholarship Revoked:** Non-compliance detected, full fee charged
- **Mid-Year Award:** Pro-rata discount from award month

**IMPACT:**
- **Merit Scholarship:**
  - Priya scores 95% in Grade 9 final exams
  - Awarded 50% merit scholarship for Grade 10
  - Grade 10 tuition: ₹1,50,000
  - With scholarship: ₹75,000
  - Savings: ₹75,000/year
- **Scholarship Revocation:**
  - Rohan has 50% sports scholarship
  - Attendance drops to 65% (requirement: 75%+)
  - Scholarship revoked in October
  - Fee recalculated: ₹60,000 → ₹1,20,000
  - Outstanding increased by ₹60,000
  - Parent notified: "Scholarship revoked due to low attendance"
- **Mid-Year Award:**
  - Kavya wins state-level science competition in September
  - Awarded 25% scholarship from October
  - Remaining 6 months: ₹60,000
  - With 25% discount: ₹45,000
  - Savings: ₹15,000

**BUSINESS LOGIC:**
```
FUNCTION apply_scholarship(student, scholarship):
  // Update student profile
  student.scholarship_type = scholarship.type
  student.scholarship_percentage = scholarship.percentage
  student.scholarship_start_date = scholarship.start_date
  student.scholarship_conditions = scholarship.conditions
  
  // Recalculate fees
  new_fee = calculate_student_fee(student)
  
  // Adjust pending invoices
  pending_invoices = GET invoices WHERE student = student AND status = "UNPAID"
  
  FOR each invoice IN pending_invoices:
    IF invoice.due_date >= scholarship.start_date:
      old_amount = invoice.amount
      new_amount = old_amount * (1 - scholarship.percentage/100)
      discount = old_amount - new_amount
      
      invoice.amount = new_amount
      invoice.notes = "Scholarship applied: {scholarship.percentage}% off"
      
      student.outstanding -= discount
    END IF
  END FOR
  
  NOTIFY parent("Scholarship awarded: {scholarship.percentage}% fee waiver")
END FUNCTION

FUNCTION monitor_scholarship_compliance(student):
  IF student.scholarship_percentage = 0:
    RETURN  // No scholarship
  END IF
  
  conditions = student.scholarship_conditions
  compliant = TRUE
  
  // Check attendance
  IF conditions.min_attendance:
    IF student.attendance_percentage < conditions.min_attendance:
      compliant = FALSE
      reason = "Attendance {student.attendance_percentage}% < required {conditions.min_attendance}%"
    END IF
  END IF
  
  // Check grades
  IF conditions.min_grade:
    IF student.average_grade < conditions.min_grade:
      compliant = FALSE
      reason = "Grade {student.average_grade}% < required {conditions.min_grade}%"
    END IF
  END IF
  
  // Check sports participation
  IF conditions.sports_participation:
    IF student.sports_participation_days < conditions.min_days:
      compliant = FALSE
      reason = "Sports participation insufficient"
    END IF
  END IF
  
  IF NOT compliant:
    ALERT("Scholarship at risk: {student.name} - {reason}")
    SEND warning_to_parent
    
    // Grace period: 1 month to improve
    SCHEDULE review(30_days_later)
    
    // If still non-compliant after grace period
    IF still_non_compliant:
      REVOKE_SCHOLARSHIP(student, reason)
    END IF
  END IF
END FUNCTION
```

**EXAMPLE:**
- **Aarav's Scholarship Journey:**
  - **Grade 9:** Scores 92%, awarded 50% merit scholarship for Grade 10
  - **Grade 10 Fee:** ₹1,50,000 → ₹75,000 (with scholarship)
  - **Quarterly:** ₹18,750 each (instead of ₹37,500)
  - **Mid-Year Review (October):**
    - Attendance: 88% ✓ (requirement: 75%+)
    - Grades: 85% ✓ (requirement: 80%+)
    - Scholarship continues
  - **Year-End Review (March):**
    - Final grade: 90%
    - Scholarship renewed for Grade 11
  - **Total Savings over 3 years (Grades 10-12):** ₹2,25,000

---

*[Continuing with remaining connections 9-30...]*

---

## 📥 INBOUND CONNECTIONS (Other Modules → Fee Management)

### FROM STUDENT MANAGEMENT MODULE

**WHY:** Student profile changes affect fee calculations (grade promotion, category changes, sibling additions).

**DATA RECEIVED:**
- Grade promotion (fee structure changes)
- New sibling admission (sibling discount applied)
- Category change (scholarship awarded, staff ward status)
- Status change (withdrawal triggers refund calculation)

**IMPACT:**
- Fee structure auto-updated
- Invoices regenerated
- Discounts applied automatically
- Pro-rata calculations for mid-year changes

**TRIGGER:** Grade promotion, sibling admission, status change

---

### FROM TRANSPORT MANAGEMENT MODULE

**WHY:** Transport enrollment/withdrawal affects transport fee charges.

**DATA RECEIVED:**
- Transport enrollment (add transport fee)
- Transport withdrawal (pro-rata refund)
- Route change (fee adjustment)

**IMPACT:**
- Transport fee added/removed from invoice
- Pro-rata calculations for mid-year changes
- Refunds processed

**TRIGGER:** Transport enrollment change, route change

---

### FROM HOSTEL & MESS MANAGEMENT MODULE

**WHY:** Hostel/mess enrollment affects boarding fees.

**DATA RECEIVED:**
- Hostel enrollment (add hostel fee)
- Mess enrollment (add mess fee)
- Room type change (fee adjustment)
- Dietary preference change (mess fee adjustment)

**IMPACT:**
- Boarding fees added/adjusted
- Package discounts applied
- Pro-rata calculations

**TRIGGER:** Boarding enrollment change, room/diet change

---

### FROM LIBRARY MANAGEMENT MODULE

**WHY:** Library fines must be added to student fee account.

**DATA RECEIVED:**
- Overdue fines (daily calculation)
- Lost book charges
- Damaged book charges

**IMPACT:**
- Fines added to fee account
- Appears in next invoice
- Blocks services if unpaid

**TRIGGER:** Book overdue, book lost/damaged

---

### FROM EXAM & ASSESSMENT MODULE

**WHY:** Exam fees must be charged and collected.

**DATA RECEIVED:**
- Board exam registration (exam fee)
- Competitive exam registration (exam fee)
- Revaluation requests (revaluation fee)

**IMPACT:**
- Exam fees added to invoice
- Payment required for admit card
- Registration confirmed after payment

**TRIGGER:** Exam registration, revaluation request

---

### FROM EVENTS & ACTIVITIES MODULE

**WHY:** Event participation fees, competition fees, trip costs must be charged.

**DATA RECEIVED:**
- Field trip registration (trip cost)
- Competition registration (entry fee)
- Activity enrollment (activity fee)

**IMPACT:**
- Event fees added to invoice
- Payment required for participation
- Consent + payment both needed

**TRIGGER:** Event registration, activity enrollment

---

### FROM CANTEEN & MEAL MANAGEMENT MODULE

**WHY:** Canteen credit top-ups and meal plan charges.

**DATA RECEIVED:**
- Meal plan enrollment (monthly/quarterly charges)
- Canteen credit requests (prepaid balance)
- Negative balance alerts (exceeded credit limit)

**IMPACT:**
- Meal charges added to invoice
- Canteen credit topped up after payment
- Spending limits enforced

**TRIGGER:** Meal plan enrollment, credit top-up request

---

### FROM UNIFORM & BOOKS MODULE

**WHY:** Uniform and textbook purchases charged to fee account.

**DATA RECEIVED:**
- Uniform purchase (itemized costs)
- Textbook purchase (book costs)
- Stationery purchase

**IMPACT:**
- Purchase costs added to invoice
- Items issued after payment
- Bulk orders discounted

**TRIGGER:** Uniform/book purchase

---

### FROM ALUMNI MODULE

**WHY:** Alumni donations, legacy fee benefits.

**DATA RECEIVED:**
- Alumni donations (credited to school fund)
- Legacy student identification (fee benefits)

**IMPACT:**
- Donations recorded and receipted
- Legacy discounts applied to children
- Tax receipts (80G) generated

**TRIGGER:** Donation received, legacy student enrolls

---

### FROM GOVERNMENT COMPLIANCE MODULE

**WHY:** EWS quota fee structures, government scholarship disbursements.

**DATA RECEIVED:**
- EWS quota approval (75% fee reduction)
- Government scholarship approval (amount)
- Compliance audit requirements

**IMPACT:**
- EWS fee structure applied
- Government scholarship credited
- Audit reports generated

**TRIGGER:** EWS approval, scholarship disbursement

---

## 📊 SUMMARY

**Fee Management Module connects to 30+ modules**

**Critical Outbound Dependencies:**
1. Student Management (individualized billing, service blocking)
2. Parent Portal (transparency, online payments)
3. Transport (transport fee, service suspension)
4. Hostel/Mess (boarding fees, service blocking)
5. Exams (admit card blocking, exam fees)
6. Scholarships (discounts, compliance monitoring)

**Critical Inbound Dependencies:**
1. Student Management (profile changes affect fees)
2. Library (fines added to account)
3. Transport/Hostel (enrollment changes)
4. Events (participation fees)
5. Exams (exam fees)

**Financial Flows:**
- **Revenue Streams:** Tuition, Transport, Hostel, Mess, Exams, Events, Library fines
- **Payment Modes:** Cash, Cheque, Online (UPI/Cards/Net Banking), Wallets
- **Collection Efficiency:** 92% (with automated reminders)
- **Average Outstanding:** 8% of annual revenue
- **Payment Compliance:** 85% on-time payments

**Service Integration:**
- **Blocking Thresholds:** Exams (₹50K), TC (₹0), Library (₹20K), Transport (60 days overdue)
- **Auto-unblocking:** Within 2 hours of payment
- **Refund Processing:** 15 days for withdrawals
- **Receipt Generation:** Instant (online), Same day (offline)

**Data Freshness:**
- Real-time: Online payments, service blocking/unblocking
- Hourly: Outstanding calculations, defaulter lists
- Daily: Fine calculations, reminder triggers
- Monthly: Invoice generation, scholarship compliance
- Quarterly: Financial reports, collection analytics
- Annual: Fee structure updates, scholarship renewals

This module is the **"financial backbone"** of the ERP - every service delivery is tied to fee payment status, making it critical for school operations and revenue management.

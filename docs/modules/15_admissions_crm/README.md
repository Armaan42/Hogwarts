# ADMISSIONS & CRM MODULE - COMPLETE DEPENDENCY ANALYSIS

## MODULE OVERVIEW

**Name:** Admissions & CRM Module 
**Role:** Student Recruitment & Enrollment Pipeline - Lead to Student Conversion 
**Type:** Core Business Development & Enrollment Module 
**Dependencies:** 12+ modules rely on admissions data for student onboarding 

**Primary Functions:**
- Lead Generation & Management
- Inquiry Handling & Follow-ups
- Online Application Portal
- Entrance Test Management
- Interview Scheduling & Evaluation
- Admission Decision & Offer Letters
- Fee Payment & Enrollment Confirmation
- Document Verification & Collection
- Waitlist Management
- Sibling & Alumni Preferences
- Admission Analytics & Reporting
- CRM & Parent Relationship Management

---

## OUTBOUND CONNECTIONS (Admissions & CRM → Other Modules)

### 1. TO STUDENT MANAGEMENT MODULE

**WHY This Connection Exists:**
The admissions process converts prospective students (leads) into enrolled students. Once admission is confirmed and fees are paid, all prospect data must flow into the permanent student management system to create the official student record.

**DATA FLOW:**
- **Personal Information:**
 - Full Name (as per birth certificate)
 - Date of Birth
 - Gender
 - Blood Group
 - Aadhaar Number
 - Nationality, Religion, Caste Category
 - Mother Tongue
- **Parent/Guardian Details:**
 - Father's name, occupation, education, annual income
 - Mother's name, occupation, education, annual income
 - Guardian (if applicable)
 - Contact numbers (mobile, WhatsApp, landline)
 - Email addresses
 - Residential address (current & permanent)
- **Previous Academic Records:**
 - Previous school name, board, location
 - Last class attended
 - Transfer Certificate (TC) number and date
 - Academic performance (last year marks/grade)
- **Admission-Specific Data:**
 - Admission number (unique ID)
 - Applied grade/class
 - Admission category (General/Sibling/Alumni/Staff/EWS)
 - Entrance test score
 - Interview evaluation score
 - Admission date
 - Fee structure assigned
- **Medical Information:**
 - Chronic conditions (diabetes, asthma, epilepsy)
 - Allergies (food, medication, environmental)
 - Current medications
 - Emergency medical contact
- **Sibling Information:**
 - Existing siblings in school (names, admission numbers, grades)
 - Sibling relationship mapping
- **Special Requirements:**
 - Transport enrollment request
 - Hostel enrollment request
 - Dietary preferences (for mess)
 - Learning disabilities or special needs

**TRIGGER EVENT:**
- **Admission Confirmed:** Fee payment received, documents verified
- **Enrollment Complete:** Student record creation initiated
- **Section Assignment:** Student allocated to specific section
- **Portal Access:** Parent and student login credentials generated

**IMPACT:**
- **Complete Student Profile Created:**
 - Priya's admission confirmed for Grade 9
 - Admission number: 2024/09/1234 generated
 - Student record created with all admission data
 - Section assigned: 9A (based on entrance test score and availability)
 - Parent portal access granted: login credentials sent via email
 - Student ID card generation initiated
 - School uniform size recorded for procurement
- **Sibling Linking:**
 - Aarav (Grade 8) already enrolled
 - Sister Ananya admitted to Grade 6
 - System links siblings: Aarav ↔ Ananya
 - Both students' profiles updated with sibling information
 - Fee discount automatically applied to both
- **Medical Alert Setup:**
 - Rohan admitted with asthma (exercise-induced)
 - Medical profile flagged: "Asthma - inhaler required"
 - Alerts sent to: Class teacher, Sports teacher, Infirmary nurse
 - Emergency protocol created
 - Inhaler stocked in infirmary
- **Transport & Hostel Enrollment:**
 - Kavya (from different city) requests boarding
 - Hostel room allocated: Girls Hostel A, Room 305, Bed 2
 - Mess enrolled: Vegetarian meal plan
 - Transport not required (boarder)
 - Hostel + Mess fees added to invoice

**BUSINESS LOGIC:**
```
FUNCTION create_student_from_admission(admission):
 // Verify admission prerequisites
 IF admission.fee_payment_status != "PAID":
 RETURN "Error: Fee payment pending"
 END IF
 
 IF admission.document_verification_status != "COMPLETE":
 RETURN "Error: Document verification incomplete"
 END IF
 
 // Generate admission number
 admission_number = GENERATE_ADMISSION_NUMBER(admission.academic_year, admission.grade)
 // Format: YYYY/GG/NNNN (e.g., 2024/09/1234)
 
 // Create student record
 student = CREATE Student
 student.admission_number = admission_number
 student.name = admission.applicant_name
 student.dob = admission.dob
 student.gender = admission.gender
 student.blood_group = admission.blood_group
 student.grade = admission.applied_grade
 student.admission_date = TODAY
 student.status = "ACTIVE"
 student.category = admission.category
 
 // Link parents
 student.father = admission.father_details
 student.mother = admission.mother_details
 student.guardian = admission.guardian_details
 student.emergency_contacts = admission.emergency_contacts
 
 // Previous school records
 student.previous_school = admission.previous_school
 student.tc_number = admission.tc_number
 student.last_grade_attended = admission.last_grade
 
 // Medical information
 student.medical_profile = CREATE MedicalProfile
 student.medical_profile.blood_group = admission.blood_group
 student.medical_profile.chronic_conditions = admission.medical_conditions
 student.medical_profile.allergies = admission.allergies
 student.medical_profile.current_medications = admission.medications
 
 // Admission-specific data
 student.entrance_test_score = admission.entrance_test_score
 student.interview_score = admission.interview_score
 student.admission_category = admission.category
 
 // Assign section
 section = ASSIGN_SECTION(student.grade, admission.entrance_test_score)
 student.section = section
 
 // Link siblings if any
 IF admission.has_siblings:
 FOR each sibling IN admission.sibling_admission_numbers:
 existing_sibling = GET Student WHERE admission_number = sibling
 LINK_SIBLINGS(student, existing_sibling)
 
 // Update fee structure for both (sibling discount)
 RECALCULATE_FEE(student)
 RECALCULATE_FEE(existing_sibling)
 END FOR
 END IF
 
 // Transport enrollment
 IF admission.transport_requested:
 ENROLL_IN_TRANSPORT(student, admission.residential_address)
 END IF
 
 // Hostel enrollment
 IF admission.hostel_requested:
 ALLOCATE_HOSTEL_ROOM(student, admission.gender, admission.dietary_preference)
 ENROLL_IN_MESS(student, admission.dietary_preference)
 END IF
 
 // Create portal access
 parent_login = CREATE_PARENT_PORTAL_ACCESS(student.parents)
 student_login = CREATE_STUDENT_PORTAL_ACCESS(student)
 
 // Send welcome communications
 SEND_EMAIL(student.parent_email, welcome_email_template, {
 student_name: student.name,
 admission_number: admission_number,
 section: section,
 parent_login: parent_login,
 start_date: ACADEMIC_YEAR_START
 })
 
 SEND_SMS(student.parent_mobile, "Admission confirmed! {student.name} enrolled in Grade {student.grade}, Section {section}. Admission #: {admission_number}")
 
 // Trigger dependent processes
 GENERATE_ID_CARD(student)
 GENERATE_FEE_SCHEDULE(student)
 ASSIGN_ROLL_NUMBER(student, section)
 ADD_TO_TIMETABLE(student, section)
 
 // Notify relevant departments
 NOTIFY class_teacher("New student: {student.name}, Section {section}")
 NOTIFY transport_coordinator("New transport enrollment: {student.name}") IF admission.transport_requested
 NOTIFY hostel_warden("New boarder: {student.name}") IF admission.hostel_requested
 
 RETURN student
END FUNCTION

FUNCTION assign_section(grade, entrance_test_score):
 // Get all sections for the grade
 sections = GET Sections WHERE grade = grade ORDER BY section_name
 
 // Strategy: Balanced distribution based on test scores
 // Top scorers distributed across sections for balanced classes
 
 FOR each section IN sections:
 IF section.current_strength < section.max_capacity:
 // Check score distribution in section
 section_avg_score = CALCULATE_AVERAGE_SCORE(section)
 
 // Assign to maintain balanced average
 IF ABS(section_avg_score - entrance_test_score) < 15:
 section.students.add(student)
 section.current_strength += 1
 RETURN section
 END IF
 END IF
 END FOR
 
 // If no balanced section found, assign to section with lowest strength
 section = GET Section WHERE grade = grade ORDER BY current_strength ASC LIMIT 1
 section.students.add(student)
 section.current_strength += 1
 RETURN section
END FUNCTION
```

**EXAMPLE:**
- **Priya's Admission Journey (Complete Flow):**
 - **March 1:** Inquiry submitted online, registration fee ₹10,000 paid
 - Status: "Registered"
 - Admission test scheduled: March 15
 - **March 15:** Entrance test appeared
 - Subjects: Math (85/100), English (90/100), Science (88/100)
 - Total: 263/300 (87.67%)
 - Percentile: 92nd
 - **March 20:** Interview conducted
 - Interview score: 42/50
 - Overall score: (263 + 42) = 305/350 (87.14%)
 - **March 22:** Admission offered
 - Email sent: "Congratulations! Admission offered to Grade 9"
 - Fee payment deadline: April 5
 - Total admission package: ₹97,500
 - **April 2:** Fee payment received
 - Admission fee: ₹40,000
 - Registration: ₹10,000 (already paid)
 - Caution deposit: ₹10,000
 - First quarter tuition: ₹37,500
 - Total paid: ₹97,500
 - **April 3:** Student record created
 - Admission number: 2024/09/1234
 - Section assigned: 9A (based on score, balanced distribution)
 - Roll number: 15
 - Parent portal access: priya.parent@school.portal.com
 - Student portal access: priya.2024.09.1234@student.portal.com
 - **April 5:** Welcome package sent
 - Email with fee schedule (remaining ₹1,12,500 in 3 installments)
 - School handbook PDF
 - Uniform size form
 - Transport route options (if interested)
 - School calendar
 - **April 10:** ID card generated and dispatched
 - **April 15:** Classes begin, Priya attends first day

---

### 2. TO FEE MANAGEMENT MODULE

**WHY This Connection Exists:**
Admission is the first financial transaction in the student lifecycle. Registration fee, admission fee, security deposit, and first installment must be collected and tracked. Fee payment confirmation triggers admission confirmation and student record creation.

**DATA FLOW:**
- **Admission Fee Structure:**
 - Registration fee: ₹10,000 (non-refundable, paid at inquiry stage)
 - Admission fee: ₹40,000 (one-time, paid at admission confirmation)
 - Security/Caution deposit: ₹10,000 (refundable at exit)
 - First quarter tuition fee: Grade-specific (₹30,000 - ₹50,000)
 - **Total admission package:** ₹90,000 - ₹1,10,000
- **Category-based Adjustments:**
 - General category: Full fee
 - Sibling: ₹5,000 discount on admission fee
 - Alumni child: ₹10,000 discount on admission fee
 - Staff ward: 50% waiver on admission fee
 - Scholarship: Admission fee waived (only registration + deposit)
 - EWS quota: 75% discount on all fees
- **Payment Timeline:**
 - Registration fee: At inquiry/application submission
 - Admission package: Within 7 days of admission offer
 - Seat forfeiture: If payment not received by deadline
- **Payment Methods:**
 - Online: Credit/Debit card, UPI, Net banking, Wallets
 - Offline: Cash, Cheque, Demand Draft (at school office)
 - Installment: Not available for admission package (full payment required)
- **Payment Confirmation:**
 - Instant receipt generation
 - SMS + Email confirmation
 - Admission status updated: "Pending" → "Confirmed"
 - Student record creation triggered

**TRIGGER EVENT:**
- **Registration Fee Paid:** Application form unlocked, can submit application
- **Admission Offer Made:** Fee payment link sent, deadline set
- **Admission Fee Paid:** Seat confirmed, student record created
- **Payment Deadline Missed:** Seat released to waitlist
- **Refund Request:** If admission withdrawn before session starts

**IMPACT:**
- **Admission Package Billing:**
 - Aarav offered admission to Grade 8
 - Fee structure:
 - Registration: ₹10,000 (already paid)
 - Admission: ₹40,000
 - Caution deposit: ₹10,000
 - Q1 tuition: ₹37,500
 - **Total due:** ₹87,500 (excluding paid registration)
 - Payment deadline: March 20
 - Payment link sent via email + SMS
- **Sibling Discount Application:**
 - Priya (Grade 9) already enrolled
 - Brother Aarav gets admission to Grade 6
 - Admission fee: ₹40,000 - ₹5,000 (sibling discount) = ₹35,000
 - Both siblings' fee accounts updated with discount
 - Total family saving: ₹5,000 on admission + ongoing annual discounts
- **Scholarship Student:**
 - Rohan awarded 50% merit scholarship
 - Admission package:
 - Registration: ₹10,000 (paid)
 - Admission: ₹0 (waived for scholarship)
 - Caution deposit: ₹10,000
 - Q1 tuition: ₹37,500 × 50% = ₹18,750
 - **Total due:** ₹28,750
- **Payment Deadline Management:**
 - Kavya offered admission, deadline March 15
 - Payment not received by March 15, 11:59 PM
 - March 16, 12:01 AM: Seat automatically released
 - Next waitlist candidate (Ananya) offered seat
 - Email sent: "Seat offered to you (from waitlist), pay by March 20"
- **Refund Processing:**
 - Arjun paid ₹97,500 admission package
 - Family relocates before session starts
 - Refund policy: Admission fee non-refundable, rest refundable
 - Refund amount: ₹10,000 (deposit) + ₹37,500 (tuition) = ₹47,500
 - Processed within 30 days

**BUSINESS LOGIC:**
```
FUNCTION calculate_admission_fees(admission):
 fees = {}
 
 // Base fees
 fees.registration = 10000 // Non-refundable
 fees.admission = 40000 // One-time
 fees.caution_deposit = 10000 // Refundable
 fees.first_quarter_tuition = GET_QUARTERLY_FEE(admission.grade)
 
 // Apply category-based adjustments
 IF admission.category = "SIBLING":
 fees.admission -= 5000 // ₹5,000 discount
 
 ELSE IF admission.category = "ALUMNI_CHILD":
 fees.admission -= 10000 // ₹10,000 discount
 
 ELSE IF admission.category = "STAFF_WARD":
 fees.admission *= 0.5 // 50% waiver
 fees.first_quarter_tuition *= 0.5
 
 ELSE IF admission.category = "SCHOLARSHIP":
 scholarship_percent = admission.scholarship_percentage
 fees.admission = 0 // Waived
 fees.first_quarter_tuition *= (1 - scholarship_percent/100)
 
 ELSE IF admission.category = "EWS":
 fees.admission *= 0.25 // 75% discount
 fees.first_quarter_tuition *= 0.25
 END IF
 
 // Calculate total
 total = fees.registration + fees.admission + fees.caution_deposit + fees.first_quarter_tuition
 
 // Subtract already paid registration fee
 already_paid = GET_PAID_AMOUNT(admission, "REGISTRATION")
 total_due = total - already_paid
 
 // Create invoice
 invoice = CREATE_INVOICE
 invoice.admission = admission
 invoice.fee_breakdown = fees
 invoice.total_amount = total
 invoice.already_paid = already_paid
 invoice.amount_due = total_due
 invoice.due_date = admission.payment_deadline
 invoice.payment_link = GENERATE_PAYMENT_LINK(invoice)
 
 RETURN invoice
END FUNCTION

FUNCTION process_admission_payment(admission, payment):
 // Verify payment amount
 invoice = GET_INVOICE(admission)
 
 IF payment.amount < invoice.amount_due:
 RETURN "Error: Insufficient payment. Due: ₹{invoice.amount_due}, Paid: ₹{payment.amount}"
 END IF
 
 // Record payment
 payment_record = CREATE_PAYMENT
 payment_record.admission = admission
 payment_record.invoice = invoice
 payment_record.amount = payment.amount
 payment_record.mode = payment.mode // ONLINE, CASH, CHEQUE
 payment_record.transaction_id = payment.transaction_id
 payment_record.timestamp = NOW
 payment_record.status = "SUCCESS"
 
 // Update invoice
 invoice.status = "PAID"
 invoice.paid_amount = payment.amount
 invoice.paid_date = NOW
 invoice.payment_mode = payment.mode
 
 // Update admission status
 admission.fee_payment_status = "PAID"
 admission.status = "CONFIRMED"
 
 // Generate receipt
 receipt = GENERATE_RECEIPT(payment_record)
 
 // Send confirmations
 SEND_EMAIL(admission.parent_email, receipt.pdf, 
 "Payment successful! Admission confirmed for {admission.applicant_name}")
 SEND_SMS(admission.parent_mobile, 
 "Fee ₹{payment.amount} received. Admission confirmed. Receipt: {receipt.number}")
 
 // Trigger student record creation
 TRIGGER_STUDENT_RECORD_CREATION(admission)
 
 // Notify admissions team
 NOTIFY admissions_team("Payment received: {admission.applicant_name}, proceed with enrollment")
 
 RETURN {status: "SUCCESS", receipt: receipt, admission_status: "CONFIRMED"}
END FUNCTION

FUNCTION handle_payment_deadline_expiry(admission):
 // Check if payment received
 IF admission.fee_payment_status = "PAID":
 RETURN // Payment received, no action needed
 END IF
 
 // Payment deadline passed, seat not confirmed
 admission.status = "SEAT_FORFEITED"
 admission.seat_forfeiture_date = NOW
 
 // Release seat
 grade = admission.applied_grade
 RELEASE_SEAT(grade)
 
 // Notify applicant
 SEND_EMAIL(admission.parent_email, 
 "Admission offer expired. Payment not received by deadline. Seat released.")
 SEND_SMS(admission.parent_mobile, 
 "Admission offer for {admission.applicant_name} expired due to non-payment.")
 
 // Offer to next waitlist candidate
 next_candidate = GET_NEXT_WAITLIST_CANDIDATE(grade)
 
 IF next_candidate EXISTS:
 MAKE_ADMISSION_OFFER(next_candidate)
 SEND_EMAIL(next_candidate.parent_email, 
 "Congratulations! Seat available from waitlist. Pay by {new_deadline}")
 END IF
 
 LOG "Seat forfeited: {admission.applicant_name}, offered to: {next_candidate.applicant_name}"
END FUNCTION
```

**EXAMPLE:**
- **Sharma Family - Sibling Admission:**
 - **Existing Student:** Priya (Grade 9), Admission #2023/09/0567
 - **New Applicant:** Brother Aarav applying for Grade 6
 - **March 1:** Aarav's application submitted, registration fee ₹10,000 paid
 - **March 15:** Entrance test (scored 88%)
 - **March 22:** Admission offered with sibling discount
 - **Fee Calculation:**
 - Registration: ₹10,000 (paid)
 - Admission: ₹40,000 - ₹5,000 (sibling) = ₹35,000
 - Caution deposit: ₹10,000
 - Q1 tuition: ₹30,000
 - **Total due:** ₹75,000
 - **March 25:** Father pays ₹75,000 online via UPI
 - **March 25 (5 mins later):**
 - Receipt #ADM/2024/06/1234 generated
 - Email + SMS confirmation sent
 - Admission status: "Confirmed"
 - Student record creation initiated
 - **March 26:** Aarav's admission number assigned: 2024/06/1234
 - **Ongoing Benefit:**
 - Priya's annual fee: ₹1,50,000 → ₹1,35,000 (10% sibling discount)
 - Aarav's annual fee: ₹1,20,000 → ₹1,08,000 (10% sibling discount)
 - **Total family saving:** ₹27,000/year

---

### 3. TO COMMUNICATION MODULE

**WHY This Connection Exists:**
Admission is a multi-stage journey requiring constant communication with parents. Each stage (inquiry, application, test, interview, offer, payment, enrollment) needs timely, personalized communication across multiple channels (email, SMS, app notifications) to ensure smooth conversion and excellent parent experience.

**DATA FLOW:**
- **Inquiry Stage:**
 - Inquiry acknowledgment (instant)
 - School information brochure (email)
 - Open house invitation
 - Follow-up calls (CRM-driven)
- **Application Stage:**
 - Application received confirmation
 - Document checklist
 - Document verification status updates
 - Application review status
- **Entrance Test Stage:**
 - Test schedule notification
 - Admit card (email + downloadable)
 - Test day reminders (1 day before, morning of test)
 - Test center directions and instructions
- **Interview Stage:**
 - Interview schedule (date, time, venue)
 - Interview preparation guidelines
 - Interview reminders
- **Admission Decision:**
 - Admission offered (congratulations email + SMS)
 - Waitlisted (status update + expected timeline)
 - Rejected (polite communication with feedback)
- **Fee Payment:**
 - Payment link and instructions
 - Payment deadline reminders (7 days, 3 days, 1 day before)
 - Payment confirmation (instant)
 - Payment receipt (email PDF)
- **Enrollment:**
 - Welcome email with school handbook
 - Orientation schedule
 - Uniform purchase details
 - Book list
 - First day instructions
- **Ongoing Updates:**
 - Admission status changes
 - Document submission reminders
 - Important dates and deadlines

**TRIGGER EVENT:**
- **Inquiry Submitted:** Instant acknowledgment + brochure
- **Application Submitted:** Confirmation + document checklist
- **Test Scheduled:** Admit card sent 7 days before
- **Interview Scheduled:** Notification sent 3 days before
- **Admission Decision Made:** Offer/Waitlist/Rejection email sent
- **Payment Due:** Reminders at 7, 3, 1 days before deadline
- **Payment Received:** Instant confirmation
- **Enrollment Complete:** Welcome package sent

**IMPACT:**
- **Instant Inquiry Response:**
 - Parent submits inquiry form at 11 PM
 - Instant auto-reply email: "Thank you for your interest in XYZ School"
 - Email includes: School brochure PDF, admission process overview, contact details
 - SMS sent: "Inquiry received. Our admissions team will contact you within 24 hours."
 - Next day 10 AM: Admissions counselor calls parent
- **Entrance Test Communication:**
 - Test scheduled for March 15
 - March 8 (7 days before): Admit card email sent
 - PDF admit card with student photo, test center, timing
 - Test syllabus and sample questions
 - Instructions: "Bring admit card, ID proof, stationery"
 - March 14 (1 day before): Reminder SMS
 - "Reminder: Entrance test tomorrow 9 AM at Main Campus. Bring admit card."
 - March 15, 7 AM: Morning reminder
 - "Test today at 9 AM. Reach by 8:45 AM. Best wishes!"
- **Admission Offer Communication:**
 - March 22: Admission decision made
 - March 22, 10 AM: Email sent
 - Subject: " Congratulations! Admission Offered to Grade 9"
 - Body: Personalized congratulations, fee details, payment link, deadline
 - Attachments: Offer letter PDF, fee structure, payment instructions
 - March 22, 10:05 AM: SMS sent
 - "Congratulations! Admission offered to Priya for Grade 9. Pay by April 5. Link: [payment_link]"
 - March 22, 10:10 AM: App notification (if parent has app)
 - "Admission Offer Received! Tap to view details and pay fees."
- **Payment Reminder Sequence:**
 - Deadline: April 5
 - March 29 (7 days before): Email reminder
 - "Reminder: Fee payment due in 7 days (April 5). Amount: ₹87,500. Pay now: [link]"
 - April 2 (3 days before): Email + SMS reminder
 - "URGENT: Fee payment due in 3 days. Secure your seat by paying ₹87,500. Link: [link]"
 - April 4 (1 day before): Email + SMS + App notification
 - "FINAL REMINDER: Fee payment due tomorrow (April 5). Pay now to confirm admission."
 - April 5, 6 PM (6 hours before deadline): Last call
 - "Last 6 hours to pay! Deadline: 11:59 PM today. Amount: ₹87,500. Link: [link]"
- **Payment Confirmation:**
 - April 3, 2:30 PM: Parent pays ₹87,500 online
 - April 3, 2:31 PM: Instant confirmations
 - Email: "Payment successful! ₹87,500 received. Admission confirmed. Receipt attached."
 - SMS: "Payment ₹87,500 received. Admission confirmed for Priya. Receipt: ADM/2024/09/1234"
 - App notification: "Admission Confirmed! Welcome to XYZ School family."
- **Welcome Package:**
 - April 5: Email sent
 - Subject: "Welcome to XYZ School! Important Information"
 - Attachments:
 - School handbook PDF
 - Fee schedule (remaining installments)
 - Uniform purchase details (vendor, sizes, prices)
 - Book list for Grade 9
 - School calendar (holidays, events, exam dates)
 - Orientation schedule (April 10, 10 AM)
 - First day instructions (April 15)
 - Body: Warm welcome message, principal's note, contact details for queries

**BUSINESS LOGIC:**
```
FUNCTION send_admission_stage_communication(admission, stage):
 parent_email = admission.parent_email
 parent_mobile = admission.parent_mobile
 applicant_name = admission.applicant_name
 
 IF stage = "INQUIRY_RECEIVED":
 // Instant auto-reply
 SEND_EMAIL(parent_email, inquiry_acknowledgment_template, {
 parent_name: admission.parent_name,
 applicant_name: applicant_name,
 attachments: [school_brochure.pdf, admission_process.pdf]
 })
 
 SEND_SMS(parent_mobile, 
 "Thank you for your interest in XYZ School. Our team will contact you within 24 hours.")
 
 // Schedule follow-up call
 SCHEDULE_TASK(admissions_counselor, "Call {parent_name} about {applicant_name}'s inquiry", 
 due_date=TOMORROW, priority=HIGH)
 
 ELSE IF stage = "APPLICATION_SUBMITTED":
 SEND_EMAIL(parent_email, application_confirmation_template, {
 application_number: admission.application_number,
 applicant_name: applicant_name,
 document_checklist: GENERATE_DOCUMENT_CHECKLIST(admission.grade)
 })
 
 SEND_SMS(parent_mobile, 
 "Application received for {applicant_name}. Application #: {admission.application_number}")
 
 ELSE IF stage = "ENTRANCE_TEST_SCHEDULED":
 test_date = admission.entrance_test_date
 test_venue = admission.entrance_test_venue
 
 // Send admit card 7 days before test
 admit_card_pdf = GENERATE_ADMIT_CARD(admission)
 
 SEND_EMAIL(parent_email, test_admit_card_template, {
 applicant_name: applicant_name,
 test_date: test_date,
 test_time: "9:00 AM",
 test_venue: test_venue,
 attachments: [admit_card_pdf, test_syllabus.pdf, sample_questions.pdf]
 })
 
 // Schedule reminders
 SCHEDULE_SMS(parent_mobile, 
 "Reminder: {applicant_name}'s entrance test tomorrow at 9 AM. Bring admit card.",
 send_date=test_date - 1 day, send_time="6:00 PM")
 
 SCHEDULE_SMS(parent_mobile, 
 "Test today at 9 AM. Reach by 8:45 AM. Best wishes!",
 send_date=test_date, send_time="7:00 AM")
 
 ELSE IF stage = "ADMISSION_OFFERED":
 offer_letter_pdf = GENERATE_OFFER_LETTER(admission)
 fee_structure_pdf = GENERATE_FEE_STRUCTURE(admission)
 payment_link = GENERATE_PAYMENT_LINK(admission)
 
 SEND_EMAIL(parent_email, admission_offer_template, {
 applicant_name: applicant_name,
 grade: admission.applied_grade,
 fee_amount: admission.total_admission_fee,
 payment_deadline: admission.payment_deadline,
 payment_link: payment_link,
 attachments: [offer_letter_pdf, fee_structure_pdf]
 })
 
 SEND_SMS(parent_mobile, 
 " Congratulations! Admission offered to {applicant_name} for Grade {admission.applied_grade}. Pay by {admission.payment_deadline}. Link: {payment_link}")
 
 SEND_APP_NOTIFICATION(parent_app, 
 title="Admission Offer Received!",
 body="Congratulations! Tap to view details and pay fees.",
 action_link=payment_link)
 
 // Schedule payment reminders
 SCHEDULE_PAYMENT_REMINDERS(admission)
 
 ELSE IF stage = "PAYMENT_RECEIVED":
 receipt_pdf = GET_PAYMENT_RECEIPT(admission)
 
 SEND_EMAIL(parent_email, payment_confirmation_template, {
 applicant_name: applicant_name,
 amount_paid: admission.amount_paid,
 receipt_number: admission.receipt_number,
 admission_status: "CONFIRMED",
 attachments: [receipt_pdf]
 })
 
 SEND_SMS(parent_mobile, 
 "Payment ₹{admission.amount_paid} received. Admission confirmed for {applicant_name}. Receipt: {admission.receipt_number}")
 
 SEND_APP_NOTIFICATION(parent_app, 
 title="Admission Confirmed!",
 body="Welcome to XYZ School family. Enrollment details sent via email.")
 
 ELSE IF stage = "ENROLLMENT_COMPLETE":
 SEND_EMAIL(parent_email, welcome_package_template, {
 applicant_name: applicant_name,
 admission_number: admission.admission_number,
 grade: admission.applied_grade,
 section: admission.assigned_section,
 orientation_date: ORIENTATION_DATE,
 first_day: ACADEMIC_YEAR_START,
 attachments: [
 school_handbook.pdf,
 fee_schedule.pdf,
 uniform_details.pdf,
 book_list.pdf,
 school_calendar.pdf
 ]
 })
 
 END IF
END FUNCTION

FUNCTION schedule_payment_reminders(admission):
 payment_deadline = admission.payment_deadline
 parent_email = admission.parent_email
 parent_mobile = admission.parent_mobile
 payment_link = admission.payment_link
 amount_due = admission.amount_due
 
 // 7 days before deadline
 SCHEDULE_EMAIL(parent_email, payment_reminder_template, {
 days_remaining: 7,
 amount_due: amount_due,
 payment_link: payment_link
 }, send_date=payment_deadline - 7 days, send_time="10:00 AM")
 
 // 3 days before deadline
 SCHEDULE_EMAIL(parent_email, payment_urgent_reminder_template, {
 days_remaining: 3,
 amount_due: amount_due,
 payment_link: payment_link
 }, send_date=payment_deadline - 3 days, send_time="10:00 AM")
 
 SCHEDULE_SMS(parent_mobile, 
 "URGENT: Fee payment due in 3 days. Amount: ₹{amount_due}. Link: {payment_link}",
 send_date=payment_deadline - 3 days, send_time="10:00 AM")
 
 // 1 day before deadline
 SCHEDULE_EMAIL(parent_email, payment_final_reminder_template, {
 amount_due: amount_due,
 payment_link: payment_link
 }, send_date=payment_deadline - 1 day, send_time="10:00 AM")
 
 SCHEDULE_SMS(parent_mobile, 
 "FINAL REMINDER: Fee payment due tomorrow. Amount: ₹{amount_due}. Link: {payment_link}",
 send_date=payment_deadline - 1 day, send_time="10:00 AM")
 
 SCHEDULE_APP_NOTIFICATION(parent_app, 
 title="Payment Due Tomorrow!",
 body="Pay ₹{amount_due} to confirm admission.",
 action_link=payment_link,
 send_date=payment_deadline - 1 day, send_time="10:00 AM")
 
 // 6 hours before deadline
 SCHEDULE_SMS(parent_mobile, 
 "Last 6 hours! Pay ₹{amount_due} before 11:59 PM. Link: {payment_link}",
 send_date=payment_deadline, send_time="6:00 PM")
END FUNCTION
```

**EXAMPLE:**
- **Rohan's Complete Communication Journey:**
 - **Feb 1, 11:30 PM:** Parent submits online inquiry
 - **Feb 1, 11:31 PM:** Auto-reply email received (brochure attached)
 - **Feb 2, 10:15 AM:** Admissions counselor Ms. Gupta calls
 - Discusses school, answers questions, schedules campus visit
 - **Feb 5:** Campus visit, parent impressed
 - **Feb 6:** Application submitted online, registration fee ₹10,000 paid
 - **Feb 6, 2 mins later:** Email confirmation with application #APP/2024/0567
 - **Feb 10:** Application reviewed, entrance test scheduled for Feb 20
 - **Feb 13:** Admit card email received (7 days before test)
 - **Feb 19, 6 PM:** SMS reminder: "Test tomorrow 9 AM"
 - **Feb 20, 7 AM:** SMS: "Test today, reach by 8:45 AM"
 - **Feb 20:** Rohan appears for test, scores 265/300 (88%)
 - **Feb 25:** Interview scheduled for March 1
 - **Feb 28:** Email with interview guidelines
 - **March 1:** Interview conducted, scored 45/50
 - **March 5:** Admission decision made (offered)
 - **March 5, 10 AM:** Email + SMS: "Congratulations! Admission offered"
 - Payment deadline: March 20
 - Amount: ₹87,500
 - **March 13:** Email reminder (7 days before deadline)
 - **March 17:** Email + SMS urgent reminder (3 days before)
 - **March 19:** Email + SMS + App final reminder (1 day before)
 - **March 18, 3 PM:** Parent pays ₹87,500 online
 - **March 18, 3:01 PM:** Email + SMS confirmation, receipt sent
 - **March 20:** Welcome email with school handbook, fee schedule, uniform details
 - **April 10:** Orientation attended
 - **April 15:** First day of school, Rohan joins Grade 9, Section A

---

### 4. TO ASSESSMENT MODULE

**WHY This Connection Exists:**
Entrance tests are the primary evaluation tool for admission decisions. Test scores, percentile rankings, and cutoff marks determine which applicants receive admission offers. The assessment module conducts, evaluates, and reports entrance test results to the admissions module.

**DATA FLOW:**
- **Entrance Test Schedule:**
 - Test date, time, venue
 - Test duration (typically 2-3 hours)
 - Subjects tested (Math, English, Science, Reasoning)
 - Marks per subject (usually 100 marks each)
- **Test Hall Allocation:**
 - Applicant seating arrangement
 - Hall capacity management
 - Invigilator assignment
- **Answer Sheet Evaluation:**
 - OMR scanning or manual evaluation
 - Subject-wise marks
 - Total marks and percentage
 - Percentile calculation (relative to all test-takers)
- **Result Publication:**
 - Individual scores sent to applicants
 - Cutoff marks announced
 - Shortlist for interview
 - Merit list generation

**TRIGGER EVENT:**
- **Test Scheduled:** Applicants notified, admit cards sent
- **Test Conducted:** Answer sheets collected for evaluation
- **Results Published:** Scores sent to applicants, shortlist created
- **Cutoff Announced:** Admission offers made to qualifying candidates

**IMPACT:**
- **Test Evaluation:**
 - 500 applicants appear for Grade 9 entrance test
 - Subjects: Math (100), English (100), Science (100)
 - Total: 300 marks
 - Evaluation completed in 3 days
 - Results published on school website and sent via email
- **Cutoff-based Selection:**
 - Cutoff set at 70% (210/300 marks)
 - 350 students score above cutoff (70%)
 - 150 students below cutoff (30%)
 - Top 350 shortlisted for interview
- **Merit-based Offers:**
 - Top 50 scorers (85%+): Direct admission offer
 - Next 300 (70-85%): Interview required
 - Below 70%: Not shortlisted
- **Percentile Ranking:**
 - Aarav scores 265/300 (88.33%)
 - Percentile: 92nd (better than 92% of test-takers)
 - Shortlisted for interview with high priority

**BUSINESS LOGIC:**
```
FUNCTION conduct_entrance_test(test_date, grade):
 // Get all registered applicants
 applicants = GET Admissions WHERE entrance_test_date = test_date 
 AND applied_grade = grade 
 AND status = "REGISTERED"
 
 // Allocate test halls
 halls = ALLOCATE_TEST_HALLS(applicants.count)
 
 FOR each applicant IN applicants:
 // Assign seat
 seat = ASSIGN_SEAT(applicant, halls)
 applicant.test_hall = seat.hall
 applicant.test_seat_number = seat.number
 
 // Update status
 applicant.test_attendance = "SCHEDULED"
 END FOR
 
 // On test day
 FOR each applicant IN applicants:
 IF applicant.present_in_hall:
 applicant.test_attendance = "PRESENT"
 DISTRIBUTE_ANSWER_SHEET(applicant)
 ELSE:
 applicant.test_attendance = "ABSENT"
 END IF
 END FOR
 
 RETURN applicants
END FUNCTION

FUNCTION evaluate_entrance_test(applicants):
 FOR each applicant IN applicants:
 IF applicant.test_attendance != "PRESENT":
 CONTINUE
 END IF
 
 // Evaluate answer sheet
 answer_sheet = GET_ANSWER_SHEET(applicant)
 
 scores = {
 math: EVALUATE_SUBJECT(answer_sheet.math, math_answer_key),
 english: EVALUATE_SUBJECT(answer_sheet.english, english_answer_key),
 science: EVALUATE_SUBJECT(answer_sheet.science, science_answer_key)
 }
 
 total_score = scores.math + scores.english + scores.science
 percentage = (total_score / 300) * 100
 
 // Store scores
 applicant.entrance_test_scores = scores
 applicant.entrance_test_total = total_score
 applicant.entrance_test_percentage = percentage
 END FOR
 
 // Calculate percentiles
 all_scores = GET all_applicants.entrance_test_total ORDER BY DESC
 
 FOR each applicant IN applicants:
 rank = FIND_RANK(applicant.entrance_test_total, all_scores)
 percentile = ((all_scores.count - rank) / all_scores.count) * 100
 applicant.entrance_test_percentile = percentile
 END FOR
 
 RETURN applicants
END FUNCTION

FUNCTION determine_cutoff_and_shortlist(applicants, seats_available):
 // Sort by total score
 sorted_applicants = SORT applicants BY entrance_test_total DESC
 
 // Determine cutoff (typically 70% or top N candidates)
 cutoff_percentage = 70
 cutoff_marks = 210 // 70% of 300
 
 // Alternative: Top N candidates
 // cutoff_rank = seats_available * 2 // 2x seats for interview
 // cutoff_marks = sorted_applicants[cutoff_rank].entrance_test_total
 
 shortlisted = []
 rejected = []
 
 FOR each applicant IN applicants:
 IF applicant.entrance_test_total >= cutoff_marks:
 applicant.status = "SHORTLISTED"
 shortlisted.add(applicant)
 
 // Top scorers get direct offer
 IF applicant.entrance_test_percentage >= 85:
 applicant.interview_required = FALSE
 applicant.admission_decision = "OFFERED"
 ELSE:
 applicant.interview_required = TRUE
 SCHEDULE_INTERVIEW(applicant)
 END IF
 ELSE:
 applicant.status = "NOT_SHORTLISTED"
 rejected.add(applicant)
 END IF
 END FOR
 
 // Publish results
 PUBLISH_RESULTS(applicants, cutoff_marks)
 
 RETURN {shortlisted: shortlisted, rejected: rejected, cutoff: cutoff_marks}
END FUNCTION
```

**EXAMPLE:**
- **Grade 9 Entrance Test (March 15, 2024):**
 - **Registered:** 500 applicants
 - **Appeared:** 480 applicants (96% attendance)
 - **Absent:** 20 applicants
 - **Test Halls:** 5 halls (100 seats each)
 - **Evaluation:** Completed in 3 days (March 18)
 - **Results Published:** March 20
 - **Score Distribution:**
 - 85%+ (Excellent): 50 students
 - 70-85% (Good): 300 students
 - Below 70%: 130 students
 - **Cutoff:** 210/300 (70%)
 - **Shortlisted:** 350 students
 - **Direct Offers:** 50 students (top scorers)
 - **Interview Required:** 300 students
 - **Rejected:** 130 students
 - **Individual Results:**
 - **Priya:** 263/300 (87.67%), Percentile 92nd → Direct offer
 - **Aarav:** 245/300 (81.67%), Percentile 78th → Interview required
 - **Rohan:** 195/300 (65%), Percentile 35th → Not shortlisted

---

### 5. TO PARENT PORTAL

**WHY This Connection Exists:**
Parents need complete visibility into the admission process. From inquiry to enrollment, every stage should be trackable online. Application submission, document upload, test results, admission decision, fee payment, and enrollment status must be accessible 24/7 through the parent portal.

**DATA FLOW:**
- **Application Dashboard:**
 - Application status (Submitted/Under Review/Shortlisted/Offered/Confirmed)
 - Application number and date
 - Applied grade and category
 - Document submission status
 - Next steps and pending actions
- **Entrance Test:**
 - Test schedule (date, time, venue)
 - Admit card download
 - Test results (scores, percentile, cutoff)
 - Shortlist status
- **Interview:**
 - Interview schedule
 - Interview guidelines
 - Interview feedback (if applicable)
- **Admission Decision:**
 - Offer letter download
 - Waitlist position (if waitlisted)
 - Rejection reason (if rejected)
- **Fee Payment:**
 - Fee structure and breakdown
 - Payment link
 - Payment deadline
 - Payment history and receipts
- **Enrollment:**
 - Admission number
 - Section assignment
 - Welcome documents
 - Orientation schedule

**TRIGGER EVENT:**
- **Application Submitted:** Dashboard activated
- **Test Results Published:** Scores displayed
- **Admission Decision:** Offer/Waitlist/Rejection shown
- **Payment Received:** Status updated to "Confirmed"
- **Enrollment Complete:** Student details displayed

**IMPACT:**
- **Real-time Status Tracking:**
 - Parent logs in at 10 PM
 - Sees: "Application Under Review - Expected decision by March 25"
 - Documents: 4/5 uploaded (1 pending: Previous school TC)
 - Next step: "Upload TC to complete application"
- **Test Results Access:**
 - March 20, 10 AM: Results published
 - Parent logs in, sees:
 - Math: 85/100
 - English: 90/100
 - Science: 88/100
 - Total: 263/300 (87.67%)
 - Percentile: 92nd
 - Status: "Shortlisted - Direct Admission Offer"
- **Fee Payment:**
 - Admission offered, fee payment required
 - Portal shows:
 - Amount due: ₹87,500
 - Deadline: April 5
 - Days remaining: 7
 - "Pay Now" button (redirects to payment gateway)
 - After payment: "Payment Successful - Admission Confirmed"
- **Document Upload:**
 - Required documents checklist:
 - Birth certificate (uploaded)
 - Aadhaar card (uploaded)
 - Previous school report card (uploaded)
 - Passport photo (uploaded)
 - Transfer certificate (pending)
 - Parent uploads TC → Status changes to 
 - Application status: "Document Verification Complete"

**BUSINESS LOGIC:**
```
FUNCTION parent_admission_dashboard(parent):
 // Get all applications by this parent
 applications = GET Admissions WHERE parent_email = parent.email 
 OR parent_mobile = parent.mobile
 
 dashboard = []
 
 FOR each application IN applications:
 app_summary = {
 applicant_name: application.applicant_name,
 application_number: application.application_number,
 applied_grade: application.applied_grade,
 application_date: application.application_date,
 status: application.status,
 
 // Document status
 documents: {
 required: GET_REQUIRED_DOCUMENTS(application.grade),
 uploaded: application.uploaded_documents,
 pending: CALCULATE_PENDING_DOCUMENTS(application)
 },
 
 // Test details
 entrance_test: {
 scheduled: application.entrance_test_date,
 admit_card_available: application.admit_card_generated,
 results_published: application.test_results_published,
 scores: application.entrance_test_scores,
 percentile: application.entrance_test_percentile,
 shortlisted: application.status IN ["SHORTLISTED", "OFFERED"]
 },
 
 // Interview details
 interview: {
 required: application.interview_required,
 scheduled: application.interview_date,
 completed: application.interview_completed,
 score: application.interview_score
 },
 
 // Admission decision
 decision: {
 status: application.admission_decision, // OFFERED, WAITLISTED, REJECTED
 offer_letter: application.offer_letter_pdf,
 waitlist_position: application.waitlist_position,
 rejection_reason: application.rejection_reason
 },
 
 // Fee payment
 fee_payment: {
 amount_due: application.admission_fee_amount,
 payment_deadline: application.payment_deadline,
 payment_link: application.payment_link,
 paid: application.fee_payment_status = "PAID",
 receipt: application.payment_receipt
 },
 
 // Enrollment
 enrollment: {
 confirmed: application.status = "CONFIRMED",
 admission_number: application.admission_number,
 section: application.assigned_section,
 orientation_date: ORIENTATION_DATE,
 first_day: ACADEMIC_YEAR_START
 },
 
 // Next steps
 next_steps: CALCULATE_NEXT_STEPS(application)
 }
 
 dashboard.add(app_summary)
 END FOR
 
 RETURN dashboard
END FUNCTION

FUNCTION calculate_next_steps(application):
 next_steps = []
 
 IF application.status = "APPLICATION_SUBMITTED":
 pending_docs = CALCULATE_PENDING_DOCUMENTS(application)
 IF pending_docs.count > 0:
 next_steps.add("Upload pending documents: {pending_docs}")
 ELSE:
 next_steps.add("Application under review. Decision by {expected_date}")
 END IF
 
 ELSE IF application.status = "SHORTLISTED":
 IF application.interview_required AND NOT application.interview_scheduled:
 next_steps.add("Interview will be scheduled soon")
 ELSE IF application.interview_scheduled AND NOT application.interview_completed:
 next_steps.add("Attend interview on {application.interview_date}")
 ELSE:
 next_steps.add("Awaiting admission decision")
 END IF
 
 ELSE IF application.status = "OFFERED":
 IF application.fee_payment_status != "PAID":
 days_remaining = application.payment_deadline - TODAY
 next_steps.add("Pay admission fee (₹{application.admission_fee_amount}) within {days_remaining} days")
 ELSE:
 next_steps.add("Admission confirmed! Await orientation details")
 END IF
 
 ELSE IF application.status = "WAITLISTED":
 next_steps.add("You are on waitlist (Position: {application.waitlist_position}). Will notify if seat available")
 
 ELSE IF application.status = "CONFIRMED":
 next_steps.add("Enrollment complete! Orientation on {ORIENTATION_DATE}")
 END IF
 
 RETURN next_steps
END FUNCTION
```

**EXAMPLE:**
- **Mr. Sharma's Portal Experience (Priya's Application):**
 - **March 1:** Application submitted
 - Portal shows: "Application #APP/2024/0567 submitted"
 - Documents: 4/5 uploaded (TC pending)
 - Next step: "Upload TC"
 - **March 5:** TC uploaded
 - Portal shows: "All documents uploaded "
 - Status: "Under Review"
 - **March 10:** Test scheduled
 - Portal shows: "Entrance test: March 15, 9 AM, Main Campus"
 - "Download Admit Card" button available
 - **March 20:** Results published
 - Portal shows:
 - Scores: Math 85, English 90, Science 88
 - Total: 263/300 (87.67%)
 - Percentile: 92nd
 - Status: "Shortlisted - Direct Admission Offer"
 - **March 22:** Admission offered
 - Portal shows:
 - " Congratulations! Admission Offered"
 - "Download Offer Letter" button
 - Fee: ₹87,500
 - Deadline: April 5
 - "Pay Now" button
 - **April 2:** Fee paid
 - Portal shows:
 - "Payment Successful "
 - "Admission Confirmed"
 - Receipt #ADM/2024/09/1234
 - Admission Number: 2024/09/1234
 - Section: 9A
 - **April 5:** Enrollment complete
 - Portal shows:
 - Welcome message
 - Orientation: April 10
 - First day: April 15
 - Download: School handbook, fee schedule, uniform details

---

### 6. TO TIMETABLE MODULE

**WHY This Connection Exists:**
New admissions determine section requirements and capacity planning. If admissions exceed existing section capacity, new sections must be created. Section allocation affects timetable generation, teacher assignment, and classroom allocation.

**DATA FLOW:**
- **Grade-wise Admission Count:**
 - Total admissions per grade
 - Section capacity (typically 40 students/section)
 - Number of sections required
- **Section Creation:**
 - New section names (A, B, C, D, E...)
 - Section capacity allocation
 - Balanced distribution of students
- **Student-Section Mapping:**
 - Each student assigned to specific section
 - Roll numbers assigned within section
 - Section strength tracking

**TRIGGER EVENT:**
- **Admission Season End:** Final admission count available
- **Section Capacity Exceeded:** New section creation required
- **Student Enrolled:** Section assignment needed

**IMPACT:**
- **Section Planning:**
 - Grade 9: 180 admissions confirmed
 - Existing sections: 4 (A, B, C, D) × 40 = 160 capacity
 - Shortfall: 20 students
 - Decision: Create Section E (capacity 40)
 - Final: 5 sections × 36 students = 180 (balanced)
- **Timetable Impact:**
 - New section E requires:
 - 8 periods/day × 6 days = 48 periods/week
 - Teachers for all subjects
 - Dedicated classroom
 - Lab slots (Science, Computer)
 - Timetable module adjusts:
 - Teacher workload increased
 - Classroom allocation updated
 - Lab schedules revised
- **Balanced Distribution:**
 - Students distributed across sections based on entrance test scores
 - Each section has mix of high, medium, low scorers
 - Ensures balanced class performance

**BUSINESS LOGIC:**
```
FUNCTION plan_sections_for_grade(grade, admissions):
 total_students = admissions.count
 section_capacity = 40
 
 // Calculate sections needed
 sections_needed = CEILING(total_students / section_capacity)
 
 // Get existing sections
 existing_sections = GET Sections WHERE grade = grade
 
 IF sections_needed > existing_sections.count:
 // Create new sections
 sections_to_create = sections_needed - existing_sections.count
 
 FOR i = 1 TO sections_to_create:
 new_section_name = GET_NEXT_SECTION_NAME(existing_sections)
 
 new_section = CREATE Section
 new_section.grade = grade
 new_section.name = new_section_name
 new_section.capacity = section_capacity
 new_section.current_strength = 0
 new_section.academic_year = CURRENT_ACADEMIC_YEAR
 
 existing_sections.add(new_section)
 
 NOTIFY timetable_module("New section created: Grade {grade}, Section {new_section_name}")
 END FOR
 END IF
 
 // Distribute students across sections (balanced by test scores)
 sorted_students = SORT admissions BY entrance_test_total DESC
 
 section_index = 0
 FOR each student IN sorted_students:
 // Round-robin distribution for balance
 section = existing_sections[section_index]
 
 ASSIGN_TO_SECTION(student, section)
 section.current_strength += 1
 
 section_index = (section_index + 1) % existing_sections.count
 END FOR
 
 RETURN existing_sections
END FUNCTION

FUNCTION assign_to_section(student, section):
 student.section = section
 student.roll_number = section.current_strength + 1
 
 section.students.add(student)
 
 // Update timetable module
 NOTIFY timetable_module("Student added: {student.name}, Grade {student.grade}, Section {section.name}")
END FUNCTION
```

**EXAMPLE:**
- **Grade 9 Section Planning (2024-25):**
 - **Admissions:** 180 students confirmed
 - **Existing Sections:** A, B, C, D (4 sections, 160 capacity)
 - **Analysis:** Need 180 ÷ 40 = 4.5 → 5 sections
 - **Action:** Create Section E
 - **Distribution:**
 - Section A: 36 students (Roll 1-36)
 - Section B: 36 students (Roll 1-36)
 - Section C: 36 students (Roll 1-36)
 - Section D: 36 students (Roll 1-36)
 - Section E: 36 students (Roll 1-36)
 - **Balanced by Scores:**
 - Each section has:
 - 7 students (85%+ scores)
 - 22 students (70-85% scores)
 - 7 students (60-70% scores)
 - Ensures balanced class performance

---

### 7. TO TRANSPORT MODULE

**WHY This Connection Exists:**
Many students enroll for transport during admission. Student's residential address determines route assignment. Transport fee is added to admission invoice. Transport enrollment must be processed before school starts.

**DATA FLOW:**
- **Transport Enrollment Request:**
 - Student residential address
 - Distance from school
 - Transport required (Yes/No)
- **Route Assignment:**
 - Route number based on address
 - Bus number
 - Pickup point and time
 - Drop point and time
- **Transport Fee:**
 - Route-based fee (distance-based)
 - Added to admission invoice
 - Annual or quarterly payment

**TRIGGER EVENT:**
- **Admission Confirmed:** Transport enrollment processed
- **Address Provided:** Route assignment calculated
- **Transport Enrolled:** RFID card issued

**IMPACT:**
- **Route Assignment:**
 - Priya's address: Sector 14, 12 km from school
 - Route assigned: Route 3 (covers Sector 12-16)
 - Bus: Bus #12
 - Pickup: Sector 14 Main Gate, 7:15 AM
 - Drop: Same point, 3:45 PM
 - Transport fee: ₹24,000/year
- **Fee Integration:**
 - Admission invoice updated:
 - Tuition: ₹1,50,000
 - Transport: ₹24,000
 - Total: ₹1,74,000
 - Parent pays combined amount

**BUSINESS LOGIC:**
```
FUNCTION enroll_in_transport(student, address):
 // Calculate distance from school
 distance = CALCULATE_DISTANCE(address, SCHOOL_ADDRESS)
 
 // Find appropriate route
 route = FIND_ROUTE_FOR_ADDRESS(address)
 
 IF route NOT EXISTS:
 RETURN "Error: No route available for this address. Contact transport office."
 END IF
 
 // Assign to route
 transport_enrollment = CREATE TransportEnrollment
 transport_enrollment.student = student
 transport_enrollment.route = route
 transport_enrollment.pickup_point = FIND_NEAREST_PICKUP_POINT(address, route)
 transport_enrollment.distance = distance
 
 // Calculate fee based on distance
 transport_fee = CALCULATE_TRANSPORT_FEE(distance)
 
 // Add to student invoice
 ADD_FEE(student, "TRANSPORT", transport_fee)
 
 // Issue RFID card
 rfid_card = ISSUE_RFID_CARD(student, "TRANSPORT")
 
 NOTIFY parent("Transport enrolled. Route: {route.number}, Pickup: {transport_enrollment.pickup_point}, Time: {route.pickup_time}")
 
 RETURN transport_enrollment
END FUNCTION
```

**EXAMPLE:**
- **Aarav's Transport Enrollment:**
 - Address: Sector 8, 8 km from school
 - Route: Route 2 (Sector 5-10)
 - Bus: #7
 - Pickup: Sector 8 Market, 7:25 AM
 - Transport fee: ₹18,000/year
 - RFID card: #TR-2024-0567
 - First day: Bus arrives at 7:25 AM, Aarav boards with RFID

---

### 8. TO HOSTEL MODULE

**WHY This Connection Exists:**
Students from distant cities require boarding facilities. Hostel room allocation and mess enrollment must be completed during admission. Hostel and mess fees are substantial additions to the admission invoice.

**DATA FLOW:**
- **Boarding Request:**
 - Student requires hostel (Yes/No)
 - Room preference (single/shared)
 - Dietary preference (veg/non-veg)
- **Room Allocation:**
 - Hostel block (Boys/Girls)
 - Floor and room number
 - Bed number
 - Roommates
- **Mess Enrollment:**
 - Meal plan (breakfast, lunch, dinner)
 - Dietary preference
 - Special dietary requirements
- **Hostel & Mess Fees:**
 - Hostel fee (room type based)
 - Mess fee (dietary preference based)
 - Package discount (10%)

**TRIGGER EVENT:**
- **Admission Confirmed:** Hostel allocation processed
- **Boarding Requested:** Room assigned
- **Mess Enrolled:** Meal card issued

**IMPACT:**
- **Hostel Allocation:**
 - Rohan (from different city) requests boarding
 - Gender: Male
 - Room preference: Shared (cost-effective)
 - Allocated: Boys Hostel A, Floor 2, Room 205, Bed 2
 - Roommates: Aarav (Bed 1), Arjun (Bed 3)
 - Warden: Mr. Sharma
- **Mess Enrollment:**
 - Dietary preference: Vegetarian
 - Meal plan: Full (breakfast + lunch + dinner)
 - Meal card: #MESS-2024-0234
- **Fee Calculation:**
 - Hostel (shared): ₹60,000/year
 - Mess (veg): ₹48,000/year
 - Subtotal: ₹1,08,000
 - Package discount (10%): -₹10,800
 - Total boarding fee: ₹97,200/year
 - Added to admission invoice

**BUSINESS LOGIC:**
```
FUNCTION allocate_hostel_room(student, gender, room_preference):
 // Get appropriate hostel
 IF gender = "MALE":
 hostel = GET Hostel WHERE type = "BOYS" AND has_vacancy
 ELSE:
 hostel = GET Hostel WHERE type = "GIRLS" AND has_vacancy
 END IF
 
 // Find room based on preference
 IF room_preference = "SINGLE":
 room = FIND_SINGLE_ROOM(hostel)
 hostel_fee = 120000
 ELSE IF room_preference = "DOUBLE":
 room = FIND_DOUBLE_ROOM(hostel)
 hostel_fee = 80000
 ELSE: // TRIPLE (shared)
 room = FIND_TRIPLE_ROOM(hostel)
 hostel_fee = 60000
 END IF
 
 // Allocate bed
 bed = GET_AVAILABLE_BED(room)
 
 allocation = CREATE HostelAllocation
 allocation.student = student
 allocation.hostel = hostel
 allocation.room = room
 allocation.bed = bed
 allocation.warden = hostel.warden
 
 student.hostel_allocation = allocation
 student.boarding_status = "BOARDER"
 
 // Add hostel fee
 ADD_FEE(student, "HOSTEL", hostel_fee)
 
 RETURN allocation
END FUNCTION

FUNCTION enroll_in_mess(student, dietary_preference):
 // Calculate mess fee
 IF dietary_preference = "VEGETARIAN":
 mess_fee = 48000
 ELSE IF dietary_preference = "NON_VEGETARIAN":
 mess_fee = 60000
 ELSE IF dietary_preference = "SPECIAL_DIET":
 mess_fee = 72000
 END IF
 
 mess_enrollment = CREATE MessEnrollment
 mess_enrollment.student = student
 mess_enrollment.dietary_preference = dietary_preference
 mess_enrollment.meal_plan = "FULL" // B+L+D
 
 // Issue meal card
 meal_card = ISSUE_MEAL_CARD(student)
 
 student.mess_enrollment = mess_enrollment
 
 // Add mess fee
 ADD_FEE(student, "MESS", mess_fee)
 
 // Apply package discount if both hostel and mess
 IF student.hostel_allocation AND student.mess_enrollment:
 total_boarding_fee = student.hostel_fee + student.mess_fee
 discount = total_boarding_fee * 0.10
 APPLY_DISCOUNT(student, "BOARDING_PACKAGE", discount)
 END IF
 
 RETURN mess_enrollment
END FUNCTION
```

**EXAMPLE:**
- **Kavya's Boarding Enrollment:**
 - From: Bangalore (800 km away)
 - Requires: Boarding
 - Room: Shared (triple)
 - Dietary: Vegetarian
 - **Allocation:**
 - Hostel: Girls Hostel A
 - Room: 305, Bed 2
 - Roommates: Priya, Ananya
 - Warden: Ms. Gupta
 - **Fees:**
 - Tuition: ₹1,50,000
 - Hostel: ₹60,000
 - Mess: ₹48,000
 - Subtotal: ₹2,58,000
 - Boarding discount (10%): -₹10,800
 - **Total: ₹2,47,200/year**

---

## INBOUND CONNECTIONS (Other Modules → Admissions)

### FROM MARKETING & WEBSITE

**WHY:** Lead generation drives admissions pipeline.

**DATA RECEIVED:**
- Website inquiry forms
- Open house registrations
- Ad campaign leads (Google, Facebook)
- School tour requests
- Referral leads

**IMPACT:**
- 2,000 inquiries generated per admission season
- 60% convert to applications (1,200)
- 40% of applications get admission (480)
- Final enrollment: 400 students

**TRIGGER:** Lead captured online, event registration

---

### FROM STUDENT MANAGEMENT

**WHY:** Sibling data enables sibling preference and discounts.

**DATA RECEIVED:**
- Current students list
- Sibling relationships
- Alumni database

**IMPACT:**
- Priya (current Grade 9) has brother Aarav applying
- Sibling preference: Priority admission + ₹5,000 discount
- Both siblings get 10% annual fee discount
- Family saves ₹27,000/year

**TRIGGER:** Application submitted with sibling reference

---

### FROM FEE MANAGEMENT

**WHY:** Payment confirmation triggers admission confirmation.

**DATA RECEIVED:**
- Payment status (paid/pending/failed)
- Payment receipts
- Transaction IDs

**IMPACT:**
- Admission offered to Rohan, deadline March 20
- Payment received March 18
- Admission auto-confirmed
- Student record creation triggered

**TRIGGER:** Payment received, receipt generated

---

### FROM ASSESSMENT MODULE

**WHY:** Entrance test scores determine admission eligibility.

**DATA RECEIVED:**
- Test scores (subject-wise, total)
- Percentile rankings
- Cutoff marks
- Merit list

**IMPACT:**
- Cutoff: 70% (210/300)
- Aarav: 245/300 (82%) → Shortlisted
- Rohan: 195/300 (65%) → Not shortlisted
- Priya: 263/300 (88%) → Direct offer

**TRIGGER:** Test results published

---

### FROM PARENT PORTAL

**WHY:** Parents submit applications and documents online.

**DATA RECEIVED:**
- Online application forms
- Document uploads (birth certificate, TC, report cards)
- Payment confirmations
- Communication preferences

**IMPACT:**
- 1,200 applications submitted online (100%)
- 1,000 complete with all documents (83%)
- 200 incomplete, reminders sent (17%)
- Document verification automated

**TRIGGER:** Application submitted, document uploaded

---

## SUMMARY

**Admissions & CRM Module connects to 12+ modules**

**Admission Funnel (Example School, Annual):**
- **Inquiries:** 2,000
- **Applications:** 1,200 (60% conversion)
- **Entrance Test Appeared:** 1,000 (83%)
- **Shortlisted (Test + Interview):** 600 (60%)
- **Offers Made:** 500 (83%)
- **Admissions Confirmed:** 400 (80% yield)
- **Seats Available:** 400 (Grade 1-12 combined)

**Admission Categories & Discounts:**
- **General:** 60% (no discount)
- **Sibling:** 20% (₹5,000 admission fee discount + 10% annual fee discount)
- **Alumni Child:** 10% (₹10,000 admission fee discount + 15% annual fee discount)
- **Staff Children:** 5% (50% fee waiver)
- **Scholarship:** 5% (25-100% fee waiver based on merit)

**Admission Timeline (Annual Cycle):**
- **November:** Admission announcement, inquiry portal opens
- **December:** Applications open, campus tours
- **January:** Entrance tests conducted
- **February:** Interviews, admission decisions
- **March:** Admission offers, fee payment deadline
- **April:** Academic year starts, new students join

**Fee Structure (Admission Package - Grade 9 Example):**
- **Registration:** ₹10,000 (non-refundable, paid at inquiry)
- **Admission:** ₹40,000 (one-time, paid at confirmation)
- **Caution Deposit:** ₹10,000 (refundable at exit)
- **First Quarter Tuition:** ₹37,500
- **Total Admission Package:** ₹97,500

**Additional Fees (Optional):**
- **Transport:** ₹12,000 - ₹30,000/year (distance-based)
- **Hostel:** ₹60,000 - ₹1,20,000/year (room type)
- **Mess:** ₹48,000 - ₹72,000/year (dietary preference)

**Entrance Test Statistics:**
- **Subjects:** Math, English, Science (100 marks each)
- **Total:** 300 marks
- **Duration:** 2.5 hours
- **Cutoff:** 70% (210/300)
- **Pass Rate:** 70% (350/500 shortlisted)

**Communication Touchpoints:**
- **Inquiry Stage:** 3 touchpoints (acknowledgment, brochure, follow-up call)
- **Application Stage:** 5 touchpoints (confirmation, document reminders, status updates)
- **Test Stage:** 4 touchpoints (admit card, reminders, results)
- **Admission Stage:** 6 touchpoints (offer, payment reminders, confirmation)
- **Enrollment Stage:** 3 touchpoints (welcome, orientation, first day)
- **Total:** 21 touchpoints per applicant

**Data Freshness:**
- **Real-time:** Application submissions, payment confirmations, test results
- **Daily:** Lead follow-ups, application reviews, document verification
- **Weekly:** Entrance test schedules, interview slots, admission decisions
- **Monthly:** Admission analytics, conversion reports, pipeline reviews
- **Seasonal:** Admission campaigns (Nov-Mar), enrollment planning

**CRM Metrics:**
- **Lead Response Time:** < 24 hours (95% within 24 hours)
- **Application Processing:** 3-5 days
- **Document Verification:** 2-3 days
- **Admission Decision:** 7 days post-interview
- **Parent Satisfaction:** 4.5/5
- **Net Promoter Score (NPS):** 72 (Excellent)

**Technology Integration:**
- **Online Application:** 100% digital
- **Payment Gateway:** Multiple options (UPI, cards, net banking)
- **Document Upload:** Cloud storage, automated verification
- **OMR Scanning:** Entrance test evaluation
- **CRM System:** Lead tracking, automated follow-ups
- **Parent Portal:** 24/7 access to application status

This module is the **"enrollment pipeline engine"** - converting prospective families into enrolled students through systematic lead management, transparent processes, data-driven decision-making, and excellent parent experience, ensuring optimal seat utilization and revenue generation while maintaining admission quality standards.


---

# Submodule Breakdown

# ADMISSIONS & CRM MODULE - SUBMODULE OVERVIEW

**Module Code:** ADM-015  
**Category:** Core Foundation  
**Priority:** P0  
**Owner:** Admissions Team

## Submodule Breakdown

This module is divided into **7 submodules**, each handling a specific aspect of admissions & crm management:

### 1. Enquiry Management
**Code:** ADM-001  
**File:** `01_enquiry_management.md`  
**Purpose:** Enquiry Management functionality  

### 2. Application Processing
**Code:** ADM-002  
**File:** `02_application_processing.md`  
**Purpose:** Application Processing functionality  

### 3. Entrance Test & Interview
**Code:** ADM-003  
**File:** `03_entrance_test_interview.md`  
**Purpose:** Entrance Test & Interview functionality  

### 4. Seat Allocation & Waitlist
**Code:** ADM-004  
**File:** `04_seat_allocation_waitlist.md`  
**Purpose:** Seat Allocation & Waitlist functionality  

### 5. Document Collection
**Code:** ADM-005  
**File:** `05_document_collection.md`  
**Purpose:** Document Collection functionality  

### 6. Admission Confirmation
**Code:** ADM-006  
**File:** `06_admission_confirmation.md`  
**Purpose:** Admission Confirmation functionality  

### 7. CRM & Lead Nurturing
**Code:** ADM-007  
**File:** `07_crm_lead_nurturing.md`  
**Purpose:** CRM & Lead Nurturing functionality  

## Integration Points

Admissions & CRM connects to multiple modules across the Hogwarts ERP system.

## Development Priority

**Phase 1 (Critical):** Submodules 1-3  
**Phase 2 (High):** Submodules 4-5  
**Phase 3 (Medium):** Remaining submodules  

---

**Status:** Submodule structure defined ⏳  
**Last Updated:** January 15, 2026  
**Note:** Detailed submodule documentation to be created

# HEALTH & WELLNESS MODULE - COMPLETE DEPENDENCY ANALYSIS

## MODULE OVERVIEW

**Name:** Health & Wellness Module 
**Role:** Medical Care & Mental Health Hub - Student & Staff Well-being 
**Type:** Critical Health & Safety Module 
**Dependencies:** 15+ modules rely on health data for student care and compliance 

**Primary Functions:**
- Medical Records Management (students & staff)
- Infirmary Operations & Sick Bay Management
- Vaccination & Immunization Tracking
- Health Screening & Annual Check-ups
- Emergency Medical Response
- Chronic Condition Management (diabetes, asthma, epilepsy)
- Mental Health & Counseling Services
- Sports Injury Management
- Medicine Inventory & Dispensing
- Health Insurance Integration
- Parent Health Notifications
- School Doctor & Nurse Management
- Health Compliance & Reporting

---

## OUTBOUND CONNECTIONS (Health & Wellness → Other Modules)

### 1. TO STUDENT MANAGEMENT MODULE

**WHY This Connection Exists:**
Medical history is a core student attribute. Chronic conditions affect daily activities and emergency response. Vaccination records required for admission. Health status impacts sports participation and exam accommodations.

**DATA FLOW:**
- **Medical Profile:**
 - Blood group (A+, B+, O+, AB+, A-, B-, O-, AB-)
 - Chronic conditions (diabetes, asthma, epilepsy, heart condition)
 - Allergies (food, medication, environmental)
 - Current medications
 - Emergency contact (parent, local guardian, family doctor)
- **Vaccination Records:**
 - Mandatory vaccines (MMR, DPT, Hepatitis B, etc.)
 - COVID-19 vaccination status
 - Booster doses and due dates
 - Vaccination certificates
- **Health Restrictions:**
 - Sports restrictions (no contact sports, limited physical activity)
 - Dietary restrictions (medical reasons)
 - Exam accommodations (extra time, separate room, scribe)
 - Activity limitations (no swimming, no field trips)
- **Health Incidents:**
 - Infirmary visits (symptoms, diagnosis, treatment)
 - Emergency hospitalizations
 - Injuries (sports, accidents)
 - Mental health counseling sessions

**TRIGGER EVENT:**
- **Admission:** Medical records collected, vaccination verified
- **Infirmary Visit:** Health incident logged to student profile
- **Chronic Condition Diagnosed:** Medical profile updated, alerts set
- **Emergency Hospitalization:** Incident recorded, parents notified
- **Annual Health Check-up:** Medical profile updated
- **TC Request:** Health clearance checked

**IMPACT:**
- **Chronic Condition Management:**
 - Priya (Grade 9) has Type 1 Diabetes (insulin-dependent)
 - Medical profile updated:
 - Condition: Type 1 Diabetes
 - Medication: Insulin injections (3 times/day)
 - Emergency protocol: If unconscious, administer glucagon, call ambulance
 - Dietary restriction: Low sugar, controlled carbs (linked to mess)
 - Sports restriction: Monitor blood sugar before/after sports
 - Alerts sent to:
 - Class teacher: "Priya diabetic, watch for hypoglycemia symptoms"
 - Sports teacher: "Check blood sugar before PE class"
 - Hostel warden: "Ensure insulin injections administered"
 - Mess manager: "Special diabetic meal plan"
 - Emergency kit: Glucagon kept in infirmary + hostel
- **Allergy Management:**
 - Aarav (Grade 8) severe peanut allergy (anaphylaxis risk)
 - Medical profile:
 - Allergy: Peanuts (SEVERE - anaphylaxis)
 - Emergency medication: EpiPen (kept in infirmary)
 - Emergency protocol: If exposed, administer EpiPen immediately, call ambulance
 - Alerts sent to:
 - Mess manager: "NO PEANUTS in Aarav's meals" (flagged in red)
 - Class teacher: "Aarav peanut allergy, EpiPen in infirmary"
 - Parents: "Ensure Aarav doesn't share food with classmates"
 - Canteen: Peanut-containing items labeled clearly
- **Sports Injury:**
 - Rohan (Grade 10) sprains ankle during football match
 - Taken to infirmary immediately
 - Nurse diagnosis: Grade 2 ankle sprain
 - Treatment: Ice, compression bandage, pain relief
 - Rest: 2 weeks, no sports
 - Logged to student profile:
 - Incident: Ankle sprain (right ankle)
 - Date: March 15, 2024
 - Treatment: RICE protocol, ibuprofen
 - Follow-up: Physiotherapy (5 sessions)
 - Sports restriction: 2 weeks rest, then gradual return
 - Parent notified: "Rohan sprained ankle, resting for 2 weeks"
 - Sports teacher notified: "Rohan excused from PE for 2 weeks"
- **Mental Health Support:**
 - Kavya (Grade 11) experiencing anxiety and depression
 - Referred to school counselor by class teacher
 - Counseling sessions: 8 sessions over 2 months
 - Logged to student profile (confidential):
 - Mental health concern: Anxiety, academic pressure
 - Counselor: Ms. Mehta
 - Sessions: 8 (weekly)
 - Progress: Improved coping mechanisms, stress reduced
 - Recommendations: Continue monitoring, reduce academic load
 - Exam accommodations: Extra 30 mins (anxiety management)
 - Parent involvement: Counselor sessions with parents (consent-based)

**BUSINESS LOGIC:**
```
FUNCTION update_medical_profile(student, medical_data):
 medical_profile = GET_OR_CREATE medical_profile WHERE student = student
 
 // Update basic medical info
 medical_profile.blood_group = medical_data.blood_group
 medical_profile.chronic_conditions = medical_data.chronic_conditions
 medical_profile.allergies = medical_data.allergies
 medical_profile.current_medications = medical_data.current_medications
 
 // Set emergency contacts
 medical_profile.emergency_contacts = medical_data.emergency_contacts
 medical_profile.family_doctor = medical_data.family_doctor
 
 // Check for critical conditions
 FOR each condition IN medical_data.chronic_conditions:
 IF condition.severity = "CRITICAL":
 // Alert all relevant stakeholders
 ALERT class_teacher("CRITICAL: {student.name} has {condition.name}")
 ALERT sports_teacher("Medical restriction: {student.name}")
 ALERT infirmary_nurse("Critical condition: {student.name} - {condition.name}")
 
 // Create emergency protocol
 CREATE emergency_protocol(student, condition)
 END IF
 END FOR
 
 // Check for severe allergies
 FOR each allergy IN medical_data.allergies:
 IF allergy.severity = "SEVERE":
 ALERT mess_manager("SEVERE ALLERGY: {student.name} - {allergy.allergen}")
 ALERT class_teacher("Allergy alert: {student.name} - {allergy.allergen}")
 
 // Ensure emergency medication available
 IF allergy.emergency_medication:
 STOCK_MEDICATION(infirmary, allergy.emergency_medication)
 END IF
 END IF
 END FOR
 
 // Update student profile
 student.medical_profile = medical_profile
 
 NOTIFY parent("Medical profile updated for {student.name}")
 
 RETURN medical_profile
END FUNCTION

FUNCTION log_infirmary_visit(student, symptoms, diagnosis, treatment):
 visit = CREATE InfirmaryVisit
 visit.student = student
 visit.date = TODAY
 visit.time = NOW
 visit.symptoms = symptoms
 visit.diagnosis = diagnosis
 visit.treatment = treatment
 visit.nurse = current_nurse
 
 // Assess severity
 IF diagnosis.severity = "EMERGENCY":
 // Call ambulance, notify parents immediately
 CALL_AMBULANCE()
 CALL_PARENT_IMMEDIATELY(student, "MEDICAL EMERGENCY: {diagnosis.condition}")
 NOTIFY principal("Medical emergency: {student.name}")
 
 visit.ambulance_called = TRUE
 visit.hospital = "City Hospital"
 
 ELSE IF diagnosis.severity = "SERIOUS":
 // Call parents, may need doctor visit
 CALL_PARENT(student, "Infirmary visit: {diagnosis.condition}, monitoring")
 
 // Monitor in infirmary
 visit.monitoring_required = TRUE
 visit.monitoring_duration = "4 hours"
 
 ELSE:
 // Routine illness, notify parents via SMS
 SEND_SMS(parent, "{student.name} visited infirmary: {symptoms}, {treatment}")
 END IF
 
 // Dispense medication if needed
 IF treatment.medications:
 FOR each medication IN treatment.medications:
 DISPENSE_MEDICATION(medication, student, dosage=treatment.dosage)
 END FOR
 END IF
 
 // Update student medical history
 student.medical_history.add(visit)
 
 // Check if student needs rest
 IF treatment.rest_required:
 // Excuse from classes
 EXCUSE_FROM_CLASSES(student, duration=treatment.rest_duration)
 NOTIFY class_teacher("{student.name} resting in infirmary, excused from classes")
 END IF
 
 RETURN visit
END FUNCTION

FUNCTION handle_medical_emergency(student, emergency_type, location):
 emergency = CREATE MedicalEmergency
 emergency.student = student
 emergency.type = emergency_type
 emergency.location = location
 emergency.timestamp = NOW
 
 // Immediate actions (parallel)
 CALL_AMBULANCE()
 CALL_PARENT_IMMEDIATELY(student, "EMERGENCY: {emergency_type} at {location}")
 NOTIFY principal("Medical emergency: {student.name}")
 NOTIFY school_doctor()
 
 // Get medical history
 medical_history = student.medical_profile
 emergency.medical_history = medical_history
 emergency.blood_group = medical_history.blood_group
 emergency.allergies = medical_history.allergies
 emergency.current_medications = medical_history.current_medications
 
 // Assign staff to accompany
 IF student.boarding_status = "BOARDER":
 warden.accompany_to_hospital(student)
 ELSE:
 class_teacher.accompany_to_hospital(student)
 END IF
 
 // Track ambulance and hospital
 emergency.ambulance_arrival_time = TRACK_AMBULANCE()
 emergency.hospital = "City Hospital" // Nearest hospital
 emergency.hospital_admission_time = TRACK_ADMISSION()
 
 // Continuous parent updates
 SEND_SMS(parent, "Ambulance dispatched, ETA 10 mins")
 SEND_SMS(parent, "Ambulance arrived, {student.name} being taken to City Hospital")
 SEND_SMS(parent, "Reached hospital, doctors attending")
 
 // Log emergency
 student.medical_history.add(emergency)
 
 RETURN emergency
END FUNCTION
```

**EXAMPLE:**
- **Ananya's Health Journey (Grade 9, Full Year):**
 - **April (Admission):**
 - Medical check-up: All clear
 - Blood group: B+
 - Vaccinations: Up to date
 - No chronic conditions
 - No allergies
 - **June:**
 - Infirmary visit: Fever (101°F), throat pain
 - Diagnosis: Viral throat infection
 - Treatment: Paracetamol, throat lozenges, rest
 - Rest: 1 day in infirmary, 1 day at home
 - Parent notified, picked up next day
 - **September:**
 - Sports injury: Twisted knee during basketball
 - Diagnosis: Minor ligament strain
 - Treatment: Ice, compression, rest
 - Physiotherapy: 3 sessions
 - Sports restriction: 1 week
 - **December:**
 - Diagnosed with mild asthma (exercise-induced)
 - Medical profile updated: Asthma (exercise-induced)
 - Medication: Inhaler (Salbutamol) kept in infirmary
 - Sports teacher alerted: "Ananya may need inhaler during PE"
 - **March:**
 - Annual health check-up: All parameters normal
 - Asthma well-controlled with inhaler
 - No other issues

---

### 2. TO PARENT ENGAGEMENT PORTAL

**WHY This Connection Exists:**
Parents need real-time health updates. Medical records accessible for emergencies. Vaccination reminders and health screening schedules. Consent for medical procedures. Counseling session updates (with consent).

**DATA FLOW:**
- **Health Dashboard:**
 - Medical profile (blood group, allergies, conditions)
 - Vaccination status and due dates
 - Recent infirmary visits
 - Upcoming health screenings
 - Medicine dispensed
- **Real-time Notifications:**
 - Infirmary visit alerts
 - Emergency medical alerts
 - Vaccination due reminders
 - Health screening schedules
 - Counseling session updates (consent-based)
- **Health Records:**
 - Downloadable medical certificates
 - Vaccination certificates
 - Fitness certificates (for sports)
 - Health screening reports
- **Consent Management:**
 - Medical procedure consent
 - Counseling consent
 - Health data sharing consent

**TRIGGER EVENT:**
- **Infirmary Visit:** Parent notified immediately
- **Emergency:** Multi-channel urgent alert
- **Vaccination Due:** Reminder sent 1 week before
- **Health Screening Scheduled:** Notification sent
- **Medicine Dispensed:** Parent informed
- **Counseling Session:** Update sent (with consent)

**IMPACT:**
- **Real-time Infirmary Alerts:**
 - **10:30 AM:** Aarav visits infirmary (headache, nausea)
 - **10:35 AM:** Nurse diagnoses: Migraine
 - **10:36 AM:** Parent receives SMS: "Aarav visited infirmary with headache. Diagnosed: Migraine. Given pain relief, resting."
 - **11:00 AM:** Aarav feeling better, returns to class
 - **11:05 AM:** Parent receives SMS: "Aarav feeling better, returned to class"
- **Emergency Alert:**
 - **2:15 PM:** Priya (diabetic) found unconscious in classroom
 - **2:16 PM:** Nurse administers glucagon (emergency protocol)
 - **2:17 PM:** Ambulance called
 - **2:17 PM:** Parent receives call: "EMERGENCY: Priya unconscious, glucagon given, ambulance called"
 - **2:18 PM:** Parent receives SMS: "Ambulance dispatched, taking Priya to City Hospital"
 - **2:30 PM:** Ambulance arrives, Priya taken to hospital
 - **2:31 PM:** Parent receives SMS: "Priya in ambulance, heading to City Hospital, teacher accompanying"
 - **2:45 PM:** Priya reaches hospital
 - **2:46 PM:** Parent receives SMS: "Reached City Hospital, doctors attending"
 - **3:00 PM:** Parent arrives at hospital, takes over
- **Vaccination Reminder:**
 - **Rohan's COVID-19 booster due in 1 week**
 - **Parent receives SMS:** "Reminder: Rohan's COVID-19 booster due on March 20. Please schedule appointment."
 - **Parent portal shows:** "Vaccination Due: COVID-19 Booster (March 20)"
 - **Parent schedules:** Appointment at nearby clinic
 - **March 20:** Rohan gets booster
 - **March 21:** Parent uploads certificate to portal
 - **System updates:** Vaccination status "Up to date"
- **Health Dashboard:**
 - Mrs. Sharma logs into parent portal
 - Sees Aarav's health dashboard:
 - Blood group: O+
 - Allergies: Peanuts (SEVERE)
 - Chronic conditions: None
 - Recent infirmary visits:
 - March 10: Headache (migraine, pain relief given)
 - Feb 15: Fever (viral infection, 1 day rest)
 - Vaccinations: All up to date
 - Next health screening: April 15 (annual check-up)
 - Medicine dispensed (last 30 days): Paracetamol (2 times)

**BUSINESS LOGIC:**
```
FUNCTION parent_health_dashboard(parent):
 children = GET students WHERE parent_id = parent.id
 
 FOR each child IN children:
 health_summary = {
 student: child.name,
 grade: child.grade,
 medical_profile: {
 blood_group: child.medical_profile.blood_group,
 allergies: child.medical_profile.allergies,
 chronic_conditions: child.medical_profile.chronic_conditions,
 current_medications: child.medical_profile.current_medications
 },
 recent_infirmary_visits: GET infirmary_visits(child, last_30_days),
 vaccination_status: {
 up_to_date: CHECK_VACCINATION_STATUS(child),
 due_vaccines: GET due_vaccines(child),
 next_due_date: GET next_vaccination_date(child)
 },
 upcoming_screenings: GET health_screenings(child, upcoming),
 medicine_dispensed: GET medicines_dispensed(child, last_30_days)
 }
 
 dashboard.add(health_summary)
 END FOR
 
 RETURN dashboard
END FUNCTION

FUNCTION send_infirmary_visit_notification(student, visit):
 parent_contacts = GET student.parent_contacts
 
 // Determine notification urgency
 IF visit.diagnosis.severity = "EMERGENCY":
 // Multi-channel urgent alert
 MAKE_CALL(parent_contacts.mobile, "MEDICAL EMERGENCY: {visit.diagnosis.condition}")
 SEND_SMS(parent_contacts.mobile, "EMERGENCY: {student.name} - {visit.diagnosis.condition}. Ambulance called.")
 SEND_EMAIL(parent_contacts.email, emergency_email_template)
 SEND_APP_NOTIFICATION(parent_contacts.app, "MEDICAL EMERGENCY", priority=URGENT)
 
 ELSE IF visit.diagnosis.severity = "SERIOUS":
 // Call + SMS
 MAKE_CALL(parent_contacts.mobile, "Infirmary visit: {visit.diagnosis.condition}")
 SEND_SMS(parent_contacts.mobile, "{student.name} in infirmary: {visit.symptoms}. {visit.diagnosis.condition}. Monitoring.")
 SEND_APP_NOTIFICATION(parent_contacts.app, "Infirmary Visit - Monitoring Required")
 
 ELSE:
 // SMS + App notification
 SEND_SMS(parent_contacts.mobile, "{student.name} visited infirmary: {visit.symptoms}. {visit.treatment.summary}.")
 SEND_APP_NOTIFICATION(parent_contacts.app, "Infirmary Visit")
 END IF
 
 // Update parent portal
 UPDATE parent_portal SET latest_health_update = visit
END FUNCTION

FUNCTION send_vaccination_reminder(student, vaccine, due_date):
 parent_contacts = GET student.parent_contacts
 
 days_until_due = due_date - TODAY
 
 IF days_until_due = 7:
 // 1 week reminder
 message = "Reminder: {student.name}'s {vaccine.name} due on {due_date}. Please schedule appointment."
 SEND_SMS(parent_contacts.mobile, message)
 SEND_EMAIL(parent_contacts.email, vaccination_reminder_template)
 
 ELSE IF days_until_due = 1:
 // 1 day reminder
 message = "URGENT: {student.name}'s {vaccine.name} due tomorrow ({due_date}). Please schedule."
 SEND_SMS(parent_contacts.mobile, message)
 
 ELSE IF days_until_due = -7:
 // Overdue reminder
 message = "OVERDUE: {student.name}'s {vaccine.name} was due on {due_date}. Please schedule immediately."
 SEND_SMS(parent_contacts.mobile, message)
 ALERT school_nurse("Vaccination overdue: {student.name} - {vaccine.name}")
 END IF
END FUNCTION
```

**EXAMPLE:**
- **Mr. Sharma's Health Portal Experience:**
 - **Logs in at 9 PM**
 - **Sees Aarav's Health Dashboard:**
 - Blood group: O+
 - Allergy: Peanuts (SEVERE) - EpiPen in infirmary
 - No chronic conditions
 - Recent visits: 1 (headache, March 10)
 - Vaccinations: All up to date
 - Next screening: April 15
 - **Receives notification:** "Aarav's annual health check-up scheduled: April 15, 10 AM"
 - **Marks calendar:** Ensure Aarav available on April 15
 - **Satisfied:** Child's health well-monitored

---

### 3. TO ATTENDANCE MANAGEMENT MODULE

**WHY This Connection Exists:**
Medical leave affects attendance records. Infirmary rest excuses class absence. Chronic conditions may require frequent absences. Medical certificates validate leave.

**DATA FLOW:**
- **Medical Leave:**
 - Infirmary rest (half-day, full-day)
 - Home rest (doctor's recommendation)
 - Hospitalization (multi-day absence)
 - Medical certificate (for extended absence)
- **Attendance Adjustments:**
 - Medical leave marked (not counted as absence)
 - Attendance percentage calculation (medical leave excluded)
 - Exam eligibility (medical leave considered)
- **Chronic Condition Accommodations:**
 - Frequent medical absences expected
 - Attendance exemptions (with medical certificate)

**TRIGGER EVENT:**
- **Infirmary Rest:** Attendance marked as medical leave
- **Doctor Certificate Submitted:** Leave validated, attendance adjusted
- **Hospitalization:** Multi-day medical leave applied
- **Chronic Condition:** Attendance exemption granted

**IMPACT:**
- **Infirmary Rest:**
 - Priya visits infirmary at 10 AM (stomach pain)
 - Nurse diagnosis: Gastritis
 - Treatment: Antacid, rest
 - Rest: Half-day in infirmary
 - **Attendance:**
 - Morning session: Present (was in class)
 - Afternoon session: Medical leave (infirmary rest)
 - **System auto-marks:** Half-day medical leave
 - **Class teacher notified:** "Priya resting in infirmary, excused from afternoon classes"
- **Home Rest (Doctor Certificate):**
 - Rohan has fever (102°F), parent takes to doctor
 - Doctor prescribes: 3 days rest at home
 - Parent submits medical certificate via portal
 - **System applies:** 3 days medical leave (Mon-Wed)
 - **Attendance marked:** Medical leave (not absence)
 - **Attendance %:** Not affected (medical leave excluded)
- **Hospitalization:**
 - Aarav hospitalized (appendicitis surgery)
 - Hospitalized: March 10-15 (6 days)
 - Parent submits hospital discharge summary
 - **System applies:** 6 days medical leave
 - **Attendance:** Medical leave (not counted as absence)
 - **Exam eligibility:** Not affected (medical leave valid)
- **Chronic Condition Accommodation:**
 - Kavya (Grade 11) has chronic migraine
 - Frequent absences: 2-3 days/month
 - Medical certificate from neurologist: "Chronic migraine, may require frequent rest"
 - **System grants:** Attendance exemption (up to 5 days/month medical leave)
 - **Attendance calculation:** Medical leave excluded from total
 - **Exam eligibility:** Granted despite <75% attendance (medical exemption)

**BUSINESS LOGIC:**
```
FUNCTION mark_medical_leave(student, leave_type, duration, medical_certificate):
 leave = CREATE MedicalLeave
 leave.student = student
 leave.type = leave_type // INFIRMARY_REST, HOME_REST, HOSPITALIZATION
 leave.start_date = duration.start_date
 leave.end_date = duration.end_date
 leave.days = CALCULATE_WORKING_DAYS(start_date, end_date)
 leave.medical_certificate = medical_certificate
 leave.approved_by = school_nurse OR principal
 
 // Mark attendance as medical leave
 FOR each date IN (leave.start_date TO leave.end_date):
 IF is_working_day(date):
 attendance = GET_OR_CREATE attendance WHERE student = student AND date = date
 attendance.status = "MEDICAL_LEAVE"
 attendance.leave_record = leave
 attendance.excused = TRUE
 END IF
 END FOR
 
 // Update student medical history
 student.medical_history.add(leave)
 
 // Notify stakeholders
 NOTIFY class_teacher("{student.name} on medical leave: {leave.start_date} to {leave.end_date}")
 NOTIFY parent("Medical leave applied: {leave.days} days")
 
 // Check if medical certificate required
 IF leave.days >= 3 AND NOT medical_certificate:
 REQUEST_MEDICAL_CERTIFICATE(parent, "Please submit medical certificate for {leave.days}-day absence")
 END IF
 
 RETURN leave
END FUNCTION

FUNCTION calculate_attendance_with_medical_exemption(student, term):
 total_working_days = COUNT working_days IN term
 days_present = COUNT attendance WHERE student = student AND term = term AND status = "PRESENT"
 medical_leave_days = COUNT attendance WHERE student = student AND term = term AND status = "MEDICAL_LEAVE"
 
 // Exclude medical leave from total working days
 effective_working_days = total_working_days - medical_leave_days
 
 // Calculate attendance percentage
 attendance_percentage = (days_present / effective_working_days) * 100
 
 // Check exam eligibility (75% requirement)
 IF attendance_percentage >= 75:
 exam_eligible = TRUE
 ELSE:
 // Check if medical exemption applies
 IF student.has_chronic_condition AND medical_leave_days >= 10:
 // Medical exemption granted
 exam_eligible = TRUE
 exemption_reason = "Chronic medical condition, medical leave: {medical_leave_days} days"
 ELSE:
 exam_eligible = FALSE
 exemption_reason = "Attendance below 75%, no medical exemption"
 END IF
 END IF
 
 RETURN {
 attendance_percentage: attendance_percentage,
 exam_eligible: exam_eligible,
 exemption_reason: exemption_reason
 }
END FUNCTION
```

**EXAMPLE:**
- **Priya's Attendance with Medical Leave:**
 - **Term:** April-September (120 working days)
 - **Attendance:**
 - Present: 100 days
 - Medical leave: 15 days (diabetes-related absences)
 - Unauthorized absence: 5 days
 - **Calculation:**
 - Effective working days: 120 - 15 = 105 days
 - Attendance %: (100 / 105) × 100 = 95.2%
 - **Exam Eligibility:** Yes (95.2% > 75%)
 - **Medical leave:** Excluded from attendance calculation 

---

### 4. TO FEE MANAGEMENT MODULE

**WHY This Connection Exists:**
Medical expenses may be reimbursed (if school insurance). Health insurance premium collected as part of fees. Medical emergency expenses tracked. Infirmary charges (if applicable).

**DATA FLOW:**
- **Health Insurance Premium:**
 - Annual premium (₹5,000-₹10,000)
 - Coverage details (hospitalization, accidents)
 - Claim process
- **Medical Expense Reimbursement:**
 - Emergency hospitalization costs
 - Accident treatment costs
 - Insurance claim submission
 - Reimbursement processing
- **Infirmary Charges:**
 - Medicine costs (if not covered)
 - Special treatments
 - External doctor consultation fees

**TRIGGER EVENT:**
- **Admission:** Health insurance premium added to fee
- **Medical Emergency:** Expenses tracked for insurance claim
- **Insurance Claim:** Reimbursement processed
- **Medicine Dispensed:** Cost tracked (if chargeable)

**IMPACT:**
- **Health Insurance Premium:**
 - All students enrolled in school health insurance
 - Annual premium: ₹8,000/student
 - Coverage: Hospitalization up to ₹5 lakhs, accidents up to ₹2 lakhs
 - Added to annual fee invoice
 - Aarav's fee: Tuition ₹1,50,000 + Insurance ₹8,000 = ₹1,58,000
- **Medical Emergency Reimbursement:**
 - Priya hospitalized (diabetic emergency)
 - Hospital bill: ₹45,000
 - Parent pays hospital directly
 - Submits insurance claim via school
 - School processes claim with insurance company
 - Reimbursement: ₹40,000 (₹5,000 deductible)
 - Credited to parent's account within 30 days
- **Infirmary Medicine Charges:**
 - Most medicines: Free (covered by school)
 - Expensive medicines: Charged to student account
 - Rohan prescribed: Antibiotic course (₹500)
 - Charged to fee account: "Infirmary Medicine: ₹500"
 - Appears in next quarterly invoice

**BUSINESS LOGIC:**
```
FUNCTION add_health_insurance_premium(student):
 insurance_premium = 8000 // Annual premium
 
 // Add to fee account
 ADD_CHARGE(student, "HEALTH_INSURANCE", insurance_premium, "Annual health insurance premium")
 
 // Create insurance policy
 policy = CREATE HealthInsurancePolicy
 policy.student = student
 policy.policy_number = GENERATE_POLICY_NUMBER()
 policy.premium = insurance_premium
 policy.coverage = {
 hospitalization: 500000, // ₹5 lakhs
 accidents: 200000, // ₹2 lakhs
 deductible: 5000 // ₹5,000
 }
 policy.valid_from = ACADEMIC_YEAR_START
 policy.valid_until = ACADEMIC_YEAR_END
 
 student.health_insurance = policy
 
 NOTIFY parent("Health insurance enrolled: Policy #{policy.policy_number}, Coverage: ₹5L hospitalization")
 
 RETURN policy
END FUNCTION

FUNCTION process_insurance_claim(student, medical_expense):
 policy = student.health_insurance
 
 // Verify policy active
 IF policy.valid_until < TODAY:
 RETURN "Error: Insurance policy expired"
 END IF
 
 // Verify coverage
 IF medical_expense.type = "HOSPITALIZATION" AND medical_expense.amount > policy.coverage.hospitalization:
 RETURN "Error: Expense exceeds coverage limit"
 END IF
 
 // Create claim
 claim = CREATE InsuranceClaim
 claim.student = student
 claim.policy = policy
 claim.expense = medical_expense
 claim.claim_amount = medical_expense.amount - policy.coverage.deductible
 claim.status = "SUBMITTED"
 claim.submission_date = TODAY
 
 // Submit to insurance company
 SUBMIT_TO_INSURANCE_COMPANY(claim)
 
 // Track claim
 claim.expected_settlement = TODAY + 30 days
 
 NOTIFY parent("Insurance claim submitted: ₹{claim.claim_amount}, expected settlement in 30 days")
 
 RETURN claim
END FUNCTION
```

**EXAMPLE:**
- **Aarav's Insurance Claim:**
 - **Accident:** Fracture during sports (March 10)
 - **Hospitalization:** 3 days
 - **Hospital bill:** ₹65,000
 - **Parent pays:** ₹65,000 at hospital
 - **March 12:** Submits claim via school portal
 - Hospital bills uploaded
 - Discharge summary uploaded
 - **March 15:** School processes claim, submits to insurance
 - **April 10:** Insurance approves ₹60,000 (₹5,000 deductible)
 - **April 15:** ₹60,000 credited to parent's bank account
 - **Parent's net expense:** ₹5,000 (deductible)

---

### 5. TO SPORTS & PHYSICAL EDUCATION MODULE

**WHY This Connection Exists:**
Medical fitness required for sports participation. Sports injuries managed by health module. Pre-sports health screening. Medical restrictions affect sports eligibility.

**DATA FLOW:**
- **Sports Fitness Certificate:**
 - Annual medical check-up
 - Fitness for sports participation
 - Restrictions (if any)
- **Sports Injury Management:**
 - Injury assessment and treatment
 - Recovery timeline
 - Return-to-play protocol
 - Physiotherapy referrals
- **Medical Restrictions:**
 - No contact sports (heart condition)
 - Limited physical activity (asthma)
 - No swimming (ear infection)

**TRIGGER EVENT:**
- **Sports Season Start:** Fitness certificate required
- **Sports Injury:** Treatment and recovery managed
- **Medical Condition Diagnosed:** Sports restrictions applied
- **Injury Recovery:** Return-to-play clearance

**IMPACT:**
- **Sports Fitness Screening:**
 - Annual sports fitness check-up (August)
 - All students participating in sports screened
 - Aarav's screening:
 - Blood pressure: Normal
 - Heart rate: Normal
 - Respiratory: Normal
 - Musculoskeletal: Normal
 - **Fitness certificate:** Issued, valid for 1 year
 - **Sports eligibility:** Approved for all sports
- **Sports Injury Management:**
 - Rohan fractures wrist during cricket (March 10)
 - Taken to hospital, X-ray confirms fracture
 - Treatment: Cast applied, 6 weeks healing
 - **Sports restriction:** No sports for 6 weeks
 - **Physiotherapy:** 8 sessions after cast removal
 - **Return-to-play protocol:**
 - Week 7: Cast removed, physiotherapy starts
 - Week 9: Gentle exercises (no contact)
 - Week 11: Gradual return to cricket (batting only)
 - Week 13: Full clearance (bowling allowed)
 - **Sports teacher notified:** "Rohan cleared for full sports participation"
- **Medical Restriction:**
 - Priya (diabetic) wants to join school football team
 - School doctor assesses: "Diabetes well-controlled, can play with precautions"
 - **Restrictions:**
 - Check blood sugar before practice/match
 - Carry glucose tablets during play
 - Inform coach if feeling dizzy/weak
 - **Sports teacher briefed:** "Priya diabetic, monitor during practice"
 - **Priya joins team:** Plays safely with precautions

**BUSINESS LOGIC:**
```
FUNCTION issue_sports_fitness_certificate(student, screening_results):
 certificate = CREATE SportsFitnessCertificate
 certificate.student = student
 certificate.screening_date = TODAY
 certificate.screening_results = screening_results
 certificate.valid_until = TODAY + 1 year
 
 // Assess fitness
 IF screening_results.all_normal:
 certificate.fitness_status = "FIT_FOR_ALL_SPORTS"
 certificate.restrictions = NONE
 
 ELSE IF screening_results.has_minor_issues:
 certificate.fitness_status = "FIT_WITH_RESTRICTIONS"
 certificate.restrictions = DETERMINE_RESTRICTIONS(screening_results)
 
 ELSE:
 certificate.fitness_status = "NOT_FIT"
 certificate.restrictions = "No sports participation until medical clearance"
 END IF
 
 // Update student profile
 student.sports_fitness = certificate
 
 // Notify sports teacher
 NOTIFY sports_teacher("{student.name} sports fitness: {certificate.fitness_status}")
 
 // Notify parent
 NOTIFY parent("Sports fitness certificate issued: {certificate.fitness_status}")
 
 RETURN certificate
END FUNCTION

FUNCTION manage_sports_injury(student, injury_details):
 injury = CREATE SportsInjury
 injury.student = student
 injury.type = injury_details.type // FRACTURE, SPRAIN, CONCUSSION, etc.
 injury.body_part = injury_details.body_part
 injury.severity = injury_details.severity
 injury.date = TODAY
 injury.treatment = injury_details.treatment
 
 // Determine recovery timeline
 recovery_timeline = ESTIMATE_RECOVERY(injury.type, injury.severity)
 injury.expected_recovery = TODAY + recovery_timeline
 
 // Set sports restriction
 restriction = CREATE SportsRestriction
 restriction.student = student
 restriction.reason = "Sports injury: {injury.type}"
 restriction.restriction_type = "NO_SPORTS"
 restriction.start_date = TODAY
 restriction.end_date = injury.expected_recovery
 
 student.sports_restrictions.add(restriction)
 
 // Notify stakeholders
 NOTIFY sports_teacher("{student.name} injured: {injury.type}, no sports for {recovery_timeline} days")
 NOTIFY parent("Sports injury: {injury.type}, recovery: {recovery_timeline} days")
 
 // Schedule follow-up
 IF injury.requires_physiotherapy:
 SCHEDULE_PHYSIOTHERAPY(student, sessions=injury.physio_sessions)
 END IF
 
 // Track recovery
 injury.recovery_milestones = DEFINE_RECOVERY_MILESTONES(injury)
 
 RETURN injury
END FUNCTION
```

**EXAMPLE:**
- **Kavya's Sports Participation:**
 - **August:** Sports fitness screening
 - All parameters normal
 - Fitness certificate: Fit for all sports
 - **September:** Joins school basketball team
 - **October:** Sprains ankle during practice
 - Treatment: RICE protocol, 1 week rest
 - Sports restriction: 1 week no sports
 - **October (Week 2):** Ankle healed, returns to basketball
 - **March:** Completes basketball season, no further injuries

---

### 6. TO HOSTEL MANAGEMENT MODULE

**WHY This Connection Exists:**
Boarders' health monitored closely. Hostel infirmary for night emergencies. Chronic condition management in hostel. Medicine administration by hostel nurse.

**DATA FLOW:**
- **Hostel Health Monitoring:**
 - Chronic condition alerts to warden
 - Medicine administration schedule
 - Emergency protocols
- **Hostel Infirmary:**
 - Night-time health issues
 - Emergency response
 - Warden coordination
- **Medicine Administration:**
 - Daily medicines (diabetes, asthma)
 - Warden ensures compliance
 - Medicine stock in hostel

**TRIGGER EVENT:**
- **Boarder Admitted:** Health alerts sent to warden
- **Medicine Required:** Stock maintained in hostel
- **Night Emergency:** Hostel nurse responds
- **Chronic Condition:** Warden briefed on management

**IMPACT:**
- **Chronic Condition Management (Boarder):**
 - Priya (Grade 9, boarder, diabetic)
 - Insulin injections: 3 times/day (before meals)
 - Hostel nurse administers injections
 - Warden monitors: Ensures Priya takes injections on time
 - Emergency kit: Glucagon kept in hostel infirmary
 - **Daily routine:**
 - 7:30 AM: Insulin injection (before breakfast)
 - 1:00 PM: Insulin injection (before lunch)
 - 7:30 PM: Insulin injection (before dinner)
 - **Warden checklist:** Verifies injections given daily
- **Night-time Emergency:**
 - Rohan (boarder) has severe stomach pain at 11 PM
 - Warden takes to hostel infirmary
 - Hostel nurse assesses: Possible appendicitis
 - **Emergency protocol:**
 - Call school doctor (11:05 PM)
 - Doctor advises: Take to hospital immediately
 - Call ambulance (11:10 PM)
 - Call parent (11:10 PM): "Rohan severe stomach pain, taking to hospital"
 - Warden accompanies to hospital
 - **Hospital:** Appendicitis confirmed, surgery next morning
 - **Parent:** Arrives at hospital, takes over care

**BUSINESS LOGIC:**
```
FUNCTION setup_hostel_health_monitoring(student):
 IF student.boarding_status != "BOARDER":
 RETURN
 END IF
 
 // Alert warden about chronic conditions
 IF student.medical_profile.chronic_conditions:
 FOR each condition IN student.medical_profile.chronic_conditions:
 ALERT warden("Boarder health alert: {student.name} has {condition.name}")
 
 // Create medicine administration schedule
 IF condition.requires_daily_medication:
 schedule = CREATE MedicineSchedule
 schedule.student = student
 schedule.medication = condition.medication
 schedule.dosage = condition.dosage
 schedule.frequency = condition.frequency
 schedule.administered_by = hostel_nurse
 
 // Add to warden's daily checklist
 warden.daily_checklist.add("Verify {student.name} took {medication.name}")
 END IF
 
 // Stock emergency medication in hostel
 IF condition.emergency_medication:
 STOCK_IN_HOSTEL_INFIRMARY(condition.emergency_medication)
 END IF
 END FOR
 END IF
 
 // Alert about severe allergies
 IF student.medical_profile.has_severe_allergies:
 ALERT warden("SEVERE ALLERGY: {student.name} - {allergy.allergen}")
 STOCK_IN_HOSTEL_INFIRMARY(allergy.emergency_medication)
 END IF
END FUNCTION

FUNCTION handle_hostel_night_emergency(student, symptoms):
 emergency = CREATE HostelNightEmergency
 emergency.student = student
 emergency.symptoms = symptoms
 emergency.time = NOW
 emergency.warden = current_warden
 
 // Hostel nurse assessment
 assessment = hostel_nurse.assess(student, symptoms)
 emergency.assessment = assessment
 
 IF assessment.severity = "CRITICAL":
 // Immediate hospital transfer
 CALL_AMBULANCE()
 CALL_PARENT_IMMEDIATELY(student, "Night emergency: {symptoms}, taking to hospital")
 CALL_SCHOOL_DOCTOR()
 
 emergency.ambulance_called = TRUE
 warden.accompany_to_hospital(student)
 
 ELSE IF assessment.severity = "SERIOUS":
 // Call school doctor for advice
 doctor_advice = CALL_SCHOOL_DOCTOR(student, symptoms, assessment)
 
 IF doctor_advice = "HOSPITAL":
 CALL_AMBULANCE()
 CALL_PARENT(student, "Doctor advised hospital visit: {symptoms}")
 ELSE:
 // Monitor in hostel infirmary
 emergency.monitoring = TRUE
 hostel_nurse.monitor(student, duration=assessment.monitoring_duration)
 END IF
 
 ELSE:
 // Routine illness, treat in hostel infirmary
 treatment = hostel_nurse.treat(student, symptoms)
 emergency.treatment = treatment
 
 // Inform parent next morning
 SCHEDULE_PARENT_NOTIFICATION(student, "Night infirmary visit: {symptoms}, {treatment}")
 END IF
 
 RETURN emergency
END FUNCTION
```

**EXAMPLE:**
- **Aarav's Hostel Health Management:**
 - **Boarder:** Boys Hostel A
 - **Medical condition:** Asthma (exercise-induced)
 - **Medication:** Inhaler (Salbutamol)
 - **Hostel setup:**
 - Inhaler kept in hostel infirmary
 - Warden alerted: "Aarav has asthma, inhaler in infirmary"
 - Emergency protocol: If severe attack, call ambulance
 - **Incident (November):**
 - Aarav has asthma attack at 10 PM (after playing)
 - Warden takes to hostel infirmary
 - Hostel nurse: Administers inhaler, monitors for 30 mins
 - Aarav recovers, returns to room
 - Parent notified next morning: "Aarav had mild asthma attack, managed with inhaler"

---

### 7. TO ACADEMIC PERFORMANCE MODULE

**WHY This Connection Exists:**
Health issues affect academic performance. Medical accommodations for exams (extra time, separate room). Chronic illness may require academic support. Mental health impacts learning.

**DATA FLOW:**
- **Exam Accommodations:**
 - Extra time (anxiety, ADHD, dyslexia)
 - Separate room (medical conditions requiring privacy)
 - Scribe (physical disability, injury)
 - Breaks (diabetes, chronic pain)
- **Academic Support:**
 - Missed classes due to illness
 - Catch-up sessions
 - Extended assignment deadlines
- **Mental Health Impact:**
 - Anxiety affecting exam performance
 - Depression impacting motivation
 - Counseling support for academic stress

**TRIGGER EVENT:**
- **Medical Condition Diagnosed:** Exam accommodations assessed
- **Extended Illness:** Academic support arranged
- **Mental Health Concern:** Counseling + academic adjustments
- **Exam Season:** Accommodations implemented

**IMPACT:**
- **Exam Accommodation (Anxiety):**
 - Kavya (Grade 11) diagnosed with exam anxiety
 - Counselor recommendation: Extra 30 mins per exam
 - **Exam accommodation:**
 - Extra time: 30 mins (3-hour exam → 3.5 hours)
 - Separate room: Quiet room to reduce stress
 - Breaks: 5-min break allowed if needed
 - **Implementation:**
 - Exam controller notified
 - Separate room arranged for all exams
 - Invigilator briefed: "Kavya has anxiety, allow breaks"
 - **Result:** Kavya performs better with accommodations
- **Academic Support (Extended Illness):**
 - Rohan hospitalized 10 days (appendicitis surgery)
 - Missed: 2 weeks of classes
 - **Academic support:**
 - Class notes provided by classmates
 - Catch-up sessions: 5 sessions with teachers (1 hour each)
 - Extended assignment deadline: 2 weeks extension
 - Remedial classes: After recovery
 - **Result:** Rohan catches up, no academic impact
- **Mental Health Support:**
 - Priya experiencing depression (academic pressure)
 - Counseling: 8 sessions over 2 months
 - **Academic adjustments:**
 - Reduced homework load (50% reduction)
 - Extended assignment deadlines
 - No pressure for 100% attendance
 - Focus on core subjects only
 - **Result:** Priya's mental health improves, academic performance stabilizes

**BUSINESS LOGIC:**
```
FUNCTION assess_exam_accommodations(student, medical_condition):
 accommodation = CREATE ExamAccommodation
 accommodation.student = student
 accommodation.medical_condition = medical_condition
 accommodation.recommended_by = school_counselor OR school_doctor
 
 // Determine accommodations based on condition
 IF medical_condition.type = "ANXIETY" OR medical_condition.type = "ADHD":
 accommodation.extra_time = 30 minutes
 accommodation.separate_room = TRUE
 accommodation.breaks_allowed = TRUE
 
 ELSE IF medical_condition.type = "DYSLEXIA":
 accommodation.extra_time = 45 minutes
 accommodation.scribe_allowed = TRUE
 accommodation.separate_room = TRUE
 
 ELSE IF medical_condition.type = "PHYSICAL_DISABILITY":
 accommodation.scribe_allowed = TRUE
 accommodation.wheelchair_accessible_room = TRUE
 accommodation.extra_time = 30 minutes
 
 ELSE IF medical_condition.type = "DIABETES":
 accommodation.breaks_allowed = TRUE
 accommodation.snacks_allowed = TRUE
 accommodation.separate_room = TRUE
 
 END IF
 
 // Update student profile
 student.exam_accommodations = accommodation
 
 // Notify exam controller
 NOTIFY exam_controller("Exam accommodation: {student.name} - {accommodation.details}")
 
 // Notify parent
 NOTIFY parent("Exam accommodations approved: {accommodation.summary}")
 
 RETURN accommodation
END FUNCTION

FUNCTION provide_academic_support_for_illness(student, illness_duration):
 support = CREATE AcademicSupport
 support.student = student
 support.reason = "Extended illness: {illness_duration} days"
 support.start_date = TODAY
 
 // Missed classes
 missed_classes = GET classes WHERE student.grade AND date IN illness_period
 support.missed_classes = missed_classes
 
 // Arrange catch-up sessions
 FOR each subject IN student.subjects:
 teacher = GET teacher WHERE subject = subject
 SCHEDULE catch_up_session(teacher, student, duration=1 hour)
 END FOR
 
 // Extend assignment deadlines
 pending_assignments = GET assignments WHERE student = student AND due_date < TODAY + 14 days
 FOR each assignment IN pending_assignments:
 assignment.due_date += 14 days // 2-week extension
 NOTIFY teacher("Assignment deadline extended for {student.name}: {assignment.title}")
 END FOR
 
 // Provide class notes
 FOR each missed_class IN missed_classes:
 notes = GET class_notes(missed_class)
 SEND_TO_STUDENT(student, notes)
 END FOR
 
 // Notify parent
 NOTIFY parent("Academic support arranged: Catch-up sessions, extended deadlines, class notes provided")
 
 RETURN support
END FUNCTION
```

**EXAMPLE:**
- **Ananya's Exam Accommodation:**
 - **Condition:** ADHD (diagnosed Grade 9)
 - **Challenges:** Difficulty focusing, needs breaks
 - **Accommodation:**
 - Extra time: 30 mins per exam
 - Separate room: Quiet room, fewer distractions
 - Breaks: 5-min break every hour
 - **Grade 10 Board Exams:**
 - Math exam: 3 hours → 3.5 hours (with breaks)
 - Separate room: Small room, 1 invigilator
 - Breaks: 2 breaks taken (5 mins each)
 - **Result:** Ananya completes exam comfortably, scores 85%

---

### 8. TO COMMUNICATION & NOTIFICATION MODULE

**WHY This Connection Exists:**
Health emergencies require immediate multi-channel alerts. Vaccination reminders sent to all parents. Health screening schedules communicated. Wellness tips and health campaigns.

**DATA FLOW:**
- **Emergency Alerts:**
 - Medical emergency (student)
 - Disease outbreak (school-wide)
 - Health advisory (seasonal)
- **Routine Notifications:**
 - Vaccination reminders
 - Health screening schedules
 - Wellness tips
 - Health campaign announcements
- **Communication Channels:**
 - SMS (urgent)
 - Email (detailed)
 - App notifications (real-time)
 - Automated calls (emergencies)

**TRIGGER EVENT:**
- **Medical Emergency:** Multi-channel urgent alert
- **Vaccination Due:** Reminder sent
- **Health Screening Scheduled:** Notification sent
- **Disease Outbreak:** School-wide advisory
- **Health Campaign:** Announcement sent

**IMPACT:**
- **Medical Emergency Alert:**
 - Priya (diabetic) unconscious in class
 - **Parent receives (within 2 mins):**
 - Automated call: "Medical emergency, Priya unconscious, ambulance called"
 - SMS: "EMERGENCY: Priya unconscious, taken to City Hospital"
 - Email: Detailed emergency report
 - App notification: Real-time updates
 - **All channels activated:** Maximum reach
- **Vaccination Reminder (School-wide):**
 - COVID-19 booster due for 500 students
 - **Communication:**
 - SMS to all 500 parents: "COVID-19 booster due, schedule appointment"
 - Email: Detailed vaccination schedule, nearby clinics
 - App notification: "Vaccination reminder"
 - **Result:** 480/500 students vaccinated within 2 weeks (96%)
- **Health Screening Announcement:**
 - Annual health check-up scheduled (August)
 - **Communication:**
 - Email: Detailed schedule, grade-wise timings
 - SMS: "Health check-up: Aug 15-20, your child's slot: Aug 16, 10 AM"
 - App: Calendar invite added
 - **Result:** 95% students attend health check-up
- **Disease Outbreak Advisory:**
 - Dengue outbreak in city (monsoon season)
 - **School-wide advisory:**
 - Email: Dengue prevention tips, symptoms to watch
 - SMS: "Dengue alert: Use mosquito repellent, check for fever"
 - App: Health advisory article
 - **Result:** Parents vigilant, early detection of 3 cases, all recovered

**BUSINESS LOGIC:**
```
FUNCTION send_medical_emergency_alert(student, emergency_details):
 parent_contacts = GET student.parent_contacts
 
 // Multi-channel urgent alert
 // 1. Automated call (highest priority)
 MAKE_AUTOMATED_CALL(parent_contacts.mobile, 
 "This is an urgent medical emergency alert. {student.name} {emergency_details.condition}. {emergency_details.action_taken}. Please contact school immediately.")
 
 // 2. SMS (immediate)
 SEND_SMS(parent_contacts.mobile,
 "EMERGENCY: {student.name} - {emergency_details.condition}. {emergency_details.action_taken}. Hospital: {emergency_details.hospital}. Contact: {school_phone}")
 
 // 3. Email (detailed)
 SEND_EMAIL(parent_contacts.email,
 subject="MEDICAL EMERGENCY: {student.name}",
 body=emergency_email_template(student, emergency_details))
 
 // 4. App notification (real-time updates)
 SEND_APP_NOTIFICATION(parent_contacts.app,
 title="MEDICAL EMERGENCY",
 body="{student.name} - {emergency_details.condition}",
 priority=URGENT,
 sound=TRUE)
 
 // Track delivery
 LOG notification_delivery(student, "MEDICAL_EMERGENCY", channels=["CALL", "SMS", "EMAIL", "APP"])
END FUNCTION

FUNCTION send_vaccination_reminder_campaign(vaccine, due_students):
 // Batch notification to all parents
 FOR each student IN due_students:
 parent_contacts = GET student.parent_contacts
 
 // SMS
 SEND_SMS(parent_contacts.mobile,
 "Reminder: {student.name}'s {vaccine.name} due. Please schedule appointment at nearby clinic.")
 
 // Email (with clinic list)
 SEND_EMAIL(parent_contacts.email,
 subject="Vaccination Reminder: {vaccine.name}",
 body=vaccination_reminder_template(student, vaccine, nearby_clinics))
 
 // App notification
 SEND_APP_NOTIFICATION(parent_contacts.app,
 title="Vaccination Due",
 body="{vaccine.name} due for {student.name}")
 END FOR
 
 // Track campaign
 LOG vaccination_campaign(vaccine, students_count=due_students.count, date=TODAY)
END FUNCTION

FUNCTION send_health_advisory(advisory_type, message, target_audience):
 // School-wide health advisory
 IF target_audience = "ALL_PARENTS":
 parents = GET all_active_parents
 ELSE IF target_audience = "SPECIFIC_GRADE":
 parents = GET parents WHERE student.grade = target_grade
 END IF
 
 FOR each parent IN parents:
 // Email (detailed advisory)
 SEND_EMAIL(parent.email,
 subject="Health Advisory: {advisory_type}",
 body=health_advisory_template(advisory_type, message))
 
 // SMS (brief alert)
 SEND_SMS(parent.mobile,
 "Health Advisory: {message.brief}")
 
 // App (article)
 SEND_APP_NOTIFICATION(parent.app,
 title="Health Advisory",
 body=message.brief,
 article_link=message.detailed_article)
 END FOR
 
 LOG health_advisory(advisory_type, target_audience, date=TODAY)
END FUNCTION
```

**EXAMPLE:**
- **Dengue Outbreak Advisory:**
 - **Trigger:** 5 dengue cases reported in nearby area
 - **School action:** Send health advisory to all parents
 - **Communication (sent to 1,500 parents):**
 - **Email:** Detailed dengue prevention guide
 - Symptoms to watch: Fever, headache, joint pain
 - Prevention: Mosquito repellent, eliminate stagnant water
 - Action: If symptoms, consult doctor immediately
 - **SMS:** "Dengue alert: Use repellent, watch for fever, consult doctor if symptoms"
 - **App:** Health advisory article with images
 - **Result:**
 - 3 students develop dengue (early detection due to alert)
 - All 3 recover fully (early treatment)
 - No school-wide outbreak

---

## INBOUND CONNECTIONS (Other Modules → Health & Wellness)

### FROM STUDENT MANAGEMENT MODULE

**WHY:** Student enrollment triggers medical record creation.

**DATA RECEIVED:**
- New admissions (medical records required)
- Student personal details (age, gender, address)
- Emergency contacts

**IMPACT:**
- Medical profiles created for new students
- Vaccination verification during admission
- Health screening scheduled

**TRIGGER:** Admission, enrollment

---

### FROM PARENT PORTAL

**WHY:** Parents submit medical certificates and health updates.

**DATA RECEIVED:**
- Medical certificates (for leave validation)
- Vaccination certificates (updates)
- Health condition updates (new diagnosis)
- Consent for medical procedures

**IMPACT:**
- Medical records updated
- Leave validated
- Vaccinations marked complete

**TRIGGER:** Parent uploads document, updates health info

---

### FROM SPORTS MODULE

**WHY:** Sports injuries require medical treatment.

**DATA RECEIVED:**
- Injury reports (from sports teacher)
- Fitness assessment requests
- Return-to-play clearance requests

**IMPACT:**
- Injuries treated and tracked
- Fitness certificates issued
- Recovery monitored

**TRIGGER:** Injury reported, fitness assessment needed

---

### FROM HOSTEL MODULE

**WHY:** Boarders' health issues reported by warden.

**DATA RECEIVED:**
- Night-time health issues
- Medicine administration logs
- Chronic condition monitoring

**IMPACT:**
- Health issues addressed
- Medicine compliance tracked
- Warden supported in health management

**TRIGGER:** Boarder reports sick, medicine due

---

### FROM ATTENDANCE MODULE

**WHY:** Medical leave requires health validation.

**DATA RECEIVED:**
- Leave applications (medical reason)
- Absence patterns (frequent illness)

**IMPACT:**
- Medical certificates requested
- Chronic conditions investigated
- Leave validated

**TRIGGER:** Medical leave applied, frequent absences

---

## SUMMARY

**Health & Wellness Module connects to 15+ modules**

**Critical Outbound Dependencies:**
1. Student Management (medical profiles, chronic conditions, health incidents)
2. Parent Portal (health dashboard, emergency alerts, vaccination reminders)
3. Attendance (medical leave, attendance exemptions)
4. Fee Management (health insurance, medical expense reimbursement)
5. Sports & PE (fitness certificates, injury management, medical restrictions)
6. Hostel Management (boarder health monitoring, medicine administration)
7. Academic Performance (exam accommodations, academic support)
8. Communication (emergency alerts, health advisories, vaccination campaigns)

**Critical Inbound Dependencies:**
1. Student Management (enrollment triggers medical record creation)
2. Parent Portal (medical certificates, health updates)
3. Sports Module (injury reports, fitness requests)
4. Hostel Module (boarder health issues)
5. Attendance Module (medical leave validation)

**Health Metrics:**
- **Students with Chronic Conditions:** 5% (75/1,500 students)
- **Infirmary Visits:** 150-200/month
- **Medical Emergencies:** 2-3/year
- **Vaccination Compliance:** 98%
- **Health Screening Coverage:** 95%
- **Counseling Sessions:** 50-60/month

**Common Chronic Conditions:**
- Asthma: 3% of students
- Diabetes: 0.5% of students
- Allergies (severe): 2% of students
- ADHD: 1% of students
- Epilepsy: 0.2% of students

**Infirmary Services:**
- **Operating Hours:** 8 AM - 8 PM (weekdays), 8 AM - 2 PM (weekends)
- **Staff:** 2 nurses (day shift), 1 nurse (night shift for boarders), 1 school doctor (visiting, 3 days/week)
- **Emergency Response Time:** < 5 minutes
- **Medicine Stock:** 100+ common medicines
- **Equipment:** First aid, oxygen, nebulizer, glucometer, BP monitor, thermometer

**Data Freshness:**
- Real-time: Infirmary visits, emergency alerts, medicine dispensing
- Hourly: Health monitoring (chronic conditions)
- Daily: Medicine administration logs, health incident reports
- Weekly: Vaccination reminders, health screening schedules
- Monthly: Health analytics, counseling session summaries
- Annually: Health check-ups, fitness certificates, vaccination audits

This module is the **"health & safety backbone"** - ensuring student and staff well-being through comprehensive medical care, mental health support, emergency response, and preventive health programs.


---

# Submodule Breakdown

# HEALTH & WELLNESS MODULE - SUBMODULE OVERVIEW

**Module Code:** HEALTH-009  
**Category:** Student Services  
**Priority:** P1  
**Owner:** Health Services

## Submodule Breakdown

This module is divided into **7 submodules**, each handling a specific aspect of health & wellness management:

### 1. Medical Records Management
**Code:** HEALTH-001  
**File:** `01_medical_records_management.md`  
**Purpose:** Medical Records Management functionality  

### 2. Infirmary Visit Tracking
**Code:** HEALTH-002  
**File:** `02_infirmary_visit_tracking.md`  
**Purpose:** Infirmary Visit Tracking functionality  

### 3. Vaccination & Immunization
**Code:** HEALTH-003  
**File:** `03_vaccination_immunization.md`  
**Purpose:** Vaccination & Immunization functionality  

### 4. Health Screening & Checkups
**Code:** HEALTH-004  
**File:** `04_health_screening_checkups.md`  
**Purpose:** Health Screening & Checkups functionality  

### 5. Emergency Medical Response
**Code:** HEALTH-005  
**File:** `05_emergency_medical_response.md`  
**Purpose:** Emergency Medical Response functionality  

### 6. Mental Health & Counseling
**Code:** HEALTH-006  
**File:** `06_mental_health_counseling.md`  
**Purpose:** Mental Health & Counseling functionality  

### 7. Sports Injury Management
**Code:** HEALTH-007  
**File:** `07_sports_injury_management.md`  
**Purpose:** Sports Injury Management functionality  

## Integration Points

Health & Wellness connects to multiple modules across the Hogwarts ERP system.

## Development Priority

**Phase 1 (Critical):** Submodules 1-3  
**Phase 2 (High):** Submodules 4-5  
**Phase 3 (Medium):** Remaining submodules  

---

**Status:** Submodule structure defined ⏳  
**Last Updated:** January 15, 2026  
**Note:** Detailed submodule documentation to be created

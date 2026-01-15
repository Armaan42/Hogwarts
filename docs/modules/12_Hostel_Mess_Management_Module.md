# HOSTEL & MESS MANAGEMENT MODULE - COMPLETE DEPENDENCY ANALYSIS

## MODULE OVERVIEW

**Name:** Hostel & Mess Management Module 
**Role:** Residential Life & Dining Operations - Boarding Student Care & Nutrition 
**Type:** Critical Residential & Wellness Module 
**Dependencies:** 18+ modules rely on hostel/mess data for boarding student operations 

**Primary Functions:**
- Room Allocation & Management
- Hostel Attendance (Night Roll Call)
- Leave & Gate Pass Management
- Visitor Management (Parents, Guardians)
- Mess Enrollment & Meal Planning
- Dietary Preference & Restriction Management
- Meal Attendance Tracking (RFID-based)
- Menu Planning & Nutrition Management
- Mess Fee Calculation & Collection
- Warden & Staff Management
- Hostel Discipline & Conduct
- Emergency Response & Safety
- Laundry & Housekeeping Services
- Hostel Maintenance & Facilities

---

## OUTBOUND CONNECTIONS (Hostel & Mess Management → Other Modules)

### 1. TO STUDENT MANAGEMENT MODULE

**WHY This Connection Exists:**
Boarding status is a core student attribute. Hostel room assignment and mess enrollment are part of student profile. Hostel conduct affects character certificates. Residential life experiences shape student development.

**DATA FLOW:**
- **Boarding Status:**
 - BOARDER (lives in hostel)
 - DAY_SCHOLAR (goes home daily)
 - WEEKLY_BOARDER (stays Mon-Fri, goes home weekends)
 - TEMPORARY_BOARDER (occasional stay)
- **Hostel Details:**
 - Hostel block (Boys Hostel A/B, Girls Hostel A/B)
 - Floor number
 - Room number
 - Bed number
 - Roommates (2-3 students per room)
 - Room type (single/double/triple sharing)
- **Mess Enrollment:**
 - Meal plan (breakfast + lunch + dinner)
 - Dietary preference (vegetarian/non-vegetarian/vegan/Jain)
 - Dietary restrictions (allergies, religious, medical)
 - Special diet (medical conditions: diabetes, celiac)
- **Hostel Conduct:**
 - Hostel attendance percentage
 - Discipline incidents (curfew violations, room damage)
 - Hostel responsibilities (room captain, floor monitor)
 - Hostel achievements (cleanliness awards, best room)

**TRIGGER EVENT:**
- **Admission as Boarder:** Hostel room allocated, mess enrolled
- **Boarding Status Change:** Day scholar → Boarder or vice versa
- **Room Change:** New room assigned (conflict resolution, preference)
- **Dietary Change:** Medical condition, religious conversion, preference
- **Hostel Incident:** Discipline violation logged
- **Withdrawal:** Hostel vacated, mess enrollment cancelled

**IMPACT:**
- **Room Allocation:**
 - Aarav (Grade 8) admitted as boarder
 - Assigned: Boys Hostel A, Floor 2, Room 205, Bed 2
 - Roommates: Rohan (Bed 1), Arjun (Bed 3)
 - Room type: Triple sharing
 - Warden: Mr. Sharma
 - Parent receives: "Aarav allocated Room 205, Boys Hostel A, Warden: Mr. Sharma (98xxx)"
- **Dietary Management:**
 - Priya (Grade 9, boarder) is vegetarian with peanut allergy
 - Mess enrollment: Vegetarian meal plan
 - Dietary restriction: NO PEANUTS (flagged in red)
 - Mess staff alerted: "Priya - PEANUT ALLERGY, verify all meals"
 - Menu items with peanuts blocked for Priya's meal card
- **Hostel Conduct:**
 - Kavya (Grade 10, boarder) excellent hostel conduct
 - Attendance: 100% (never missed night roll call)
 - Responsibilities: Floor Monitor (Floor 3, Girls Hostel B)
 - Achievements: "Best Room Award" (3 times), "Cleanliness Champion"
 - Character certificate mentions: "Exemplary residential conduct, responsible floor monitor"
- **Boarding Status Change:**
 - Rohan (Grade 9) was day scholar, family relocating far from school
 - Converts to boarder in October (mid-year)
 - Hostel room allocated: Boys Hostel B, Room 310
 - Mess enrolled: Non-vegetarian meal plan
 - Hostel fee: ₹80,000/year, pro-rata for 6 months = ₹40,000
 - Mess fee: ₹60,000/year, pro-rata for 6 months = ₹30,000
 - Total additional fee: ₹70,000

**BUSINESS LOGIC:**
```
FUNCTION allocate_hostel_room(student, gender, grade):
 // Get appropriate hostel block
 IF gender = "MALE":
 hostel_blocks = GET hostels WHERE type = "BOYS"
 ELSE:
 hostel_blocks = GET hostels WHERE type = "GIRLS"
 END IF
 
 // Find available room
 FOR each block IN hostel_blocks:
 FOR each floor IN block.floors:
 FOR each room IN floor.rooms:
 IF room.available_beds > 0:
 // Allocate bed
 bed_number = GET first_available_bed(room)
 
 allocation = CREATE HostelAllocation
 allocation.student = student
 allocation.hostel_block = block
 allocation.floor = floor
 allocation.room = room
 allocation.bed = bed_number
 allocation.allocation_date = TODAY
 allocation.warden = block.warden
 
 // Update student profile
 student.boarding_status = "BOARDER"
 student.hostel_allocation = allocation
 
 // Update room occupancy
 room.available_beds -= 1
 room.occupied_beds += 1
 room.students.add(student)
 
 // Notify stakeholders
 SEND SMS(parent, "Hostel allocated: {block.name}, Room {room.number}, Bed {bed_number}, Warden: {block.warden.name}")
 NOTIFY warden("New student: {student.name}, Room {room.number}")
 
 RETURN allocation
 END IF
 END FOR
 END FOR
 END FOR
 
 RETURN "Error: No available rooms"
END FUNCTION

FUNCTION enroll_in_mess(student, meal_plan, dietary_preference, dietary_restrictions):
 mess_enrollment = CREATE MessEnrollment
 mess_enrollment.student = student
 mess_enrollment.meal_plan = meal_plan // FULL (B+L+D), LUNCH_DINNER, BREAKFAST_DINNER
 mess_enrollment.dietary_preference = dietary_preference // VEG, NON_VEG, VEGAN, JAIN
 mess_enrollment.dietary_restrictions = dietary_restrictions // Allergies, medical, religious
 mess_enrollment.enrollment_date = TODAY
 
 // Create meal card (RFID)
 meal_card = CREATE MealCard
 meal_card.student = student
 meal_card.rfid_number = GENERATE_RFID()
 meal_card.status = "ACTIVE"
 meal_card.dietary_restrictions = dietary_restrictions
 
 // Flag critical allergies
 IF dietary_restrictions.contains("PEANUT_ALLERGY"):
 meal_card.critical_alert = "PEANUT ALLERGY - VERIFY ALL MEALS"
 ALERT mess_manager("Critical allergy: {student.name} - PEANUTS")
 END IF
 
 // Update student profile
 student.mess_enrollment = mess_enrollment
 student.meal_card = meal_card
 
 // Calculate mess fee
 mess_fee = CALCULATE_MESS_FEE(meal_plan, dietary_preference)
 ADD_FEE(student, "MESS_FEE", mess_fee)
 
 NOTIFY parent("Mess enrolled: {meal_plan}, {dietary_preference}, Meal card: {meal_card.rfid_number}")
 
 RETURN mess_enrollment
END FUNCTION

FUNCTION log_hostel_incident(student, incident_type, description, severity):
 incident = CREATE HostelIncident
 incident.student = student
 incident.type = incident_type // CURFEW_VIOLATION, ROOM_DAMAGE, NOISE_COMPLAINT, UNAUTHORIZED_GUEST
 incident.description = description
 incident.severity = severity // MINOR, MODERATE, MAJOR
 incident.date = TODAY
 incident.reported_by = current_user (warden)
 
 // Determine action
 IF severity = "MAJOR":
 action = "PARENT_CONFERENCE_REQUIRED"
 NOTIFY parent("Serious hostel incident: {incident_type}. Meeting required.")
 NOTIFY principal("Major hostel incident: {student.name}")
 ELSE IF severity = "MODERATE":
 action = "WARNING_LETTER"
 SEND warning_letter(parent)
 ELSE:
 action = "VERBAL_WARNING"
 LOG warning(student)
 END IF
 
 incident.action_taken = action
 
 // Update student conduct record
 student.hostel_conduct_score -= severity_points(severity)
 
 // Add to student profile
 student.hostel_incidents.add(incident)
 
 RETURN incident
END FUNCTION
```

**EXAMPLE:**
- **Ananya's Boarding Journey (Grade 9):**
 - **Admission (April):**
 - Admitted as boarder
 - Hostel: Girls Hostel A, Floor 3, Room 305, Bed 1
 - Roommates: Priya (Bed 2), Kavya (Bed 3)
 - Warden: Ms. Gupta
 - Mess: Vegetarian meal plan (no dietary restrictions)
 - **Mid-Year (October):**
 - Develops lactose intolerance (medical diagnosis)
 - Dietary restriction updated: NO DAIRY
 - Mess manager notified, menu adjusted
 - Meal card flagged: "LACTOSE INTOLERANT"
 - **Year-End (March):**
 - Hostel attendance: 98% (missed 2 nights due to medical leave)
 - Conduct: Excellent (no incidents)
 - Achievement: "Best Room Award" (cleanest room)
 - Character certificate: "Excellent residential conduct, responsible and cooperative"

---

### 2. TO FEE MANAGEMENT MODULE

**WHY This Connection Exists:**
Hostel and mess fees are significant charges for boarding students. Room type determines hostel fee. Meal plan determines mess fee. Non-payment leads to service suspension. Mid-year changes require pro-rata calculations.

**DATA FLOW:**
- **Hostel Fee Structure:**
 - Single room: ₹1,20,000/year
 - Double sharing: ₹80,000/year
 - Triple sharing: ₹60,000/year
- **Mess Fee Structure:**
 - Vegetarian: ₹48,000/year (₹4,000/month)
 - Non-vegetarian: ₹60,000/year (₹5,000/month)
 - Special diet (medical): ₹72,000/year (₹6,000/month)
- **Package Discount:**
 - Hostel + Mess together: 10% discount
 - Example: ₹60,000 (hostel) + ₹48,000 (mess) = ₹1,08,000
 - With discount: ₹97,200
- **Payment Status:**
 - Paid → Full hostel/mess access
 - Overdue 30 days → Warning
 - Overdue 60 days → Mess suspended (hostel continues)
 - Overdue 90 days → Hostel eviction notice

**TRIGGER EVENT:**
- **Boarding Enrollment:** Hostel + mess fees added to invoice
- **Room Type Change:** Fee adjusted (single ↔ double ↔ triple)
- **Dietary Change:** Mess fee adjusted (veg ↔ non-veg ↔ special)
- **Payment Overdue:** Service suspension warnings
- **Boarding Withdrawal:** Pro-rata refund calculated

**IMPACT:**
- **Boarding Package Fee:**
 - Aarav (Grade 8, boarder):
 - Hostel: Triple sharing = ₹60,000/year
 - Mess: Vegetarian = ₹48,000/year
 - Subtotal: ₹1,08,000
 - Package discount (10%): -₹10,800
 - **Total boarding fee: ₹97,200/year**
 - Quarterly: ₹24,300 each (April, July, Oct, Jan)
- **Room Type Change:**
 - Priya initially in triple sharing (₹60,000/year)
 - Requests single room in October (privacy for studies)
 - Single room available: ₹1,20,000/year
 - Additional cost: ₹60,000/year
 - Pro-rata for 6 months (Oct-Mar): ₹30,000
 - Parent pays ₹30,000, Priya moves to single room
- **Service Suspension:**
 - Rohan's mess fee overdue 65 days (₹20,000 pending)
 - Mess card blocked, cannot swipe for meals
 - Hostel access continues (separate fee, paid)
 - Rohan must eat outside or pay cash per meal (₹200/meal)
 - After 3 days, parent pays ₹20,000
 - Mess card reactivated same day
- **Mid-Year Withdrawal:**
 - Kavya withdraws from boarding in November (family relocating nearby)
 - Hostel fee: ₹80,000/year (double sharing)
 - Used: 7 months (April-October) = ₹46,667
 - Mess fee: ₹48,000/year
 - Used: 7 months = ₹28,000
 - Total used: ₹74,667
 - Total paid: ₹97,200 (with package discount)
 - Refund: ₹97,200 - ₹74,667 = ₹22,533
 - Processed within 15 days

**BUSINESS LOGIC:**
```
FUNCTION calculate_boarding_fee(student):
 IF student.boarding_status != "BOARDER":
 RETURN 0
 END IF
 
 // Hostel fee based on room type
 room_type = student.hostel_allocation.room.type
 
 IF room_type = "SINGLE":
 hostel_fee = 120000
 ELSE IF room_type = "DOUBLE":
 hostel_fee = 80000
 ELSE IF room_type = "TRIPLE":
 hostel_fee = 60000
 END IF
 
 // Mess fee based on dietary preference
 dietary_preference = student.mess_enrollment.dietary_preference
 
 IF dietary_preference = "SPECIAL_DIET":
 mess_fee = 72000
 ELSE IF dietary_preference = "NON_VEG":
 mess_fee = 60000
 ELSE: // VEG, VEGAN, JAIN
 mess_fee = 48000
 END IF
 
 total = hostel_fee + mess_fee
 
 // Package discount (10% if enrolled in both)
 IF student.hostel_allocation AND student.mess_enrollment:
 total = total * 0.90 // 10% discount
 END IF
 
 // Pro-rata if enrolled mid-year
 IF student.boarding_enrollment_month > 4: // After April
 months_remaining = 12 - student.boarding_enrollment_month
 total = (total / 12) * months_remaining
 END IF
 
 RETURN total
END FUNCTION

FUNCTION suspend_mess_access(student, reason):
 student.mess_access = "SUSPENDED"
 student.mess_suspension_date = TODAY
 student.mess_suspension_reason = reason
 
 // Block meal card
 BLOCK_MEAL_CARD(student.meal_card)
 
 // Notify stakeholders
 SEND SMS(parent, "Mess access suspended: {reason}. Clear dues to restore.")
 NOTIFY mess_manager("Student {student.name} suspended from mess")
 NOTIFY warden("Mess suspended: {student.name}, monitor student welfare")
 
 LOG mess_suspension(student, reason)
END FUNCTION

FUNCTION restore_mess_access(student):
 student.mess_access = "ACTIVE"
 student.mess_restoration_date = TODAY
 
 // Unblock meal card
 UNBLOCK_MEAL_CARD(student.meal_card)
 
 // Notify stakeholders
 SEND SMS(parent, "Mess access restored. {student.name} can use mess from next meal.")
 NOTIFY mess_manager("Student {student.name} mess access restored")
 
 LOG mess_restoration(student)
END FUNCTION
```

**EXAMPLE:**
- **Rohan's Boarding Fee Journey:**
 - **Admission (April):** Boarder, triple sharing, vegetarian
 - Hostel: ₹60,000/year
 - Mess: ₹48,000/year
 - Package: ₹1,08,000 - 10% = ₹97,200
 - Quarterly: ₹24,300
 - **Q1 (April):** ₹24,300 paid 
 - **Q2 (July):** ₹24,300 paid 
 - **Q3 (October):** Not paid, overdue 35 days
 - Warning: "Pay within 25 days to avoid mess suspension"
 - **Day 60:** Still unpaid
 - Mess suspended, meal card blocked
 - Rohan cannot swipe for meals
 - **Day 65:** Parent pays ₹48,600 (Q3 + Q4)
 - Mess access restored same day
 - **Year-End:** All fees paid, no issues

---

### 3. TO ATTENDANCE MANAGEMENT MODULE

**WHY This Connection Exists:**
Hostel students have different attendance patterns than day scholars. Night roll call tracks hostel attendance. Weekend leave affects hostel presence. Boarders expected to have 100% school attendance (already on campus).

**DATA FLOW:**
- **Hostel Attendance:**
 - Night roll call (9 PM daily)
 - Students present in hostel
 - Students on approved leave (weekend home, medical)
 - Unauthorized absence (missing from hostel)
- **School Attendance for Boarders:**
 - Boarders expected 100% school attendance
 - Absence from class (even though in hostel) → Investigation
 - Medical leave (in hostel infirmary)
- **Weekend Leave:**
 - Student checks out Friday evening
 - Expected return Sunday evening
 - Late return logged

**TRIGGER EVENT:**
- **Night Roll Call:** Hostel attendance marked (9 PM daily)
- **Weekend Leave Request:** Approved/rejected
- **Student Missing:** Warden alerted, parent called
- **Late Return:** Logged, parent notified
- **School Absence (Boarder):** Anomaly flagged

**IMPACT:**
- **Night Roll Call:**
 - **9:00 PM:** Warden conducts roll call, Boys Hostel A, Floor 2
 - **Expected:** 40 students
 - **Present:** 38 students
 - **Absent:** Aarav, Rohan
 - **System checks:**
 - Aarav: Approved weekend leave (went home Friday, returns Sunday)
 - Rohan: No leave approval 
 - **Warden investigates:** Rohan in library (forgot to inform warden)
 - **Rohan marked present** after verification
 - **Incident logged:** "Rohan failed to inform warden of library visit, verbal warning"
- **Boarder School Absence:**
 - **Priya (boarder) present in hostel but absent from class**
 - **System flags:** "Anomaly: Boarder absent from school"
 - **Warden investigates:** Priya unwell, resting in room
 - **Medical leave applied retroactively**
 - **Infirmary visit logged**
- **Weekend Leave:**
 - **Kavya requests weekend leave (Friday-Sunday)**
 - **Reason:** "Going home for cousin's wedding"
 - **Warden approves**
 - **Friday 5 PM:** Kavya checks out (parent picks up)
 - **Sunday 6 PM:** Expected return
 - **Sunday 8 PM:** Kavya hasn't returned
 - **Warden calls parent:** "Kavya not back yet, ETA?"
 - **Parent:** "Traffic jam, will reach by 9 PM"
 - **9:15 PM:** Kavya returns, late return logged
 - **No penalty** (genuine reason, parent informed)

**BUSINESS LOGIC:**
```
FUNCTION hostel_night_roll_call(hostel_block, floor):
 expected_students = GET students WHERE hostel_block = hostel_block 
 AND floor = floor 
 AND boarding_status = "BOARDER"
 
 present_students = []
 absent_students = []
 
 FOR each student IN expected_students:
 // Check if student has approved leave
 has_leave = CHECK leave_request WHERE student = student 
 AND date = TODAY 
 AND status = "APPROVED"
 
 IF has_leave:
 student.hostel_attendance = "ON_LEAVE"
 CONTINUE
 END IF
 
 // Manual roll call by warden
 is_present = WARDEN_MARKS_PRESENT(student)
 
 IF is_present:
 present_students.add(student)
 student.hostel_attendance = "PRESENT"
 ELSE:
 absent_students.add(student)
 student.hostel_attendance = "ABSENT"
 ALERT("Student missing from hostel: {student.name}")
 END IF
 END FOR
 
 // Investigate absences
 FOR each student IN absent_students:
 NOTIFY warden("URGENT: Investigate {student.name} missing from roll call")
 CALL parent("Your child not present in hostel roll call, please contact warden immediately")
 END FOR
 
 RETURN {present: present_students, absent: absent_students}
END FUNCTION

FUNCTION process_weekend_leave(student, leave_details):
 leave_request = CREATE WeekendLeave
 leave_request.student = student
 leave_request.checkout_date = leave_details.checkout_date // Friday
 leave_request.expected_return = leave_details.return_date // Sunday
 leave_request.reason = leave_details.reason
 leave_request.parent_contact = leave_details.parent_contact
 leave_request.status = "PENDING"
 
 // Warden approval
 IF warden_approves(leave_request):
 leave_request.status = "APPROVED"
 leave_request.approved_by = warden
 
 // Pre-mark hostel attendance as "ON_LEAVE"
 FOR each date IN (checkout_date TO expected_return):
 hostel_attendance = GET_OR_CREATE WHERE student = student AND date = date
 hostel_attendance.status = "ON_LEAVE"
 hostel_attendance.leave_request = leave_request
 END FOR
 
 NOTIFY parent("Weekend leave approved. Checkout: {checkout_date}, Return by: {expected_return}")
 ELSE:
 leave_request.status = "REJECTED"
 NOTIFY parent("Weekend leave rejected: {warden.rejection_reason}")
 END IF
 
 RETURN leave_request
END FUNCTION

FUNCTION check_boarder_school_attendance_anomaly():
 // Run at 10 AM daily (after school attendance marking)
 boarders = GET students WHERE boarding_status = "BOARDER"
 
 FOR each student IN boarders:
 hostel_present = CHECK hostel_attendance(student, YESTERDAY) = "PRESENT"
 school_attendance = CHECK school_attendance(student, TODAY)
 
 IF hostel_present AND school_attendance = "ABSENT":
 // Boarder in hostel but absent from school
 ALERT("Anomaly: {student.name} in hostel but absent from school")
 NOTIFY warden("Investigate: {student.name} absent from school")
 
 // Common reasons: Sick in room, infirmary visit, unauthorized absence
 INVESTIGATE(student)
 END IF
 END FOR
END FUNCTION
```

**EXAMPLE:**
- **Aarav's Weekend Leave:**
 - **Thursday:** Requests weekend leave (Friday-Sunday)
 - Reason: "Going home for Diwali"
 - Parent contact: Father (98xxx)
 - **Thursday Evening:** Warden approves
 - **Friday 5 PM:** Aarav checks out
 - Parent picks up from hostel
 - Warden logs: "Aarav checked out 5:00 PM, expected return Sunday 6 PM"
 - **Friday-Sunday:** Hostel attendance pre-marked "ON_LEAVE"
 - **Sunday 5:45 PM:** Aarav returns
 - Warden logs: "Aarav checked in 5:45 PM (15 mins early)"
 - **Sunday 9 PM:** Night roll call
 - Aarav marked "PRESENT" 

---

### 4. TO HEALTH & WELLNESS MODULE

**WHY This Connection Exists:**
Hostel infirmary handles boarder health issues. Medical conditions affect mess diet. Health emergencies require immediate response. Mental health support for homesickness and stress.

**DATA FLOW:**
- **Medical Conditions:**
 - Chronic conditions (diabetes, asthma, epilepsy)
 - Allergies (food, medication)
 - Current medications
 - Emergency contact (parent, local guardian)
- **Infirmary Visits:**
 - Symptoms and diagnosis
 - Treatment provided
 - Medication dispensed
 - Rest period (in room vs infirmary)
- **Health Emergencies:**
 - Serious illness/injury
 - Ambulance dispatch
 - Hospital admission
 - Parent notification
- **Mental Health:**
 - Homesickness counseling
 - Stress management
 - Peer conflict resolution
 - Academic pressure support

**TRIGGER EVENT:**
- **Student Reports Sick:** Infirmary visit logged
- **Medical Emergency:** Ambulance called, parent notified
- **Chronic Condition:** Mess diet adjusted, warden alerted
- **Mental Health Concern:** Counselor assigned

**IMPACT:**
- **Chronic Condition Management:**
 - Priya (Grade 9, boarder) has Type 1 Diabetes
 - Medical profile: Insulin-dependent, requires 3 injections/day
 - Mess diet: Special diabetic meal plan (low sugar, controlled carbs)
 - Warden alerted: "Priya diabetic, monitor for hypoglycemia symptoms"
 - Infirmary: Insulin stored, nurse administers injections
 - Emergency protocol: If unconscious, administer glucagon, call ambulance
- **Infirmary Visit:**
 - Rohan (Grade 8, boarder) reports fever at 10 PM
 - Warden takes to infirmary
 - Nurse checks: Temperature 102°F, throat infection suspected
 - Treatment: Paracetamol, throat lozenges
 - Rest: Rohan stays in infirmary overnight (monitored)
 - Next morning: Fever down to 99°F
 - Rohan returns to room, excused from school for 1 day
 - Parent notified throughout
- **Medical Emergency:**
 - Aarav (Grade 10, boarder) falls from stairs, head injury
 - Warden calls ambulance immediately
 - Parent called: "Aarav injured, ambulance dispatched, going to City Hospital"
 - Ambulance arrives in 15 mins, Aarav taken to hospital
 - Warden accompanies Aarav
 - Hospital: CT scan, minor concussion, observation for 24 hours
 - Parent arrives at hospital
 - Aarav discharged next day, returns to hostel
 - Medical leave: 3 days rest
- **Mental Health Support:**
 - Kavya (Grade 6, new boarder) homesick, crying at night
 - Roommates report to warden
 - Warden counsels Kavya, calls parents for reassurance
 - Counselor assigned: 3 sessions over 2 weeks
 - Counselor teaches coping strategies
 - After 2 weeks: Kavya adjusted, making friends, homesickness reduced

**BUSINESS LOGIC:**
```
FUNCTION log_infirmary_visit(student, symptoms, treatment):
 visit = CREATE InfirmaryVisit
 visit.student = student
 visit.date = TODAY
 visit.time = NOW
 visit.symptoms = symptoms
 visit.diagnosis = nurse.diagnosis
 visit.treatment = treatment
 visit.medication_dispensed = treatment.medications
 visit.rest_period = treatment.rest_duration
 
 // Check if serious
 IF symptoms.severity = "SERIOUS":
 ALERT("Medical emergency: {student.name}")
 CALL ambulance
 NOTIFY parent IMMEDIATELY
 NOTIFY principal
 ELSE:
 // Routine illness
 NOTIFY parent("Infirmary visit: {student.name}, {symptoms}, {treatment}")
 END IF
 
 // Update student health record
 student.health_record.add(visit)
 
 // Adjust mess diet if needed
 IF treatment.requires_special_diet:
 NOTIFY mess_manager("Special diet for {student.name}: {treatment.diet_requirements}")
 END IF
 
 RETURN visit
END FUNCTION

FUNCTION handle_medical_emergency(student, emergency_type):
 emergency = CREATE MedicalEmergency
 emergency.student = student
 emergency.type = emergency_type
 emergency.timestamp = NOW
 emergency.location = student.hostel_allocation.room
 
 // Immediate actions
 CALL ambulance
 NOTIFY parent IMMEDIATELY("EMERGENCY: {student.name} - {emergency_type}. Ambulance called.")
 NOTIFY principal
 NOTIFY warden
 
 // Assign staff to accompany
 warden.accompany_to_hospital(student)
 
 // Get medical history
 medical_history = student.health_record
 emergency.medical_history = medical_history
 
 // Track ambulance and hospital
 emergency.ambulance_arrival_time = TRACK_AMBULANCE()
 emergency.hospital = "City Hospital" // Nearest hospital
 
 LOG emergency
 
 RETURN emergency
END FUNCTION
```

**EXAMPLE:**
- **Ananya's Asthma Management:**
 - **Medical Profile:** Asthma (exercise-induced)
 - **Medication:** Inhaler (Salbutamol), kept in infirmary
 - **Warden Alerted:** "Ananya has asthma, inhaler in infirmary"
 - **Sports Teacher Alerted:** "Ananya may need inhaler during PE"
 - **Incident (October):** Ananya has asthma attack during sports
 - **Sports teacher:** Takes Ananya to infirmary
 - **Nurse:** Administers inhaler, monitors for 30 mins
 - **Ananya recovers:** Returns to hostel, rests
 - **Parent notified:** "Asthma attack managed, Ananya fine now"

---

### 5. TO PARENT ENGAGEMENT PORTAL

**WHY This Connection Exists:**
Parents of boarders need visibility into child's residential life. Hostel attendance, mess consumption, leave requests, and health updates must be accessible. Video calls and visitor scheduling.

**DATA FLOW:**
- **Hostel Dashboard:**
 - Room details (block, floor, room, roommates)
 - Warden contact information
 - Hostel attendance (night roll call)
 - Leave history (weekend leaves, medical leaves)
 - Hostel conduct (incidents, achievements)
- **Mess Dashboard:**
 - Meal plan and dietary preference
 - Meal consumption tracking (breakfast, lunch, dinner)
 - Mess fee status
 - Menu for the week
- **Leave Management:**
 - Submit weekend leave requests
 - Track leave approval status
 - Schedule visitor appointments
- **Communication:**
 - Video call scheduling with child
 - Message warden
 - Emergency contact numbers

**TRIGGER EVENT:**
- **Parent Login:** Hostel/mess dashboard displayed
- **Night Roll Call:** Attendance status updated
- **Leave Request Submitted:** Confirmation sent
- **Leave Approved/Rejected:** Notification sent
- **Hostel Incident:** Alert sent to parent
- **Meal Missed:** Notification (if student skips multiple meals)

**IMPACT:**
- **Hostel Dashboard:**
 - Mrs. Sharma logs into parent portal
 - Sees Aarav's hostel dashboard:
 - Room: Boys Hostel A, Floor 2, Room 205, Bed 2
 - Roommates: Rohan, Arjun
 - Warden: Mr. Sharma (98xxx, sharma@school.com)
 - Hostel attendance (last 7 days): 7/7 (100%)
 - Leave history: 2 weekend leaves (both approved, returned on time)
 - Conduct: Excellent (no incidents)
 - Achievement: "Best Room Award" (October)
- **Mess Dashboard:**
 - Mess plan: Vegetarian, Full (B+L+D)
 - This week's consumption:
 - Monday: B , L , D 
 - Tuesday: B , L (missed), D 
 - Wednesday: B , L , D 
 - Mess fee: ₹48,000/year, paid 
 - This week's menu: (Monday) Aloo Paratha, Dal, Rice, Paneer...
- **Leave Request:**
 - **Thursday 8 PM:** Father submits weekend leave request via portal
 - Dates: Friday 5 PM - Sunday 6 PM
 - Reason: "Family function"
 - Pickup person: Father (ID proof uploaded)
 - **Thursday 9 PM:** Warden approves
 - **Notification:** "Weekend leave approved for Aarav (Fri-Sun)"
 - **Friday 5 PM:** Father picks up Aarav from hostel
 - **Sunday 5:45 PM:** Aarav returns, check-in logged
 - **Portal updated:** "Returned on time "
- **Meal Missed Alert:**
 - Priya misses lunch 3 days in a row (Mon, Tue, Wed)
 - System alerts parent: "Priya missed lunch 3 consecutive days. Please check if everything is okay."
 - Mother calls Priya: "Why skipping lunch?"
 - Priya: "Eating in canteen with friends"
 - Mother: "Okay, but don't waste mess food"

**BUSINESS LOGIC:**
```
FUNCTION parent_hostel_dashboard(parent):
 children = GET students WHERE parent_id = parent.id AND boarding_status = "BOARDER"
 
 FOR each child IN children:
 hostel_summary = {
 student: child.name,
 grade: child.grade,
 room_details: {
 hostel: child.hostel_allocation.block.name,
 floor: child.hostel_allocation.floor,
 room: child.hostel_allocation.room.number,
 bed: child.hostel_allocation.bed,
 roommates: child.hostel_allocation.room.students (exclude child)
 },
 warden: {
 name: child.hostel_allocation.warden.name,
 phone: child.hostel_allocation.warden.phone,
 email: child.hostel_allocation.warden.email
 },
 attendance: {
 last_7_days: GET hostel_attendance(child, last_7_days),
 this_month: GET hostel_attendance_percentage(child, current_month)
 },
 leave_history: GET leave_requests(child, last_30_days),
 conduct: {
 incidents: GET hostel_incidents(child, current_year),
 achievements: GET hostel_achievements(child, current_year)
 },
 mess: {
 meal_plan: child.mess_enrollment.meal_plan,
 dietary_preference: child.mess_enrollment.dietary_preference,
 this_week_consumption: GET meal_consumption(child, this_week),
 mess_fee_status: child.mess_fee_status
 }
 }
 
 dashboard.add(hostel_summary)
 END FOR
 
 RETURN dashboard
END FUNCTION

FUNCTION submit_leave_request_via_portal(parent, student, leave_details):
 // Verify parent authorization
 IF parent NOT IN student.authorized_parents:
 RETURN "Error: Unauthorized"
 END IF
 
 leave_request = CREATE LeaveRequest
 leave_request.student = student
 leave_request.requested_by = parent
 leave_request.checkout_date = leave_details.checkout_date
 leave_request.return_date = leave_details.return_date
 leave_request.reason = leave_details.reason
 leave_request.pickup_person = leave_details.pickup_person
 leave_request.pickup_person_id = leave_details.id_proof
 leave_request.status = "PENDING"
 leave_request.submitted_date = NOW
 
 // Notify warden for approval
 NOTIFY warden("Leave request: {student.name}, {leave_details.reason}, {checkout_date} to {return_date}")
 
 // Confirmation to parent
 SEND SMS(parent, "Leave request submitted for {student.name}. Awaiting warden approval.")
 
 RETURN leave_request
END FUNCTION

FUNCTION alert_parent_meal_missed(student, meal_type, consecutive_days):
 IF consecutive_days >= 3:
 parent_contacts = GET student.parent_contacts
 
 message = "ALERT: {student.name} missed {meal_type} for {consecutive_days} consecutive days. Please check if everything is okay."
 
 SEND SMS(parent_contacts.mobile, message)
 SEND APP_NOTIFICATION(parent_contacts.app, message)
 
 // Also notify warden
 NOTIFY warden("Student {student.name} missing {meal_type} repeatedly, check welfare")
 END IF
END FUNCTION
```

**EXAMPLE:**
- **Mr. Sharma's Portal Experience (Aarav's Father):**
 - **Logs in Sunday 8 PM**
 - **Hostel Dashboard:**
 - Aarav present in hostel 
 - Last night roll call: Present 
 - This week: 7/7 attendance
 - No incidents, conduct excellent
 - **Mess Dashboard:**
 - This week: 20/21 meals consumed (missed 1 lunch)
 - Menu looks good, balanced nutrition
 - **Schedules Video Call:**
 - Clicks "Schedule Video Call"
 - Selects: Next Saturday 6 PM
 - Warden approves
 - Saturday 6 PM: Video call with Aarav for 30 mins
 - **Mr. Sharma satisfied:** Child safe, eating well, happy in hostel

---

### 6. TO SECURITY & VISITOR MANAGEMENT MODULE

**WHY This Connection Exists:**
Hostel security requires strict visitor control. Parents visiting must be verified. Students leaving hostel need gate passes. Emergency evacuations need hostel headcount.

**DATA FLOW:**
- **Visitor Authorization:**
 - Authorized visitors (parents, guardians, relatives)
 - Visitor ID verification
 - Visit purpose and duration
 - Warden approval required
- **Student Exit/Entry:**
 - Weekend leave checkout/checkin
 - Medical emergency exit
 - Unauthorized exit attempts
- **Emergency Evacuation:**
 - Students in hostel (night)
 - Students on leave
 - Total headcount reconciliation

**TRIGGER EVENT:**
- **Visitor Arrives:** ID verification, warden approval
- **Student Checkout:** Gate pass verified
- **Student Checkin:** Return logged
- **Unauthorized Exit:** Alert triggered
- **Emergency:** Evacuation headcount

**IMPACT:**
- **Parent Visit:**
 - Mrs. Sharma wants to visit Aarav (Saturday 2 PM)
 - Submits visitor request via portal (Friday)
 - Warden approves
 - Saturday 2 PM: Mrs. Sharma arrives at hostel gate
 - Security verifies: ID card, visitor approval
 - Mrs. Sharma allowed entry, meets Aarav in visitor room
 - Visit duration: 2 hours
 - 4 PM: Mrs. Sharma exits, visit logged
- **Weekend Leave Checkout:**
 - Priya has approved weekend leave (Friday-Sunday)
 - Friday 5 PM: Father arrives to pick up Priya
 - Security checks: Leave approval , Father's ID 
 - Priya checks out with father
 - Gate pass: "Priya checked out 5:00 PM, return by Sunday 6 PM"
 - Sunday 5:45 PM: Priya returns
 - Security logs: "Priya checked in 5:45 PM "
- **Unauthorized Exit Attempt:**
 - Rohan tries to exit hostel at 10 PM (after curfew, no leave approval)
 - Security stops: "No leave approval, cannot exit"
 - Rohan insists: "Emergency at home"
 - Security calls warden
 - Warden verifies with parent: No emergency
 - Rohan sent back to room
 - Incident logged: "Unauthorized exit attempt, disciplinary action"
- **Emergency Evacuation (Fire Alarm):**
 - Fire alarm at 11 PM
 - Warden evacuates all students to assembly point
 - Headcount:
 - Boys Hostel A: 120 students expected, 115 present, 5 on weekend leave 
 - Girls Hostel A: 100 students expected, 98 present, 2 on weekend leave 
 - Total: 213/220 accounted for 
 - False alarm, students return to hostel

**BUSINESS LOGIC:**
```
FUNCTION process_hostel_visitor(visitor, student):
 // Verify visitor authorization
 IF visitor NOT IN student.authorized_visitors:
 RETURN "Error: Unauthorized visitor. Contact warden for approval."
 END IF
 
 // Verify ID
 id_verified = VERIFY_ID(visitor.id_proof)
 IF NOT id_verified:
 RETURN "Error: ID verification failed"
 END IF
 
 // Get warden approval (if first-time visitor)
 IF visitor.first_time_visit:
 warden_approval = REQUEST_WARDEN_APPROVAL(visitor, student)
 IF NOT warden_approval:
 RETURN "Error: Warden approval required"
 END IF
 END IF
 
 // Log visitor entry
 visit_log = CREATE VisitorLog
 visit_log.visitor = visitor
 visit_log.student = student
 visit_log.entry_time = NOW
 visit_log.purpose = visitor.visit_purpose
 visit_log.approved_by = warden
 
 // Allow entry
 ALLOW_ENTRY(visitor)
 NOTIFY student("Visitor {visitor.name} arrived, meet in visitor room")
 
 RETURN visit_log
END FUNCTION

FUNCTION process_weekend_checkout(student, pickup_person):
 // Verify leave approval
 leave_request = GET leave_request WHERE student = student AND status = "APPROVED" AND checkout_date = TODAY
 
 IF NOT leave_request:
 RETURN "Error: No approved leave for today"
 END IF
 
 // Verify pickup person
 IF pickup_person NOT IN leave_request.authorized_pickup_persons:
 RETURN "Error: Unauthorized pickup person"
 END IF
 
 // Verify ID
 id_verified = VERIFY_ID(pickup_person.id_proof)
 IF NOT id_verified:
 RETURN "Error: ID verification failed"
 END IF
 
 // Log checkout
 checkout_log = CREATE CheckoutLog
 checkout_log.student = student
 checkout_log.checkout_time = NOW
 checkout_log.pickup_person = pickup_person
 checkout_log.expected_return = leave_request.return_date
 checkout_log.leave_request = leave_request
 
 // Update student status
 student.current_location = "ON_LEAVE"
 
 // Notify parent
 SEND SMS(parent, "{student.name} checked out at {NOW} with {pickup_person.name}")
 
 RETURN checkout_log
END FUNCTION
```

**EXAMPLE:**
- **Kavya's Parent Visit:**
 - **Saturday 10 AM:** Mother wants to visit Kavya
 - **Mother arrives at hostel gate**
 - **Security:** "Please show ID and visitor approval"
 - **Mother:** Shows ID card + visitor approval (pre-approved in portal)
 - **Security:** Verifies ID, checks approval 
 - **Security:** "Allowed, meet in visitor room"
 - **Mother meets Kavya:** 2-hour visit (10 AM - 12 PM)
 - **12 PM:** Mother exits, visit logged
 - **Kavya returns to room**

---

### 7. TO DISCIPLINE & BEHAVIOR MANAGEMENT MODULE

**WHY This Connection Exists:**
Hostel conduct is part of overall student discipline. Curfew violations, room damage, noise complaints are disciplinary issues. Hostel responsibilities (room captain) are leadership opportunities.

**DATA FLOW:**
- **Hostel Violations:**
 - Curfew violations (out after 10 PM)
 - Room damage (broken furniture, wall damage)
 - Noise complaints (loud music, disturbances)
 - Unauthorized guests
 - Substance abuse (smoking, alcohol)
- **Disciplinary Actions:**
 - Verbal warning
 - Written warning
 - Parent conference
 - Hostel suspension (temporary)
 - Expulsion (severe cases)
- **Hostel Responsibilities:**
 - Room captain (maintains room cleanliness)
 - Floor monitor (assists warden)
 - Mess captain (collects feedback)

**TRIGGER EVENT:**
- **Violation Reported:** Incident logged, action taken
- **Repeated Violations:** Escalated to principal
- **Responsibility Assigned:** Leadership opportunity
- **Conduct Review:** Term-end assessment

**IMPACT:**
- **Curfew Violation:**
 - Rohan (Grade 9, boarder) found outside room at 11 PM (curfew: 10 PM)
 - Warden: "Why are you out after curfew?"
 - Rohan: "Studying in friend's room, lost track of time"
 - **First offense:** Verbal warning
 - **Incident logged:** "Curfew violation, verbal warning issued"
 - **Parent notified:** "Rohan violated curfew, warned"
 - **Second offense (2 weeks later):** Written warning + parent conference
 - **Third offense:** 3-day hostel suspension (stays with day scholar friend's family)
- **Room Damage:**
 - Aarav and roommates playing cricket in room, break window
 - Warden inspects: Window glass shattered
 - **Damage cost:** ₹2,000 (window replacement)
 - **Action:** Cost split among 3 roommates (₹667 each)
 - **Added to fee account:** ₹667 for each student
 - **Disciplinary:** Written warning, no cricket in room
 - **Parent notified:** "Room damage, ₹667 charged"
- **Leadership Opportunity:**
 - Priya (Grade 10, boarder) excellent conduct, responsible
 - Warden appoints: Floor Monitor (Floor 3, Girls Hostel A)
 - **Responsibilities:**
 - Assist warden in night roll call
 - Mediate roommate conflicts
 - Organize floor cleanliness drives
 - Report maintenance issues
 - **Recognition:** Certificate, mentioned in report card
 - **Merit points:** +50 points
 - **Character certificate:** "Exemplary leadership as floor monitor"

**BUSINESS LOGIC:**
```
FUNCTION log_hostel_violation(student, violation_type, description, severity):
 violation = CREATE HostelViolation
 violation.student = student
 violation.type = violation_type
 violation.description = description
 violation.severity = severity
 violation.date = TODAY
 violation.reported_by = warden
 
 // Check violation history
 past_violations = GET violations WHERE student = student AND date > (TODAY - 90 days)
 violation_count = past_violations.count
 
 // Determine action based on severity and history
 IF severity = "MAJOR" OR violation_count >= 3:
 action = "PARENT_CONFERENCE_REQUIRED"
 NOTIFY parent("Serious hostel violation: {violation_type}. Meeting required.")
 NOTIFY principal("Major hostel violation: {student.name}")
 
 IF violation_count >= 3:
 action = "HOSTEL_SUSPENSION"
 SUSPEND_FROM_HOSTEL(student, duration=3 days)
 END IF
 
 ELSE IF severity = "MODERATE":
 action = "WRITTEN_WARNING"
 SEND warning_letter(parent)
 
 ELSE:
 action = "VERBAL_WARNING"
 LOG warning(student)
 END IF
 
 violation.action_taken = action
 
 // Update student conduct score
 student.hostel_conduct_score -= severity_points(severity)
 
 // Add to discipline module
 DISCIPLINE_MODULE.log_incident(student, "HOSTEL_VIOLATION", violation)
 
 RETURN violation
END FUNCTION

FUNCTION assign_hostel_responsibility(student, role):
 responsibility = CREATE HostelResponsibility
 responsibility.student = student
 responsibility.role = role // ROOM_CAPTAIN, FLOOR_MONITOR, MESS_CAPTAIN
 responsibility.assigned_date = TODAY
 responsibility.assigned_by = warden
 
 // Grant privileges
 IF role = "FLOOR_MONITOR":
 student.hostel_privileges.add("ASSIST_ROLL_CALL")
 student.hostel_privileges.add("MEDIATE_CONFLICTS")
 END IF
 
 // Award merit points
 AWARD_MERIT_POINTS(student, 50, "Hostel leadership: {role}")
 
 // Update student profile
 student.hostel_responsibilities.add(responsibility)
 
 // Notify stakeholders
 NOTIFY parent("{student.name} appointed as {role} in hostel. Congratulations!")
 NOTIFY student("You are now {role}. Thank you for your leadership!")
 
 RETURN responsibility
END FUNCTION
```

**EXAMPLE:**
- **Kavya's Hostel Conduct Journey:**
 - **Grade 9 (Year 1 as boarder):**
 - Conduct: Excellent, no violations
 - Attendance: 100%
 - Room cleanliness: Always maintained
 - **Grade 10 (Year 2):**
 - Appointed: Room Captain (Room 305)
 - Responsibilities: Maintain room cleanliness, organize study hours
 - Achievement: "Best Room Award" (3 times)
 - Merit points: +50
 - **Grade 11 (Year 3):**
 - Promoted: Floor Monitor (Floor 3, 30 students)
 - Responsibilities: Assist warden, mediate conflicts, organize events
 - Recognition: Certificate, mentioned in report card
 - Merit points: +100
 - **Grade 12 (Year 4):**
 - Hostel Prefect (entire Girls Hostel A, 100 students)
 - Leadership: Organizes hostel events, mentors junior students
 - Character certificate: "Exceptional hostel leadership, role model for peers"

---

### 8. TO INVENTORY & ASSET MANAGEMENT MODULE

**WHY This Connection Exists:**
Hostel furniture and equipment are school assets. Room damage requires asset replacement. Laundry machines, kitchen equipment need maintenance. Inventory tracking prevents theft and loss.

**DATA FLOW:**
- **Hostel Assets:**
 - Furniture (beds, desks, chairs, wardrobes)
 - Electronics (fans, lights, geysers)
 - Laundry equipment (washing machines, dryers)
 - Kitchen equipment (stoves, refrigerators, utensils)
- **Asset Allocation:**
 - Room-wise asset list
 - Asset condition (new, good, fair, poor)
 - Asset depreciation
- **Damage/Loss:**
 - Damaged assets (student-caused vs wear-and-tear)
 - Lost assets (theft, misplacement)
 - Replacement cost charged to students

**TRIGGER EVENT:**
- **Room Allocated:** Assets assigned to room
- **Asset Damaged:** Damage assessment, cost charged
- **Asset Lost:** Investigation, replacement
- **Maintenance Required:** Asset serviced/replaced

**IMPACT:**
- **Room Asset Allocation:**
 - Room 205 (Boys Hostel A):
 - 3 beds (₹5,000 each)
 - 3 study desks (₹3,000 each)
 - 3 chairs (₹1,000 each)
 - 3 wardrobes (₹4,000 each)
 - 2 fans (₹2,000 each)
 - 1 tube light (₹500)
 - Total room assets: ₹44,500
 - Students: Aarav, Rohan, Arjun
 - Asset condition: All "Good"
- **Asset Damage:**
 - Aarav breaks study desk (leg broken during horseplay)
 - Warden assesses: Desk unrepairable, needs replacement
 - Replacement cost: ₹3,000
 - Charged to Aarav's fee account
 - New desk ordered and installed
 - Old desk written off
- **Laundry Equipment Maintenance:**
 - Washing machine in Boys Hostel A breaks down
 - Warden reports to maintenance
 - Technician repairs: ₹5,000
 - Logged in asset management
 - Laundry service resumes next day

**BUSINESS LOGIC:**
```
FUNCTION allocate_room_assets(room):
 // Standard asset list per room
 assets = [
 {type: "BED", quantity: room.capacity, unit_cost: 5000},
 {type: "DESK", quantity: room.capacity, unit_cost: 3000},
 {type: "CHAIR", quantity: room.capacity, unit_cost: 1000},
 {type: "WARDROBE", quantity: room.capacity, unit_cost: 4000},
 {type: "FAN", quantity: 2, unit_cost: 2000},
 {type: "LIGHT", quantity: 1, unit_cost: 500}
 ]
 
 FOR each asset IN assets:
 FOR i = 1 TO asset.quantity:
 CREATE Asset
 asset_record.type = asset.type
 asset_record.location = room
 asset_record.purchase_cost = asset.unit_cost
 asset_record.condition = "NEW"
 asset_record.purchase_date = TODAY
 
 room.assets.add(asset_record)
 END FOR
 END FOR
 
 RETURN room.assets
END FUNCTION

FUNCTION process_asset_damage(student, asset, damage_type):
 damage_report = CREATE AssetDamageReport
 damage_report.student = student
 damage_report.asset = asset
 damage_report.damage_type = damage_type
 damage_report.date = TODAY
 damage_report.reported_by = warden
 
 // Assess damage
 IF damage_type = "BEYOND_REPAIR":
 replacement_cost = asset.current_value
 damage_report.action = "REPLACE"
 damage_report.cost = replacement_cost
 
 // Charge student
 ADD_CHARGE(student, "ASSET_DAMAGE", replacement_cost, "Damaged: {asset.type}")
 
 // Write off old asset
 WRITE_OFF_ASSET(asset)
 
 // Order replacement
 ORDER_REPLACEMENT(asset)
 
 ELSE IF damage_type = "REPAIRABLE":
 repair_cost = ESTIMATE_REPAIR_COST(asset)
 damage_report.action = "REPAIR"
 damage_report.cost = repair_cost
 
 // Charge student
 ADD_CHARGE(student, "ASSET_REPAIR", repair_cost, "Repair: {asset.type}")
 
 // Schedule repair
 SCHEDULE_REPAIR(asset)
 END IF
 
 // Notify parent
 NOTIFY parent("Asset damage: {asset.type}, cost: ₹{damage_report.cost}")
 
 RETURN damage_report
END FUNCTION
```

**EXAMPLE:**
- **Room 305 Asset Damage:**
 - **Incident:** Priya's wardrobe door hinge broken
 - **Warden assesses:** Repairable (hinge replacement)
 - **Repair cost:** ₹500
 - **Charged to Priya:** ₹500 added to fee account
 - **Repair:** Carpenter fixes hinge next day
 - **Asset updated:** Condition changed from "Good" to "Fair"

---

## INBOUND CONNECTIONS (Other Modules → Hostel & Mess Management)

### FROM STUDENT MANAGEMENT MODULE

**WHY:** Student enrollment status determines hostel/mess eligibility.

**DATA RECEIVED:**
- New admissions (boarding students)
- Withdrawals (vacate hostel, refund)
- Grade promotions (room reassignment if needed)
- Status changes (suspended students)

**IMPACT:**
- New boarders allocated rooms
- Withdrawn students vacate, refunds processed
- Suspended students may lose hostel privileges

**TRIGGER:** Admission, withdrawal, status change

---

### FROM FEE MANAGEMENT MODULE

**WHY:** Payment status determines hostel/mess access.

**DATA RECEIVED:**
- Hostel fee payment status
- Mess fee payment status
- Outstanding dues

**IMPACT:**
- Mess suspended if payment overdue 60 days
- Hostel eviction notice if overdue 90 days
- Access restored after payment

**TRIGGER:** Payment made/overdue

---

### FROM HEALTH & WELLNESS MODULE

**WHY:** Medical conditions affect mess diet and hostel care.

**DATA RECEIVED:**
- Chronic conditions (diabetes, asthma)
- Allergies (food, medication)
- Medical emergencies
- Mental health concerns

**IMPACT:**
- Mess diet adjusted for medical conditions
- Warden alerted for chronic conditions
- Emergency protocols activated

**TRIGGER:** Medical condition diagnosed, emergency

---

### FROM PARENT PORTAL

**WHY:** Parents submit leave requests and visitor appointments.

**DATA RECEIVED:**
- Weekend leave requests
- Visitor appointment requests
- Dietary preference changes

**IMPACT:**
- Leave requests processed
- Visitor appointments scheduled
- Mess diet updated

**TRIGGER:** Parent request submitted

---

### FROM SECURITY MODULE

**WHY:** Entry/exit logs validate hostel attendance.

**DATA RECEIVED:**
- Student checkout/checkin logs
- Visitor entry/exit logs
- Unauthorized exit attempts

**IMPACT:**
- Hostel attendance validated
- Leave compliance tracked
- Security incidents logged

**TRIGGER:** Entry/exit at hostel gate

---

### FROM MAINTENANCE MODULE

**WHY:** Hostel facilities require regular maintenance.

**DATA RECEIVED:**
- Maintenance schedules
- Repair requests
- Equipment breakdowns

**IMPACT:**
- Facilities maintained
- Repairs completed
- Equipment replaced if needed

**TRIGGER:** Maintenance due, breakdown reported

---

## SUMMARY

**Hostel & Mess Management Module connects to 18+ modules**

**Critical Outbound Dependencies:**
1. Student Management (boarding status, room allocation, conduct)
2. Fee Management (hostel/mess fees, service suspension)
3. Attendance (night roll call, weekend leave)
4. Health & Wellness (medical conditions, infirmary, emergencies)
5. Parent Portal (hostel dashboard, leave requests, communication)
6. Security (visitor control, student exit/entry)
7. Discipline (hostel violations, conduct)

**Critical Inbound Dependencies:**
1. Student Management (enrollment status)
2. Fee Management (payment status)
3. Health & Wellness (medical conditions)
4. Parent Portal (leave requests, visitor appointments)
5. Security (entry/exit logs)
6. Maintenance (facility upkeep)

**Hostel Metrics:**
- **Total Capacity:** 400 beds (200 boys, 200 girls)
- **Current Occupancy:** 350 students (87.5%)
- **Room Types:** Single (10%), Double (40%), Triple (50%)
- **Hostel Attendance:** 98% (night roll call)
- **Weekend Leave:** 30% students go home weekly
- **Conduct Violations:** 5% students/year

**Mess Metrics:**
- **Meals Served:** 1,050 meals/day (350 students × 3 meals)
- **Dietary Breakdown:** Vegetarian 70%, Non-veg 25%, Special diet 5%
- **Meal Attendance:** 92% (some students skip meals)
- **Food Wastage:** 8% (target: <10%)
- **Nutrition Standards:** FSSAI compliant, balanced diet

**Data Freshness:**
- Real-time: Night roll call, meal card swipes, visitor entry/exit
- Hourly: Meal consumption tracking
- Daily: Leave requests, hostel attendance reports
- Weekly: Menu planning, inventory checks
- Monthly: Conduct reviews, facility inspections
- Quarterly: Room reassignments, asset audits

This module is the **"residential life backbone"** - ensuring safe, comfortable, and nurturing living environment for boarding students while maintaining discipline, nutrition, and parent connectivity.

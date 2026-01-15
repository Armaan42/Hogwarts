# ATTENDANCE MANAGEMENT MODULE - COMPLETE DEPENDENCY ANALYSIS

## MODULE OVERVIEW

**Name:** Attendance Management Module 
**Role:** Daily Operations Core - Student Presence Tracking & Compliance 
**Type:** Critical Operational Module 
**Dependencies:** 20+ modules rely on attendance data for operations and analytics 

**Primary Functions:**
- Daily Attendance Marking (class-wise, period-wise)
- Leave Request Management (planned absences)
- Biometric Integration (face recognition, RFID, fingerprint)
- Attendance Reports & Analytics
- Defaulter Identification (low attendance alerts)
- Attendance Certificates Generation
- Parent Notifications (absence alerts)
- Compliance Monitoring (minimum 75% attendance requirement)
- Late Arrival & Early Departure Tracking
- Attendance-based Eligibility (exams, scholarships, promotions)

---

## OUTBOUND CONNECTIONS (Attendance Management → Other Modules)

### 1. TO STUDENT MANAGEMENT MODULE

**WHY This Connection Exists:**
Attendance is a core component of student record. Cumulative attendance percentage affects promotion, scholarship eligibility, and character certificates. Attendance patterns indicate student engagement and potential dropout risk.

**DATA FLOW:**
- **Daily Attendance Status:**
 - Present (P)
 - Absent (A)
 - Authorized Leave (AL) - with approved leave request
 - Late Arrival (L) - arrived after 8:30 AM
 - Early Departure (ED) - left before regular dismissal
 - Holiday (H)
 - Sunday/School Closed (SC)
- **Cumulative Attendance:**
 - Total working days (excluding holidays/Sundays)
 - Days present
 - Days absent (authorized + unauthorized)
 - Attendance percentage (monthly, term-wise, annual)
- **Attendance Trends:**
 - Declining attendance pattern (alert trigger)
 - Consecutive absences (3+ days triggers parent call)
 - Frequent late arrivals (disciplinary concern)
- **Subject-wise Attendance:**
 - For elective subjects (some students may miss specific periods)
 - Important for subject teachers to track engagement

**TRIGGER EVENT:**
- **Daily Attendance Marked:** Student record updated with today's status
- **Month End:** Monthly attendance percentage calculated
- **Term End:** Term attendance percentage calculated, added to report card
- **Low Attendance Detected:** Alert sent to counselor, parent
- **Attendance Certificate Request:** System pulls cumulative data

**IMPACT:**
- **Promotion Eligibility:**
 - Rohan (Grade 9): Attendance 68% (requirement: 75%)
 - System flags: "Not eligible for promotion, requires principal approval"
 - Principal reviews case, grants conditional promotion with warning
- **Scholarship Compliance:**
 - Priya has 50% merit scholarship (requires 75%+ attendance)
 - Attendance drops to 72% in October
 - System sends warning: "Scholarship at risk, improve attendance"
 - If remains below 75% for 2 consecutive months → Scholarship revoked
- **Character Certificate:**
 - Kavya requests character certificate for college admission
 - System shows: "Attendance: 96% (Excellent)"
 - Certificate states: "Regular and punctual student"
- **Dropout Risk Prediction:**
 - Aarav's attendance: April 90%, May 85%, June 78%, July 70%
 - AI flags: "Declining attendance pattern, dropout risk HIGH"
 - Counselor intervention triggered

**BUSINESS LOGIC:**
```
FUNCTION update_student_attendance(student, date, status):
 // Record daily attendance
 attendance_record = CREATE AttendanceRecord
 attendance_record.student = student
 attendance_record.date = date
 attendance_record.status = status
 attendance_record.marked_by = current_user
 attendance_record.timestamp = NOW
 
 // Update cumulative statistics
 student.attendance_stats.total_days = COUNT working_days(current_academic_year)
 student.attendance_stats.days_present = COUNT attendance WHERE status IN ["P", "L", "ED"]
 student.attendance_stats.days_absent = COUNT attendance WHERE status = "A"
 student.attendance_stats.authorized_leave = COUNT attendance WHERE status = "AL"
 student.attendance_stats.percentage = (days_present / total_days) * 100
 
 // Check for alerts
 IF student.attendance_stats.percentage < 75:
 ALERT("Low attendance: {student.name} - {percentage}%")
 NOTIFY parent("Your child's attendance is {percentage}%, below required 75%")
 END IF
 
 // Check for consecutive absences
 consecutive_absences = COUNT consecutive_days WHERE status = "A"
 IF consecutive_absences >= 3:
 ALERT("Consecutive absences: {student.name} - {consecutive_absences} days")
 TRIGGER parent_call(student)
 END IF
 
 // Check declining pattern
 current_month_percentage = CALCULATE monthly_attendance(current_month)
 previous_month_percentage = CALCULATE monthly_attendance(previous_month)
 
 IF current_month_percentage < previous_month_percentage - 10:
 ALERT("Declining attendance: {student.name} - dropped {difference}%")
 ASSIGN counselor_intervention(student)
 END IF
 
 RETURN attendance_record
END FUNCTION

FUNCTION check_promotion_eligibility(student):
 annual_attendance = student.attendance_stats.percentage
 
 IF annual_attendance < 75:
 RETURN {
 eligible: FALSE,
 reason: "Attendance {annual_attendance}% below required 75%",
 action: "Requires principal approval for promotion"
 }
 ELSE:
 RETURN {eligible: TRUE}
 END IF
END FUNCTION
```

**EXAMPLE:**
- **Rohan (Grade 8) - Attendance Journey:**
 - **April:** 22/24 days present = 91.67%
 - **May:** 20/23 days present = 86.96%
 - **June:** 18/25 days present = 72% 
 - **System Alert (June 30):** "Rohan's attendance dropped to 72%, below 75% threshold"
 - **Parent Notification:** SMS sent: "Rohan's attendance is 72% this month. Minimum 75% required for promotion."
 - **July:** Rohan improves, 23/24 days present = 95.83%
 - **Cumulative (April-July):** 83/96 days = 86.46% 
 - **Year-end:** Annual attendance 84%, eligible for promotion

---

### 2. TO PARENT ENGAGEMENT PORTAL

**WHY This Connection Exists:**
Parents need real-time visibility into their child's attendance. Daily absence alerts help parents ensure child reached school safely. Attendance trends help parents intervene early.

**DATA FLOW:**
- **Real-time Attendance Status:**
 - Today's attendance (marked/unmarked)
 - Time of arrival (if late)
 - Time of departure (if early)
- **Attendance Dashboard:**
 - Current month attendance percentage
 - This week: 4/5 days present
 - Subject-wise attendance (for high school)
 - Attendance calendar (color-coded: green=present, red=absent, yellow=late)
- **Absence Alerts:**
 - Immediate SMS if child marked absent
 - Daily summary: "Aarav was present today, arrived 8:15 AM"
- **Leave Request Interface:**
 - Submit leave request with reason and dates
 - Upload doctor's certificate (for medical leave)
 - Track leave request status (pending/approved/rejected)

**TRIGGER EVENT:**
- **Attendance Marked:** Parent receives notification within 30 minutes
- **Child Absent:** Immediate SMS alert
- **Late Arrival:** Notification with arrival time
- **Leave Request Submitted:** Confirmation sent
- **Leave Approved/Rejected:** Status update notification
- **Low Attendance Alert:** Weekly summary if attendance < 80%

**IMPACT:**
- **Safety Assurance:**
 - Priya's attendance marked "Present" at 8:10 AM
 - Mother receives SMS at 8:15 AM: "Priya marked present, arrived 8:10 AM"
 - Mother knows child reached school safely
- **Absence Alert:**
 - Rohan marked absent at 9:00 AM
 - Father receives SMS at 9:05 AM: "Rohan marked absent today. If this is incorrect, contact school immediately."
 - Father realizes Rohan bunked school, calls immediately
- **Leave Request:**
 - Kavya has doctor appointment on March 15
 - Mother submits leave request via portal on March 12
 - Uploads doctor's appointment letter
 - Class teacher approves on March 13
 - March 15: Kavya's attendance auto-marked "AL" (Authorized Leave)
 - Doesn't affect attendance percentage
- **Attendance Monitoring:**
 - Mr. Sharma logs into portal, sees Aarav's attendance dashboard:
 - This month: 18/22 days = 81.82%
 - Last month: 20/24 days = 83.33%
 - Trend: Slightly declining
 - Calendar shows: 3 absences this month (2 medical, 1 unauthorized)
 - Mr. Sharma discusses with Aarav, ensures better attendance

**BUSINESS LOGIC:**
```
FUNCTION send_attendance_notification(student, attendance_status):
 parent_contacts = GET student.parent_contacts
 
 IF attendance_status = "ABSENT":
 // Immediate alert for absence
 message = "ALERT: {student.name} marked ABSENT today ({date}). If incorrect, contact school."
 SEND SMS(parent_contacts.mobile, message) PRIORITY=HIGH
 SEND EMAIL(parent_contacts.email, message)
 SEND APP_NOTIFICATION(parent_contacts.app, message)
 
 ELSE IF attendance_status = "PRESENT":
 // Confirmation notification
 arrival_time = GET student.arrival_time(date)
 message = "{student.name} marked PRESENT, arrived {arrival_time}"
 SEND SMS(parent_contacts.mobile, message)
 
 ELSE IF attendance_status = "LATE":
 // Late arrival notification
 arrival_time = GET student.arrival_time(date)
 message = "{student.name} arrived LATE at {arrival_time} (school starts 8:30 AM)"
 SEND SMS(parent_contacts.mobile, message)
 
 ELSE IF attendance_status = "EARLY_DEPARTURE":
 // Early departure notification
 departure_time = GET student.departure_time(date)
 message = "{student.name} left school EARLY at {departure_time} (regular dismissal: 3:00 PM)"
 SEND SMS(parent_contacts.mobile, message)
 END IF
END FUNCTION

FUNCTION submit_leave_request(student, leave_details):
 leave_request = CREATE LeaveRequest
 leave_request.student = student
 leave_request.start_date = leave_details.start_date
 leave_request.end_date = leave_details.end_date
 leave_request.reason = leave_details.reason
 leave_request.type = leave_details.type // MEDICAL, FAMILY, OTHER
 leave_request.supporting_document = leave_details.document
 leave_request.requested_by = current_user (parent)
 leave_request.status = "PENDING"
 
 // Notify class teacher for approval
 NOTIFY class_teacher("Leave request from {student.name}: {reason}, {start_date} to {end_date}")
 
 // Confirmation to parent
 SEND SMS(parent, "Leave request submitted for {student.name}. Awaiting approval.")
 
 RETURN leave_request
END FUNCTION

FUNCTION approve_leave_request(leave_request):
 leave_request.status = "APPROVED"
 leave_request.approved_by = current_user
 leave_request.approved_date = NOW
 
 // Pre-mark attendance as Authorized Leave
 FOR each date IN (leave_request.start_date TO leave_request.end_date):
 IF is_working_day(date):
 attendance = GET_OR_CREATE attendance WHERE student = leave_request.student AND date = date
 attendance.status = "AL" // Authorized Leave
 attendance.leave_request = leave_request
 END IF
 END FOR
 
 // Notify parent
 SEND SMS(parent, "Leave approved for {student.name} from {start_date} to {end_date}")
 
 RETURN leave_request
END FUNCTION
```

**EXAMPLE:**
- **Priya's Medical Leave:**
 - **March 10, 8 PM:** Mother submits leave request via portal
 - Dates: March 12-14 (3 days)
 - Reason: "Viral fever, doctor advised rest"
 - Uploads: Doctor's certificate (PDF)
 - **March 11, 9 AM:** Class teacher Ms. Sharma reviews request, approves
 - **March 11, 9:05 AM:** Mother receives SMS: "Leave approved for Priya (March 12-14)"
 - **March 12-14:** Priya's attendance auto-marked "AL" each day
 - **March 15:** Priya returns to school
 - **Month-end:** Attendance calculation:
 - Total working days: 24
 - Present: 21 days
 - Authorized Leave: 3 days
 - Attendance %: 21/24 = 87.5% (AL doesn't count as absent)

---

### 3. TO ACADEMIC PERFORMANCE & ASSESSMENT MODULE

**WHY This Connection Exists:**
Attendance directly correlates with academic performance. Students with low attendance miss lessons, perform poorly in exams. Minimum attendance required to appear for exams (75% rule).

**DATA FLOW:**
- **Exam Eligibility:**
 - Student attendance percentage
 - Subject-wise attendance (for subject-specific exams)
 - Attendance up to exam date (not full year)
- **Performance Correlation:**
 - Attendance % vs exam scores (for analytics)
 - Students with <75% attendance flagged for remedial classes
- **Admit Card Blocking:**
 - Attendance < 75% → Admit card blocked
 - Requires principal approval to appear for exams

**TRIGGER EVENT:**
- **Exam Schedule Created:** System checks attendance eligibility
- **Admit Card Generation:** Attendance verification
- **Low Attendance Detected:** Remedial class enrollment triggered
- **Result Analysis:** Attendance-performance correlation calculated

**IMPACT:**
- **Exam Eligibility:**
 - Rohan (Grade 10): Attendance 72% (requirement: 75%)
 - Board exams in March
 - Admit card blocked in February
 - Principal reviews: "Rohan had 2 weeks medical leave (dengue), genuine case"
 - Principal grants exemption, admit card issued
- **Performance Correlation:**
 - Analytics show: Students with 90%+ attendance average 82% marks
 - Students with 75-80% attendance average 68% marks
 - Students with <75% attendance average 54% marks
 - School uses data to enforce attendance policies
- **Remedial Classes:**
 - Priya's Math attendance: 68% (missed 8 classes)
 - System auto-enrolls Priya in Saturday remedial Math classes
 - Covers missed topics: Algebra, Trigonometry
 - Priya catches up, scores 78% in exam (was predicted 60%)

**BUSINESS LOGIC:**
```
FUNCTION check_exam_eligibility(student, exam):
 // Calculate attendance up to exam date
 exam_date = exam.start_date
 academic_year_start = GET academic_year_start_date
 
 working_days = COUNT working_days BETWEEN academic_year_start AND exam_date
 days_present = COUNT attendance WHERE student = student 
 AND date BETWEEN academic_year_start AND exam_date 
 AND status IN ["P", "L", "ED", "AL"]
 
 attendance_percentage = (days_present / working_days) * 100
 
 IF attendance_percentage < 75:
 RETURN {
 eligible: FALSE,
 attendance: attendance_percentage,
 reason: "Attendance {attendance_percentage}% below required 75%",
 action: "Requires principal approval to appear for exam"
 }
 ELSE:
 RETURN {
 eligible: TRUE,
 attendance: attendance_percentage
 }
 END IF
END FUNCTION

FUNCTION identify_remedial_candidates(subject):
 students = GET students WHERE enrolled_in_subject = subject
 remedial_candidates = []
 
 FOR each student IN students:
 subject_attendance = CALCULATE subject_attendance(student, subject)
 
 IF subject_attendance < 80:
 missed_classes = GET missed_classes(student, subject)
 remedial_candidates.add({
 student: student,
 attendance: subject_attendance,
 missed_topics: EXTRACT topics_from_classes(missed_classes)
 })
 END IF
 END FOR
 
 // Auto-enroll in remedial classes
 FOR each candidate IN remedial_candidates:
 ENROLL candidate.student IN remedial_class(subject, candidate.missed_topics)
 NOTIFY parent("Enrolled in remedial {subject} classes to cover missed topics")
 END FOR
 
 RETURN remedial_candidates
END FUNCTION
```

**EXAMPLE:**
- **Aarav (Grade 11, Science) - Exam Eligibility:**
 - **Academic year:** April 2024 - March 2025
 - **Mid-term exams:** October 2024
 - **Attendance (April-September):** 105/120 days = 87.5% 
 - **Eligible for mid-term exams:** Yes
 - **Admit card issued:** September 25
 - **Final exams:** March 2025
 - **Attendance (April-February):** 168/220 days = 76.36% 
 - **Eligible for final exams:** Yes
 - **Board exams:** Attendance 76.36% , admit card issued

---

### 4. TO SCHOLARSHIP & FINANCIAL AID MODULE

**WHY This Connection Exists:**
Most scholarships require minimum 75% attendance. Scholarship compliance monitoring checks attendance monthly. Non-compliance leads to scholarship revocation.

**DATA FLOW:**
- **Attendance Percentage:** Monthly, term-wise, annual
- **Attendance Trend:** Improving/declining/stable
- **Authorized vs Unauthorized Absences:** Authorized leave doesn't violate scholarship
- **Consecutive Absences:** Red flag for scholarship committee

**TRIGGER EVENT:**
- **Monthly Attendance Calculation:** Scholarship compliance check
- **Attendance < 75%:** Warning sent to student/parent
- **Attendance < 75% for 2 consecutive months:** Scholarship review triggered
- **Attendance < 70%:** Scholarship revoked (with grace period)

**IMPACT:**
- **Scholarship Compliance:**
 - Priya has 50% merit scholarship (requires 75%+ attendance)
 - **September:** Attendance 78% 
 - **October:** Attendance 72% (below threshold)
 - **System Alert:** "Scholarship at risk, improve attendance"
 - **November:** Attendance 68% (2nd consecutive month below 75%)
 - **Scholarship Committee Review:** Priya had medical issues (hospitalized 1 week)
 - **Decision:** Grace period granted, scholarship continues if December > 80%
 - **December:** Attendance 88% 
 - **Scholarship retained**
- **Scholarship Revocation:**
 - Rohan has 25% sports scholarship (requires 75% attendance + sports participation)
 - **Attendance:** 65% (frequent bunking)
 - **Sports participation:** Also poor (missed 5 practice sessions)
 - **Scholarship revoked in November**
 - **Fee recalculated:** ₹90,000 → ₹1,20,000
 - **Outstanding increased by ₹30,000**

**BUSINESS LOGIC:**
```
FUNCTION monitor_scholarship_attendance(student):
 IF student.scholarship_percentage = 0:
 RETURN // No scholarship
 END IF
 
 scholarship_conditions = student.scholarship_conditions
 
 IF NOT scholarship_conditions.min_attendance:
 RETURN // No attendance requirement
 END IF
 
 required_attendance = scholarship_conditions.min_attendance // Usually 75%
 current_attendance = student.attendance_stats.percentage
 
 IF current_attendance < required_attendance:
 // Check if this is first month or consecutive
 previous_month_attendance = CALCULATE monthly_attendance(previous_month)
 
 IF previous_month_attendance < required_attendance:
 // Second consecutive month below threshold
 ALERT("CRITICAL: {student.name} scholarship at risk - 2 months below {required_attendance}%")
 TRIGGER scholarship_review(student)
 
 // Grace period: 1 month to improve
 grace_period_end = END_OF_NEXT_MONTH
 SEND WARNING(parent, "Scholarship will be revoked if attendance not improved to {required_attendance}% by {grace_period_end}")
 
 // Check after grace period
 SCHEDULE check_after_grace_period(student, grace_period_end)
 
 ELSE:
 // First month below threshold
 SEND WARNING(parent, "Attendance {current_attendance}% below scholarship requirement {required_attendance}%")
 END IF
 END IF
END FUNCTION

FUNCTION revoke_scholarship(student, reason):
 old_scholarship = student.scholarship_percentage
 student.scholarship_percentage = 0
 student.scholarship_revoked_date = NOW
 student.scholarship_revocation_reason = reason
 
 // Recalculate fees
 new_fee = calculate_student_fee(student)
 old_fee = student.current_annual_fee
 additional_fee = new_fee - old_fee
 
 // Add to outstanding
 student.outstanding += additional_fee
 
 // Generate revised invoice
 GENERATE revised_invoice(student, additional_fee, "Scholarship revoked: {reason}")
 
 // Notify stakeholders
 NOTIFY parent("Scholarship revoked due to {reason}. Additional fee: ₹{additional_fee}")
 NOTIFY principal("Scholarship revoked: {student.name} - {reason}")
 
 LOG scholarship_revocation(student, old_scholarship, reason)
END FUNCTION
```

**EXAMPLE:**
- **Kavya's Scholarship Compliance Journey:**
 - **Grade 9:** 50% merit scholarship awarded (requires 75% attendance, 80% grades)
 - **Monthly Attendance Tracking:**
 - April: 92% 
 - May: 88% 
 - June: 82% 
 - July: 74% (just below threshold)
 - August: 70% (2nd month below 75%)
 - **August 31:** System triggers scholarship review
 - **September 1:** Parent receives alert: "Scholarship at risk. Improve attendance to 75%+ by September 30 or scholarship will be revoked."
 - **September:** Kavya improves, 86% attendance
 - **Scholarship retained**
 - **Year-end:** Annual attendance 84%, scholarship renewed for Grade 10

---

### 5. TO TRANSPORT MANAGEMENT MODULE

**WHY This Connection Exists:**
Bus attendance tracking ensures students boarded/deboarded safely. Frequent bus absences (student using alternate transport) may indicate transport service not needed. Late arrivals often correlate with bus delays.

**DATA FLOW:**
- **Bus Boarding/Deboarding Logs:**
 - Student boarded bus at pickup point (RFID scan)
 - Student deboarded at school (RFID scan)
 - Timestamps for both events
- **Bus Attendance vs School Attendance:**
 - Student boarded bus but marked absent in school → Investigation needed
 - Student didn't board bus but present in school → Using alternate transport
- **Late Arrival Correlation:**
 - Bus delayed → All students on that bus marked late (excused)
 - Student late but bus on time → Individual late (unexcused)

**TRIGGER EVENT:**
- **Bus Boarding:** RFID scan logs boarding time
- **Bus Arrival at School:** RFID scans at school gate
- **Student Absent from Bus:** Alert sent to parent
- **Mismatch Detected:** Bus attendance ≠ school attendance

**IMPACT:**
- **Safety Tracking:**
 - Aarav's bus route: Pickup 7:20 AM, School arrival 8:15 AM
 - **7:22 AM:** Aarav boards bus (RFID scan), parent receives SMS: "Aarav boarded Bus 5"
 - **8:17 AM:** Aarav deboards at school (RFID scan), parent receives SMS: "Aarav reached school"
 - **9:00 AM:** School attendance marked "Present"
 - **Complete tracking:** Parent knows child's journey from home to school
- **Mismatch Investigation:**
 - **Priya enrolled in Bus Route 3**
 - **March 15:** Priya doesn't board bus (no RFID scan)
 - **Parent receives alert:** "Priya didn't board bus today"
 - **9:00 AM:** Priya marked "Present" in school
 - **System flags:** "Priya present in school but didn't use bus"
 - **Transport manager investigates:** Priya's father dropped her (car breakdown)
 - **One-time incident:** No action
 - **If frequent (10+ times/month):** Transport manager contacts parent: "Do you still need transport service?"
- **Bus Delay Handling:**
 - **Bus Route 7 delayed due to traffic**
 - **All 45 students on Bus 7 arrive at 9:10 AM (40 mins late)**
 - **System auto-marks:** All 45 students as "Present" (not "Late")
 - **Reason logged:** "Bus Route 7 delayed due to traffic"
 - **No penalty for students**

**BUSINESS LOGIC:**
```
FUNCTION process_bus_boarding(student, bus, pickup_point, timestamp):
 boarding_log = CREATE BusBoardingLog
 boarding_log.student = student
 boarding_log.bus = bus
 boarding_log.pickup_point = pickup_point
 boarding_log.boarding_time = timestamp
 boarding_log.status = "BOARDED"
 
 // Notify parent
 SEND SMS(parent, "{student.name} boarded {bus.route_name} at {timestamp}")
 
 // Update expected arrival
 expected_arrival = timestamp + bus.travel_time
 boarding_log.expected_school_arrival = expected_arrival
 
 RETURN boarding_log
END FUNCTION

FUNCTION process_school_arrival(student, bus, timestamp):
 boarding_log = GET boarding_log WHERE student = student AND date = TODAY
 
 IF boarding_log:
 boarding_log.school_arrival_time = timestamp
 boarding_log.status = "ARRIVED"
 
 // Notify parent
 SEND SMS(parent, "{student.name} reached school at {timestamp}")
 
 // Check if bus delayed
 IF timestamp > boarding_log.expected_school_arrival + 15 minutes:
 bus_delayed = TRUE
 delay_minutes = timestamp - boarding_log.expected_school_arrival
 LOG bus_delay(bus, delay_minutes, "Traffic/breakdown")
 END IF
 ELSE:
 // Student at school but didn't board bus
 ALERT("Mismatch: {student.name} at school but no bus boarding record")
 END IF
END FUNCTION

FUNCTION reconcile_bus_and_school_attendance():
 // Run at 10 AM daily
 bus_students = GET students WHERE transport_enrolled = TRUE
 
 FOR each student IN bus_students:
 boarded_bus = CHECK boarding_log(student, TODAY)
 present_in_school = CHECK attendance(student, TODAY) = "P"
 
 IF boarded_bus AND NOT present_in_school:
 ALERT("CRITICAL: {student.name} boarded bus but not present in school")
 INVESTIGATE(student)
 CALL parent IMMEDIATELY
 
 ELSE IF NOT boarded_bus AND present_in_school:
 LOG "Student used alternate transport: {student.name}"
 INCREMENT alternate_transport_count(student)
 
 // If frequent (>10 times/month)
 IF alternate_transport_count > 10:
 NOTIFY transport_manager("Frequent alternate transport: {student.name}")
 END IF
 END IF
 END FOR
END FUNCTION
```

**EXAMPLE:**
- **Rohan's Daily Bus Journey:**
 - **7:15 AM:** Rohan boards Bus Route 5 at Sector 14 stop
 - RFID scan logged
 - Parent SMS: "Rohan boarded Bus 5 at 7:15 AM"
 - **8:05 AM:** Bus arrives at school
 - Rohan deboards, RFID scan at school gate
 - Parent SMS: "Rohan reached school at 8:05 AM"
 - **8:30 AM:** School starts
 - **9:00 AM:** Class teacher marks attendance
 - Rohan marked "Present"
 - **Complete tracking:** Bus boarding → School arrival → Attendance marked

---

### 6. TO HOSTEL & MESS MANAGEMENT MODULE

**WHY This Connection Exists:**
Hostel students have different attendance patterns (no daily entry/exit like day scholars). Hostel attendance tracks students present in hostel premises. Mess attendance tracks meal consumption.

**DATA FLOW:**
- **Hostel Attendance:**
 - Students present in hostel (night roll call)
 - Weekend home leave (student not in hostel)
 - Unauthorized absence from hostel (student missing)
- **Mess Attendance:**
 - Breakfast attendance (who ate breakfast)
 - Lunch attendance
 - Dinner attendance
 - Meal wastage tracking (ordered but didn't eat)
- **School Attendance for Boarders:**
 - Boarders expected to have 100% school attendance (already on campus)
 - Absence from class (even though in hostel) → Investigation

**TRIGGER EVENT:**
- **Night Roll Call:** Hostel attendance marked (9 PM daily)
- **Meal Time:** Mess attendance logged (RFID scan at mess)
- **Student Missing:** Hostel warden alerted
- **Weekend Leave:** Student checks out (Friday) and checks in (Sunday)

**IMPACT:**
- **Hostel Roll Call:**
 - **9:00 PM:** Warden conducts roll call in Boys Hostel Floor 3
 - **40 students expected, 38 present, 2 absent**
 - **Absent:** Rohan, Aarav
 - **System checks:** Rohan has approved weekend leave 
 - **Aarav:** No leave approval 
 - **Warden investigates:** Aarav in library (forgot to inform)
 - **Aarav marked present after verification**
- **Mess Attendance:**
 - **Priya (boarder) enrolled in mess**
 - **Breakfast (7:30 AM):** RFID scan → Present
 - **Lunch (1:00 PM):** No RFID scan → Absent (ate in canteen)
 - **Dinner (7:30 PM):** RFID scan → Present
 - **Monthly mess attendance:** 85% (missed 15% meals)
 - **Mess manager:** "Priya frequently skipping lunch, adjust meal plan?"
- **Boarder Class Attendance:**
 - **Kavya (boarder) present in hostel but absent from class**
 - **System flags:** "Anomaly: Boarder absent from class"
 - **Warden investigates:** Kavya unwell, resting in room
 - **Medical leave applied retroactively**

**BUSINESS LOGIC:**
```
FUNCTION hostel_roll_call(hostel_block, floor):
 expected_students = GET students WHERE hostel_block = hostel_block 
 AND floor = floor 
 AND status = "BOARDER"
 
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
 NOTIFY warden("Investigate: {student.name} missing from roll call")
 CALL parent("Your child not present in hostel roll call, please contact warden")
 END FOR
 
 RETURN {present: present_students, absent: absent_students}
END FUNCTION

FUNCTION track_mess_attendance(student, meal_type):
 // RFID scan at mess entry
 mess_attendance = CREATE MessAttendance
 mess_attendance.student = student
 mess_attendance.date = TODAY
 mess_attendance.meal_type = meal_type // BREAKFAST, LUNCH, DINNER
 mess_attendance.timestamp = NOW
 
 // Update monthly statistics
 student.mess_stats.total_meals_expected += 1
 student.mess_stats.meals_consumed += 1
 student.mess_stats.attendance_percentage = (meals_consumed / total_meals_expected) * 100
 
 RETURN mess_attendance
END FUNCTION
```

**EXAMPLE:**
- **Arjun (Grade 9, Boarder) - Weekly Attendance:**
 - **Monday:**
 - Hostel roll call (9 PM): Present 
 - School attendance: Present 
 - Mess: Breakfast , Lunch , Dinner 
 - **Friday:**
 - School attendance: Present 
 - 5:00 PM: Checks out for weekend home leave
 - Hostel roll call (9 PM): On Leave 
 - **Sunday:**
 - 6:00 PM: Checks back into hostel
 - Hostel roll call (9 PM): Present 
 - **Monthly Summary:**
 - School attendance: 95% (1 day medical leave)
 - Hostel attendance: 100% (all nights accounted for)
 - Mess attendance: 88% (occasionally eats outside)

---

### 7. TO DISCIPLINE & BEHAVIOR MANAGEMENT MODULE

**WHY This Connection Exists:**
Attendance patterns indicate behavioral issues. Frequent unauthorized absences, bunking classes, late arrivals are disciplinary concerns. Attendance is part of conduct assessment.

**DATA FLOW:**
- **Attendance Violations:**
 - Unauthorized absences (bunking)
 - Frequent late arrivals (>5 times/month)
 - Early departures without permission
 - Proxy attendance (friend marking attendance)
- **Attendance Trends:**
 - Sudden drop in attendance (potential issue)
 - Pattern of specific period absences (bunking specific subject)
- **Conduct Score Impact:**
 - Good attendance (>90%) → Positive conduct points
 - Poor attendance (<75%) → Negative conduct points

**TRIGGER EVENT:**
- **Unauthorized Absence Detected:** Disciplinary incident created
- **Frequent Late Arrivals:** Warning issued
- **Bunking Detected:** Parent called, detention assigned
- **Proxy Attendance:** Serious disciplinary action

**IMPACT:**
- **Bunking Detection:**
 - **Rohan (Grade 10) present in Period 1-3, absent in Period 4 (Math), present in Period 5-6**
 - **System flags:** "Possible bunking: Rohan absent only in Math period"
 - **Math teacher confirms:** Rohan not in class
 - **Discipline team investigates:** Rohan found in playground
 - **Disciplinary action:** Detention, parent called
 - **Incident logged:** "Bunking class - Math Period 4"
- **Late Arrival Pattern:**
 - **Priya late 8 times in September (threshold: 5)**
 - **System alert:** "Frequent late arrivals: Priya"
 - **Class teacher investigates:** Bus route delayed frequently
 - **Action:** Transport manager notified to fix route timing
 - **No disciplinary action (genuine reason)**
- **Proxy Attendance:**
 - **Biometric system detects:** Aarav's fingerprint scanned at 8:15 AM
 - **But Aarav's face not recognized by CCTV at gate**
 - **System flags:** "Possible proxy attendance"
 - **Investigation:** Friend used Aarav's finger (fake fingerprint)
 - **Serious violation:** 3-day suspension for both students

**BUSINESS LOGIC:**
```
FUNCTION detect_bunking(student, date):
 period_attendance = GET period_wise_attendance WHERE student = student AND date = date
 
 // Check for gaps (present in some periods, absent in others)
 periods = SORT period_attendance BY period_number
 
 present_periods = []
 absent_periods = []
 
 FOR each period IN periods:
 IF period.status = "PRESENT":
 present_periods.add(period)
 ELSE:
 absent_periods.add(period)
 END IF
 END FOR
 
 // If present in periods before and after an absent period → Possible bunking
 IF present_periods.count > 0 AND absent_periods.count > 0:
 FOR each absent_period IN absent_periods:
 has_period_before = EXISTS present_period WHERE period_number < absent_period.number
 has_period_after = EXISTS present_period WHERE period_number > absent_period.number
 
 IF has_period_before AND has_period_after:
 ALERT("Possible bunking: {student.name} - {absent_period.subject}")
 CREATE disciplinary_incident(student, "BUNKING", absent_period)
 END IF
 END FOR
 END IF
END FUNCTION

FUNCTION track_late_arrivals(student):
 current_month_late_count = COUNT attendance WHERE student = student 
 AND month = CURRENT_MONTH 
 AND status = "LATE"
 
 IF current_month_late_count >= 5:
 ALERT("Frequent late arrivals: {student.name} - {current_month_late_count} times this month")
 
 // Investigate reason
 late_records = GET attendance WHERE student = student AND status = "LATE"
 
 // Check if bus-related
 IF student.transport_enrolled:
 bus_delays = COUNT bus_delay_logs WHERE bus = student.bus_route AND date IN late_records.dates
 
 IF bus_delays >= current_month_late_count * 0.8:
 // 80%+ late arrivals due to bus delays
 LOG "Late arrivals due to bus delays: {student.name}"
 NOTIFY transport_manager("Bus route causing frequent late arrivals")
 ELSE:
 // Individual issue
 SEND WARNING(parent, "Frequent late arrivals: {current_month_late_count} times")
 CREATE disciplinary_warning(student, "FREQUENT_LATE_ARRIVAL")
 END IF
 ELSE:
 // Day scholar, individual responsibility
 SEND WARNING(parent, "Frequent late arrivals: {current_month_late_count} times")
 CREATE disciplinary_warning(student, "FREQUENT_LATE_ARRIVAL")
 END IF
 END IF
END FUNCTION
```

**EXAMPLE:**
- **Kavya's Bunking Incident:**
 - **Date:** March 15
 - **Period-wise Attendance:**
 - Period 1 (English): Present 
 - Period 2 (Math): Present 
 - Period 3 (Physics): Absent 
 - Period 4 (Chemistry): Absent 
 - Period 5 (Biology): Present 
 - Period 6 (Hindi): Present 
 - **System flags:** "Possible bunking: Kavya absent in Periods 3-4 only"
 - **Investigation:** Kavya found in library during Physics/Chemistry
 - **Reason:** "Didn't prepare for Physics test, hiding in library"
 - **Disciplinary Action:**
 - Incident logged: "Bunking classes"
 - Parent called
 - Detention: 1 hour after school
 - Merit points deducted: -10
 - Warning letter issued
 - **Follow-up:** Counselor session to address exam anxiety

---

### 8. TO AI & PREDICTIVE ANALYTICS MODULE

**WHY This Connection Exists:**
Attendance is a key predictor of student outcomes. ML models use attendance patterns to predict dropout risk, academic performance, and identify students needing intervention.

**DATA FLOW:**
- **Attendance Features for ML:**
 - Current attendance percentage
 - Attendance trend (improving/declining/stable)
 - Consecutive absences count
 - Authorized vs unauthorized absence ratio
 - Late arrival frequency
 - Subject-specific attendance patterns
 - Month-over-month attendance change

**TRIGGER EVENT:**
- **Weekly:** ML model recalculates risk scores
- **Attendance Drop >10%:** Immediate alert
- **Consecutive Absences >3:** Intervention triggered
- **Term End:** Performance prediction updated

**IMPACT:**
- **Dropout Risk Prediction:**
 - **Rohan (Grade 9):**
 - April: 90% attendance
 - May: 85%
 - June: 78%
 - July: 70%
 - **AI Model:** "Declining attendance pattern, dropout risk: 65% (HIGH)"
 - **Counselor intervention triggered automatically**
 - **Root cause analysis:** Family financial issues
 - **Action:** Fee waiver applied, dropout risk reduced to 25%
- **Performance Forecasting:**
 - **Priya:** Attendance 95%, past grades 88%
 - **AI predicts:** "Final exam score: 85-90% (high confidence)"
 - **Aarav:** Attendance 72%, past grades 65%
 - **AI predicts:** "Final exam score: 50-60%, at risk of failing"
 - **Remedial classes recommended**

**BUSINESS LOGIC:**
```
FUNCTION calculate_dropout_risk_from_attendance(student):
 // Extract attendance features
 current_attendance = student.attendance_stats.percentage
 attendance_trend = CALCULATE_TREND(student.monthly_attendance, last_3_months)
 consecutive_absences = GET_MAX_CONSECUTIVE_ABSENCES(student)
 unauthorized_ratio = student.unauthorized_absences / student.total_absences
 
 features = {
 current_attendance: current_attendance,
 attendance_declining: attendance_trend < -5, // Dropped >5% in 3 months
 consecutive_absences: consecutive_absences,
 unauthorized_ratio: unauthorized_ratio,
 late_arrival_frequency: student.late_arrivals_per_month
 }
 
 // Feed to ML model
 dropout_probability = ML_MODEL.predict_dropout(features)
 
 // Attendance is a strong predictor
 // Weight: 30% of total dropout risk score
 attendance_risk_score = dropout_probability * 0.30
 
 RETURN attendance_risk_score
END FUNCTION
```

**EXAMPLE:**
- **AI-Driven Intervention:**
 - **Student:** Ananya (Grade 8)
 - **Attendance Pattern:**
 - January: 92%
 - February: 88%
 - March: 82%
 - April: 75%
 - **AI Analysis:**
 - Declining trend: -17% over 4 months
 - Consecutive absences: 4 days (max)
 - Unauthorized ratio: 60%
 - **Dropout Risk:** 58% (MEDIUM-HIGH)
 - **Automatic Actions:**
 - Counselor assigned
 - Parent meeting scheduled
 - Attendance improvement plan created
 - **Intervention:**
 - Counselor discovers: Ananya being bullied
 - Anti-bullying measures implemented
 - Ananya's attendance improves to 90% in May
 - Dropout risk reduced to 15%

---

## INBOUND CONNECTIONS (Other Modules → Attendance Management)

### FROM STUDENT MANAGEMENT MODULE

**WHY:** Student enrollment status determines who needs attendance marked.

**DATA RECEIVED:**
- Active student list (for daily rosters)
- New admissions (add to attendance rosters)
- Withdrawals (remove from rosters)
- Section assignments (for class-wise attendance)
- Leave requests (pre-mark as authorized leave)

**IMPACT:**
- Attendance rosters auto-updated
- New students added immediately
- Withdrawn students removed
- Leave requests pre-marked

**TRIGGER:** Admission, withdrawal, section change, leave approval

---

### FROM TIMETABLE & SCHEDULING MODULE

**WHY:** Period-wise attendance requires timetable data.

**DATA RECEIVED:**
- Daily timetable (which teacher, which subject, which period)
- Exam schedules (no attendance during exams)
- Holiday calendar (no attendance on holidays)
- Special events (assembly, sports day)

**IMPACT:**
- Period-wise attendance aligned with timetable
- Correct teacher sees correct students
- Holidays auto-marked
- Event days handled appropriately

**TRIGGER:** Timetable change, holiday declared, event scheduled

---

### FROM TRANSPORT MANAGEMENT MODULE

**WHY:** Bus delays affect late arrival marking.

**DATA RECEIVED:**
- Bus delay notifications
- Students on delayed bus
- Delay duration and reason

**IMPACT:**
- Students on delayed bus not marked "Late"
- Reason logged: "Bus Route 5 delayed"
- No penalty for students

**TRIGGER:** Bus delay reported

---

### FROM HEALTH & WELLNESS MODULE

**WHY:** Medical emergencies and infirmary visits affect attendance.

**DATA RECEIVED:**
- Student sent to infirmary during class
- Medical leave recommendations
- Hospitalization notifications

**IMPACT:**
- Attendance adjusted (present but in infirmary)
- Medical leave auto-applied
- Authorized absence marked

**TRIGGER:** Infirmary visit, medical leave

---

### FROM DISCIPLINE MODULE

**WHY:** Suspensions affect attendance marking.

**DATA RECEIVED:**
- Suspension orders (student not allowed in school)
- Detention schedules
- Disciplinary leave

**IMPACT:**
- Suspended students marked "Suspended" (not absent)
- Doesn't count against attendance percentage
- Detention attendance tracked separately

**TRIGGER:** Suspension imposed, detention assigned

---

### FROM EVENTS & ACTIVITIES MODULE

**WHY:** Students participating in school events (sports, competitions) are not absent.

**DATA RECEIVED:**
- Event participation lists
- Inter-school competition participants
- Field trip attendees

**IMPACT:**
- Event participants marked "On School Duty" (not absent)
- Counts as present for attendance percentage
- Separate tracking for event attendance

**TRIGGER:** Event scheduled, student registered

---

### FROM EXAM & ASSESSMENT MODULE

**WHY:** Exam days have different attendance rules.

**DATA RECEIVED:**
- Exam schedules
- Students appearing for exams
- Exam hall attendance

**IMPACT:**
- Exam day attendance marked at exam hall
- Students not appearing marked absent
- Exam attendance separate from class attendance

**TRIGGER:** Exam scheduled, exam conducted

---

### FROM PARENT PORTAL

**WHY:** Parents submit leave requests.

**DATA RECEIVED:**
- Leave request submissions
- Supporting documents (medical certificates)
- Leave cancellations

**IMPACT:**
- Leave requests routed to teachers for approval
- Approved leaves pre-marked in attendance
- Parents notified of approval/rejection

**TRIGGER:** Leave request submitted

---

### FROM BIOMETRIC SYSTEMS

**WHY:** Automated attendance marking via biometrics.

**DATA RECEIVED:**
- Fingerprint scans
- Face recognition data
- RFID card swipes
- Timestamps

**IMPACT:**
- Attendance auto-marked
- Arrival/departure times logged
- Proxy attendance prevented
- Real-time parent notifications

**TRIGGER:** Biometric scan

---

### FROM SECURITY & GATE MODULE

**WHY:** Entry/exit logs validate attendance.

**DATA RECEIVED:**
- Gate entry logs (who entered campus)
- Gate exit logs (early departures)
- Visitor logs (parents picking up child)

**IMPACT:**
- Entry logs cross-verified with attendance
- Early departures flagged
- Gate pass system integrated

**TRIGGER:** Gate entry/exit

---

## SUMMARY

**Attendance Management Module connects to 20+ modules**

**Critical Outbound Dependencies:**
1. Student Management (attendance % affects promotion, scholarships, certificates)
2. Parent Portal (real-time notifications, leave requests)
3. Academic Performance (exam eligibility, performance correlation)
4. Scholarships (compliance monitoring, revocation)
5. Transport (bus attendance tracking, safety)
6. Discipline (bunking detection, conduct assessment)
7. AI Analytics (dropout prediction, intervention triggers)

**Critical Inbound Dependencies:**
1. Student Management (active rosters, leave approvals)
2. Timetable (period-wise attendance alignment)
3. Transport (bus delay handling)
4. Health (medical leave integration)
5. Events (school duty marking)
6. Biometric Systems (automated marking)

**Attendance Metrics:**
- **Minimum Requirement:** 75% for promotion and exams
- **Scholarship Requirement:** 75-80% depending on scholarship type
- **Excellent Attendance:** 90%+ (recognized in certificates)
- **At-Risk Threshold:** <75% triggers interventions
- **Critical Threshold:** <70% for 2 consecutive months → Scholarship revocation

**Attendance Types:**
- **P** (Present): Regular attendance
- **A** (Absent): Unauthorized absence
- **AL** (Authorized Leave): Approved leave (medical, family)
- **L** (Late): Arrived after 8:30 AM
- **ED** (Early Departure): Left before regular dismissal
- **H** (Holiday): School holiday
- **SD** (School Duty): Event/competition participation
- **S** (Suspended): Disciplinary suspension

**Data Freshness:**
- Real-time: Biometric scans, parent notifications, bus boarding
- Hourly: Attendance roster updates, mismatch detection
- Daily: Attendance marking, bunking detection, parent summaries
- Weekly: Low attendance alerts, AI risk recalculation
- Monthly: Scholarship compliance, attendance reports
- Term-wise: Report card attendance, promotion eligibility
- Annual: Attendance certificates, year-end analytics

This module is the **"daily operations heartbeat"** of the school - attendance data flows into almost every other module and drives critical decisions on student progression, safety, and intervention.


---

# Submodule Breakdown

# ATTENDANCE MANAGEMENT MODULE - SUBMODULE OVERVIEW

**Module Code:** ATT-008  
**Category:** Operations  
**Priority:** P0  
**Owner:** Academic Operations

## Submodule Breakdown

This module is divided into **7 submodules**, each handling a specific aspect of attendance management management:

### 1. Daily Attendance Marking
**Code:** ATT-001  
**File:** `01_daily_attendance_marking.md`  
**Purpose:** Daily Attendance Marking functionality  

### 2. Period-wise Attendance
**Code:** ATT-002  
**File:** `02_period-wise_attendance.md`  
**Purpose:** Period-wise Attendance functionality  

### 3. Biometric/RFID Integration
**Code:** ATT-003  
**File:** `03_biometric_rfid_integration.md`  
**Purpose:** Biometric/RFID Integration functionality  

### 4. Leave Management
**Code:** ATT-004  
**File:** `04_leave_management.md`  
**Purpose:** Leave Management functionality  

### 5. Absence Alerts & Notifications
**Code:** ATT-005  
**File:** `05_absence_alerts_notifications.md`  
**Purpose:** Absence Alerts & Notifications functionality  

### 6. Attendance Reports & Analytics
**Code:** ATT-006  
**File:** `06_attendance_reports_analytics.md`  
**Purpose:** Attendance Reports & Analytics functionality  

### 7. Compliance Tracking (75% rule)
**Code:** ATT-007  
**File:** `07_compliance_tracking_75%_rule.md`  
**Purpose:** Compliance Tracking (75% rule) functionality  

## Integration Points

Attendance Management connects to multiple modules across the Hogwarts ERP system.

## Development Priority

**Phase 1 (Critical):** Submodules 1-3  
**Phase 2 (High):** Submodules 4-5  
**Phase 3 (Medium):** Remaining submodules  

---

**Status:** Submodule structure defined ⏳  
**Last Updated:** January 15, 2026  
**Note:** Detailed submodule documentation to be created

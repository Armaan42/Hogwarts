# HR & TEACHER MANAGEMENT MODULE - COMPLETE DEPENDENCY ANALYSIS

## MODULE OVERVIEW

**Name:** HR & Teacher Management Module 
**Role:** Human Capital Management - Faculty & Staff Operations 
**Type:** Core Administrative & HR Module 
**Dependencies:** 20+ modules rely on teacher/staff data for school operations 

**Primary Functions:**
- Teacher & Staff Recruitment
- Onboarding & Documentation
- Attendance & Leave Management
- Payroll & Salary Processing
- Performance Evaluation & Appraisals
- Professional Development & Training
- Timetable & Workload Management
- Subject & Class Assignment
- Substitute Teacher Management
- Grievance & Complaint Handling
- Exit Management & Clearance
- Compliance & Statutory Reporting

---

## OUTBOUND CONNECTIONS (HR & Teacher Management → Other Modules)

### 1. TO TIMETABLE & SCHEDULING MODULE

**WHY This Connection Exists:**
Teacher availability determines timetable feasibility. Subject expertise dictates class assignments. Workload balancing prevents teacher burnout. Leave/absence requires substitute arrangements.

**DATA FLOW:**
- **Teacher Availability:**
 - Working days and hours
 - Leave schedule (planned absences)
 - Part-time vs full-time status
 - Preferred teaching slots (if any)
- **Subject Expertise:**
 - Subjects qualified to teach
 - Grade levels (primary/middle/high school)
 - Specializations (honors, AP, IB)
 - Language proficiency (for language teachers)
- **Workload Allocation:**
 - Maximum periods per week (24-30 periods)
 - Current teaching load
 - Non-teaching duties (exam invigilation, club advisor)
 - Administrative responsibilities (HOD, coordinator)
- **Substitute Requirements:**
 - Teacher on leave → Substitute needed
 - Subject and grade level
 - Duration of absence

**TRIGGER EVENT:**
- **Timetable Creation:** Teacher availability checked
- **Teacher Hired:** Added to timetable pool
- **Teacher on Leave:** Substitute assigned
- **Workload Exceeded:** Alert to principal
- **Subject Change:** Timetable adjustment needed

**IMPACT:**
- **Timetable Feasibility:**
 - Grade 10 needs 6 Math periods/week
 - Math teachers: Mr. Verma (full-time, 30 periods/week), Ms. Kapoor (part-time, 15 periods/week)
 - System allocates:
 - Mr. Verma: 4 sections × 6 periods = 24 periods (Math)
 - Ms. Kapoor: 2 sections × 6 periods = 12 periods (Math)
 - Remaining capacity: Mr. Verma (6 periods), Ms. Kapoor (3 periods)
 - Used for remedial classes, doubt sessions
- **Substitute Management:**
 - Ms. Sharma (English, Grade 9) on medical leave (3 days)
 - System identifies substitute: Mr. Gupta (English teacher, has free periods)
 - Mr. Gupta assigned to cover Ms. Sharma's classes
 - Students notified: "English class with Mr. Gupta (substitute)"
 - Mr. Gupta's original free periods now teaching periods
 - Workload: Mr. Gupta 30 periods → 36 periods (temporary, 3 days)
- **Workload Balancing:**
 - Mr. Patel (Science) assigned: 28 periods teaching + 4 periods lab supervision + 2 periods exam invigilation = 34 periods
 - System alerts: "Mr. Patel workload 34 periods > maximum 30"
 - Principal reviews: Reduces lab supervision to 2 periods
 - Final workload: 32 periods (acceptable with approval)
- **New Teacher Integration:**
 - Ms. Gupta joins as Math teacher (mid-year, October)
 - Subjects: Math (Grades 6-10), Computer Science (Grade 6-8)
 - Availability: Full-time, 30 periods/week
 - Timetable adjusted: Ms. Gupta takes 3 sections Math (18 periods) + 2 sections Computer (12 periods) = 30 periods
 - Relieves Mr. Verma's workload from 30 → 24 periods

**BUSINESS LOGIC:**
```
FUNCTION allocate_teaching_load(teacher, academic_year):
 // Get teacher qualifications
 subjects = teacher.qualified_subjects
 grade_levels = teacher.qualified_grades
 max_periods = teacher.employment_type = "FULL_TIME" ? 30 : 15
 
 // Get school requirements
 sections_needing_teachers = GET sections WHERE subject IN subjects AND grade IN grade_levels
 
 allocated_periods = 0
 allocations = []
 
 FOR each section IN sections_needing_teachers:
 IF allocated_periods + section.periods_per_week <= max_periods:
 // Allocate this section to teacher
 allocation = CREATE TeachingAllocation
 allocation.teacher = teacher
 allocation.section = section
 allocation.subject = section.subject
 allocation.periods_per_week = section.periods_per_week
 
 allocations.add(allocation)
 allocated_periods += section.periods_per_week
 ELSE:
 BREAK // Teacher capacity reached
 END IF
 END FOR
 
 // Check for under-utilization
 IF allocated_periods < max_periods * 0.8:
 ALERT("Teacher under-utilized: {teacher.name}, {allocated_periods}/{max_periods} periods")
 END IF
 
 // Check for over-utilization
 IF allocated_periods > max_periods:
 ALERT("Teacher over-utilized: {teacher.name}, {allocated_periods}/{max_periods} periods")
 END IF
 
 teacher.current_teaching_load = allocated_periods
 teacher.teaching_allocations = allocations
 
 RETURN allocations
END FUNCTION

FUNCTION assign_substitute_teacher(absent_teacher, absence_dates):
 // Get absent teacher's classes during absence period
 classes_to_cover = GET classes WHERE teacher = absent_teacher AND date IN absence_dates
 
 // Find substitute teacher
 // Priority: Same subject teacher with free periods
 subject = absent_teacher.primary_subject
 potential_substitutes = GET teachers WHERE qualified_subjects CONTAINS subject AND status = "ACTIVE"
 
 FOR each substitute IN potential_substitutes:
 FOR each class IN classes_to_cover:
 // Check if substitute has free period at this time
 IF substitute.has_free_period(class.date, class.time):
 // Assign substitute
 CREATE SubstituteAssignment
 assignment.original_teacher = absent_teacher
 assignment.substitute_teacher = substitute
 assignment.class = class
 assignment.date = class.date
 assignment.time = class.time
 
 // Update timetable
 class.teacher = substitute (temporary)
 
 // Notify stakeholders
 NOTIFY students("Class with {substitute.name} (substitute for {absent_teacher.name})")
 NOTIFY substitute("Substitute duty: {class.subject}, {class.section}, {class.date} {class.time}")
 
 RETURN assignment
 END IF
 END FOR
 END FOR
 
 // If no substitute with free period, find any available teacher
 // (may require adjusting their schedule or hiring external substitute)
 RETURN "No substitute available, manual intervention required"
END FUNCTION
```

**EXAMPLE:**
- **Ms. Sharma's Leave & Substitute:**
 - **Monday:** Ms. Sharma (English, Grade 9) applies for medical leave (Tue-Thu, 3 days)
 - **Monday Evening:** Principal approves leave
 - **System Action:**
 - Identifies classes to cover: Grade 9A (2 periods), 9B (2 periods), 9C (2 periods) = 6 periods/day × 3 days = 18 periods
 - Searches for substitute: Mr. Gupta (English teacher) has free periods during these slots
 - Assigns Mr. Gupta as substitute for all 18 periods
 - **Tuesday Morning:** Students receive notification: "English class with Mr. Gupta (substitute)"
 - **Tuesday-Thursday:** Mr. Gupta teaches Ms. Sharma's classes
 - **Friday:** Ms. Sharma returns, resumes normal schedule
 - **Mr. Gupta:** Receives substitute allowance (₹500/day × 3 days = ₹1,500)

---

### 2. TO PAYROLL & FINANCE MODULE

**WHY This Connection Exists:**
Teacher salaries are the largest expense for schools. Attendance affects salary (leave deductions). Performance bonuses and increments. Statutory deductions (PF, tax). Payslip generation and disbursement.

**DATA FLOW:**
- **Salary Structure:**
 - Basic salary
 - Allowances (HRA, DA, transport, medical)
 - Deductions (PF, ESI, professional tax, TDS)
 - Gross salary and net salary
- **Attendance-based Salary:**
 - Working days in month
 - Days present
 - Paid leave taken
 - Unpaid leave (LOP - Loss of Pay)
 - Leave deduction calculation
- **Variable Components:**
 - Performance bonus (annual)
 - Substitute teaching allowance
 - Extra-curricular activity allowance (club advisor, sports coach)
 - Overtime (parent-teacher meetings, exam duties)
- **Statutory Compliance:**
 - PF contribution (12% employee + 12% employer)
 - ESI (if salary < ₹21,000/month)
 - Professional tax (state-specific)
 - TDS (income tax deduction)

**TRIGGER EVENT:**
- **Month End:** Salary processing for all teachers
- **Leave Taken:** Salary adjustment calculated
- **Performance Appraisal:** Bonus/increment added
- **Joining/Exit:** Pro-rata salary calculation
- **Payslip Generation:** Sent to teacher email

**IMPACT:**
- **Monthly Salary Processing:**
 - Mr. Verma (Math teacher):
 - Basic: ₹50,000
 - HRA: ₹20,000 (40% of basic)
 - DA: ₹5,000
 - Transport: ₹2,000
 - **Gross:** ₹77,000
 - Deductions:
 - PF (12%): ₹6,000
 - Professional Tax: ₹200
 - TDS: ₹3,500
 - **Net Salary:** ₹67,300
 - **Payslip generated:** Sent to mr.verma@school.com
 - **Salary credited:** 1st of next month
- **Leave Deduction:**
 - Ms. Sharma takes 5 days unpaid leave (LOP) in March
 - Monthly salary: ₹60,000
 - Working days in March: 26
 - Per day salary: ₹60,000 / 26 = ₹2,308
 - Deduction: ₹2,308 × 5 = ₹11,540
 - **Net salary (March):** ₹60,000 - ₹11,540 = ₹48,460
- **Performance Bonus:**
 - Annual appraisal (March):
 - Mr. Patel: Excellent performance (student results 92% avg, parent feedback 4.8/5)
 - Bonus: ₹50,000 (1 month basic salary)
 - Increment: 10% (basic ₹50,000 → ₹55,000 from April)
 - **March Payslip:** Includes ₹50,000 bonus
 - **April onwards:** New basic ₹55,000
- **Substitute Allowance:**
 - Mr. Gupta substitutes for Ms. Sharma (3 days, 6 periods/day)
 - Substitute rate: ₹500/day
 - Allowance: ₹500 × 3 = ₹1,500
 - **Added to monthly salary:** Regular salary + ₹1,500

**BUSINESS LOGIC:**
```
FUNCTION calculate_monthly_salary(teacher, month):
 // Get attendance
 working_days = COUNT working_days IN month
 days_present = COUNT attendance WHERE teacher = teacher AND month = month AND status = "PRESENT"
 paid_leave = COUNT leave WHERE teacher = teacher AND month = month AND type = "PAID"
 unpaid_leave = COUNT leave WHERE teacher = teacher AND month = month AND type = "UNPAID"
 
 // Calculate gross salary
 basic = teacher.salary_structure.basic
 hra = basic * 0.40
 da = teacher.salary_structure.da
 transport = teacher.salary_structure.transport
 gross_salary = basic + hra + da + transport
 
 // Calculate leave deduction
 per_day_salary = gross_salary / working_days
 leave_deduction = per_day_salary * unpaid_leave
 
 // Add variable components
 substitute_allowance = GET substitute_allowance(teacher, month)
 extra_curricular_allowance = GET extra_curricular_allowance(teacher, month)
 
 // Calculate deductions
 pf_deduction = basic * 0.12
 professional_tax = 200 // State-specific
 tds = CALCULATE_TDS(gross_salary * 12) // Annual tax / 12
 
 total_deductions = pf_deduction + professional_tax + tds + leave_deduction
 
 // Net salary
 net_salary = gross_salary + substitute_allowance + extra_curricular_allowance - total_deductions
 
 // Generate payslip
 payslip = CREATE Payslip
 payslip.teacher = teacher
 payslip.month = month
 payslip.gross_salary = gross_salary
 payslip.deductions = total_deductions
 payslip.net_salary = net_salary
 payslip.breakdown = {
 basic: basic,
 hra: hra,
 da: da,
 transport: transport,
 substitute_allowance: substitute_allowance,
 extra_curricular: extra_curricular_allowance,
 pf: pf_deduction,
 tax: professional_tax,
 tds: tds,
 leave_deduction: leave_deduction
 }
 
 // Send payslip
 SEND_EMAIL(teacher.email, payslip.pdf)
 
 // Process payment
 TRANSFER_SALARY(teacher.bank_account, net_salary, date=1st_of_next_month)
 
 RETURN payslip
END FUNCTION

FUNCTION process_annual_increment(teacher, appraisal_rating):
 current_basic = teacher.salary_structure.basic
 
 // Increment based on rating
 IF appraisal_rating >= 4.5: // Excellent
 increment_percentage = 10
 bonus = current_basic * 1 // 1 month basic as bonus
 ELSE IF appraisal_rating >= 3.5: // Good
 increment_percentage = 7
 bonus = current_basic * 0.5
 ELSE IF appraisal_rating >= 2.5: // Average
 increment_percentage = 5
 bonus = 0
 ELSE: // Below average
 increment_percentage = 0
 bonus = 0
 END IF
 
 new_basic = current_basic * (1 + increment_percentage/100)
 
 // Update salary structure
 teacher.salary_structure.basic = new_basic
 teacher.salary_structure.effective_from = NEXT_FINANCIAL_YEAR
 
 // Add bonus to next payslip
 IF bonus > 0:
 ADD_TO_NEXT_PAYSLIP(teacher, "PERFORMANCE_BONUS", bonus)
 END IF
 
 NOTIFY teacher("Annual increment: {increment_percentage}%, New basic: ₹{new_basic}, Bonus: ₹{bonus}")
 
 RETURN {increment: increment_percentage, new_basic: new_basic, bonus: bonus}
END FUNCTION
```

**EXAMPLE:**
- **Mr. Verma's Annual Salary Journey:**
 - **April 2024:** Basic ₹50,000, Gross ₹77,000, Net ₹67,300/month
 - **Monthly:** Salary credited on 1st of next month
 - **September:** Substitutes for absent teacher (2 days), earns ₹1,000 extra
 - **March 2025:** Annual appraisal
 - Rating: 4.7/5 (Excellent)
 - Increment: 10%
 - New basic: ₹55,000 (effective April 2025)
 - Bonus: ₹50,000 (paid in March payslip)
 - **March 2025 Payslip:** Regular salary + ₹50,000 bonus
 - **April 2025 onwards:** New gross ₹84,700, Net ₹74,030/month

---

### 3. TO ATTENDANCE MANAGEMENT MODULE

**WHY This Connection Exists:**
Teacher attendance affects class continuity. Biometric/manual attendance tracking. Leave management (casual, sick, earned). Attendance reports for payroll and compliance.

**DATA FLOW:**
- **Daily Attendance:**
 - Present/Absent/Late/Half-day
 - Check-in time (biometric/manual)
 - Check-out time
 - Total hours worked
- **Leave Records:**
 - Leave type (casual, sick, earned, maternity, paternity)
 - Leave balance (remaining days)
 - Leave approval status
 - Leave dates and duration
- **Attendance Analytics:**
 - Monthly attendance percentage
 - Punctuality (on-time arrivals)
 - Absenteeism patterns
 - Leave utilization

**TRIGGER EVENT:**
- **Daily Check-in:** Attendance marked
- **Leave Application:** Submitted for approval
- **Leave Approved:** Attendance pre-marked as leave
- **Absent without Leave:** Alert to principal
- **Month End:** Attendance report generated

**IMPACT:**
- **Daily Attendance:**
 - **8:00 AM:** School starts, teachers expected by 7:45 AM
 - **7:42 AM:** Mr. Verma checks in (biometric scan) - On time 
 - **8:15 AM:** Ms. Sharma checks in - Late (30 mins)
 - **System logs:** Ms. Sharma late, reason required
 - **Ms. Sharma:** Submits reason: "Traffic jam"
 - **3:30 PM:** School ends
 - **3:35 PM:** Mr. Verma checks out - Total hours: 7h 53m 
 - **3:32 PM:** Ms. Sharma checks out - Total hours: 7h 17m (late arrival deducted)
- **Leave Management:**
 - **Ms. Gupta applies for casual leave (2 days, Mon-Tue)**
 - **Leave balance:** Casual leave 10/12 remaining
 - **Principal approves**
 - **System actions:**
 - Deducts 2 days from casual leave balance (now 8/12)
 - Pre-marks attendance as "Casual Leave" for Mon-Tue
 - Triggers substitute teacher assignment
 - **Mon-Tue:** Ms. Gupta on leave, substitute covers classes
 - **Wednesday:** Ms. Gupta returns, resumes normal schedule
- **Absent without Leave:**
 - **Mr. Patel absent on Friday, no leave application**
 - **System flags:** "Unauthorized absence"
 - **Principal alerted:** "Mr. Patel absent without leave"
 - **HR calls Mr. Patel:** "Why absent without informing?"
 - **Mr. Patel:** "Family emergency, forgot to apply leave"
 - **HR:** "Apply leave retroactively, provide proof"
 - **Mr. Patel:** Applies emergency leave, attaches hospital bill
 - **Principal approves:** Absence converted to emergency leave
- **Monthly Attendance Report:**
 - **March 2025:**
 - Working days: 26
 - Mr. Verma: 26/26 (100%), 0 late arrivals
 - Ms. Sharma: 24/26 (92.3%), 3 late arrivals, 2 casual leave
 - Mr. Patel: 23/26 (88.5%), 1 late, 2 sick leave, 1 emergency leave

**BUSINESS LOGIC:**
```
FUNCTION mark_teacher_attendance(teacher, date, check_in_time):
 attendance = CREATE TeacherAttendance
 attendance.teacher = teacher
 attendance.date = date
 attendance.check_in_time = check_in_time
 
 school_start_time = "07:45 AM"
 
 // Check if late
 IF check_in_time > school_start_time:
 late_minutes = check_in_time - school_start_time
 attendance.status = "LATE"
 attendance.late_minutes = late_minutes
 
 // Alert if late > 30 mins
 IF late_minutes > 30:
 ALERT principal("Teacher late: {teacher.name}, {late_minutes} mins")
 REQUEST_REASON(teacher)
 END IF
 ELSE:
 attendance.status = "PRESENT"
 END IF
 
 RETURN attendance
END FUNCTION

FUNCTION process_leave_application(teacher, leave_details):
 leave_application = CREATE LeaveApplication
 leave_application.teacher = teacher
 leave_application.leave_type = leave_details.type // CASUAL, SICK, EARNED, MATERNITY
 leave_application.start_date = leave_details.start_date
 leave_application.end_date = leave_details.end_date
 leave_application.days = CALCULATE_WORKING_DAYS(start_date, end_date)
 leave_application.reason = leave_details.reason
 leave_application.status = "PENDING"
 
 // Check leave balance
 leave_balance = GET leave_balance(teacher, leave_details.type)
 
 IF leave_application.days > leave_balance:
 RETURN "Error: Insufficient leave balance. Available: {leave_balance} days"
 END IF
 
 // Send for approval
 NOTIFY principal("Leave application: {teacher.name}, {leave_details.type}, {leave_application.days} days")
 
 RETURN leave_application
END FUNCTION

FUNCTION approve_leave(leave_application):
 leave_application.status = "APPROVED"
 leave_application.approved_by = principal
 leave_application.approved_date = TODAY
 
 // Deduct from leave balance
 teacher = leave_application.teacher
 leave_type = leave_application.leave_type
 days = leave_application.days
 
 teacher.leave_balance[leave_type] -= days
 
 // Pre-mark attendance as leave
 FOR each date IN (leave_application.start_date TO leave_application.end_date):
 IF is_working_day(date):
 attendance = CREATE TeacherAttendance
 attendance.teacher = teacher
 attendance.date = date
 attendance.status = leave_type
 attendance.leave_application = leave_application
 END IF
 END FOR
 
 // Trigger substitute assignment
 ASSIGN_SUBSTITUTE_TEACHER(teacher, leave_application.dates)
 
 // Notify teacher
 SEND_EMAIL(teacher.email, "Leave approved: {leave_type}, {start_date} to {end_date}")
 
 RETURN leave_application
END FUNCTION
```

**EXAMPLE:**
- **Ms. Sharma's Leave Journey:**
 - **Leave Balance (April 2024):**
 - Casual Leave: 12 days
 - Sick Leave: 12 days
 - Earned Leave: 15 days
 - **May:** Takes 2 days casual leave (family function)
 - Balance: Casual 10, Sick 12, Earned 15
 - **July:** Takes 3 days sick leave (fever)
 - Balance: Casual 10, Sick 9, Earned 15
 - **December:** Takes 5 days earned leave (vacation)
 - Balance: Casual 10, Sick 9, Earned 10
 - **March 2025:** Year-end
 - Total leave taken: 10 days (2 casual + 3 sick + 5 earned)
 - Attendance: 242/252 working days (96%)
 - Unused leave: Casual 10, Sick 9, Earned 10 (carried forward to next year)

---

### 4. TO STUDENT ACADEMIC PERFORMANCE MODULE

**WHY This Connection Exists:**
Teacher effectiveness measured by student results. Subject-wise performance indicates teaching quality. Teacher-student ratio affects learning outcomes. Performance data drives teacher training and support.

**DATA FLOW:**
- **Student Results by Teacher:**
 - Class average marks
 - Pass percentage
 - Improvement from previous term
 - Subject-wise performance
- **Teaching Effectiveness:**
 - Student feedback ratings
 - Parent feedback
 - Peer observations
 - Principal evaluations
- **Value Addition:**
 - Student improvement (entry level vs exit level)
 - Weak student support (remedial classes)
 - Toppers produced

**TRIGGER EVENT:**
- **Results Published:** Teacher performance analyzed
- **Student Feedback:** Collected term-wise
- **Appraisal Time:** Performance data compiled
- **Low Performance:** Support/training recommended

**IMPACT:**
- **Teacher Performance Analysis:**
 - **Mr. Verma (Math, Grade 10):**
 - Sections taught: 10A, 10B, 10C, 10D (4 sections, 140 students)
 - Mid-term results:
 - 10A: Average 78%, Pass 100%
 - 10B: Average 82%, Pass 100%
 - 10C: Average 75%, Pass 98%
 - 10D: Average 80%, Pass 100%
 - **Overall:** Average 78.75%, Pass 99.5%
 - **School average (all Math teachers):** 72%, Pass 95%
 - **Analysis:** Mr. Verma performing 6.75% above school average 
 - **Student feedback:** 4.7/5 (Excellent)
 - **Parent feedback:** 4.5/5 (Very Good)
 - **Appraisal rating:** 4.8/5 (Excellent)
- **Underperforming Teacher Support:**
 - **Ms. Kapoor (Science, Grade 9):**
 - Sections: 9A, 9B (2 sections, 70 students)
 - Results: Average 62%, Pass 85%
 - **School average:** 72%, Pass 95%
 - **Analysis:** 10% below average, needs support
 - **Principal action:**
 - Assigns mentor: Mr. Patel (senior Science teacher)
 - Classroom observations: Mr. Patel observes Ms. Kapoor's classes
 - Feedback: "Improve student engagement, use more practical examples"
 - Training: Sends Ms. Kapoor to pedagogy workshop
 - **Next term:** Ms. Kapoor improves to 70% average, 92% pass
- **Value Addition Tracking:**
 - **Mr. Gupta (English, Grade 8):**
 - Entry level (April): Class average 65%
 - Exit level (March): Class average 78%
 - **Improvement:** +13% (value addition)
 - **Recognition:** "Best Value Addition Award"

**BUSINESS LOGIC:**
```
FUNCTION analyze_teacher_performance(teacher, term):
 // Get all classes taught by teacher
 classes = GET classes WHERE teacher = teacher AND term = term
 
 total_students = 0
 total_marks = 0
 students_passed = 0
 
 FOR each class IN classes:
 results = GET results WHERE class = class AND term = term
 
 total_students += results.count
 total_marks += SUM(results.marks)
 students_passed += COUNT(results WHERE marks >= passing_marks)
 END FOR
 
 // Calculate metrics
 average_marks = total_marks / total_students
 pass_percentage = (students_passed / total_students) * 100
 
 // Compare with school average
 school_average = GET school_average(teacher.subject, term)
 performance_gap = average_marks - school_average
 
 // Get feedback
 student_feedback = GET student_feedback(teacher, term)
 parent_feedback = GET parent_feedback(teacher, term)
 
 performance_report = {
 teacher: teacher,
 term: term,
 students_taught: total_students,
 average_marks: average_marks,
 pass_percentage: pass_percentage,
 school_average: school_average,
 performance_gap: performance_gap,
 student_feedback: student_feedback,
 parent_feedback: parent_feedback
 }
 
 // Determine rating
 IF performance_gap >= 5 AND student_feedback >= 4.5:
 performance_report.rating = "EXCELLENT"
 ELSE IF performance_gap >= 0 AND student_feedback >= 3.5:
 performance_report.rating = "GOOD"
 ELSE IF performance_gap >= -5 AND student_feedback >= 2.5:
 performance_report.rating = "AVERAGE"
 ELSE:
 performance_report.rating = "NEEDS_IMPROVEMENT"
 RECOMMEND training_and_support(teacher)
 END IF
 
 RETURN performance_report
END FUNCTION

FUNCTION calculate_value_addition(teacher, class, academic_year):
 // Entry level assessment (beginning of year)
 entry_assessment = GET assessment WHERE class = class AND type = "ENTRY_LEVEL"
 entry_average = CALCULATE_AVERAGE(entry_assessment.results)
 
 // Exit level assessment (end of year)
 exit_assessment = GET assessment WHERE class = class AND type = "FINAL_EXAM"
 exit_average = CALCULATE_AVERAGE(exit_assessment.results)
 
 // Value addition
 value_addition = exit_average - entry_average
 value_addition_percentage = (value_addition / entry_average) * 100
 
 value_addition_report = {
 teacher: teacher,
 class: class,
 entry_level: entry_average,
 exit_level: exit_average,
 value_addition: value_addition,
 value_addition_percentage: value_addition_percentage
 }
 
 // Recognition for high value addition
 IF value_addition_percentage >= 15:
 AWARD(teacher, "BEST_VALUE_ADDITION_AWARD")
 END IF
 
 RETURN value_addition_report
END FUNCTION
```

**EXAMPLE:**
- **Annual Performance Review (March 2025):**
 - **Mr. Verma (Math):**
 - Average marks: 78.75% (school avg: 72%)
 - Pass %: 99.5% (school avg: 95%)
 - Student feedback: 4.7/5
 - Parent feedback: 4.5/5
 - **Rating:** EXCELLENT
 - **Increment:** 10%, Bonus: ₹50,000
 - **Ms. Sharma (English):**
 - Average marks: 74% (school avg: 72%)
 - Pass %: 96% (school avg: 95%)
 - Student feedback: 4.2/5
 - Parent feedback: 4.0/5
 - **Rating:** GOOD
 - **Increment:** 7%, Bonus: ₹30,000
 - **Ms. Kapoor (Science):**
 - Average marks: 70% (school avg: 72%, improved from 62%)
 - Pass %: 92% (school avg: 95%)
 - Student feedback: 3.8/5
 - Parent feedback: 3.5/5
 - **Rating:** AVERAGE (improved from NEEDS_IMPROVEMENT)
 - **Increment:** 5%, No bonus
 - **Recommendation:** Continue mentorship program

---

### 5. TO PROFESSIONAL DEVELOPMENT & TRAINING MODULE

**WHY This Connection Exists:**
Teacher skill development improves teaching quality. Mandatory training for new teachers. Subject-specific workshops. Technology integration training. Leadership development for senior teachers.

**DATA FLOW:**
- **Training Needs:**
 - Subject expertise gaps
 - Pedagogy improvements
 - Technology skills (LMS, smart boards)
 - Classroom management
 - Special education (IEP students)
- **Training Programs:**
 - Induction training (new teachers)
 - Subject workshops
 - Technology training
 - Leadership programs (HODs, coordinators)
 - External certifications (Cambridge, IB)
- **Training Completion:**
 - Attendance records
 - Certificates earned
 - Skills acquired
 - Post-training assessment

**TRIGGER EVENT:**
- **New Teacher Joins:** Induction training scheduled
- **Performance Gap:** Training recommended
- **New Technology:** Training organized
- **Certification Needed:** External program enrollment
- **Training Completed:** Certificate added to profile

**IMPACT:**
- **New Teacher Induction:**
 - **Ms. Gupta joins (October):**
 - Week 1: Induction training (5 days)
 - Day 1: School policies, culture, values
 - Day 2: Curriculum overview, teaching methods
 - Day 3: Technology systems (LMS, attendance, gradebook)
 - Day 4: Classroom management, student discipline
 - Day 5: Mentorship assignment, Q&A
 - **Mentor assigned:** Mr. Verma (senior Math teacher)
 - **Classroom observations:** Mr. Verma observes Ms. Gupta's classes (first month)
 - **Feedback sessions:** Weekly for first 3 months
 - **Result:** Ms. Gupta successfully integrated, performing well
- **Technology Training:**
 - **School implements new LMS (January):**
 - Training organized: All teachers (3-day workshop)
 - Topics: LMS navigation, assignment creation, grading, communication
 - Hands-on practice: Each teacher creates sample course
 - **Attendance:** 95% teachers (2 absent due to leave)
 - **Post-training:** 90% teachers using LMS effectively
 - **Follow-up:** Support sessions for struggling teachers
- **Subject-Specific Workshop:**
 - **Math teachers workshop (August):**
 - Topic: "Teaching Algebra through Real-world Applications"
 - Participants: Mr. Verma, Ms. Gupta, Ms. Kapoor (3 Math teachers)
 - Duration: 2 days
 - **Outcome:** Teachers implement new methods, student engagement improves
- **Leadership Development:**
 - **Mr. Verma (senior teacher) selected for HOD training:**
 - Program: "Educational Leadership Certificate" (6 months)
 - Topics: Team management, curriculum design, teacher mentoring
 - **Completion:** Certificate earned
 - **Promotion:** Appointed as Math HOD (April 2025)

**BUSINESS LOGIC:**
```
FUNCTION schedule_induction_training(new_teacher):
 induction = CREATE InductionProgram
 induction.teacher = new_teacher
 induction.start_date = new_teacher.joining_date
 induction.duration = 5 days
 
 // Day-wise schedule
 induction.schedule = [
 {day: 1, topic: "School Policies & Culture", trainer: principal},
 {day: 2, topic: "Curriculum & Teaching Methods", trainer: academic_coordinator},
 {day: 3, topic: "Technology Systems", trainer: IT_coordinator},
 {day: 4, topic: "Classroom Management", trainer: discipline_coordinator},
 {day: 5, topic: "Mentorship & Q&A", trainer: assigned_mentor}
 ]
 
 // Assign mentor (senior teacher in same subject)
 mentor = GET senior_teacher WHERE subject = new_teacher.subject
 induction.mentor = mentor
 
 // Schedule classroom observations
 FOR week IN (1 TO 12): // First 3 months
 SCHEDULE classroom_observation(mentor, new_teacher, week)
 END FOR
 
 NOTIFY new_teacher("Induction training scheduled: {induction.start_date}")
 NOTIFY mentor("You are mentor for {new_teacher.name}")
 
 RETURN induction
END FUNCTION

FUNCTION recommend_training(teacher, performance_gap):
 training_needs = []
 
 // Identify training needs based on performance gap
 IF performance_gap.student_results < school_average:
 training_needs.add("PEDAGOGY_IMPROVEMENT")
 END IF
 
 IF performance_gap.student_feedback < 3.5:
 training_needs.add("CLASSROOM_MANAGEMENT")
 END IF
 
 IF performance_gap.technology_usage < 50%:
 training_needs.add("TECHNOLOGY_INTEGRATION")
 END IF
 
 // Find relevant training programs
 FOR each need IN training_needs:
 programs = GET training_programs WHERE category = need
 RECOMMEND programs TO teacher
 END FOR
 
 NOTIFY principal("Training recommended for {teacher.name}: {training_needs}")
 
 RETURN training_needs
END FUNCTION
```

**EXAMPLE:**
- **Ms. Kapoor's Development Journey:**
 - **July:** Performance below average (62% vs 72% school avg)
 - **August:** Recommended for training
 - Pedagogy workshop: "Engaging Science Teaching"
 - Classroom management: "Student Motivation Techniques"
 - **September:** Attends both workshops (4 days total)
 - **October:** Mentor assigned (Mr. Patel, senior Science teacher)
 - Weekly classroom observations
 - Feedback and improvement suggestions
 - **November:** Implements new methods
 - More practical experiments
 - Real-world examples
 - Student group activities
 - **December:** Mid-term results improve to 70%
 - **March:** Final results 72% (matches school average)
 - **Outcome:** Training successful, Ms. Kapoor on track

---

### 6. TO PARENT ENGAGEMENT MODULE

**WHY This Connection Exists:**
Parents need teacher contact information. Parent-teacher meetings scheduled. Teacher feedback on student progress. Communication channels (email, phone, app messaging).

**DATA FLOW:**
- **Teacher Contact Info:**
 - Name and photo
 - Subject and classes taught
 - Email and phone (school-provided)
 - Office hours (for parent consultations)
- **Parent-Teacher Meetings:**
 - Schedule (term-wise, 2-3 times/year)
 - Appointment booking
 - Meeting notes and feedback
- **Progress Reports:**
 - Teacher comments on student performance
 - Strengths and areas for improvement
 - Recommendations for parents
- **Communication:**
 - Email messaging
 - In-app messaging
 - Phone calls (for urgent matters)

**TRIGGER EVENT:**
- **Parent Login:** Teacher contact info displayed
- **PTM Scheduled:** Appointment booking opens
- **Report Card:** Teacher comments included
- **Parent Message:** Teacher notified, responds
- **Concern Raised:** Teacher follows up

**IMPACT:**
- **Teacher Contact Directory:**
 - Parent portal shows:
 - **Aarav (Grade 8) Teachers:**
 - Math: Mr. Verma (verma@school.com, 98xxx)
 - Science: Mr. Patel (patel@school.com, 98xxx)
 - English: Ms. Sharma (sharma@school.com, 98xxx)
 - Social Studies: Ms. Gupta (gupta@school.com, 98xxx)
 - Office hours: Mon-Fri, 3:30-4:30 PM
- **Parent-Teacher Meeting:**
 - **PTM scheduled:** November 15 (Saturday, 9 AM - 1 PM)
 - **Mr. Sharma (Aarav's father) books appointments:**
 - 9:00 AM: Math (Mr. Verma) - 15 mins
 - 9:30 AM: Science (Mr. Patel) - 15 mins
 - 10:00 AM: English (Ms. Sharma) - 15 mins
 - **November 15:**
 - **9:00 AM:** Meets Mr. Verma
 - Feedback: "Aarav excellent in Math, scored 88%. Encourage problem-solving."
 - Recommendation: "Enroll in Math Olympiad preparation"
 - **9:30 AM:** Meets Mr. Patel
 - Feedback: "Good in Science (82%), but needs to improve practical skills"
 - Recommendation: "More hands-on experiments at home"
 - **10:00 AM:** Meets Ms. Sharma
 - Feedback: "English good (75%), but reading comprehension weak"
 - Recommendation: "Read 2 books/month, discuss with Aarav"
 - **Mr. Sharma:** Takes notes, implements recommendations
- **In-App Messaging:**
 - **Mrs. Sharma (Priya's mother) messages Ms. Gupta (Math teacher):**
 - "Priya struggling with Algebra, can you suggest extra resources?"
 - **Ms. Gupta responds (within 24 hours):**
 - "I recommend Khan Academy videos on Algebra. Also, Priya can attend my doubt sessions (Tuesdays, 3:30 PM). I'll give her extra practice worksheets."
 - **Mrs. Sharma:** "Thank you! Will ensure Priya attends."

**BUSINESS LOGIC:**
```
FUNCTION schedule_parent_teacher_meeting(date, time_slots):
 ptm = CREATE ParentTeacherMeeting
 ptm.date = date
 ptm.time_slots = time_slots // e.g., 9:00-9:15, 9:15-9:30, etc.
 
 // Notify all parents
 parents = GET all_active_parents
 FOR each parent IN parents:
 SEND_EMAIL(parent.email, "PTM scheduled: {date}. Book appointments via portal.")
 SEND_SMS(parent.mobile, "PTM on {date}. Book slots online.")
 END FOR
 
 // Open booking
 ptm.booking_status = "OPEN"
 
 RETURN ptm
END FUNCTION

FUNCTION book_ptm_appointment(parent, teacher, time_slot):
 // Check if slot available
 IF time_slot.booked:
 RETURN "Error: Slot already booked"
 END IF
 
 // Book appointment
 appointment = CREATE PTMAppointment
 appointment.parent = parent
 appointment.teacher = teacher
 appointment.time_slot = time_slot
 appointment.status = "CONFIRMED"
 
 time_slot.booked = TRUE
 time_slot.parent = parent
 
 // Send confirmations
 SEND_EMAIL(parent.email, "PTM appointment confirmed: {teacher.name}, {time_slot.time}")
 SEND_EMAIL(teacher.email, "PTM appointment: {parent.name} (parent of {student.name}), {time_slot.time}")
 
 RETURN appointment
END FUNCTION

FUNCTION send_teacher_message(parent, teacher, message):
 // Create message thread
 thread = GET_OR_CREATE message_thread(parent, teacher)
 
 message_record = CREATE Message
 message_record.thread = thread
 message_record.sender = parent
 message_record.recipient = teacher
 message_record.content = message
 message_record.timestamp = NOW
 
 // Notify teacher
 SEND_EMAIL(teacher.email, "New message from {parent.name}: {message}")
 SEND_APP_NOTIFICATION(teacher.app, "New parent message")
 
 // Track response time
 TRACK_RESPONSE_TIME(teacher, message_record)
 
 RETURN message_record
END FUNCTION
```

**EXAMPLE:**
- **Parent-Teacher Communication:**
 - **Mr. Sharma concerned about Aarav's declining Math grades:**
 - September: 88%
 - October: 82%
 - November: 75%
 - **Mr. Sharma messages Mr. Verma (Math teacher) via portal:**
 - "Aarav's Math grades declining. What's the issue?"
 - **Mr. Verma responds (same day):**
 - "Aarav distracted in class lately. Topics getting harder (Trigonometry). Suggest extra classes and reducing gaming time."
 - **Mr. Sharma:** Implements suggestions
 - **December:** Aarav's grade improves to 80%
 - **Mr. Sharma thanks Mr. Verma**

---

*[Continuing with remaining 2 outbound connections...]*

### 7. TO COMPLIANCE & STATUTORY REPORTING MODULE

**WHY This Connection Exists:**
Schools must comply with labor laws and education regulations. Teacher qualifications must meet standards. Statutory reports (PF, ESI, tax) required. Background verification and police clearance.

**DATA FLOW:**
- **Qualification Verification:**
 - Educational certificates (B.Ed, M.Ed, subject degrees)
 - Teaching certifications (CTET, TET, state-specific)
 - Experience certificates
 - Background verification reports
- **Statutory Compliance:**
 - PF registration and contributions
 - ESI registration (if applicable)
 - Professional tax registration
 - Income tax (Form 16 generation)
- **Regulatory Reporting:**
 - Teacher-student ratio (as per norms)
 - Qualified teacher percentage
 - Training hours completed
 - Safety and child protection training

**TRIGGER EVENT:**
- **Teacher Hired:** Verification process initiated
- **Monthly:** PF/ESI contributions reported
- **Quarterly:** Professional tax filed
- **Annually:** Form 16 generated, regulatory reports submitted

**IMPACT:**
- **Qualification Verification (New Hire):**
 - Ms. Gupta applies for Math teacher position
 - Submits: B.Sc (Math), B.Ed, CTET certificate, 5 years experience
 - HR verifies:
 - B.Sc degree: Verified with university 
 - B.Ed: Verified 
 - CTET: Verified with CBSE portal 
 - Experience: Verified with previous school 
 - Background check: Police verification 
 - **All verified:** Ms. Gupta eligible for hiring
- **Statutory Compliance:**
 - **Monthly PF Contribution (Mr. Verma):**
 - Basic salary: ₹50,000
 - Employee PF (12%): ₹6,000
 - Employer PF (12%): ₹6,000
 - Total: ₹12,000 deposited to PF account
 - Reported to EPFO portal
 - **Annual Form 16 (Tax certificate):**
 - Generated in April for previous financial year
 - Shows: Total salary, deductions, tax paid
 - Sent to all teachers for income tax filing
- **Regulatory Reporting:**
 - **Annual School Report to Education Department:**
 - Total teachers: 50
 - Qualified teachers (B.Ed + CTET): 48 (96%)
 - Teacher-student ratio: 1:30 (within norms of 1:35)
 - Training hours/teacher: 40 hours/year (meets 30-hour requirement)
 - Child protection training: 100% teachers completed

**BUSINESS LOGIC:**
```
FUNCTION verify_teacher_qualifications(teacher):
 verification = CREATE QualificationVerification
 verification.teacher = teacher
 
 // Verify educational certificates
 FOR each certificate IN teacher.certificates:
 verification_status = VERIFY_WITH_INSTITUTION(certificate)
 verification.results[certificate] = verification_status
 END FOR
 
 // Verify teaching certification (CTET/TET)
 IF teacher.teaching_certification:
 ctet_verified = VERIFY_WITH_CBSE(teacher.teaching_certification)
 verification.ctet_verified = ctet_verified
 END IF
 
 // Background check
 police_verification = REQUEST_POLICE_VERIFICATION(teacher)
 verification.police_clearance = police_verification
 
 // Overall status
 IF all_verified(verification):
 verification.status = "VERIFIED"
 teacher.verification_status = "VERIFIED"
 ALLOW_HIRING(teacher)
 ELSE:
 verification.status = "FAILED"
 REJECT_HIRING(teacher)
 END IF
 
 RETURN verification
END FUNCTION

FUNCTION generate_statutory_reports(month):
 teachers = GET all_active_teachers
 
 // PF Report
 pf_report = []
 FOR each teacher IN teachers:
 IF teacher.salary >= 15000: // PF applicable
 employee_pf = teacher.basic_salary * 0.12
 employer_pf = teacher.basic_salary * 0.12
 pf_report.add({teacher, employee_pf, employer_pf})
 END IF
 END FOR
 SUBMIT_TO_EPFO(pf_report)
 
 // ESI Report (if salary < ₹21,000)
 esi_report = []
 FOR each teacher IN teachers:
 IF teacher.gross_salary < 21000:
 employee_esi = teacher.gross_salary * 0.0075
 employer_esi = teacher.gross_salary * 0.0325
 esi_report.add({teacher, employee_esi, employer_esi})
 END IF
 END FOR
 SUBMIT_TO_ESIC(esi_report)
 
 // Professional Tax
 pt_report = []
 FOR each teacher IN teachers:
 pt = CALCULATE_PROFESSIONAL_TAX(teacher.gross_salary)
 pt_report.add({teacher, pt})
 END FOR
 SUBMIT_TO_STATE_GOVT(pt_report)
 
 RETURN {pf_report, esi_report, pt_report}
END FUNCTION
```

**EXAMPLE:**
- **Annual Compliance Report (March 2025):**
 - **PF Contributions:** ₹72,00,000 (50 teachers × avg ₹12,000/month × 12 months)
 - **ESI Contributions:** ₹1,20,000 (5 support staff)
 - **Professional Tax:** ₹1,20,000 (50 teachers × ₹200/month × 12 months)
 - **Form 16:** Generated for all 50 teachers
 - **Regulatory Report:** Submitted to Education Department
 - Teacher-student ratio: 1:30 
 - Qualified teachers: 96% 
 - Training compliance: 100% 

---

### 8. TO FACILITIES & INFRASTRUCTURE MODULE

**WHY This Connection Exists:**
Teachers need classrooms, staff rooms, and resources. Smart boards, projectors, lab equipment allocated to teachers. Maintenance requests for classroom facilities. Parking and locker assignments.

**DATA FLOW:**
- **Classroom Allocation:**
 - Assigned classrooms for each teacher
 - Smart board and projector availability
 - Lab allocation (Science, Computer, Art)
- **Staff Room:**
 - Desk and locker assignment
 - Common area facilities
- **Resource Allocation:**
 - Laptops, tablets for teachers
 - Teaching aids (charts, models)
 - Lab equipment (for lab teachers)
- **Maintenance Requests:**
 - Classroom AC not working
 - Projector malfunction
 - Furniture damage

**TRIGGER EVENT:**
- **Teacher Joins:** Classroom and resources allocated
- **Facility Issue:** Maintenance request raised
- **Resource Needed:** Request submitted
- **Teacher Exits:** Resources returned

**IMPACT:**
- **Classroom Allocation:**
 - Mr. Verma (Math) assigned:
 - Primary classroom: Room 205 (Grade 10A home room)
 - Smart board: Yes
 - Projector: Yes
 - Whiteboard: Yes
 - Ms. Sharma (English) assigned:
 - Primary classroom: Room 310 (Grade 9A home room)
 - Smart board: Yes
 - Audio system: Yes (for language listening exercises)
- **Resource Allocation:**
 - All teachers receive:
 - School laptop (Dell, i5, 8GB RAM)
 - Login credentials (LMS, gradebook, email)
 - Staff ID card (access control)
 - Locker in staff room
- **Maintenance Request:**
 - Mr. Patel (Science) reports: "Lab AC not working, students uncomfortable"
 - Maintenance ticket created
 - Technician dispatched same day
 - AC repaired within 24 hours
 - Mr. Patel notified: "Lab AC fixed"

**BUSINESS LOGIC:**
```
FUNCTION allocate_teacher_resources(teacher):
 allocation = CREATE ResourceAllocation
 allocation.teacher = teacher
 
 // Classroom allocation
 IF teacher.is_class_teacher:
 classroom = GET classroom WHERE section = teacher.assigned_section
 allocation.primary_classroom = classroom
 END IF
 
 // Technology allocation
 allocation.laptop = ASSIGN_LAPTOP(teacher)
 allocation.login_credentials = CREATE_CREDENTIALS(teacher)
 allocation.staff_id_card = GENERATE_ID_CARD(teacher)
 
 // Staff room allocation
 allocation.desk = ASSIGN_DESK(teacher)
 allocation.locker = ASSIGN_LOCKER(teacher)
 
 // Subject-specific resources
 IF teacher.subject = "SCIENCE":
 allocation.lab_access = GRANT_LAB_ACCESS(teacher, "SCIENCE_LAB")
 allocation.lab_equipment = ASSIGN_LAB_EQUIPMENT(teacher)
 ELSE IF teacher.subject = "COMPUTER":
 allocation.computer_lab_access = GRANT_LAB_ACCESS(teacher, "COMPUTER_LAB")
 ELSE IF teacher.subject = "ART":
 allocation.art_room_access = GRANT_LAB_ACCESS(teacher, "ART_ROOM")
 END IF
 
 NOTIFY teacher("Resources allocated. Collect laptop and ID from admin office.")
 
 RETURN allocation
END FUNCTION

FUNCTION raise_maintenance_request(teacher, facility, issue):
 request = CREATE MaintenanceRequest
 request.raised_by = teacher
 request.facility = facility
 request.issue = issue
 request.priority = teacher.assess_priority(issue)
 request.status = "OPEN"
 request.timestamp = NOW
 
 // Assign to maintenance team
 NOTIFY maintenance_team("New request: {facility} - {issue}, Priority: {request.priority}")
 
 // Track resolution
 IF request.priority = "URGENT":
 request.expected_resolution = NOW + 4 hours
 ELSE IF request.priority = "HIGH":
 request.expected_resolution = NOW + 24 hours
 ELSE:
 request.expected_resolution = NOW + 72 hours
 END IF
 
 RETURN request
END FUNCTION
```

**EXAMPLE:**
- **Ms. Gupta's Resource Allocation (New Hire):**
 - **Day 1:** Joins as Math teacher
 - **Resources allocated:**
 - Laptop: Dell Latitude, Serial #12345
 - Login: gupta@school.com, password sent via SMS
 - Staff ID: #T-089
 - Locker: Staff Room, Locker #45
 - Classroom: Room 408 (Grade 8A home room)
 - Smart board access: Trained during induction
 - **Day 2:** Collects laptop and ID from admin
 - **Day 3:** Starts teaching with all resources

---

## INBOUND CONNECTIONS (Other Modules → HR & Teacher Management)

### FROM TIMETABLE MODULE

**WHY:** Timetable assignments determine teacher workload.

**DATA RECEIVED:**
- Class assignments
- Period allocations
- Invigilation duties
- Extra-curricular responsibilities

**IMPACT:**
- Workload tracked
- Overload alerts generated
- Substitute needs identified

**TRIGGER:** Timetable created, changed

---

### FROM STUDENT PERFORMANCE MODULE

**WHY:** Student results measure teacher effectiveness.

**DATA RECEIVED:**
- Class average marks
- Pass percentages
- Student feedback ratings
- Parent feedback

**IMPACT:**
- Performance appraisals
- Training needs identified
- Rewards and recognition

**TRIGGER:** Results published, feedback collected

---

### FROM PAYROLL MODULE

**WHY:** Attendance affects salary calculations.

**DATA RECEIVED:**
- Attendance records
- Leave taken
- Overtime hours
- Deductions

**IMPACT:**
- Salary processed accurately
- Leave deductions applied
- Bonuses added

**TRIGGER:** Month-end payroll processing

---

### FROM PARENT PORTAL

**WHY:** Parents communicate with teachers.

**DATA RECEIVED:**
- Parent messages
- Meeting requests
- Feedback and complaints

**IMPACT:**
- Teachers respond to parents
- Meetings scheduled
- Issues resolved

**TRIGGER:** Parent sends message, books meeting

---

### FROM COMPLIANCE MODULE

**WHY:** Regulatory requirements for teachers.

**DATA RECEIVED:**
- Qualification standards
- Training mandates
- Statutory reporting requirements

**IMPACT:**
- Qualifications verified
- Training organized
- Reports submitted

**TRIGGER:** Regulation updated, audit scheduled

---

## SUMMARY

**HR & Teacher Management Module connects to 20+ modules**

**Critical Outbound Dependencies:**
1. Timetable & Scheduling (teacher availability, workload, substitutes)
2. Payroll & Finance (salary processing, statutory compliance)
3. Attendance (teacher attendance, leave management)
4. Student Performance (teaching effectiveness, appraisals)
5. Professional Development (training, skill enhancement)
6. Parent Engagement (communication, PTMs)
7. Compliance (qualification verification, statutory reporting)
8. Facilities (resource allocation, maintenance)

**Critical Inbound Dependencies:**
1. Timetable (workload assignments)
2. Student Performance (effectiveness metrics)
3. Payroll (attendance for salary)
4. Parent Portal (communication)
5. Compliance (regulatory requirements)

**Teacher Metrics:**
- **Total Teachers:** 50 (example school)
- **Teacher-Student Ratio:** 1:30
- **Qualified Teachers:** 96% (B.Ed + CTET)
- **Average Experience:** 8 years
- **Retention Rate:** 92% (low attrition)
- **Training Hours:** 40 hours/teacher/year

**Salary Structure (Average):**
- **Basic:** ₹50,000/month
- **HRA:** ₹20,000 (40%)
- **DA:** ₹5,000
- **Transport:** ₹2,000
- **Gross:** ₹77,000
- **Deductions:** ₹10,000 (PF, tax)
- **Net:** ₹67,000/month

**Leave Entitlement:**
- **Casual Leave:** 12 days/year
- **Sick Leave:** 12 days/year
- **Earned Leave:** 15 days/year
- **Maternity Leave:** 180 days (6 months)
- **Paternity Leave:** 15 days

**Data Freshness:**
- Real-time: Attendance check-in/out, leave applications
- Daily: Substitute assignments, maintenance requests
- Weekly: Performance feedback, parent communication
- Monthly: Salary processing, attendance reports
- Quarterly: Performance reviews, training programs
- Annually: Appraisals, increments, statutory reports

This module is the **"human capital backbone"** - managing the school's most valuable asset (teachers) to ensure quality education delivery, teacher satisfaction, and regulatory compliance.

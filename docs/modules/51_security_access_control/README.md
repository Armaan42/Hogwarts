# SECURITY & ACCESS CONTROL MODULE - COMPLETE DEPENDENCY ANALYSIS

## MODULE OVERVIEW

**Name:** Security & Access Control Module  
**Role:** Enterprise Security Foundation - Authentication, Authorization & Audit Engine  
**Type:** Critical Infrastructure Module  
**Dependencies:** ALL 53 modules depend on this for security  

**Primary Functions:**
- Multi-Factor Authentication (MFA) - SMS, Email, Authenticator App
- Single Sign-On (SSO) - Unified login across all platforms
- Role-Based Access Control (RBAC) - Granular permissions management
- Security Audit Logs - Complete activity tracking and compliance
- Penetration Testing - Regular security assessments
- Vulnerability Scanning - Automated threat detection
- Encryption Management - Data protection at rest and in transit
- Session Management - Secure session handling and timeout
- Password Policies - Strength requirements and rotation
- IP Whitelisting - Network-level access control
- Device Management - Trusted device registration
- Security Alerts - Real-time threat notifications

---

## OUTBOUND CONNECTIONS (Security & Access Control → Other Modules)

### 1. TO STUDENT MANAGEMENT MODULE

**WHY This Connection Exists:**
Every student needs secure login credentials to access the student portal, LMS, and exam systems. Parents need separate credentials to access the parent portal. Security module manages authentication and authorization for all student and parent accounts.

**DATA FLOW:**
- **User Account Creation:**
  - Student ID → Username generation
  - Email/Mobile → MFA setup
  - Default password generation (temporary)
  - Account activation status
- **Role Assignment:**
  - Student role with grade-specific permissions
  - Parent role with child-specific access
  - Alumni role (after graduation)
- **Access Permissions:**
  - View own profile (read-only demographic data)
  - Edit contact information (limited fields)
  - Access LMS courses (enrolled courses only)
  - View fee statements and pay online
  - Download admit cards and report cards
- **Security Policies:**
  - Password complexity requirements
  - MFA enforcement for sensitive operations
  - Session timeout settings (30 minutes inactivity)
  - Device trust management

**TRIGGER EVENT:**
- **New Student Admission:** Auto-create student and parent accounts
- **Grade Promotion:** Update role permissions for new grade level
- **Student Withdrawal:** Deactivate student account, maintain parent access for alumni
- **Password Reset Request:** Initiate secure password recovery flow
- **Suspicious Login Attempt:** Lock account, send alert to parent

**IMPACT:**
- **Secure Student Portal Access:**
  - Student logs in with username/password + OTP
  - Access limited to own data only (cannot see other students' information)
  - All actions logged for audit trail
- **Parent Multi-Child Access:**
  - Single parent account can view multiple children
  - Switch between children without re-login
  - Separate permissions for each child
- **Account Lockout Protection:**
  - 5 failed login attempts → Account locked for 30 minutes
  - Email/SMS alert sent to registered contact
  - Admin can manually unlock
- **Session Security:**
  - Auto-logout after 30 minutes inactivity
  - Concurrent session limit: 2 devices maximum
  - Force logout from all devices option

**BUSINESS LOGIC:**
```
FUNCTION create_student_account(student):
  // Generate username
  username = student.admission_number  // e.g., 2024/06/0123
  
  // Generate temporary password
  temp_password = GENERATE_RANDOM(12 chars, alphanumeric + special)
  password_hash = BCRYPT(temp_password, salt_rounds=12)
  
  // Create user account
  user = CREATE_USER
  user.username = username
  user.password_hash = password_hash
  user.email = student.email
  user.mobile = student.mobile
  user.status = "ACTIVE"
  user.must_change_password = TRUE
  user.mfa_enabled = FALSE  // Optional, enabled on first login
  
  // Assign role
  role = GET_ROLE("STUDENT_GRADE_" + student.grade)
  ASSIGN_ROLE(user, role)
  
  // Set permissions
  permissions = [
    "view_own_profile",
    "edit_contact_info",
    "access_lms_courses",
    "view_fee_statements",
    "download_admit_cards",
    "download_report_cards",
    "submit_assignments"
  ]
  GRANT_PERMISSIONS(user, permissions)
  
  // Send credentials
  SEND_EMAIL(student.email, "Login Credentials", {
    username: username,
    temp_password: temp_password,
    portal_url: "https://hogwarts.edu.in/student"
  })
  
  SEND_SMS(student.mobile, "Your login: {username}, Password: {temp_password}")
  
  // Create audit log
  LOG_AUDIT("ACCOUNT_CREATED", user.id, "Student account created")
  
  RETURN user
END FUNCTION

FUNCTION authenticate_user(username, password, otp=NULL):
  user = GET_USER(username)
  
  // Check if account exists and active
  IF user == NULL OR user.status != "ACTIVE":
    LOG_AUDIT("LOGIN_FAILED", username, "Invalid username or inactive account")
    RETURN {success: FALSE, error: "Invalid credentials"}
  END IF
  
  // Check if account locked
  IF user.locked_until > NOW:
    RETURN {success: FALSE, error: "Account locked until {user.locked_until}"}
  END IF
  
  // Verify password
  IF NOT BCRYPT_VERIFY(password, user.password_hash):
    user.failed_attempts += 1
    
    IF user.failed_attempts >= 5:
      user.locked_until = NOW + 30 MINUTES
      SEND_ALERT(user.email, "Account locked due to multiple failed attempts")
      LOG_AUDIT("ACCOUNT_LOCKED", user.id, "5 failed login attempts")
    END IF
    
    LOG_AUDIT("LOGIN_FAILED", user.id, "Incorrect password")
    RETURN {success: FALSE, error: "Invalid credentials"}
  END IF
  
  // Check MFA if enabled
  IF user.mfa_enabled:
    IF otp == NULL:
      // Send OTP
      otp_code = GENERATE_OTP(6 digits)
      STORE_OTP(user.id, otp_code, expires=5 MINUTES)
      SEND_SMS(user.mobile, "Your OTP: {otp_code}")
      RETURN {success: FALSE, requires_mfa: TRUE}
    ELSE:
      // Verify OTP
      IF NOT VERIFY_OTP(user.id, otp):
        LOG_AUDIT("MFA_FAILED", user.id, "Invalid OTP")
        RETURN {success: FALSE, error: "Invalid OTP"}
      END IF
    END IF
  END IF
  
  // Reset failed attempts
  user.failed_attempts = 0
  user.last_login = NOW
  
  // Create session
  session = CREATE_SESSION
  session.user_id = user.id
  session.token = GENERATE_JWT(user.id, user.role, expires=30 MINUTES)
  session.ip_address = REQUEST.ip
  session.user_agent = REQUEST.user_agent
  session.expires_at = NOW + 30 MINUTES
  
  // Check concurrent sessions
  active_sessions = COUNT(sessions WHERE user_id = user.id AND expires_at > NOW)
  IF active_sessions >= 2:
    // Terminate oldest session
    oldest = GET_OLDEST_SESSION(user.id)
    TERMINATE_SESSION(oldest.id)
  END IF
  
  LOG_AUDIT("LOGIN_SUCCESS", user.id, "Successful login from {REQUEST.ip}")
  
  RETURN {success: TRUE, token: session.token, user: user}
END FUNCTION
```

**EXAMPLE:**
- **Student Aarav (Grade 9) First Login:**
  - Admission Number: 2024/09/0045
  - Username: 2024/09/0045
  - Temporary Password: Xy7#mK9$pL2q (sent via email/SMS)
  - Logs in → System prompts to change password
  - Sets new password: Aarav@Hogwarts2024
  - System prompts to enable MFA → Aarav enables SMS OTP
  - Next login requires: Username + Password + OTP
- **Parent Login (Mr. Sharma, 2 children):**
  - Username: sharma.rajesh@gmail.com
  - Password: SecurePass@123
  - OTP: 847562 (sent to mobile)
  - Dashboard shows: Aarav (Grade 9), Ananya (Grade 6)
  - Can switch between children without re-login
  - Separate permissions for each child's data

---

### 2. TO HR & TEACHER MANAGEMENT MODULE

**WHY This Connection Exists:**
Teachers and staff need secure access to the ERP system with role-based permissions. A teacher should only access their assigned classes, while admin staff have broader access. HR module manages employee data, but Security module controls what each employee can access.

**DATA FLOW:**
- **Employee Account Creation:**
  - Employee ID → Username
  - Official email → Primary contact
  - Department → Default role assignment
  - Designation → Permission level
- **Role Hierarchy:**
  - Super Admin (Principal, IT Admin)
  - Admin (Vice Principal, Office Manager)
  - Academic Head (Curriculum Director)
  - Teacher (Subject Teacher, Class Teacher)
  - Support Staff (Librarian, Lab Assistant, Counselor)
  - Guest (Substitute Teacher, Intern)
- **Granular Permissions:**
  - Teachers: View assigned classes, mark attendance, enter grades
  - Class Teachers: Additional access to section students' behavioral records
  - Academic Head: View all academic data, approve curriculum changes
  - Admin: Access to all modules except financial (view-only)
  - Super Admin: Full access to all modules
- **Access Restrictions:**
  - Teachers cannot view salary information
  - Non-finance staff cannot access accounts module
  - Guest accounts expire after specified date

**TRIGGER EVENT:**
- **New Employee Joining:** Create account with probation-level access
- **Promotion:** Upgrade role and permissions
- **Department Transfer:** Update role and accessible data
- **Resignation/Termination:** Deactivate account immediately
- **Sabbatical Leave:** Temporarily suspend access

**IMPACT:**
- **Role-Based Dashboard:**
  - Teacher logs in → Sees only assigned classes and subjects
  - Class Teacher → Additional section management options
  - Principal → Complete school overview dashboard
- **Data Access Control:**
  - Math Teacher can only enter grades for Math subject
  - Cannot view or edit English grades
  - Cannot access student fee information
- **Audit Compliance:**
  - All grade entries logged with teacher ID and timestamp
  - Cannot modify grades after submission deadline (locked by system)
  - Any override requires Principal approval (logged)

**BUSINESS LOGIC:**
```
FUNCTION create_teacher_account(employee):
  username = employee.email  // Use official email as username
  temp_password = GENERATE_RANDOM(12 chars)
  
  // Determine role based on designation
  IF employee.designation == "PRINCIPAL":
    role = "SUPER_ADMIN"
  ELSE IF employee.designation == "VICE_PRINCIPAL":
    role = "ADMIN"
  ELSE IF employee.designation IN ["HOD", "ACADEMIC_COORDINATOR"]:
    role = "ACADEMIC_HEAD"
  ELSE IF employee.designation IN ["TEACHER", "PGT", "TGT", "PRT"]:
    role = "TEACHER"
  ELSE IF employee.designation IN ["LIBRARIAN", "LAB_ASSISTANT", "COUNSELOR"]:
    role = "SUPPORT_STAFF"
  ELSE:
    role = "GUEST"
  END IF
  
  user = CREATE_USER
  user.username = username
  user.password_hash = BCRYPT(temp_password)
  user.employee_id = employee.id
  user.status = "ACTIVE"
  user.mfa_enabled = TRUE  // Mandatory for staff
  
  ASSIGN_ROLE(user, role)
  
  // Grant permissions based on role
  permissions = GET_ROLE_PERMISSIONS(role)
  GRANT_PERMISSIONS(user, permissions)
  
  // Additional subject-specific permissions for teachers
  IF role == "TEACHER":
    subjects = GET_ASSIGNED_SUBJECTS(employee.id)
    FOR each subject IN subjects:
      GRANT_PERMISSION(user, "enter_grades_" + subject.id)
      GRANT_PERMISSION(user, "mark_attendance_" + subject.id)
    END FOR
  END IF
  
  SEND_EMAIL(employee.email, "ERP Login Credentials", {
    username: username,
    temp_password: temp_password,
    mfa_setup_url: "https://hogwarts.edu.in/setup-mfa"
  })
  
  LOG_AUDIT("EMPLOYEE_ACCOUNT_CREATED", user.id, "Role: {role}")
  
  RETURN user
END FUNCTION

FUNCTION check_permission(user, action, resource):
  // Check if user has permission for this action
  has_permission = USER_HAS_PERMISSION(user, action)
  
  IF NOT has_permission:
    LOG_AUDIT("PERMISSION_DENIED", user.id, "Attempted: {action} on {resource}")
    RETURN FALSE
  END IF
  
  // Additional resource-level checks
  IF action == "ENTER_GRADES":
    // Teacher can only enter grades for assigned subjects
    subject_id = resource.subject_id
    assigned_subjects = GET_ASSIGNED_SUBJECTS(user.employee_id)
    
    IF subject_id NOT IN assigned_subjects:
      LOG_AUDIT("PERMISSION_DENIED", user.id, "Not assigned to subject {subject_id}")
      RETURN FALSE
    END IF
    
    // Check if grade entry period is open
    IF resource.submission_deadline < NOW:
      LOG_AUDIT("PERMISSION_DENIED", user.id, "Grade entry deadline passed")
      RETURN FALSE
    END IF
  END IF
  
  LOG_AUDIT("PERMISSION_GRANTED", user.id, "Action: {action} on {resource}")
  RETURN TRUE
END FUNCTION
```

**EXAMPLE:**
- **Ms. Priya (Math Teacher, Grade 9):**
  - Username: priya.sharma@hogwarts.edu.in
  - Role: TEACHER
  - Assigned: Grade 9A, 9B, 9C (Math)
  - Permissions:
    - ✅ View students in 9A, 9B, 9C
    - ✅ Mark attendance for Math periods
    - ✅ Enter Math grades for 9A, 9B, 9C
    - ❌ Cannot view Grade 10 students
    - ❌ Cannot enter Science grades
    - ❌ Cannot access fee module
  - Logs in → Dashboard shows: 3 sections, 90 students, upcoming Math tests
- **Mr. Verma (Principal):**
  - Username: principal@hogwarts.edu.in
  - Role: SUPER_ADMIN
  - Permissions: Full access to all 54 modules
  - Can override grade entry deadlines (logged as admin override)
  - Receives daily security audit summary email

---

### 3. TO FEE MANAGEMENT MODULE

**WHY This Connection Exists:**
Fee payment is a sensitive financial transaction requiring strong authentication and authorization. Parents must securely access fee information and make payments. Accountants need controlled access to financial data with complete audit trails.

**DATA FLOW:**
- **Parent Payment Portal Access:**
  - Authenticated parent session
  - Child-specific fee data access
  - Payment gateway integration security
  - Transaction authorization
- **Accountant Access:**
  - Role: ACCOUNTANT
  - Permissions: View all fee data, generate reports, process refunds
  - Restrictions: Cannot delete payment records, cannot waive fees without approval
- **Security Requirements:**
  - MFA mandatory for payment transactions above ₹10,000
  - Payment OTP verification
  - Transaction signing with digital signature
  - PCI-DSS compliance for card data

**TRIGGER EVENT:**
- **Parent Initiates Payment:** Authenticate + MFA + Payment OTP
- **Accountant Processes Refund:** Requires approval workflow + audit log
- **Fee Waiver Request:** Multi-level approval (Class Teacher → Principal → Accountant)
- **Bulk Payment Upload:** Additional security verification for batch processing

**IMPACT:**
- **Secure Payment Flow:**
  - Parent logs in → Enters amount → MFA verification → Payment gateway → OTP → Success
  - All steps logged with timestamps and IP addresses
  - Failed payment attempts tracked (fraud detection)
- **Audit Trail:**
  - Every fee transaction linked to user who initiated it
  - Cannot modify payment records (immutable ledger)
  - Accountant actions require justification notes
- **Fraud Prevention:**
  - Unusual payment patterns flagged (e.g., multiple failed attempts)
  - Large payments (>₹50,000) require additional verification
  - IP address tracking for suspicious locations

**BUSINESS LOGIC:**
```
FUNCTION initiate_payment(user, student_id, amount):
  // Verify user is parent of this student
  IF NOT IS_PARENT_OF(user, student_id):
    LOG_AUDIT("PAYMENT_DENIED", user.id, "Not authorized for student {student_id}")
    RETURN {success: FALSE, error: "Unauthorized"}
  END IF
  
  // Check if MFA required for large amounts
  IF amount > 10000 AND NOT user.mfa_verified_this_session:
    otp = GENERATE_OTP(6 digits)
    STORE_OTP(user.id, otp, expires=5 MINUTES, purpose="PAYMENT")
    SEND_SMS(user.mobile, "Payment OTP: {otp}. Do not share.")
    RETURN {success: FALSE, requires_mfa: TRUE}
  END IF
  
  // Create payment session
  payment_session = CREATE_PAYMENT_SESSION
  payment_session.user_id = user.id
  payment_session.student_id = student_id
  payment_session.amount = amount
  payment_session.status = "INITIATED"
  payment_session.ip_address = REQUEST.ip
  payment_session.created_at = NOW
  
  // Generate payment gateway token
  gateway_token = PAYMENT_GATEWAY.create_token({
    amount: amount,
    customer_id: user.id,
    order_id: payment_session.id,
    callback_url: "https://hogwarts.edu.in/payment/callback"
  })
  
  LOG_AUDIT("PAYMENT_INITIATED", user.id, "Amount: ₹{amount}, Student: {student_id}")
  
  RETURN {success: TRUE, gateway_url: gateway_token.url}
END FUNCTION

FUNCTION process_refund(accountant_user, payment_id, amount, reason):
  // Check if user has refund permission
  IF NOT USER_HAS_PERMISSION(accountant_user, "PROCESS_REFUND"):
    LOG_AUDIT("REFUND_DENIED", accountant_user.id, "No permission")
    RETURN {success: FALSE, error: "Unauthorized"}
  END IF
  
  payment = GET_PAYMENT(payment_id)
  
  // Validate refund amount
  IF amount > payment.amount:
    RETURN {success: FALSE, error: "Refund exceeds payment amount"}
  END IF
  
  // Check if refund already processed
  IF payment.refund_status == "REFUNDED":
    RETURN {success: FALSE, error: "Already refunded"}
  END IF
  
  // Require approval for large refunds
  IF amount > 50000:
    approval_request = CREATE_APPROVAL_REQUEST
    approval_request.type = "REFUND"
    approval_request.amount = amount
    approval_request.reason = reason
    approval_request.requested_by = accountant_user.id
    approval_request.approver = GET_USER_BY_ROLE("PRINCIPAL")
    
    SEND_NOTIFICATION(approval_request.approver, "Refund approval required: ₹{amount}")
    LOG_AUDIT("REFUND_APPROVAL_REQUESTED", accountant_user.id, "Amount: ₹{amount}")
    
    RETURN {success: FALSE, requires_approval: TRUE, request_id: approval_request.id}
  END IF
  
  // Process refund
  refund = PAYMENT_GATEWAY.process_refund(payment.transaction_id, amount)
  
  IF refund.success:
    payment.refund_amount = amount
    payment.refund_status = "REFUNDED"
    payment.refund_date = NOW
    payment.refunded_by = accountant_user.id
    payment.refund_reason = reason
    
    LOG_AUDIT("REFUND_PROCESSED", accountant_user.id, "Payment: {payment_id}, Amount: ₹{amount}")
    
    SEND_EMAIL(payment.user.email, "Refund Processed", {
      amount: amount,
      refund_date: NOW,
      credited_in: "5-7 business days"
    })
  END IF
  
  RETURN refund
END FUNCTION
```

**EXAMPLE:**
- **Parent Payment Flow:**
  - Mr. Sharma logs in to pay ₹45,000 (quarterly fee)
  - Enters amount → System sends OTP to mobile: 847562
  - Enters OTP → Redirected to payment gateway
  - Selects UPI → Enters UPI PIN
  - Payment successful → Receipt generated
  - Audit log: "Payment ₹45,000 by user sharma.rajesh@gmail.com from IP 103.21.45.67 at 2024-01-15 14:32:18"
- **Accountant Refund:**
  - Ms. Gupta (Accountant) processes ₹15,000 refund for withdrawn student
  - Reason: "Student transferred to another school mid-term"
  - Amount < ₹50,000 → No approval needed
  - Refund processed → Parent notified via email/SMS
  - Audit log: "Refund ₹15,000 processed by gupta.meera@hogwarts.edu.in, Reason: Student transfer"

---

### 4. TO LMS (LEARNING MANAGEMENT SYSTEM)

**WHY This Connection Exists:**
LMS contains sensitive academic content, student submissions, and grade data. Security module ensures only enrolled students can access course materials, teachers can grade assignments, and content is protected from unauthorized access.

**DATA FLOW:**
- **Student Course Access:**
  - Authenticated student session
  - Enrolled courses list
  - Course-specific permissions (view content, submit assignments, take quizzes)
- **Teacher Content Management:**
  - Upload course materials
  - Create assignments and quizzes
  - Grade student submissions
- **Content Protection:**
  - DRM for video lectures (prevent download/screen recording)
  - Watermarking for PDF documents
  - Plagiarism detection for submissions

**TRIGGER EVENT:**
- **Student Accesses Course:** Verify enrollment + active session
- **Assignment Submission:** Authenticate + check deadline + virus scan
- **Quiz Attempt:** Verify identity + prevent tab switching + time limit enforcement
- **Content Download:** Apply watermark + log download event

**IMPACT:**
- **Controlled Access:**
  - Student can only see enrolled courses
  - Cannot access other students' submissions
  - Cannot view grades before publication
- **Academic Integrity:**
  - Quiz proctoring: Webcam monitoring, tab switch detection
  - Assignment plagiarism check before submission
  - Submission timestamps immutable (cannot be backdated)
- **Content Security:**
  - Video lectures: DRM-protected, cannot download
  - PDFs: Watermarked with student name and ID
  - Source code submissions: Stored securely, virus scanned

**BUSINESS LOGIC:**
```
FUNCTION access_course_content(user, course_id, content_id):
  // Verify student is enrolled
  enrollment = GET_ENROLLMENT(user.id, course_id)
  
  IF enrollment == NULL OR enrollment.status != "ACTIVE":
    LOG_AUDIT("COURSE_ACCESS_DENIED", user.id, "Not enrolled in course {course_id}")
    RETURN {success: FALSE, error: "Not enrolled"}
  END IF
  
  content = GET_CONTENT(content_id)
  
  // Check if content is published
  IF content.status != "PUBLISHED" OR content.publish_date > NOW:
    LOG_AUDIT("CONTENT_ACCESS_DENIED", user.id, "Content not yet published")
    RETURN {success: FALSE, error: "Content not available"}
  END IF
  
  // Apply watermark for downloadable content
  IF content.type == "PDF":
    watermarked_url = APPLY_WATERMARK(content.url, {
      student_name: user.name,
      student_id: user.username,
      download_date: NOW
    })
    content.url = watermarked_url
  END IF
  
  // Apply DRM for video content
  IF content.type == "VIDEO":
    drm_token = GENERATE_DRM_TOKEN(user.id, content.id, expires=2 HOURS)
    content.url = content.url + "?token=" + drm_token
  END IF
  
  LOG_AUDIT("CONTENT_ACCESSED", user.id, "Course: {course_id}, Content: {content_id}")
  
  RETURN {success: TRUE, content: content}
END FUNCTION

FUNCTION submit_assignment(user, assignment_id, file):
  assignment = GET_ASSIGNMENT(assignment_id)
  
  // Check if student is enrolled in course
  enrollment = GET_ENROLLMENT(user.id, assignment.course_id)
  IF enrollment == NULL:
    RETURN {success: FALSE, error: "Not enrolled"}
  END IF
  
  // Check deadline
  IF NOW > assignment.deadline:
    // Check if late submission allowed
    IF NOT assignment.allow_late_submission:
      LOG_AUDIT("ASSIGNMENT_DENIED", user.id, "Deadline passed")
      RETURN {success: FALSE, error: "Deadline passed"}
    ELSE:
      late_penalty = assignment.late_penalty_percent
    END IF
  END IF
  
  // Virus scan
  scan_result = VIRUS_SCAN(file)
  IF scan_result.infected:
    LOG_AUDIT("ASSIGNMENT_DENIED", user.id, "File infected: {scan_result.virus}")
    RETURN {success: FALSE, error: "File contains malware"}
  END IF
  
  // Plagiarism check
  plagiarism_score = CHECK_PLAGIARISM(file, assignment.id)
  
  // Store submission
  submission = CREATE_SUBMISSION
  submission.assignment_id = assignment_id
  submission.student_id = user.id
  submission.file_path = SECURE_UPLOAD(file)
  submission.submitted_at = NOW
  submission.plagiarism_score = plagiarism_score
  submission.is_late = (NOW > assignment.deadline)
  submission.ip_address = REQUEST.ip
  
  LOG_AUDIT("ASSIGNMENT_SUBMITTED", user.id, "Assignment: {assignment_id}, Plagiarism: {plagiarism_score}%")
  
  // Alert teacher if high plagiarism
  IF plagiarism_score > 40:
    teacher = GET_COURSE_TEACHER(assignment.course_id)
    SEND_NOTIFICATION(teacher, "High plagiarism detected: {user.name}, Score: {plagiarism_score}%")
  END IF
  
  RETURN {success: TRUE, submission_id: submission.id, plagiarism_score: plagiarism_score}
END FUNCTION
```

**EXAMPLE:**
- **Student Accessing Video Lecture:**
  - Aarav (Grade 9) opens "Algebra Chapter 5" video
  - System verifies: Enrolled in Math course ✓, Content published ✓
  - Generates DRM token valid for 2 hours
  - Video plays with watermark: "Aarav Kumar - 2024/09/0045"
  - Cannot download, cannot screen record (DRM protection)
  - Tab switch detected → Warning: "Focus on lecture, tab switches logged"
- **Assignment Submission:**
  - Priya submits Math assignment (PDF)
  - System: Virus scan ✓, Plagiarism check: 12% (acceptable)
  - Submission recorded with timestamp: 2024-01-15 23:45:32
  - Teacher receives notification: "New submission from Priya"
  - Audit log: "Assignment submitted by priya.sharma@hogwarts.edu.in, Plagiarism: 12%"

---

### 5. TO EXAM & ASSESSMENT MODULE

**WHY This Connection Exists:**
Exam data is highly sensitive - question papers must be protected from leaks, student answers must be secure, and grades must be tamper-proof. Security module ensures exam integrity through access control, encryption, and audit trails.

**DATA FLOW:**
- **Question Paper Security:**
  - Encrypted storage until exam time
  - Access restricted to exam coordinators
  - Decryption only during exam window
  - Watermarked for each student
- **Online Exam Security:**
  - Identity verification before exam
  - Proctoring (webcam, screen monitoring)
  - Tab switch prevention
  - Auto-submit on time expiry
- **Grade Protection:**
  - Grades encrypted in database
  - Only authorized teachers can enter grades
  - Grade modification requires approval
  - Immutable audit trail

**TRIGGER EVENT:**
- **Question Paper Upload:** Encrypt + restrict access + audit log
- **Exam Starts:** Decrypt question paper + enable student access
- **Student Logs into Exam:** Identity verification + proctoring activation
- **Grade Entry:** Authenticate teacher + verify subject assignment + log entry
- **Grade Modification:** Require approval workflow + justification

**IMPACT:**
- **Exam Integrity:**
  - Question papers cannot be accessed before exam time
  - Students cannot copy (tab switch detection, webcam monitoring)
  - Answers auto-saved every 30 seconds (prevent data loss)
- **Grade Security:**
  - Teachers cannot modify grades after submission deadline
  - Any grade change requires Principal approval
  - Complete history of grade modifications maintained
- **Fraud Prevention:**
  - Impersonation detection (photo verification)
  - Unusual exam patterns flagged (too fast completion, identical answers)
  - IP address tracking (detect proxy/VPN usage)

**BUSINESS LOGIC:**
```
FUNCTION upload_question_paper(teacher, exam_id, file):
  // Verify teacher is exam coordinator
  exam = GET_EXAM(exam_id)
  IF teacher.id != exam.coordinator_id:
    LOG_AUDIT("UPLOAD_DENIED", teacher.id, "Not exam coordinator")
    RETURN {success: FALSE, error: "Unauthorized"}
  END IF
  
  // Encrypt question paper
  encryption_key = GENERATE_KEY(256 bits)
  encrypted_file = AES_ENCRYPT(file, encryption_key)
  
  // Store encrypted file
  file_path = SECURE_UPLOAD(encrypted_file)
  
  // Store encryption key (encrypted with master key)
  master_key = GET_MASTER_KEY()
  encrypted_key = RSA_ENCRYPT(encryption_key, master_key)
  
  exam.question_paper_path = file_path
  exam.encryption_key = encrypted_key
  exam.uploaded_by = teacher.id
  exam.uploaded_at = NOW
  
  LOG_AUDIT("QUESTION_PAPER_UPLOADED", teacher.id, "Exam: {exam_id}, Encrypted: YES")
  
  RETURN {success: TRUE}
END FUNCTION

FUNCTION start_online_exam(student, exam_id):
  exam = GET_EXAM(exam_id)
  
  // Verify student is enrolled
  enrollment = GET_EXAM_ENROLLMENT(student.id, exam_id)
  IF enrollment == NULL:
    LOG_AUDIT("EXAM_ACCESS_DENIED", student.id, "Not enrolled")
    RETURN {success: FALSE, error: "Not enrolled"}
  END IF
  
  // Check exam window
  IF NOW < exam.start_time OR NOW > exam.end_time:
    LOG_AUDIT("EXAM_ACCESS_DENIED", student.id, "Outside exam window")
    RETURN {success: FALSE, error: "Exam not available"}
  END IF
  
  // Check if already attempted
  IF enrollment.attempt_count >= exam.max_attempts:
    LOG_AUDIT("EXAM_ACCESS_DENIED", student.id, "Max attempts reached")
    RETURN {success: FALSE, error: "No attempts remaining"}
  END IF
  
  // Identity verification
  photo_verification = VERIFY_PHOTO(student.id, REQUEST.webcam_image)
  IF NOT photo_verification.match:
    LOG_AUDIT("EXAM_ACCESS_DENIED", student.id, "Photo verification failed")
    SEND_ALERT(exam.coordinator, "Identity verification failed: {student.name}")
    RETURN {success: FALSE, error: "Identity verification failed"}
  END IF
  
  // Decrypt question paper
  master_key = GET_MASTER_KEY()
  encryption_key = RSA_DECRYPT(exam.encryption_key, master_key)
  question_paper = AES_DECRYPT(exam.question_paper_path, encryption_key)
  
  // Apply watermark
  watermarked_paper = APPLY_WATERMARK(question_paper, {
    student_name: student.name,
    student_id: student.username,
    exam_date: NOW
  })
  
  // Create exam session
  session = CREATE_EXAM_SESSION
  session.student_id = student.id
  session.exam_id = exam_id
  session.start_time = NOW
  session.end_time = NOW + exam.duration
  session.ip_address = REQUEST.ip
  session.proctoring_enabled = TRUE
  session.tab_switches = 0
  session.suspicious_activities = []
  
  // Start proctoring
  START_PROCTORING(session.id, {
    webcam: TRUE,
    screen_recording: TRUE,
    tab_switch_detection: TRUE,
    copy_paste_prevention: TRUE
  })
  
  LOG_AUDIT("EXAM_STARTED", student.id, "Exam: {exam_id}, Session: {session.id}")
  
  enrollment.attempt_count += 1
  
  RETURN {success: TRUE, question_paper: watermarked_paper, session_id: session.id}
END FUNCTION

FUNCTION monitor_exam_session(session_id, event):
  session = GET_EXAM_SESSION(session_id)
  
  // Track suspicious activities
  IF event.type == "TAB_SWITCH":
    session.tab_switches += 1
    session.suspicious_activities.append({
      type: "TAB_SWITCH",
      timestamp: NOW,
      details: event.details
    })
    
    // Warn student
    IF session.tab_switches == 1:
      SEND_WARNING(session.student_id, "Tab switching detected. This is your first warning.")
    ELSE IF session.tab_switches == 3:
      SEND_WARNING(session.student_id, "Multiple tab switches detected. Exam coordinator notified.")
      SEND_ALERT(session.exam.coordinator, "Student {session.student.name} switched tabs 3 times")
    ELSE IF session.tab_switches >= 5:
      // Auto-submit exam
      AUTO_SUBMIT_EXAM(session.id, reason="Excessive tab switching")
      LOG_AUDIT("EXAM_AUTO_SUBMITTED", session.student_id, "Reason: Tab switching")
    END IF
  END IF
  
  IF event.type == "FACE_NOT_DETECTED":
    session.suspicious_activities.append({
      type: "FACE_NOT_DETECTED",
      timestamp: NOW,
      duration: event.duration
    })
    
    IF event.duration > 30 SECONDS:
      SEND_ALERT(session.exam.coordinator, "Student {session.student.name} not visible for 30+ seconds")
    END IF
  END IF
  
  IF event.type == "COPY_PASTE_ATTEMPT":
    session.suspicious_activities.append({
      type: "COPY_PASTE",
      timestamp: NOW
    })
    SEND_ALERT(session.exam.coordinator, "Copy-paste attempt by {session.student.name}")
  END IF
  
  LOG_AUDIT("EXAM_EVENT", session.student_id, "Type: {event.type}, Session: {session_id}")
END FUNCTION
```

**EXAMPLE:**
- **Question Paper Security:**
  - Ms. Sharma (Exam Coordinator) uploads Math final exam paper
  - System encrypts with AES-256
  - Paper stored encrypted until exam day
  - Only Ms. Sharma and Principal can access before exam
  - Audit log: "Question paper uploaded by sharma.priya@hogwarts.edu.in, Encrypted: YES"
- **Online Exam:**
  - Rohan logs in for Math exam at 10:00 AM
  - System captures photo → Matches with profile photo ✓
  - Question paper decrypted and watermarked with "Rohan Verma - 2024/09/0067"
  - Proctoring starts: Webcam ON, Screen recording ON
  - At 10:15 AM: Rohan switches tab → Warning: "Tab switch detected (1/5)"
  - At 10:45 AM: Face not detected for 35 seconds → Alert sent to coordinator
  - At 11:30 AM: Exam auto-submits (90 minutes completed)
  - Audit log: "Exam completed by rohan.verma@hogwarts.edu.in, Tab switches: 1, Face detection issues: 1"

---

### 6. TO PARENT ENGAGEMENT MODULE

**WHY This Connection Exists:**
Parents need secure access to their children's information without compromising privacy. A parent should only see their own children's data, not other students'. Security module enforces strict parent-child relationship verification and data access controls.

**DATA FLOW:**
- **Parent Account Linking:**
  - Parent email/mobile → Verified during admission
  - Parent-child relationship mapping
  - Multi-child access from single account
  - Spouse/guardian additional access
- **Access Permissions:**
  - View child's academic records
  - View attendance and behavior reports
  - Pay fees and view statements
  - Communicate with teachers
  - Download report cards and certificates
- **Privacy Protection:**
  - Cannot view other students' information
  - Cannot access teacher salary or HR data
  - Cannot modify academic records (read-only)
  - Cannot access other parents' contact information

**TRIGGER EVENT:**
- **Parent Registration:** Verify relationship + create account + link children
- **Child Admission:** Auto-link to existing parent account
- **Parent Portal Login:** Authenticate + display children list
- **Data Access Request:** Verify parent-child relationship + log access
- **Communication with Teacher:** Verify relationship + route message

**IMPACT:**
- **Secure Multi-Child Access:**
  - Single parent account for all children
  - Switch between children without re-login
  - Separate data views for each child
- **Privacy Compliance:**
  - Parent A cannot see Parent B's children
  - Complete audit trail of data access
  - GDPR-compliant data handling
- **Communication Security:**
  - Messages between parent and teacher encrypted
  - Cannot send messages to other students' parents
  - Spam/abuse prevention

**BUSINESS LOGIC:**
```
FUNCTION create_parent_account(parent_info, student_id):
  // Check if parent account already exists
  existing_user = GET_USER_BY_EMAIL(parent_info.email)
  
  IF existing_user != NULL:
    // Link new child to existing account
    relationship = CREATE_PARENT_CHILD_RELATIONSHIP
    relationship.parent_id = existing_user.id
    relationship.student_id = student_id
    relationship.relationship_type = parent_info.relationship  // FATHER, MOTHER, GUARDIAN
    relationship.is_primary = parent_info.is_primary
    
    LOG_AUDIT("CHILD_LINKED", existing_user.id, "Student: {student_id}")
    
    SEND_EMAIL(parent_info.email, "New Child Linked", {
      student_name: GET_STUDENT(student_id).name,
      student_id: student_id
    })
    
    RETURN existing_user
  ELSE:
    // Create new parent account
    username = parent_info.email
    temp_password = GENERATE_RANDOM(12 chars)
    
    user = CREATE_USER
    user.username = username
    user.password_hash = BCRYPT(temp_password)
    user.email = parent_info.email
    user.mobile = parent_info.mobile
    user.name = parent_info.name
    user.status = "ACTIVE"
    user.mfa_enabled = FALSE  // Optional for parents
    
    ASSIGN_ROLE(user, "PARENT")
    
    // Link child
    relationship = CREATE_PARENT_CHILD_RELATIONSHIP
    relationship.parent_id = user.id
    relationship.student_id = student_id
    relationship.relationship_type = parent_info.relationship
    relationship.is_primary = TRUE
    
    SEND_EMAIL(parent_info.email, "Parent Portal Access", {
      username: username,
      temp_password: temp_password,
      portal_url: "https://hogwarts.edu.in/parent",
      student_name: GET_STUDENT(student_id).name
    })
    
    LOG_AUDIT("PARENT_ACCOUNT_CREATED", user.id, "Student: {student_id}")
    
    RETURN user
  END IF
END FUNCTION

FUNCTION access_student_data(parent_user, student_id, data_type):
  // Verify parent-child relationship
  relationship = GET_RELATIONSHIP(parent_user.id, student_id)
  
  IF relationship == NULL:
    LOG_AUDIT("ACCESS_DENIED", parent_user.id, "Not parent of student {student_id}")
    RETURN {success: FALSE, error: "Unauthorized"}
  END IF
  
  // Check data type permissions
  allowed_data_types = [
    "PROFILE",
    "ATTENDANCE",
    "GRADES",
    "BEHAVIOR",
    "FEE_STATEMENTS",
    "REPORT_CARDS",
    "TIMETABLE",
    "ASSIGNMENTS",
    "TEACHER_REMARKS"
  ]
  
  IF data_type NOT IN allowed_data_types:
    LOG_AUDIT("ACCESS_DENIED", parent_user.id, "Invalid data type: {data_type}")
    RETURN {success: FALSE, error: "Invalid request"}
  END IF
  
  // Fetch data
  data = GET_STUDENT_DATA(student_id, data_type)
  
  // Redact sensitive information
  IF data_type == "PROFILE":
    // Remove internal notes, counseling records
    data = REDACT_FIELDS(data, ["internal_notes", "counseling_records", "disciplinary_details"])
  END IF
  
  LOG_AUDIT("DATA_ACCESSED", parent_user.id, "Student: {student_id}, Type: {data_type}")
  
  RETURN {success: TRUE, data: data}
END FUNCTION

FUNCTION send_message_to_teacher(parent_user, student_id, teacher_id, message):
  // Verify parent-child relationship
  relationship = GET_RELATIONSHIP(parent_user.id, student_id)
  IF relationship == NULL:
    RETURN {success: FALSE, error: "Unauthorized"}
  END IF
  
  // Verify teacher is assigned to this student
  teacher_assignment = GET_TEACHER_ASSIGNMENT(teacher_id, student_id)
  IF teacher_assignment == NULL:
    LOG_AUDIT("MESSAGE_DENIED", parent_user.id, "Teacher not assigned to student")
    RETURN {success: FALSE, error: "Teacher not assigned to this student"}
  END IF
  
  // Check for spam/abuse
  recent_messages = COUNT(messages WHERE sender = parent_user.id AND created_at > NOW - 1 HOUR)
  IF recent_messages > 10:
    LOG_AUDIT("MESSAGE_DENIED", parent_user.id, "Spam detected")
    RETURN {success: FALSE, error: "Too many messages. Please wait."}
  END IF
  
  // Encrypt message
  encrypted_message = AES_ENCRYPT(message, GET_MESSAGING_KEY())
  
  // Create message
  msg = CREATE_MESSAGE
  msg.sender_id = parent_user.id
  msg.recipient_id = teacher_id
  msg.student_id = student_id
  msg.message = encrypted_message
  msg.sent_at = NOW
  msg.read = FALSE
  
  // Send notification
  teacher = GET_USER(teacher_id)
  SEND_NOTIFICATION(teacher, "New message from {parent_user.name} regarding {student.name}")
  
  LOG_AUDIT("MESSAGE_SENT", parent_user.id, "To: {teacher_id}, Student: {student_id}")
  
  RETURN {success: TRUE, message_id: msg.id}
END FUNCTION
```

**EXAMPLE:**
- **Mr. Sharma (Parent of 2 children):**
  - Username: sharma.rajesh@gmail.com
  - Children: Aarav (Grade 9), Ananya (Grade 6)
  - Logs in → Dashboard shows both children
  - Clicks "Aarav" → Views:
    - ✅ Attendance: 95% (present 171/180 days)
    - ✅ Grades: Math 85%, Science 78%, English 92%
    - ✅ Fee Status: ₹45,000 outstanding
    - ✅ Upcoming: Math test on Jan 20
  - Clicks "Ananya" → Views her separate data
  - Cannot see other students in Grade 9 or Grade 6
  - Audit log: "Data accessed by sharma.rajesh@gmail.com - Student: Aarav (2024/09/0045), Type: GRADES"
- **Communication:**
  - Mr. Sharma sends message to Aarav's Math teacher
  - Message: "Aarav is struggling with Algebra. Can we schedule a meeting?"
  - System verifies: Mr. Sharma is Aarav's parent ✓, Ms. Priya teaches Aarav Math ✓
  - Message encrypted and sent
  - Ms. Priya receives notification
  - Audit log: "Message sent by sharma.rajesh@gmail.com to priya.sharma@hogwarts.edu.in regarding student 2024/09/0045"

---

## INBOUND CONNECTIONS (Other Modules → Security & Access Control)

### 1. FROM ALL MODULES - AUTHENTICATION REQUESTS

**WHY This Connection Exists:**
Every module in the ERP system requires user authentication before allowing access. No module can function without verifying user identity and permissions.

**DATA FLOW:**
- **Authentication Request:**
  - Username/email
  - Password (hashed)
  - Optional: OTP for MFA
  - IP address and user agent
- **Authentication Response:**
  - Success/failure status
  - User ID and role
  - JWT token (if successful)
  - Session expiry time
  - Permissions list

**TRIGGER EVENT:**
- User attempts to access any module
- Session expires and needs renewal
- Sensitive operation requires re-authentication

**IMPACT:**
- Centralized authentication for all 54 modules
- Consistent security policies across entire ERP
- Single point of audit for all access attempts

---

### 2. FROM ALL MODULES - AUTHORIZATION CHECKS

**WHY This Connection Exists:**
Before performing any action, modules must verify if the user has permission. Security module is the single source of truth for all permissions.

**DATA FLOW:**
- **Authorization Request:**
  - User ID
  - Action being attempted (e.g., "ENTER_GRADES", "PROCESS_REFUND")
  - Resource being accessed (e.g., specific student, specific payment)
- **Authorization Response:**
  - Allowed/denied
  - Reason (if denied)
  - Additional constraints (e.g., "allowed but requires approval")

**TRIGGER EVENT:**
- User attempts any create/update/delete operation
- User tries to access data
- Batch operations requiring bulk permissions

**IMPACT:**
- Fine-grained access control
- Prevents unauthorized data access
- Enables role-based workflows

---

### 3. FROM AUDIT & COMPLIANCE MODULE - AUDIT LOG QUERIES

**WHY This Connection Exists:**
Security audit logs are the foundation for compliance reporting, security investigations, and forensic analysis.

**DATA FLOW:**
- **Audit Query Request:**
  - Date range
  - User ID (optional)
  - Action type (optional)
  - Module (optional)
- **Audit Response:**
  - List of audit events
  - User details
  - Timestamps
  - IP addresses
  - Action outcomes

**TRIGGER EVENT:**
- Compliance audit
- Security investigation
- User activity report generation
- Suspicious activity detection

**IMPACT:**
- Complete traceability of all actions
- Compliance with regulations (GDPR, ISO 27001)
- Forensic investigation capability

---

## SUMMARY

**Total Connections:** 53 modules depend on Security & Access Control

**Critical Dependencies:**
- ALL modules require authentication (53 outbound connections)
- ALL modules require authorization (53 outbound connections)
- ALL modules send audit logs (53 inbound connections)

**Data Flow Metrics:**
- **Authentication Requests:** ~10,000-50,000 per day (depending on school size)
- **Authorization Checks:** ~100,000-500,000 per day
- **Audit Log Entries:** ~50,000-200,000 per day
- **MFA Verifications:** ~2,000-10,000 per day

**Integration Complexity:** CRITICAL
- Security module is the foundation of entire ERP
- Single point of failure if not highly available
- Requires 99.99% uptime SLA
- Must handle peak loads (exam days, fee payment deadlines)

**Best Practices:**
1. **Defense in Depth:** Multiple layers of security (network, application, data)
2. **Least Privilege:** Users get minimum permissions needed
3. **Audit Everything:** Complete logging of all security events
4. **Regular Reviews:** Quarterly access reviews, annual penetration testing
5. **Incident Response:** Documented procedures for security breaches
6. **Encryption:** Data encrypted at rest and in transit
7. **MFA Enforcement:** Mandatory for staff, optional but encouraged for students/parents
8. **Session Management:** Automatic timeout, concurrent session limits
9. **Password Policies:** Complexity requirements, rotation, breach detection
10. **Continuous Monitoring:** Real-time alerts for suspicious activities

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 16, 2026  
**Version:** 1.0  
**Compliance:** GDPR, ISO 27001, PCI-DSS (for payment data)

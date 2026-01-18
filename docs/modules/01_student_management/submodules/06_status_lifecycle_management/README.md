# STATUS & LIFECYCLE MANAGEMENT - COMPLETE DOCUMENTATION

**Module:** Student Management  
**Submodule Code:** STD-STATUS-006  
**Category:** Core Foundation  
**Priority:** High (P1)  
**Owner:** Student Records Department

---

## 📋 OVERVIEW

The Status & Lifecycle Management submodule tracks the complete journey of a student from admission to alumni status. It manages status transitions (Active, Suspended, Withdrawn, Transferred, Alumni), maintains historical records, handles re-admissions, and ensures proper closure of student accounts with all necessary clearances.

### Purpose

Track and manage student lifecycle stages, handle status changes with proper approvals, maintain comprehensive audit trails, manage clearance processes, and ensure smooth transitions between different student statuses.

### Scope

- **Status Tracking**: Active, Suspended, Withdrawn, Transferred, Alumni
- **Lifecycle Stages**: Admission → Active → Promotion → Graduation → Alumni
- **Status Changes**: Suspension, withdrawal, transfer, re-admission
- **Clearance Process**: Fee, library, hostel, transport clearances
- **Historical Records**: Complete status change history

---

## 🎯 KEY FEATURES

### 1. Student Status Types

#### 1.1 Active Status

**Definition:**
```yaml
Status: ACTIVE
Description: Currently enrolled and attending classes
Applicable: All enrolled students
Rights:
  - Attend classes
  - Access school facilities
  - Participate in exams
  - Use library, canteen, transport
  - Participate in extracurricular activities

Conditions:
  - Fee payment up to date (or approved installment plan)
  - No pending disciplinary actions
  - Regular attendance (>75%)
  - All documents submitted

Example:
  Student: Aarav Patel (2024/06/0456)
  Current Status: ACTIVE
  Since: 01/04/2024
  Grade: 6-A
  Fee Status: Paid (Term 1)
  Attendance: 92%
  Disciplinary Record: Clean
```

#### 1.2 Suspended Status

**Definition:**
```yaml
Status: SUSPENDED
Description: Temporarily barred from attending classes
Duration: Typically 3-30 days
Reason: Disciplinary action or fee default

Types of Suspension:

1. Disciplinary Suspension:
   Reasons:
     - Serious misconduct
     - Repeated violations
     - Violence/bullying
     - Substance abuse
   Duration: 3-15 days
   Approval: Discipline Committee + Principal
   
2. Fee Default Suspension:
   Reason: Non-payment of fees
   Duration: Until fee payment
   Approval: Accounts Department
   
3. Academic Suspension:
   Reason: Severe academic deficiency
   Duration: One term/semester
   Approval: Academic Committee

Restrictions During Suspension:
  - Cannot attend classes
  - Cannot enter school premises
  - Cannot participate in exams (disciplinary)
  - Cannot use school facilities
  - Must complete assigned tasks (if applicable)

Example:
  Student: Rohan Kumar (2024/06/0457)
  Status: SUSPENDED (Disciplinary)
  Reason: Fighting with classmate
  Duration: 7 days (15/07/2024 - 21/07/2024)
  Approved By: Principal
  Conditions for Reinstatement:
    - Written apology
    - Parent meeting
    - Behavioral counseling (3 sessions)
  Reinstatement Date: 22/07/2024
```

#### 1.3 Withdrawn Status

**Definition:**
```yaml
Status: WITHDRAWN
Description: Student voluntarily left the school
Permanent: Yes (unless re-admission)

Reasons for Withdrawal:
  - Family relocation
  - Financial difficulties
  - Switching to another school
  - Health reasons
  - Personal/family issues
  - Dissatisfaction with school

Withdrawal Process:
  Step 1: Parent submits withdrawal request
  Step 2: Principal approval
  Step 3: Clearance from all departments
  Step 4: Fee settlement
  Step 5: Issue Transfer Certificate (TC)
  Step 6: Update status to WITHDRAWN

Example:
  Student: Priya Sharma (2024/06/0458)
  Status: WITHDRAWN
  Withdrawal Date: 30/06/2024
  Reason: Family relocating to Mumbai
  Last Attended: 28/06/2024
  TC Issued: 02/07/2024
  TC Number: TC/2024/0458
  Fee Settlement: Cleared
  Clearances: All obtained
```

#### 1.4 Transferred Status

**Definition:**
```yaml
Status: TRANSFERRED
Description: Moved to another branch/campus of same school

Types:
  1. Inter-Campus Transfer:
     - Within same city (different branches)
     - Student record migrated
     - Continuous enrollment
     
  2. Inter-Board Transfer:
     - CBSE to ICSE or vice versa
     - Curriculum change
     - Grade adjustment if needed

Transfer Process:
  Step 1: Transfer request
  Step 2: Seat availability check (target campus)
  Step 3: Academic record transfer
  Step 4: Fee adjustment
  Step 5: Update status to TRANSFERRED
  Step 6: Activate in target campus

Example:
  Student: Arjun Verma (2024/06/0459)
  Status: TRANSFERRED
  From: Hogwarts School, Noida Branch
  To: Hogwarts School, Greater Noida Branch
  Transfer Date: 15/08/2024
  Reason: Family moved to Greater Noida
  Grade: 6-B (continues in same grade)
  Admission Number: Retained (2024/06/0459)
  Fee: Adjusted (pro-rata)
```

#### 1.5 Alumni Status

**Definition:**
```yaml
Status: ALUMNI
Description: Successfully completed Grade 12 and graduated
Permanent: Yes

Graduation Process:
  Step 1: Complete Grade 12 board exams
  Step 2: Results declared
  Step 3: Graduation ceremony
  Step 4: Issue final certificates
  Step 5: Update status to ALUMNI
  Step 6: Add to alumni database

Alumni Benefits:
  - Alumni ID card
  - Access to alumni portal
  - Invitation to school events
  - Networking opportunities
  - Career guidance
  - Discounts on school facilities

Example:
  Student: Diya Patel (2018/08/0123)
  Status: ALUMNI
  Graduation Year: 2024
  Final Grade: 12-A
  Board: CBSE
  Percentage: 94.5%
  Stream: Science (PCM)
  Graduation Date: 31/03/2024
  Alumni ID: ALM-2024-0123
  Current: B.Tech (IIT Delhi)
```

---

### 2. Lifecycle Stages

#### 2.1 Complete Student Lifecycle

**Lifecycle Flow:**
```yaml
Stage 1: PROSPECTIVE
  - Enquiry received
  - Application submitted
  - Entrance test scheduled
  Duration: 1-2 months

Stage 2: APPLICANT
  - Entrance test completed
  - Interview done
  - Awaiting admission decision
  Duration: 1-2 weeks

Stage 3: ADMITTED
  - Admission offered
  - Admission fee paid
  - Registration pending
  Duration: 1-2 weeks

Stage 4: REGISTERED
  - Registration completed
  - Documents submitted
  - Section allocated
  Duration: 1-2 days

Stage 5: ACTIVE
  - Attending classes
  - Regular student
  Duration: 1 academic year

Stage 6: PROMOTED
  - Year-end promotion
  - Moved to next grade
  - Section re-allocation
  Duration: Annual

Stage 7: GRADUATED
  - Completed Grade 12
  - Board exams passed
  - Certificates issued
  Duration: End of Grade 12

Stage 8: ALUMNI
  - Left school
  - Alumni database
  - Lifetime status
  Duration: Permanent

Alternate Paths:
  - WITHDRAWN: Left before completion
  - TRANSFERRED: Moved to another school/branch
  - SUSPENDED: Temporary removal
  - EXPELLED: Permanent removal (rare)
```

---

### 3. Status Change Management

#### 3.1 Suspension Process

**Disciplinary Suspension Workflow:**
```yaml
Incident: Student involved in fight
Student: Rohan Kumar (2024/06/0457)
Date: 15/07/2024

Step 1: Incident Report
  - Reported by: Class Teacher
  - Witnesses: 3 students
  - Evidence: CCTV footage
  - Initial action: Sent to Principal's office

Step 2: Investigation
  - Conducted by: Discipline Committee
  - Student statement recorded
  - Parent called
  - Severity assessed: MODERATE

Step 3: Disciplinary Committee Meeting
  - Members: Principal, Vice Principal, Counselor
  - Decision: 7-day suspension
  - Conditions: Apology, counseling, parent meeting

Step 4: Suspension Order
  - Issued by: Principal
  - Duration: 15/07/2024 - 21/07/2024
  - Communicated to: Parents, Teachers, Student

Step 5: Status Update
  - Current Status: ACTIVE
  - New Status: SUSPENDED
  - Effective: 15/07/2024
  - Auto-Revert: 22/07/2024 (if conditions met)

Step 6: Monitoring
  - Counseling sessions: 3 (scheduled)
  - Parent meeting: 18/07/2024 (completed)
  - Apology letter: Submitted

Step 7: Reinstatement
  - Date: 22/07/2024
  - Conditions: All met ✓
  - Status: SUSPENDED → ACTIVE
  - Probation: 3 months
```

#### 3.2 Withdrawal Process

**Voluntary Withdrawal Workflow:**
```yaml
Student: Priya Sharma (2024/06/0458)
Request Date: 15/06/2024
Reason: Family relocating to Mumbai

Step 1: Withdrawal Request
  - Submitted by: Parent (Mr. Sharma)
  - Reason: Family relocation
  - Requested Date: 30/06/2024
  - Notice Period: 15 days ✓

Step 2: Principal Approval
  - Meeting with parent: 18/06/2024
  - Reason verified: Relocation confirmed
  - Approval: GRANTED
  - Effective Date: 30/06/2024

Step 3: Clearance Process
  Fee Department:
    - Pending Fees: ₹0 (Cleared)
    - Refund Due: ₹5,000 (Advance for Term 2)
    - Clearance: GRANTED ✓
    
  Library:
    - Books Issued: 2
    - Books Returned: 2 ✓
    - Fines: ₹0
    - Clearance: GRANTED ✓
    
  Transport:
    - Bus Pass: Surrendered ✓
    - Pending Dues: ₹0
    - Clearance: GRANTED ✓
    
  Hostel:
    - Not Applicable (Day Scholar)
    - Clearance: N/A
    
  Sports:
    - Equipment Issued: Cricket kit
    - Equipment Returned: ✓
    - Clearance: GRANTED ✓

Step 4: Fee Settlement
  - Refund Amount: ₹5,000
  - Refund Mode: Bank transfer
  - Refund Date: 05/07/2024
  - Receipt: REF/2024/0458

Step 5: Transfer Certificate
  - TC Number: TC/2024/0458
  - Issue Date: 02/07/2024
  - Issued To: Parent
  - Character: GOOD
  - Conduct: SATISFACTORY
  - Last Attended: 28/06/2024

Step 6: Status Update
  - Current Status: ACTIVE
  - New Status: WITHDRAWN
  - Effective Date: 30/06/2024
  - Last Day: 28/06/2024
  - Reason: Family relocation

Step 7: Final Actions
  - Deactivate portal access
  - Remove from WhatsApp groups
  - Update attendance register
  - Archive student records
  - Send farewell email
```

---

### 4. Clearance Management

#### 4.1 Multi-Department Clearance

**Clearance Checklist:**
```yaml
Student: Priya Sharma (Withdrawal)
Clearance Status: IN PROGRESS

1. Fee Department:
   Status: PENDING
   Pending Amount: ₹0
   Refund Due: ₹5,000
   Action: Process refund
   Cleared By: Accounts Officer
   Date: 25/06/2024
   
2. Library:
   Status: PENDING
   Books Issued: 2
   Books to Return:
     - "Mathematics Grade 6" (Due: 20/06/2024)
     - "Science Textbook" (Due: 20/06/2024)
   Fines: ₹0
   Action: Return books
   Cleared By: Librarian
   Date: 26/06/2024
   
3. Transport:
   Status: CLEARED
   Bus Pass: Surrendered
   Pending Dues: ₹0
   Cleared By: Transport Incharge
   Date: 25/06/2024
   
4. Hostel:
   Status: N/A (Day Scholar)
   
5. Sports Department:
   Status: PENDING
   Equipment Issued: Cricket kit
   Action: Return equipment
   Cleared By: Sports Teacher
   Date: 27/06/2024
   
6. Laboratory:
   Status: CLEARED
   Equipment: None issued
   Cleared By: Lab Assistant
   Date: 25/06/2024

Overall Clearance: 4/5 Complete (80%)
Pending: Library (books to return)
Expected Completion: 26/06/2024
```

---

## 📊 DATABASE SCHEMA

### Student Status History Table

```sql
CREATE TABLE student_status_history (
  status_history_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Status Change
  from_status VARCHAR(50),
  to_status VARCHAR(50) NOT NULL,
  change_date DATE NOT NULL,
  effective_date DATE NOT NULL,
  
  -- Reason & Details
  change_reason TEXT NOT NULL,
  change_type ENUM('PROMOTION', 'SUSPENSION', 'WITHDRAWAL', 'TRANSFER', 'REINSTATEMENT', 'GRADUATION', 'OTHER') NOT NULL,
  
  -- Approval
  requested_by INT,
  approved_by INT,
  approval_date DATE,
  
  -- Duration (for temporary status)
  duration_days INT,
  auto_revert_date DATE,
  
  -- Conditions
  conditions_for_revert TEXT,
  conditions_met BOOLEAN DEFAULT FALSE,
  
  -- Clearance (for withdrawal/transfer)
  clearance_required BOOLEAN DEFAULT FALSE,
  clearance_status ENUM('PENDING', 'IN_PROGRESS', 'COMPLETED') DEFAULT 'PENDING',
  clearance_completed_date DATE,
  
  -- Documents
  tc_issued BOOLEAN DEFAULT FALSE,
  tc_number VARCHAR(50),
  tc_issue_date DATE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  notes TEXT,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_to_status (to_status),
  INDEX idx_change_date (change_date)
);
```

### Clearance Records Table

```sql
CREATE TABLE clearance_records (
  clearance_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  status_history_id INT NOT NULL,
  
  -- Department
  department ENUM('FEE', 'LIBRARY', 'TRANSPORT', 'HOSTEL', 'SPORTS', 'LAB', 'OTHER') NOT NULL,
  
  -- Clearance Status
  clearance_status ENUM('PENDING', 'CLEARED', 'REJECTED') DEFAULT 'PENDING',
  clearance_date DATE,
  cleared_by INT,
  
  -- Pending Items
  pending_items TEXT,
  pending_amount DECIMAL(10,2) DEFAULT 0,
  
  -- Remarks
  remarks TEXT,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  FOREIGN KEY (status_history_id) REFERENCES student_status_history(status_history_id),
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_department (department),
  INDEX idx_clearance_status (clearance_status)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Status Change Validation

```javascript
FUNCTION validate_status_change(student_id, from_status, to_status, reason) {
  // Define allowed transitions
  allowed_transitions = {
    'ACTIVE': ['SUSPENDED', 'WITHDRAWN', 'TRANSFERRED', 'PROMOTED', 'GRADUATED'],
    'SUSPENDED': ['ACTIVE', 'WITHDRAWN', 'EXPELLED'],
    'WITHDRAWN': ['RE_ADMITTED'],
    'TRANSFERRED': [],
    'ALUMNI': []
  }
  
  // Check if transition is allowed
  IF to_status NOT IN allowed_transitions[from_status] {
    RETURN {
      valid: FALSE,
      error: `Cannot change status from ${from_status} to ${to_status}`,
      allowed: allowed_transitions[from_status]
    }
  }
  
  // Validate reason
  IF NOT reason OR reason.length < 20 {
    RETURN {
      valid: FALSE,
      error: "Detailed reason required (minimum 20 characters)"
    }
  }
  
  // Additional validations based on target status
  IF to_status == 'WITHDRAWN' {
    // Check for pending fees
    pending_fees = GET_PENDING_FEES(student_id)
    IF pending_fees > 0 {
      RETURN {
        valid: FALSE,
        error: "Cannot withdraw with pending fees",
        pending_amount: pending_fees
      }
    }
  }
  
  IF to_status == 'SUSPENDED' {
    // Check if already suspended
    current_status = GET_CURRENT_STATUS(student_id)
    IF current_status == 'SUSPENDED' {
      RETURN {
        valid: FALSE,
        error: "Student already suspended"
      }
    }
  }
  
  RETURN {valid: TRUE}
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Fee Management**: Clearance for fee dues
2. **Library Module**: Book return clearance
3. **Transport Module**: Bus pass surrender
4. **Alumni Module**: Graduate to alumni conversion

### Inbound Integrations

1. **Discipline Module**: Suspension triggers
2. **Academic Module**: Promotion/graduation triggers
3. **Admissions Module**: Re-admission processing

---

## 👥 USER WORKFLOWS

### Workflow: Student Withdrawal Process

**Actor:** Admin Officer  
**Duration:** 3-5 days  
**Trigger:** Parent withdrawal request

**Steps:**

1. Receive withdrawal request from parent
2. Schedule meeting with Principal
3. Verify reason and approve
4. Initiate clearance process
5. Monitor clearance from all departments
6. Process fee refund (if applicable)
7. Issue Transfer Certificate
8. Update status to WITHDRAWN
9. Deactivate student accounts
10. Archive records

**Expected Outcome:** Student successfully withdrawn with all clearances

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 17, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

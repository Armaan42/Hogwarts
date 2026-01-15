# STUDENT REGISTRATION & ADMISSION

**Module:** Student Management  
**Submodule Code:** STD-REG-001  
**Category:** Core Foundation  
**Priority:** Critical (P0)

## Overview

This submodule handles the conversion of prospective students into enrolled students, managing the complete registration and admission process including profile creation, unique identifier assignment, family information capture, biometric enrollment, and emergency contact setup.

## Purpose

Convert admission-confirmed students into active enrolled students with complete profiles, assign unique identifiers, link to family records, capture all mandatory documents, and set up emergency protocols.

## Key Features

### 1.1 Student Profile Creation

**Basic Information:**
- Full Name (First, Middle, Last)
- Date of Birth (DD/MM/YYYY)
- Gender (Male/Female/Other)
- Blood Group (A+, A-, B+, B-, O+, O-, AB+, AB-)
- Nationality, Religion, Caste Category (General/OBC/SC/ST)
- Mother Tongue, Languages Known
- Aadhaar Number (12-digit unique ID)
- Previous School Name & Board

**Example:**
```
Student Name: Rohan Sharma
DOB: 15/08/2012 (Age: 11 years)
Gender: Male
Blood Group: B+
Nationality: Indian
Religion: Hindu
Category: General
Mother Tongue: Hindi
Aadhaar: 1234 5678 9012
Previous School: Delhi Public School, Class 5
```

### 1.2 Unique Identifier Assignment

**Admission Number Generation:**
- Format: `YEAR/GRADE/SEQUENCE`
- Example: `2024/06/0456`
  - 2024: Academic year of admission
  - 06: Grade at admission
  - 0456: Sequential number (4 digits)

**Student ID Card:**
- Barcode/QR code with admission number
- Photo, Name, Grade, Section
- Emergency contact number
- Valid from - Valid till dates

### 1.3 Family Information

**Parent/Guardian Details:**

*Father:*
- Name: Mr. Rajesh Sharma
- Occupation: Software Engineer
- Company: TCS
- Annual Income: ₹15,00,000
- Mobile: +91-9876543210
- Email: rajesh.sharma@email.com
- Aadhaar: 9876 5432 1098

*Mother:*
- Name: Mrs. Priya Sharma
- Occupation: Teacher
- Company: ABC School
- Annual Income: ₹6,00,000
- Mobile: +91-9876543211
- Email: priya.sharma@email.com

*Guardian (if applicable):*
- Relationship to student
- Complete contact details

**Sibling Information:**
- Existing siblings in school: Yes/No
- If yes, Admission numbers of siblings
- Automatic sibling discount calculation

### 1.4 Admission Test Records

**Entrance Test Results:**
- Test Date: 10/01/2024
- English: 85/100
- Mathematics: 92/100
- General Knowledge: 78/100
- Total: 255/300 (85%)
- Result: PASS (Cutoff: 60%)

**Interview Assessment:**
- Interview Date: 15/01/2024
- Interviewer: Mrs. Gupta (Principal)
- Communication Skills: Excellent
- Confidence Level: High
- Overall Rating: 9/10
- Recommendation: Admit

### 1.5 Biometric Enrollment

**Fingerprint Registration:**
- Left Thumb, Right Thumb
- Used for: Attendance marking, Library access, Exam hall entry
- Enrollment Date: 01/04/2024

**Photo Capture:**
- Passport size photo (digital)
- Used for: ID card, Student portal, Attendance system
- Multiple copies stored

### 1.6 Emergency Evacuation Plan

**Emergency Contacts (Priority Order):**
1. Father: +91-9876543210
2. Mother: +91-9876543211
3. Grandfather: +91-9876543212

**Medical Emergency Protocol:**
- Preferred Hospital: Max Hospital, Saket
- Health Insurance: Star Health, Policy: 123456789
- Known Allergies: Peanuts, Dust
- Chronic Conditions: Asthma (mild)
- Emergency Medication: Inhaler (kept in school clinic)

**Pickup Authorization:**
- Authorized persons to pick up student:
  1. Father: Rajesh Sharma
  2. Mother: Priya Sharma
  3. Uncle: Amit Sharma (with prior notification)
- Photo ID verification mandatory

## Data Fields

### Student Master Table
```
student_id (PK)
admission_number (UNIQUE)
first_name
middle_name
last_name
date_of_birth
gender
blood_group
nationality
religion
caste_category
mother_tongue
aadhaar_number
previous_school
admission_date
academic_year
grade_at_admission
current_grade
current_section
student_status (ACTIVE/WITHDRAWN/SUSPENDED/ALUMNI)
photo_url
biometric_enrolled (boolean)
created_date
modified_date
```

### Family Information Table
```
family_id (PK)
father_name
father_occupation
father_company
father_income
father_mobile
father_email
father_aadhaar
mother_name
mother_occupation
mother_company
mother_income
mother_mobile
mother_email
mother_aadhaar
guardian_name
guardian_relationship
guardian_mobile
residential_address
permanent_address
```

### Emergency Contact Table
```
emergency_contact_id (PK)
student_id (FK)
contact_name
relationship
mobile_number
priority_order
is_authorized_for_pickup (boolean)
photo_id_type
photo_id_number
```

### Admission Test Table
```
test_id (PK)
student_id (FK)
test_date
english_marks
mathematics_marks
gk_marks
total_marks
percentage
result (PASS/FAIL)
cutoff_percentage
interview_date
interviewer_name
interview_rating
recommendation
```

## Business Rules

### Rule 1: Admission Number Generation
```
FUNCTION generate_admission_number(student, academic_year, grade):
  // Format: YEAR/GRADE/SEQUENCE
  year = academic_year  // e.g., 2024
  grade_code = ZERO_PAD(grade, 2)  // e.g., 06 for Grade 6
  
  // Get last sequence number for this year and grade
  last_sequence = GET max_sequence(academic_year, grade)
  new_sequence = last_sequence + 1
  sequence_code = ZERO_PAD(new_sequence, 4)  // e.g., 0456
  
  admission_number = year + "/" + grade_code + "/" + sequence_code
  // Result: 2024/06/0456
  
  RETURN admission_number
END FUNCTION
```

### Rule 2: Sibling Discount Auto-Application
```
FUNCTION check_sibling_discount(student):
  // Get family ID from parent contact
  family_id = GET family_id(student.parent_mobile OR student.parent_email)
  
  // Count existing active siblings
  siblings = GET students WHERE family_id = family_id AND status = "ACTIVE"
  sibling_count = COUNT(siblings)
  
  IF sibling_count = 0:
    // First child, no discount
    student.sibling_discount = 0
  ELSE IF sibling_count = 1:
    // Second child, 10% discount
    student.sibling_discount = 10
  ELSE IF sibling_count = 2:
    // Third child, 15% discount
    student.sibling_discount = 15
  ELSE:
    // Fourth+ child, 20% discount
    student.sibling_discount = 20
  END IF
  
  // Link student to family
  student.family_id = family_id
  
  // Notify fee module to apply discount
  TRIGGER fee_assignment(student)
  
  RETURN student
END FUNCTION
```

### Rule 3: Mandatory Document Verification
```
FUNCTION verify_mandatory_documents(student):
  mandatory_docs = [
    "Birth Certificate",
    "Previous School TC",
    "Aadhaar Card",
    "Parent ID Proof",
    "Address Proof",
    "Passport Size Photos (4)",
    "Caste Certificate (if applicable)"
  ]
  
  missing_docs = []
  
  FOR each doc IN mandatory_docs:
    IF NOT EXISTS document(student, doc):
      missing_docs.ADD(doc)
    END IF
  END FOR
  
  IF missing_docs.LENGTH > 0:
    student.registration_status = "INCOMPLETE"
    SEND_NOTIFICATION(student.parent_email, "Missing documents: " + missing_docs)
    RETURN FALSE
  ELSE:
    student.registration_status = "COMPLETE"
    student.status = "ACTIVE"
    RETURN TRUE
  END IF
END FUNCTION
```

## Integration Points

### Connects TO:
1. **Admissions & CRM Module**
   - Receives: Admission confirmation, entrance test results
   - Purpose: Convert admitted student to enrolled student

2. **Fee Management Module**
   - Sends: Student profile, grade, sibling count
   - Purpose: Trigger fee assignment with applicable discounts

3. **Document Management Module**
   - Sends: Student ID for document linking
   - Purpose: Store and verify mandatory documents

4. **Biometric System**
   - Sends: Student details for enrollment
   - Purpose: Register fingerprints for attendance/access

5. **Communication Module**
   - Sends: Parent contact details
   - Purpose: Send welcome email, SMS with admission number

6. **Transport Management Module**
   - Sends: Student address, pickup location
   - Purpose: Assign bus route if transport opted

7. **Hostel Management Module**
   - Sends: Student details, boarding preference
   - Purpose: Allocate room if hostel opted

### Receives FROM:
1. **Admissions Module**
   - Data: Admission confirmation, test scores, interview results
   - Trigger: Start registration process

2. **Parent Portal**
   - Data: Family information, emergency contacts
   - Trigger: Parent completes online registration form

## User Workflows

### Workflow 1: New Student Registration (Complete Process)

**Actor:** Admissions Officer

**Steps:**
1. Student admission confirmed (passed entrance test + interview)
2. Officer logs into Student Management > Registration
3. Click "New Student Registration"
4. **Enter Basic Details:**
   - Name: Rohan Sharma
   - DOB: 15/08/2012
   - Gender: Male
   - Blood Group: B+
   - Grade: 6
5. **System auto-generates** Admission Number: `2024/06/0456`
6. **Enter Family Details:**
   - Father: Rajesh Sharma, Mobile: 9876543210
   - Mother: Priya Sharma, Mobile: 9876543211
7. **System checks** for existing siblings using parent mobile
8. **System finds** 1 sibling (Admission No: 2018/08/0123)
9. **System auto-applies** 10% sibling discount
10. **Enter Emergency Contacts:**
    - Priority 1: Father
    - Priority 2: Mother
    - Priority 3: Grandfather
11. **Upload Documents:**
    - Birth Certificate ✓
    - Previous School TC ✓
    - Aadhaar Card ✓
    - Photos ✓
12. **Biometric Enrollment:**
    - Capture fingerprints (both thumbs)
    - Capture photo
13. **System validates** all mandatory fields
14. **System triggers:**
    - Fee assignment (with 10% sibling discount)
    - ID card generation
    - Welcome email to parents
    - SMS with admission number
15. **Registration Status:** COMPLETE
16. **Student Status:** ACTIVE

**Expected Outcome:** Student successfully registered, admission number assigned, fee calculated with sibling discount, parents notified.

### Workflow 2: Sibling Discount Auto-Application

**Actor:** System (Automated)

**Steps:**
1. **Trigger:** New student registration (Rohan Sharma)
2. **System extracts** parent mobile: 9876543210
3. **System searches** existing students with same parent mobile
4. **System finds** 1 existing student: Priya Sharma (sister, Grade 8)
5. **System determines:** Rohan is 2nd child
6. **System applies:** 10% sibling discount
7. **System links:** Both students to same family_id
8. **System notifies:** Fee module to apply discount
9. **System logs:** "Sibling discount applied: 10% for 2nd child"
10. **System displays** on registration screen: "Sibling discount: 10% (1 sibling found)"

**Expected Outcome:** Sibling discount automatically detected and applied without manual intervention.

### Workflow 3: Incomplete Registration Follow-up

**Actor:** Admissions Officer

**Steps:**
1. Student registration started but documents missing
2. **System marks** registration_status: "INCOMPLETE"
3. **System sends** automated email to parents:
   - "Dear Mr. Sharma, Registration incomplete. Missing: Caste Certificate, Address Proof"
4. **System creates** task for admissions officer
5. Officer calls parent: "Please submit missing documents by 10/04/2024"
6. Parent submits documents on 08/04/2024
7. Officer uploads documents to student profile
8. **System validates** all documents present
9. **System updates** registration_status: "COMPLETE"
10. **System activates** student status: "ACTIVE"
11. **System triggers** fee assignment and ID card generation

**Expected Outcome:** Incomplete registrations tracked and followed up until completion.

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026  
**Documentation Standard:** Production-Ready

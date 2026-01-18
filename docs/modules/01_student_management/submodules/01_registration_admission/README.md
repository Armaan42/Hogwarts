# STUDENT REGISTRATION & ADMISSION - COMPLETE DOCUMENTATION

**Module:** Student Management  
**Submodule Code:** STD-REG-001  
**Category:** Core Foundation  
**Priority:** Critical (P0)  
**Owner:** Admissions Department

---

## 📋 OVERVIEW

The Student Registration & Admission submodule manages the complete lifecycle of converting prospective students into enrolled students. This includes profile creation, unique identifier assignment, family information capture, document verification, biometric enrollment, and emergency contact setup.

### Purpose

Transform admission-confirmed students into active enrolled students with complete digital profiles, assign unique identifiers, establish family linkages, verify mandatory documents, enroll biometric data, and configure emergency protocols for safe school operations.

### Scope

- **Pre-Registration**: Admission confirmation validation
- **Registration**: Student profile creation and data capture
- **Verification**: Document and information validation
- **Enrollment**: Biometric and system activation
- **Post-Registration**: Fee assignment, ID card generation, welcome communications

---

## 🎯 KEY FEATURES

### 1. Student Profile Creation

#### 1.1 Basic Information Capture

**Personal Details:**
- **Full Name**: First Name, Middle Name, Last Name (as per birth certificate)
- **Date of Birth**: DD/MM/YYYY format with age auto-calculation
- **Gender**: Male / Female / Other / Prefer not to say
- **Blood Group**: A+, A-, B+, B-, O+, O-, AB+, AB-, Unknown
- **Nationality**: Indian / Foreign (specify country)
- **Religion**: Hindu / Muslim / Christian / Sikh / Buddhist / Jain / Other / Prefer not to say
- **Caste Category**: General / OBC / SC / ST / EWS (for reservation purposes)
- **Mother Tongue**: Primary language spoken at home
- **Languages Known**: Hindi, English, Regional languages
- **Place of Birth**: City, State, Country

**Government Identifiers:**
- **Aadhaar Number**: 12-digit unique identification (mandatory for Indian students)
- **Passport Number**: For international students or foreign nationals
- **PAN Card**: For students above 18 years (optional)

**Academic Background:**
- **Previous School Name**: Full name of last attended school (N/A for first-time joiners)
- **Previous School Board**: CBSE / ICSE / State Board / IB / Other / N/A (for Grade 1/KG)
- **Last Grade Completed**: Grade/Class with year (N/A for first-time joiners)
- **Previous School Address**: Complete address with city, state (N/A for first-time joiners)
- **Transfer Certificate Number**: TC number from previous school (N/A for Grade 1/KG)
- **Academic Performance**: Last year percentage/CGPA (N/A for first-time joiners)
- **Preschool/Playschool Attended**: Optional for Grade 1 students (if applicable)

**Example Profile (Transfer Student - Grade 6):**
```yaml
Student Name: Aarav Patel
Date of Birth: 15/08/2012 (Age: 11 years 5 months)
Gender: Male
Blood Group: B+
Nationality: Indian
Religion: Hindu
Category: General
Mother Tongue: Gujarati
Languages: Gujarati, Hindi, English
Aadhaar: 1234 5678 9012
Previous School: Delhi Public School, Ahmedabad
Previous Board: CBSE
Last Grade: Class 5 (2023-24, 92.5%)
TC Number: DPS/AHM/2024/0456
```

**Example Profile (First-Time Joiner - Grade 1):**
```yaml
Student Name: Ananya Sharma
Date of Birth: 10/04/2018 (Age: 5 years 9 months)
Gender: Female
Blood Group: O+
Nationality: Indian
Religion: Hindu
Category: General
Mother Tongue: Hindi
Languages: Hindi, English
Aadhaar: 5678 9012 3456
Previous School: N/A (First-time school joiner)
Previous Board: N/A
Last Grade: N/A
TC Number: N/A
Preschool Attended: Little Angels Playschool (2021-2023)
```


#### 1.2 Physical Attributes

**Health & Physical Data:**
- **Height**: In cm (e.g., 145 cm)
- **Weight**: In kg (e.g., 38 kg)
- **BMI**: Auto-calculated (Weight/Height²)
- **Vision**: Normal / Wears Glasses / Contact Lenses
- **Hearing**: Normal / Impaired (specify)
- **Physical Disabilities**: None / Specify (for special accommodation)
- **Identification Marks**: Birthmarks, scars (for identification)

#### 1.3 Contact Information

**Residential Address:**
```
House/Flat No: A-204
Building/Society: Green Valley Apartments
Street/Area: Sector 15
City: Noida
State: Uttar Pradesh
PIN Code: 201301
Landmark: Near Metro Station
```

**Permanent Address:**
- Same as residential / Different (specify)
- Used for official correspondence

**Student Contact (if applicable):**
- Mobile Number: For students Grade 9+
- Email Address: For portal access

---

### 2. Unique Identifier Assignment

#### 2.1 Admission Number Generation

**Format Structure:**
```
YEAR/GRADE/SEQUENCE
Example: 2024/06/0456

Components:
- YEAR: Academic year of admission (4 digits)
- GRADE: Grade at admission (2 digits, zero-padded)
- SEQUENCE: Sequential number within year-grade (4 digits)
```

**Generation Rules:**
- **Uniqueness**: Guaranteed unique across entire school history
- **Permanence**: Never changes, even if student changes grade/section
- **Sequential**: Increments for each new admission in same year-grade
- **Format Validation**: Regex: `^\d{4}/\d{2}/\d{4}$`

**Examples:**
```
2024/06/0001 - First student admitted to Grade 6 in 2024
2024/06/0456 - 456th student admitted to Grade 6 in 2024
2024/11/0023 - 23rd student admitted to Grade 11 in 2024
2025/01/0001 - First student admitted to Grade 1 in 2025
```

#### 2.2 Student ID Card Generation

**ID Card Components:**
- **Front Side:**
  - School Logo and Name
  - Student Photo (passport size, recent)
  - Student Name (Full)
  - Admission Number
  - Grade and Section
  - Blood Group (in red)
  - QR Code / Barcode (admission number encoded)
  - Valid From - Valid Till dates

- **Back Side:**
  - Emergency Contact Numbers (2 primary)
  - Student Address
  - School Address and Contact
  - Principal's Signature
  - "If found, please return to school" message

**Digital ID Features:**
- **QR Code**: Contains admission number, name, grade, emergency contact
- **Barcode**: For library, canteen, attendance scanning
- **RFID Chip** (optional): For automated access control
- **Validity**: Academic year-based, renewed annually

**ID Card Specifications:**
- **Size**: 85.6mm × 53.98mm (CR80 standard credit card size)
- **Material**: PVC plastic, laminated
- **Thickness**: 0.76mm (30 mil)
- **Printing**: Full color, both sides
- **Security**: Hologram sticker, UV printing (optional)

---

### 3. Family Information Management

#### 3.1 Parent/Guardian Details

**Father's Information:**
```yaml
Name: Mr. Rajesh Kumar Patel
Relationship: Father
Occupation: Software Engineer
Company/Organization: Infosys Technologies Ltd
Designation: Senior Consultant
Annual Income: ₹18,00,000
Office Address: Infosys Campus, Electronic City, Bangalore
Mobile Number: +91-9876543210 (Primary)
Alternate Mobile: +91-9876543220
Email: rajesh.patel@infosys.com
Personal Email: rajesh.patel@gmail.com
Aadhaar Number: 9876 5432 1098
PAN Number: ABCDE1234F
Highest Qualification: B.Tech (Computer Science)
```

**Mother's Information:**
```yaml
Name: Mrs. Priya Rajesh Patel
Relationship: Mother
Occupation: Teacher
Company/Organization: ABC International School
Designation: Senior Mathematics Teacher
Annual Income: ₹7,50,000
Office Address: ABC School, Sector 50, Noida
Mobile Number: +91-9876543211 (Primary)
Alternate Mobile: +91-9876543221
Email: priya.patel@abcschool.edu.in
Personal Email: priya.patel@gmail.com
Aadhaar Number: 9876 5432 1099
PAN Number: ABCDE1235F
Highest Qualification: M.Sc (Mathematics), B.Ed
```

**Guardian Information (if applicable):**
- Required when: Parents deceased, abroad, or legal guardianship transferred
- **Legal Guardian Details:**
  - Name, Relationship to student
  - Complete contact information
  - Legal guardianship document number
  - Court order reference (if applicable)
  - Reason for guardianship

**Primary Contact Designation:**
- **Primary Contact**: Father / Mother / Guardian
- **Secondary Contact**: Mother / Father / Other
- **Communication Preference**: SMS / Email / WhatsApp / Phone Call
- **Preferred Language**: English / Hindi / Regional

#### 3.2 Sibling Information

**Case 1: Existing Siblings in School**
```yaml
Sibling 1:
  Name: Diya Patel
  Admission Number: 2018/08/0123
  Current Grade: 8-A
  Relationship: Elder Sister
  
Sibling 2:
  Name: Arjun Patel
  Admission Number: 2022/03/0089
  Current Grade: 3-B
  Relationship: Younger Brother
```

**Case 2: No Siblings in School**
```yaml
Siblings in School: None
Status: First child from this family
Sibling Discount: 0% (Not applicable)
Family Record: New family_id will be created
```

**Note:** System automatically checks for siblings using parent mobile/email. If no siblings found, student is marked as first child and new family record is created.

**Sibling Benefits (When Applicable):**
- **Automatic Family Linking**: All siblings linked to same family_id
- **Sibling Discount**: Auto-calculated based on number of children
  - 1st child: 0% discount (No siblings in school)
  - 2nd child: 10% discount on tuition
  - 3rd child: 15% discount on tuition
  - 4th+ child: 20% discount on tuition
- **Unified Communication**: Single SMS/email for all siblings
- **Combined Fee Receipts**: Option for consolidated billing
- **Family Portal Access**: Parents see all children in one dashboard

#### 3.3 Family Financial Information

**Income Details:**
- **Total Family Income**: ₹25,50,000 per annum
- **Income Proof**: Salary slips, ITR, Form 16
- **Income Category**: 
  - Below ₹5L: EWS category eligible
  - ₹5L-₹10L: Middle income
  - ₹10L-₹25L: Upper middle income
  - Above ₹25L: High income

**Fee Concession Eligibility:**
- **EWS Quota**: 25% fee waiver (if family income < ₹5L)
- **Staff Ward**: 50% discount (if parent is school employee)
- **Merit Scholarship**: Based on entrance test performance
- **Sports Quota**: For state/national level players
- **Sibling Discount**: As per sibling count

---

### 4. Admission Test & Interview Records

#### 4.1 Entrance Examination Details

**Test Information:**
```yaml
Test Date: 10/01/2024
Test Center: School Campus, Hall A
Test Duration: 2 hours (10:00 AM - 12:00 PM)
Test Type: Written + Aptitude

Subjects Tested:
  English:
    Marks Obtained: 85/100
    Sections: Grammar (40), Comprehension (30), Writing (30)
    Performance: Excellent
    
  Mathematics:
    Marks Obtained: 92/100
    Sections: Arithmetic (40), Algebra (30), Geometry (30)
    Performance: Outstanding
    
  General Knowledge:
    Marks Obtained: 78/100
    Sections: Current Affairs (40), Science (30), History (30)
    Performance: Good
    
  Reasoning & Aptitude:
    Marks Obtained: 88/100
    Sections: Logical Reasoning (50), Analytical (50)
    Performance: Excellent

Total Score: 343/400 (85.75%)
Cutoff Score: 240/400 (60%)
Result: PASS ✓
Rank: 12/450 (Top 3%)
```

**Test Performance Analysis:**
- **Strengths**: Mathematics, Reasoning, English
- **Areas for Improvement**: General Knowledge (current affairs)
- **Recommendation**: Excellent candidate, strong academic potential

#### 4.2 Personal Interview Assessment

**Interview Details:**
```yaml
Interview Date: 15/01/2024
Interview Time: 11:00 AM - 11:30 AM
Interview Panel:
  - Mrs. Anjali Gupta (Principal) - Lead Interviewer
  - Mr. Suresh Kumar (Vice Principal)
  - Mrs. Meera Singh (Grade 6 Coordinator)

Assessment Criteria:

Communication Skills:
  Clarity of Expression: 9/10
  Vocabulary: 8/10
  Confidence: 9/10
  Overall: Excellent
  
Personality Traits:
  Confidence Level: High
  Politeness: Excellent
  Eye Contact: Good
  Body Language: Confident
  
Academic Interest:
  Favorite Subject: Mathematics
  Reading Habits: Regular (reads 2 books/month)
  Hobbies: Chess, Coding, Cricket
  Career Aspiration: Software Engineer
  
Co-curricular Activities:
  Sports: Cricket (School team captain)
  Arts: None
  Music: Learning Guitar
  Other: Coding club member
  
Overall Assessment:
  Interview Rating: 9/10
  Personality: Confident, well-spoken, curious
  Academic Potential: Very High
  Recommendation: STRONGLY RECOMMEND ADMISSION
  Comments: "Bright student with strong analytical skills.
             Shows genuine interest in learning. Will be
             an asset to the school."
```

---

### 5. Biometric Enrollment

#### 5.1 Fingerprint Registration

**Enrollment Process:**
```yaml
Enrollment Date: 01/04/2024
Enrollment Officer: Mr. Ramesh (IT Department)
Device Used: Mantra MFS100 Fingerprint Scanner

Fingerprints Captured:
  Left Hand:
    - Left Thumb (Primary)
    - Left Index Finger (Backup)
  Right Hand:
    - Right Thumb (Primary)
    - Right Index Finger (Backup)

Quality Check:
  Left Thumb: 95% (Excellent)
  Right Thumb: 92% (Excellent)
  Left Index: 90% (Good)
  Right Index: 88% (Good)

Enrollment Status: SUCCESS ✓
Biometric ID: BIO-2024-0456
```

**Usage Applications:**
- **Attendance Marking**: Daily entry/exit scanning
- **Library Access**: Book issue/return
- **Exam Hall Entry**: Verification before exams
- **Canteen Billing**: Cashless payment via fingerprint
- **Lab Access**: Computer lab, science lab entry
- **Bus Boarding**: Transport attendance

**Security & Privacy:**
- **Data Encryption**: AES-256 encryption for stored templates
- **GDPR Compliance**: Parental consent obtained
- **Data Retention**: Until student leaves school + 1 year
- **Access Control**: Only authorized personnel can access
- **Backup**: Daily encrypted backups

#### 5.2 Facial Recognition (Optional)

**Photo Capture:**
```yaml
Capture Date: 01/04/2024
Photo Type: Passport size, digital
Resolution: 600 DPI, 2x2 inches
Background: White/Light blue
Format: JPEG, PNG

Photos Captured:
  - Front facing (primary)
  - Left profile (optional)
  - Right profile (optional)

Usage:
  - Student ID card
  - Student portal profile
  - Attendance system (facial recognition)
  - Yearbook
  - School website (with consent)
  - Academic certificates
```

**Facial Recognition System:**
- **AI-Based Attendance**: Automatic attendance via CCTV
- **Access Control**: Entry to restricted areas
- **Exam Verification**: Prevent impersonation
- **Safety**: Missing student alerts

---

### 6. Emergency Contact & Safety Protocols

#### 6.1 Emergency Contact Hierarchy

**Priority-Based Contact List:**
```yaml
Priority 1 - Father:
  Name: Mr. Rajesh Patel
  Relationship: Father
  Mobile: +91-9876543210
  Alternate: +91-9876543220
  Office: +91-120-4567890
  Available: 24/7
  Preferred Contact Method: Mobile Call
  
Priority 2 - Mother:
  Name: Mrs. Priya Patel
  Relationship: Mother
  Mobile: +91-9876543211
  Alternate: +91-9876543221
  Office: +91-120-4567891
  Available: 24/7
  Preferred Contact Method: Mobile Call
  
Priority 3 - Grandfather:
  Name: Mr. Ramesh Patel
  Relationship: Paternal Grandfather
  Mobile: +91-9876543212
  Address: Same as student
  Available: 9 AM - 9 PM
  
Priority 4 - Uncle:
  Name: Mr. Amit Patel
  Relationship: Paternal Uncle
  Mobile: +91-9876543213
  Address: Sector 18, Noida
  Available: On request
```

**Emergency Contact Rules:**
- **Minimum**: 2 contacts mandatory
- **Maximum**: 5 contacts allowed
- **Verification**: All contacts verified via OTP
- **Updates**: Parents can update via portal
- **Notification**: All contacts notified in emergency

#### 6.2 Medical Emergency Information

**Health Profile:**
```yaml
Blood Group: B+ (Verified)
Height: 145 cm
Weight: 38 kg
BMI: 18.1 (Normal)

Known Allergies:
  - Peanuts (Severe - Anaphylaxis risk)
  - Dust mites (Mild - Sneezing, runny nose)
  - Penicillin (Moderate - Rashes)

Chronic Conditions:
  - Asthma (Mild, well-controlled)
  - Seasonal Allergies (Managed with antihistamines)

Current Medications:
  - Salbutamol Inhaler (as needed for asthma)
  - Cetirizine 10mg (daily for allergies)

Vaccination Status:
  - COVID-19: Fully vaccinated (2 doses + booster)
  - Hepatitis B: Complete
  - MMR: Complete
  - Tetanus: Up to date

Medical History:
  - Appendectomy (2020)
  - Fractured left arm (2019, healed)
  - No other major surgeries
```

**Emergency Medical Protocols:**
```yaml
Preferred Hospital:
  Name: Max Super Speciality Hospital
  Address: Saket, New Delhi
  Distance: 8 km from school
  Emergency Contact: +91-11-2651-5050
  Estimated Travel Time: 15 minutes

Health Insurance:
  Provider: Star Health Insurance
  Policy Number: SH/2024/123456789
  Coverage: ₹5,00,000
  Validity: 01/04/2024 - 31/03/2025
  Cashless: Yes (Max Hospital empaneled)
  
Family Doctor:
  Name: Dr. Suresh Mehta
  Specialization: General Physician
  Clinic: Mehta Clinic, Sector 15, Noida
  Mobile: +91-9876543214
  Available: Mon-Sat, 10 AM - 8 PM

Emergency Medication (Kept in School Clinic):
  - Salbutamol Inhaler (for asthma attacks)
  - EpiPen (for severe allergic reactions)
  - Cetirizine tablets (for allergies)
  - Paracetamol (for fever)
```

**Emergency Response Plan:**
1. **Minor Injury/Illness**: School nurse handles, parents informed
2. **Moderate Emergency**: Parents called, doctor consulted
3. **Severe Emergency**: Ambulance called, parents notified, hospital transfer
4. **Life-Threatening**: 108 ambulance, nearest hospital, parents informed immediately

#### 6.3 Pickup Authorization

**Authorized Persons:**
```yaml
Person 1 - Father:
  Name: Mr. Rajesh Patel
  Relationship: Father
  Photo ID: Aadhaar Card (XXXX-XXXX-9098)
  Vehicle: Honda City (DL-01-AB-1234)
  Authorization: Permanent
  Verification: Photo ID + Face recognition
  
Person 2 - Mother:
  Name: Mrs. Priya Patel
  Relationship: Mother
  Photo ID: Aadhaar Card (XXXX-XXXX-9099)
  Vehicle: Maruti Swift (DL-01-CD-5678)
  Authorization: Permanent
  Verification: Photo ID + Face recognition
  
Person 3 - Driver:
  Name: Mr. Ramesh Singh
  Relationship: Family Driver
  Photo ID: Driving License (DL-0120240012345)
  Vehicle: Honda City (DL-01-AB-1234)
  Authorization: Permanent (with prior notification)
  Verification: Photo ID + SMS confirmation from parent
  
Person 4 - Uncle:
  Name: Mr. Amit Patel
  Relationship: Paternal Uncle
  Photo ID: Aadhaar Card (XXXX-XXXX-9100)
  Authorization: Temporary (requires prior approval)
  Verification: Photo ID + Parent confirmation call
```

**Pickup Security Protocols:**
- **Photo ID Mandatory**: All authorized persons must show government ID
- **Face Verification**: Biometric face matching (optional)
- **SMS Confirmation**: Parents receive SMS when child picked up
- **Unauthorized Pickup**: Denied, parents called immediately
- **Early Pickup**: Requires written request or parent call
- **Late Pickup**: Child waits in supervised area, parents charged late fee

---

## 📊 DATA FIELDS & DATABASE SCHEMA

### Student Master Table

```sql
CREATE TABLE students (
  -- Primary Identifiers
  student_id INT PRIMARY KEY AUTO_INCREMENT,
  admission_number VARCHAR(20) UNIQUE NOT NULL,
  
  -- Basic Information
  first_name VARCHAR(50) NOT NULL,
  middle_name VARCHAR(50),
  last_name VARCHAR(50) NOT NULL,
  full_name VARCHAR(150) GENERATED ALWAYS AS (CONCAT(first_name, ' ', IFNULL(middle_name, ''), ' ', last_name)),
  date_of_birth DATE NOT NULL,
  age INT GENERATED ALWAYS AS (TIMESTAMPDIFF(YEAR, date_of_birth, CURDATE())),
  gender ENUM('Male', 'Female', 'Other', 'Prefer not to say') NOT NULL,
  blood_group ENUM('A+', 'A-', 'B+', 'B-', 'O+', 'O-', 'AB+', 'AB-', 'Unknown'),
  
  -- Demographics
  nationality VARCHAR(50) DEFAULT 'Indian',
  religion VARCHAR(50),
  caste_category ENUM('General', 'OBC', 'SC', 'ST', 'EWS'),
  mother_tongue VARCHAR(50),
  languages_known TEXT,
  place_of_birth VARCHAR(100),
  
  -- Government IDs
  aadhaar_number VARCHAR(12) UNIQUE,
  passport_number VARCHAR(20),
  pan_number VARCHAR(10),
  
  -- Physical Attributes
  height_cm DECIMAL(5,2),
  weight_kg DECIMAL(5,2),
  bmi DECIMAL(4,2) GENERATED ALWAYS AS (weight_kg / ((height_cm/100) * (height_cm/100))),
  vision_status VARCHAR(50),
  hearing_status VARCHAR(50),
  physical_disabilities TEXT,
  identification_marks TEXT,
  
  -- Academic Background
  previous_school_name VARCHAR(200),
  previous_school_board VARCHAR(50),
  last_grade_completed VARCHAR(20),
  previous_school_address TEXT,
  transfer_certificate_number VARCHAR(50),
  previous_academic_performance VARCHAR(20),
  
  -- Admission Details
  admission_date DATE NOT NULL,
  academic_year VARCHAR(9) NOT NULL,
  grade_at_admission VARCHAR(10) NOT NULL,
  current_grade VARCHAR(10),
  current_section VARCHAR(10),
  roll_number VARCHAR(20),
  
  -- Status
  student_status ENUM('ACTIVE', 'WITHDRAWN', 'SUSPENDED', 'ALUMNI', 'TRANSFERRED') DEFAULT 'ACTIVE',
  registration_status ENUM('INCOMPLETE', 'COMPLETE', 'VERIFIED') DEFAULT 'INCOMPLETE',
  
  -- Digital Assets
  photo_url VARCHAR(255),
  biometric_enrolled BOOLEAN DEFAULT FALSE,
  biometric_id VARCHAR(50),
  qr_code_url VARCHAR(255),
  
  -- Family Linkage
  family_id INT,
  sibling_count INT DEFAULT 0,
  sibling_discount_percent DECIMAL(5,2) DEFAULT 0,
  
  -- Metadata
  created_by INT,
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_by INT,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Indexes
  INDEX idx_admission_number (admission_number),
  INDEX idx_family_id (family_id),
  INDEX idx_student_status (student_status),
  INDEX idx_current_grade (current_grade),
  INDEX idx_aadhaar (aadhaar_number),
  
  -- Foreign Keys
  FOREIGN KEY (family_id) REFERENCES families(family_id)
);
```

### Family Information Table

```sql
CREATE TABLE families (
  -- Primary Key
  family_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Father's Information
  father_name VARCHAR(100),
  father_occupation VARCHAR(100),
  father_company VARCHAR(200),
  father_designation VARCHAR(100),
  father_annual_income DECIMAL(12,2),
  father_office_address TEXT,
  father_mobile VARCHAR(15) NOT NULL,
  father_alternate_mobile VARCHAR(15),
  father_email VARCHAR(100),
  father_personal_email VARCHAR(100),
  father_aadhaar VARCHAR(12),
  father_pan VARCHAR(10),
  father_qualification VARCHAR(100),
  
  -- Mother's Information
  mother_name VARCHAR(100),
  mother_occupation VARCHAR(100),
  mother_company VARCHAR(200),
  mother_designation VARCHAR(100),
  mother_annual_income DECIMAL(12,2),
  mother_office_address TEXT,
  mother_mobile VARCHAR(15) NOT NULL,
  mother_alternate_mobile VARCHAR(15),
  mother_email VARCHAR(100),
  mother_personal_email VARCHAR(100),
  mother_aadhaar VARCHAR(12),
  mother_pan VARCHAR(10),
  mother_qualification VARCHAR(100),
  
  -- Guardian Information (if applicable)
  guardian_name VARCHAR(100),
  guardian_relationship VARCHAR(50),
  guardian_mobile VARCHAR(15),
  guardian_email VARCHAR(100),
  guardian_aadhaar VARCHAR(12),
  legal_guardianship_doc VARCHAR(100),
  guardianship_reason TEXT,
  
  -- Contact Preferences
  primary_contact ENUM('Father', 'Mother', 'Guardian') DEFAULT 'Father',
  secondary_contact ENUM('Father', 'Mother', 'Guardian') DEFAULT 'Mother',
  communication_preference ENUM('SMS', 'Email', 'WhatsApp', 'Phone') DEFAULT 'SMS',
  preferred_language VARCHAR(50) DEFAULT 'English',
  
  -- Address Information
  residential_address TEXT,
  residential_city VARCHAR(100),
  residential_state VARCHAR(100),
  residential_pincode VARCHAR(10),
  residential_landmark VARCHAR(200),
  permanent_address TEXT,
  permanent_same_as_residential BOOLEAN DEFAULT TRUE,
  
  -- Financial Information
  total_family_income DECIMAL(12,2),
  income_category ENUM('Below 5L', '5L-10L', '10L-25L', 'Above 25L'),
  income_proof_submitted BOOLEAN DEFAULT FALSE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Indexes
  INDEX idx_father_mobile (father_mobile),
  INDEX idx_mother_mobile (mother_mobile),
  UNIQUE INDEX idx_parent_contact (father_mobile, mother_mobile)
);
```

### Emergency Contact Table

```sql
CREATE TABLE emergency_contacts (
  emergency_contact_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Contact Details
  contact_name VARCHAR(100) NOT NULL,
  relationship VARCHAR(50) NOT NULL,
  mobile_number VARCHAR(15) NOT NULL,
  alternate_mobile VARCHAR(15),
  office_number VARCHAR(15),
  email VARCHAR(100),
  address TEXT,
  
  -- Priority & Authorization
  priority_order INT NOT NULL,
  is_authorized_for_pickup BOOLEAN DEFAULT FALSE,
  photo_id_type VARCHAR(50),
  photo_id_number VARCHAR(50),
  vehicle_number VARCHAR(20),
  authorization_type ENUM('Permanent', 'Temporary') DEFAULT 'Permanent',
  
  -- Availability
  available_24x7 BOOLEAN DEFAULT TRUE,
  available_from TIME,
  available_to TIME,
  preferred_contact_method ENUM('Mobile', 'SMS', 'Email', 'WhatsApp') DEFAULT 'Mobile',
  
  -- Verification
  verified BOOLEAN DEFAULT FALSE,
  verification_date DATE,
  otp_verified BOOLEAN DEFAULT FALSE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_priority (student_id, priority_order)
);
```

### Admission Test Table

```sql
CREATE TABLE admission_tests (
  test_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Test Details
  test_date DATE NOT NULL,
  test_center VARCHAR(100),
  test_duration_minutes INT,
  test_type VARCHAR(50),
  
  -- Subject-wise Marks
  english_marks DECIMAL(5,2),
  english_max_marks DECIMAL(5,2) DEFAULT 100,
  mathematics_marks DECIMAL(5,2),
  mathematics_max_marks DECIMAL(5,2) DEFAULT 100,
  gk_marks DECIMAL(5,2),
  gk_max_marks DECIMAL(5,2) DEFAULT 100,
  reasoning_marks DECIMAL(5,2),
  reasoning_max_marks DECIMAL(5,2) DEFAULT 100,
  
  -- Total Score
  total_marks DECIMAL(6,2),
  total_max_marks DECIMAL(6,2),
  percentage DECIMAL(5,2),
  
  -- Result
  cutoff_percentage DECIMAL(5,2) DEFAULT 60,
  result ENUM('PASS', 'FAIL', 'PENDING') DEFAULT 'PENDING',
  rank INT,
  total_candidates INT,
  
  -- Performance Analysis
  strengths TEXT,
  areas_for_improvement TEXT,
  overall_performance VARCHAR(50),
  
  -- Interview Details
  interview_date DATE,
  interview_time TIME,
  interview_duration_minutes INT DEFAULT 30,
  interviewer_name VARCHAR(100),
  interview_panel TEXT,
  
  -- Interview Assessment
  communication_rating INT CHECK (communication_rating BETWEEN 1 AND 10),
  confidence_rating INT CHECK (confidence_rating BETWEEN 1 AND 10),
  personality_rating INT CHECK (personality_rating BETWEEN 1 AND 10),
  academic_interest_rating INT CHECK (academic_interest_rating BETWEEN 1 AND 10),
  overall_interview_rating INT CHECK (overall_interview_rating BETWEEN 1 AND 10),
  
  -- Recommendation
  recommendation ENUM('Strongly Recommend', 'Recommend', 'Neutral', 'Not Recommended') DEFAULT 'Neutral',
  interviewer_comments TEXT,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_test_date (test_date),
  INDEX idx_result (result)
);
```

### Medical Information Table

```sql
CREATE TABLE student_medical_info (
  medical_info_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL UNIQUE,
  
  -- Allergies
  known_allergies TEXT,
  allergy_severity ENUM('Mild', 'Moderate', 'Severe'),
  
  -- Chronic Conditions
  chronic_conditions TEXT,
  current_medications TEXT,
  
  -- Vaccination Status
  covid_vaccination_status VARCHAR(50),
  hepatitis_b_status VARCHAR(50),
  mmr_status VARCHAR(50),
  tetanus_status VARCHAR(50),
  other_vaccinations TEXT,
  
  -- Medical History
  major_surgeries TEXT,
  past_hospitalizations TEXT,
  family_medical_history TEXT,
  
  -- Emergency Medical Info
  preferred_hospital_name VARCHAR(200),
  preferred_hospital_address TEXT,
  preferred_hospital_contact VARCHAR(15),
  preferred_hospital_distance_km DECIMAL(5,2),
  
  -- Health Insurance
  insurance_provider VARCHAR(100),
  insurance_policy_number VARCHAR(50),
  insurance_coverage_amount DECIMAL(12,2),
  insurance_validity_from DATE,
  insurance_validity_to DATE,
  cashless_facility BOOLEAN DEFAULT FALSE,
  
  -- Family Doctor
  family_doctor_name VARCHAR(100),
  family_doctor_specialization VARCHAR(100),
  family_doctor_clinic VARCHAR(200),
  family_doctor_mobile VARCHAR(15),
  
  -- Emergency Medication (stored in school)
  emergency_medication_list TEXT,
  medication_storage_location VARCHAR(100),
  
  -- Metadata
  last_updated_date DATE,
  updated_by INT,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE
);
```

### Biometric Enrollment Table

```sql
CREATE TABLE biometric_enrollment (
  biometric_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL UNIQUE,
  
  -- Enrollment Details
  enrollment_date DATE NOT NULL,
  enrollment_officer VARCHAR(100),
  device_used VARCHAR(100),
  
  -- Fingerprint Data
  left_thumb_template BLOB,
  left_thumb_quality INT,
  right_thumb_template BLOB,
  right_thumb_quality INT,
  left_index_template BLOB,
  left_index_quality INT,
  right_index_template BLOB,
  right_index_quality INT,
  
  -- Facial Recognition Data
  face_template BLOB,
  face_photo_url VARCHAR(255),
  face_recognition_enabled BOOLEAN DEFAULT FALSE,
  
  -- Status
  enrollment_status ENUM('SUCCESS', 'FAILED', 'PENDING') DEFAULT 'PENDING',
  quality_check_passed BOOLEAN DEFAULT FALSE,
  
  -- Usage Permissions
  attendance_enabled BOOLEAN DEFAULT TRUE,
  library_enabled BOOLEAN DEFAULT TRUE,
  exam_hall_enabled BOOLEAN DEFAULT TRUE,
  canteen_enabled BOOLEAN DEFAULT TRUE,
  lab_access_enabled BOOLEAN DEFAULT TRUE,
  
  -- Security
  data_encrypted BOOLEAN DEFAULT TRUE,
  encryption_algorithm VARCHAR(50) DEFAULT 'AES-256',
  consent_obtained BOOLEAN DEFAULT FALSE,
  consent_date DATE,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  last_used_date TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE
);
```

---

## ⚙️ BUSINESS RULES & LOGIC

### Rule 1: Admission Number Generation

```javascript
FUNCTION generate_admission_number(student, academic_year, grade) {
  // Format: YEAR/GRADE/SEQUENCE
  // Example: 2024/06/0456
  
  // Extract year from academic year
  year = academic_year.split('-')[0]  // "2024-25" → "2024"
  
  // Format grade with zero padding
  grade_code = grade.toString().padStart(2, '0')  // 6 → "06"
  
  // Get last sequence number for this year-grade combination
  query = "SELECT MAX(CAST(SUBSTRING_INDEX(admission_number, '/', -1) AS UNSIGNED)) as last_seq 
           FROM students 
           WHERE academic_year = ? AND grade_at_admission = ?"
  
  result = EXECUTE_QUERY(query, [academic_year, grade])
  last_sequence = result.last_seq || 0
  
  // Increment sequence
  new_sequence = last_sequence + 1
  sequence_code = new_sequence.toString().padStart(4, '0')  // 456 → "0456"
  
  // Construct admission number
  admission_number = `${year}/${grade_code}/${sequence_code}`
  
  // Validate format
  IF NOT MATCHES_REGEX(admission_number, /^\d{4}\/\d{2}\/\d{4}$/) {
    THROW ERROR("Invalid admission number format")
  }
  
  // Check for duplicates (should never happen, but safety check)
  IF EXISTS_IN_DATABASE(admission_number) {
    THROW ERROR("Duplicate admission number generated")
  }
  
  LOG_AUDIT("Admission number generated", {
    admission_number: admission_number,
    student_name: student.full_name,
    grade: grade,
    academic_year: academic_year
  })
  
  RETURN admission_number
}

// Example Usage:
student = {first_name: "Aarav", last_name: "Patel"}
admission_number = generate_admission_number(student, "2024-25", 6)
// Returns: "2024/06/0456"
```

### Rule 2: Sibling Discount Auto-Application

```javascript
FUNCTION check_and_apply_sibling_discount(student) {
  // Step 1: Identify family using parent contact
  parent_mobile = student.father_mobile || student.mother_mobile
  
  IF NOT parent_mobile {
    THROW ERROR("Parent mobile number required for sibling check")
  }
  
  // Step 2: Find existing family
  query = "SELECT family_id FROM families 
           WHERE father_mobile = ? OR mother_mobile = ?"
  
  family = EXECUTE_QUERY(query, [parent_mobile, parent_mobile])
  
  IF family.exists() {
    family_id = family.family_id
  } ELSE {
    // Create new family record
    family_id = CREATE_FAMILY_RECORD(student)
  }
  
  // Step 3: Count existing active siblings
  query = "SELECT COUNT(*) as sibling_count 
           FROM students 
           WHERE family_id = ? 
           AND student_status = 'ACTIVE' 
           AND student_id != ?"
  
  result = EXECUTE_QUERY(query, [family_id, student.student_id])
  sibling_count = result.sibling_count
  
  // Step 4: Calculate discount based on sibling count
  discount_percent = 0
  
  IF sibling_count == 0 {
    discount_percent = 0  // First child, no discount
  } ELSE IF sibling_count == 1 {
    discount_percent = 10  // Second child, 10% discount
  } ELSE IF sibling_count == 2 {
    discount_percent = 15  // Third child, 15% discount
  } ELSE {
    discount_percent = 20  // Fourth+ child, 20% discount
  }
  
  // Step 5: Update student record
  UPDATE students SET
    family_id = family_id,
    sibling_count = sibling_count,
    sibling_discount_percent = discount_percent
  WHERE student_id = student.student_id
  
  // Step 6: Notify fee module
  TRIGGER_EVENT("fee_assignment", {
    student_id: student.student_id,
    discount_type: "SIBLING",
    discount_percent: discount_percent,
    sibling_count: sibling_count
  })
  
  // Step 7: Log activity
  LOG_AUDIT("Sibling discount applied", {
    student_id: student.student_id,
    family_id: family_id,
    sibling_count: sibling_count,
    discount_percent: discount_percent
  })
  
  // Step 8: Send notification to parents
  SEND_SMS(parent_mobile, 
    `Sibling discount of ${discount_percent}% applied for ${student.first_name}. 
     ${sibling_count} sibling(s) found in school.`)
  
  RETURN {
    family_id: family_id,
    sibling_count: sibling_count,
    discount_percent: discount_percent,
    message: `${discount_percent}% sibling discount applied (${sibling_count} sibling(s) found)`
  }
}
```

### Rule 3: Mandatory Document Verification

```javascript
FUNCTION verify_mandatory_documents(student) {
  // Define mandatory documents based on student category
  mandatory_docs = [
    {name: "Birth Certificate", required: true},
    {name: "Previous School TC", required: student.grade_at_admission > 1},
    {name: "Aadhaar Card", required: student.nationality == "Indian"},
    {name: "Passport", required: student.nationality != "Indian"},
    {name: "Parent ID Proof (Father)", required: true},
    {name: "Parent ID Proof (Mother)", required: true},
    {name: "Address Proof", required: true},
    {name: "Passport Size Photos", required: true, count: 4},
    {name: "Caste Certificate", required: student.caste_category IN ['SC', 'ST', 'OBC']},
    {name: "Income Certificate", required: student.caste_category == 'EWS'},
    {name: "Medical Certificate", required: student.physical_disabilities != null},
    {name: "Vaccination Certificate", required: true}
  ]
  
  // Check each document
  missing_docs = []
  submitted_docs = []
  
  FOR each doc IN mandatory_docs {
    IF doc.required {
      // Check if document exists in database
      query = "SELECT * FROM student_documents 
               WHERE student_id = ? AND document_type = ?"
      
      result = EXECUTE_QUERY(query, [student.student_id, doc.name])
      
      IF result.exists() AND result.verified {
        submitted_docs.push(doc.name)
      } ELSE {
        missing_docs.push(doc.name)
      }
    }
  }
  
  // Calculate completion percentage
  total_required = mandatory_docs.filter(d => d.required).length
  completion_percent = (submitted_docs.length / total_required) * 100
  
  // Update registration status
  IF missing_docs.length == 0 {
    // All documents submitted
    UPDATE students SET
      registration_status = 'COMPLETE',
      student_status = 'ACTIVE'
    WHERE student_id = student.student_id
    
    // Trigger post-registration activities
    TRIGGER_EVENT("registration_complete", {
      student_id: student.student_id
    })
    
    // Send success notification
    SEND_EMAIL(student.parent_email,
      "Registration Complete",
      `Dear Parent, Registration for ${student.full_name} is complete. 
       Admission Number: ${student.admission_number}`)
    
    RETURN {
      status: "COMPLETE",
      completion_percent: 100,
      message: "All mandatory documents verified"
    }
    
  } ELSE {
    // Documents missing
    UPDATE students SET
      registration_status = 'INCOMPLETE'
    WHERE student_id = student.student_id
    
    // Send reminder notification
    SEND_EMAIL(student.parent_email,
      "Registration Incomplete - Documents Required",
      `Dear Parent, Registration for ${student.full_name} is incomplete.
       
       Missing Documents:
       ${missing_docs.map((d, i) => `${i+1}. ${d}`).join('\n')}
       
       Please submit these documents by ${GET_DEADLINE_DATE()}.`)
    
    // Create task for admissions officer
    CREATE_TASK({
      title: `Follow up: Missing documents for ${student.full_name}`,
      assigned_to: "Admissions Officer",
      due_date: GET_DEADLINE_DATE(),
      priority: "HIGH"
    })
    
    RETURN {
      status: "INCOMPLETE",
      completion_percent: completion_percent,
      missing_documents: missing_docs,
      message: `${missing_docs.length} document(s) pending`
    }
  }
}
```

### Rule 4: Age Validation for Grade Admission

```javascript
FUNCTION validate_age_for_grade(student, grade) {
  // Define age criteria for each grade (as of 1st April)
  age_criteria = {
    "1":  {min: 5, max: 7,  name: "Grade 1"},
    "2":  {min: 6, max: 8,  name: "Grade 2"},
    "3":  {min: 7, max: 9,  name: "Grade 3"},
    "4":  {min: 8, max: 10, name: "Grade 4"},
    "5":  {min: 9, max: 11, name: "Grade 5"},
    "6":  {min: 10, max: 12, name: "Grade 6"},
    "7":  {min: 11, max: 13, name: "Grade 7"},
    "8":  {min: 12, max: 14, name: "Grade 8"},
    "9":  {min: 13, max: 15, name: "Grade 9"},
    "10": {min: 14, max: 16, name: "Grade 10"},
    "11": {min: 15, max: 17, name: "Grade 11"},
    "12": {min: 16, max: 18, name: "Grade 12"}
  }
  
  // Calculate age as of 1st April of admission year
  admission_year = student.academic_year.split('-')[0]
  cutoff_date = new Date(`${admission_year}-04-01`)
  dob = new Date(student.date_of_birth)
  
  age_on_cutoff = CALCULATE_AGE(dob, cutoff_date)
  
  criteria = age_criteria[grade]
  
  IF NOT criteria {
    THROW ERROR("Invalid grade specified")
  }
  
  // Check if age is within acceptable range
  IF age_on_cutoff < criteria.min {
    RETURN {
      valid: false,
      reason: "UNDERAGE",
      message: `Student is ${age_on_cutoff} years old. Minimum age for ${criteria.name} is ${criteria.min} years as of 1st April ${admission_year}.`,
      recommendation: `Consider admission to Grade ${parseInt(grade) - 1}`
    }
  }
  
  IF age_on_cutoff > criteria.max {
    RETURN {
      valid: false,
      reason: "OVERAGE",
      message: `Student is ${age_on_cutoff} years old. Maximum age for ${criteria.name} is ${criteria.max} years as of 1st April ${admission_year}.`,
      recommendation: `Consider admission to Grade ${parseInt(grade) + 1} or require special approval`
    }
  }
  
  // Age is within acceptable range
  RETURN {
    valid: true,
    age_on_cutoff: age_on_cutoff,
    message: `Age validation passed. Student is ${age_on_cutoff} years old, within acceptable range (${criteria.min}-${criteria.max} years) for ${criteria.name}.`
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations (Sends Data TO)

#### 1. Fee Management Module
**Data Sent:**
- Student ID, Admission Number
- Grade, Section
- Family ID, Sibling Count
- Sibling Discount Percentage
- Fee Category (Regular/EWS/Staff Ward)
- Transport Opted (Yes/No)
- Hostel Opted (Yes/No)

**Trigger:** Registration completion
**Purpose:** Automatic fee structure assignment with applicable discounts

#### 2. Document Management Module
**Data Sent:**
- Student ID
- Document List (Birth Certificate, TC, Aadhaar, etc.)
- Document Upload Requests

**Trigger:** Registration initiation
**Purpose:** Link and verify mandatory documents

#### 3. Biometric System
**Data Sent:**
- Student ID, Admission Number
- Full Name, Photo
- Grade, Section
- Enrollment Request

**Trigger:** Biometric enrollment step
**Purpose:** Register fingerprints and facial recognition

#### 4. Communication Module
**Data Sent:**
- Parent Mobile Numbers, Emails
- Student Name, Admission Number
- Welcome Message Templates

**Trigger:** Registration completion
**Purpose:** Send welcome SMS/email with admission details

#### 5. Transport Management Module
**Data Sent:**
- Student ID, Address
- Pickup Location, Drop Location
- Parent Contact Numbers
- Transport Opted: Yes

**Trigger:** Transport option selected
**Purpose:** Assign bus route and stop

#### 6. Hostel Management Module
**Data Sent:**
- Student ID, Grade, Gender
- Boarding Preference (AC/Non-AC)
- Parent Contact
- Hostel Opted: Yes

**Trigger:** Hostel option selected
**Purpose:** Allocate hostel room

#### 7. Student Portal
**Data Sent:**
- Student ID, Admission Number
- Login Credentials (auto-generated)
- Student Photo, Basic Info

**Trigger:** Registration completion
**Purpose:** Create student portal account

#### 8. ID Card Generation System
**Data Sent:**
- Student Photo, Name
- Admission Number, Grade, Section
- Blood Group, Emergency Contact
- QR Code Data

**Trigger:** Registration completion
**Purpose:** Generate and print student ID card

### Inbound Integrations (Receives Data FROM)

#### 1. Admissions & CRM Module
**Data Received:**
- Admission Confirmation Status
- Entrance Test Scores
- Interview Results
- Application Form Data (pre-filled)

**Trigger:** Admission confirmed
**Purpose:** Start registration process with pre-populated data

#### 2. Parent Portal
**Data Received:**
- Family Information (parent details)
- Emergency Contacts
- Address Information
- Document Uploads

**Trigger:** Parent completes online registration form
**Purpose:** Capture family data directly from parents

#### 3. Payment Gateway
**Data Received:**
- Registration Fee Payment Status
- Transaction ID
- Payment Date, Amount

**Trigger:** Registration fee paid
**Purpose:** Verify payment before completing registration

---

## 👥 USER WORKFLOWS

### Workflow 1: Complete New Student Registration

**Actor:** Admissions Officer  
**Duration:** 30-45 minutes  
**Prerequisites:** Admission confirmed, registration fee paid

**Steps:**

1. **Login & Navigate**
   - Login to ERP system
   - Navigate to: Student Management → Registration → New Student

2. **Verify Admission Confirmation**
   - System displays: Admission confirmed students list
   - Select student: "Aarav Patel" (Application ID: APP-2024-1234)
   - System pre-fills data from admission application

3. **Enter/Verify Basic Details**
   ```
   First Name: Aarav
   Middle Name: Kumar
   Last Name: Patel
   Date of Birth: 15/08/2012
   Gender: Male
   Blood Group: B+
   Nationality: Indian
   Religion: Hindu
   Category: General
   Mother Tongue: Gujarati
   Aadhaar: 1234-5678-9012
   ```
   - System validates: Age appropriate for Grade 6 ✓
   - System auto-calculates: Age = 11 years 5 months

4. **Academic Background**
   ```
   Previous School: Delhi Public School, Ahmedabad
   Previous Board: CBSE
   Last Grade: Class 5 (2023-24)
   TC Number: DPS/AHM/2024/0456
   Previous Performance: 92.5%
   ```

5. **Generate Admission Number**
   - Click "Generate Admission Number"
   - System generates: **2024/06/0456**
   - System confirms: "Admission number generated successfully"

6. **Enter Family Details**
   ```
   Father:
     Name: Mr. Rajesh Kumar Patel
     Mobile: +91-9876543210
     Email: rajesh.patel@gmail.com
     Occupation: Software Engineer
     Company: Infosys
     Annual Income: ₹18,00,000
   
   Mother:
     Name: Mrs. Priya Rajesh Patel
     Mobile: +91-9876543211
     Email: priya.patel@gmail.com
     Occupation: Teacher
     Annual Income: ₹7,50,000
   ```

7. **Sibling Check (Automatic)**
   - System searches using parent mobile: 9876543210
   - System finds: 1 existing student
     - Name: Diya Patel
     - Admission No: 2018/08/0123
     - Grade: 8-A
   - System displays: "1 sibling found"
   - System auto-applies: **10% sibling discount**
   - System links: Both students to family_id: 1234

8. **Enter Address**
   ```
   Residential Address:
     House No: A-204, Green Valley Apartments
     Area: Sector 15
     City: Noida
     State: Uttar Pradesh
     PIN: 201301
   
   Permanent Address: [Same as residential] ✓
   ```

9. **Emergency Contacts**
   ```
   Priority 1:
     Name: Mr. Rajesh Patel (Father)
     Mobile: +91-9876543210
     Authorized for pickup: Yes
   
   Priority 2:
     Name: Mrs. Priya Patel (Mother)
     Mobile: +91-9876543211
     Authorized for pickup: Yes
   
   Priority 3:
     Name: Mr. Ramesh Patel (Grandfather)
     Mobile: +91-9876543212
     Authorized for pickup: No
   ```

10. **Medical Information**
    ```
    Blood Group: B+ (confirmed)
    Known Allergies: Peanuts (Severe), Dust (Mild)
    Chronic Conditions: Asthma (Mild)
    Current Medications: Salbutamol Inhaler
    Preferred Hospital: Max Hospital, Saket
    Insurance: Star Health, Policy: SH/2024/123456789
    ```

11. **Upload Documents**
    - Birth Certificate: ✓ Uploaded
    - Previous School TC: ✓ Uploaded
    - Aadhaar Card: ✓ Uploaded
    - Parent ID Proofs: ✓ Uploaded
    - Address Proof: ✓ Uploaded
    - Passport Photos (4): ✓ Uploaded
    - System validates: All mandatory documents present ✓

12. **Biometric Enrollment**
    - Click "Enroll Biometrics"
    - Capture Left Thumb: Quality 95% ✓
    - Capture Right Thumb: Quality 92% ✓
    - Capture Photo: ✓
    - System confirms: "Biometric enrollment successful"

13. **Review & Submit**
    - System displays: Registration summary
    - Officer reviews all details
    - Click "Submit Registration"

14. **System Processing (Automatic)**
    - Validates all mandatory fields ✓
    - Assigns student_status: ACTIVE
    - Sets registration_status: COMPLETE
    - Triggers fee assignment (with 10% sibling discount)
    - Generates ID card print request
    - Sends welcome SMS to parents:
      ```
      "Dear Mr. Patel, Aarav has been successfully registered.
       Admission No: 2024/06/0456. Welcome to Hogwarts School!"
      ```
    - Sends welcome email with:
      - Admission number
      - Fee structure
      - Portal login credentials
      - Important dates

15. **Confirmation**
    - System displays: "Registration completed successfully!"
    - Admission Number: 2024/06/0456
    - Sibling Discount: 10% applied
    - ID Card: Queued for printing
    - Portal Access: Activated

**Expected Outcome:**
- Student successfully registered with admission number 2024/06/0456
- Family linked with existing sibling
- 10% sibling discount auto-applied
- All documents verified
- Biometrics enrolled
- Parents notified via SMS and email
- Fee structure assigned
- ID card generation initiated
- Student portal activated

---

### Workflow 2: Incomplete Registration Follow-up

**Actor:** Admissions Officer  
**Duration:** 10-15 minutes  
**Trigger:** Documents missing, registration incomplete

**Steps:**

1. **System Alert**
   - Daily automated report: "Incomplete Registrations"
   - Shows: 5 students with pending documents

2. **Review Incomplete Registration**
   - Select student: "Rohan Sharma" (Admission No: 2024/06/0457)
   - Registration Status: INCOMPLETE
   - Missing Documents:
     - Caste Certificate
     - Address Proof

3. **Parent Communication**
   - System auto-sent email on Day 1 (registration date)
   - Officer calls parent: +91-9876543220
   - Explains: "Please submit Caste Certificate and Address Proof by 10/04/2024"
   - Parent confirms: "Will submit tomorrow"

4. **Document Submission**
   - Parent visits school on 08/04/2024
   - Submits: Caste Certificate, Address Proof
   - Officer scans and uploads documents

5. **Document Verification**
   - Officer verifies documents
   - Marks as: Verified ✓
   - System updates: All mandatory documents complete

6. **Complete Registration**
   - System auto-updates: registration_status = COMPLETE
   - System activates: student_status = ACTIVE
   - Triggers: Fee assignment, ID card generation

7. **Notification**
   - SMS sent to parent:
     ```
     "Dear Mr. Sharma, Registration for Rohan is now complete.
      All documents verified. ID card will be ready in 2 days."
     ```

**Expected Outcome:**
- Incomplete registration tracked and followed up
- Documents submitted and verified
- Registration completed
- Student activated in system
- Parent notified

---

### Workflow 3: Sibling Discount Auto-Application

**Actor:** System (Automated)  
**Duration:** < 1 second  
**Trigger:** New student registration with existing sibling

**Steps:**

1. **Registration Initiated**
   - New student: Arjun Patel
   - Parent mobile entered: +91-9876543210

2. **Family Search**
   - System queries: families table
   - Searches: father_mobile = 9876543210 OR mother_mobile = 9876543210
   - Result: family_id = 1234 found

3. **Sibling Count**
   - System queries: students table
   - Condition: family_id = 1234 AND student_status = 'ACTIVE'
   - Result: 2 existing students found
     - Diya Patel (2018/08/0123)
     - Aarav Patel (2024/06/0456)

4. **Discount Calculation**
   - Sibling count: 2
   - New student: 3rd child
   - Discount: 15% (as per policy)

5. **Apply Discount**
   - Update: students.sibling_discount_percent = 15
   - Update: students.family_id = 1234
   - Update: students.sibling_count = 2

6. **Notify Fee Module**
   - Trigger event: fee_assignment
   - Parameters:
     - student_id: new student ID
     - discount_type: SIBLING
     - discount_percent: 15

7. **Display Notification**
   - On registration screen:
     ```
     ✓ Sibling Discount Applied: 15%
     2 siblings found in school:
     - Diya Patel (Grade 8-A)
     - Aarav Patel (Grade 6-B)
     ```

8. **Parent Notification**
   - SMS sent:
     ```
     "Sibling discount of 15% applied for Arjun.
      2 siblings found in school."
     ```

     ```

**Expected Outcome:**
- Sibling relationship automatically detected
- 15% discount applied without manual intervention
- Family linkage established
- Fee module notified
- Parents informed

---

### Workflow 4: First-Time School Joiner Registration (Grade 1/KG)

**Actor:** Admissions Officer  
**Duration:** 25-30 minutes  
**Prerequisites:** Admission confirmed for Grade 1/KG student

**Steps:**

1. **Login & Navigate**
   - Navigate to: Student Management → Registration → New Student

2. **Select Grade 1 Student**
   - Student: Ananya Sharma (Application ID: APP-2024-5678)
   - Grade: 1
   - System notes: First-time school joiner

3. **Enter Basic Details**
   ```
   First Name: Ananya
   Last Name: Sharma
   DOB: 10/04/2018 (Age: 5 years 9 months)
   Gender: Female
   Blood Group: O+
   ```
   - System validates: Age appropriate for Grade 1 ✓

4. **Academic Background (First-Timer)**
   ```
   Previous School: N/A (First-time school joiner)
   Previous Board: N/A
   Last Grade: N/A
   TC Number: N/A
   Preschool Attended: Little Angels Playschool (2021-2023)
   ```
   - System marks: first_time_joiner = TRUE

5. **Generate Admission Number**
   - System generates: **2024/01/0089**

6. **Enter Family Details**
   ```
   Father: Mr. Vikram Sharma, Mobile: 9988776655
   Mother: Mrs. Neha Sharma, Mobile: 9988776656
   ```

7. **Sibling Check**
   - System searches: No siblings found
   - System displays: "First child from this family"
   - Sibling discount: 0% (Not applicable)
   - System creates: New family_id: 5678

8. **Upload Documents (Grade 1 Specific)**
   - Birth Certificate: ✓
   - Aadhaar Card: ✓
   - Parent ID Proofs: ✓
   - Address Proof: ✓
   - Passport Photos: ✓
   - Vaccination Certificate: ✓
   - **Note**: No TC required for Grade 1

9. **Medical Information**
   ```
   Allergies: None
   Chronic Conditions: None
   Vaccinations: Complete (COVID, MMR, Hepatitis B)
   ```

10. **Biometric Enrollment**
    - Fingerprints: Captured ✓
    - Photo: Captured ✓

11. **Submit Registration**
    - System validates all fields ✓
    - Status: ACTIVE
    - Welcome SMS sent to parents

**Expected Outcome:**
- First-time joiner successfully registered
- No TC required (system handles Grade 1 exception)
- New family record created
- No sibling discount applied
- Parents notified

---

### Workflow 5: Biometric Enrollment Failure & Re-enrollment

**Actor:** IT Administrator / Admissions Officer  
**Duration:** 10-15 minutes  
**Trigger:** Biometric quality check failed

**Steps:**

1. **Initial Enrollment Attempt**
   - Student: Rohan Kumar (2024/06/0458)
   - Fingerprint capture attempted
   - Left Thumb Quality: 45% ❌ (Below 70% threshold)
   - Right Thumb Quality: 50% ❌ (Below 70% threshold)
   - System marks: Biometric enrollment FAILED

2. **Failure Notification**
   - System alerts: "Biometric quality check failed"
   - Reason: Dry fingers, poor scanner contact
   - Recommendation: Clean fingers, retry

3. **Troubleshooting**
   - IT Admin cleans scanner
   - Student washes and dries hands
   - Applies hand cream (if fingers too dry)
   - Waits 5 minutes

4. **Re-enrollment Attempt**
   - Navigate to: Student Profile → Biometric Enrollment
   - Click "Re-enroll Biometrics"
   - Capture Left Thumb: Quality 88% ✓
   - Capture Right Thumb: Quality 85% ✓
   - Capture backup fingers:
     - Left Index: 82% ✓
     - Right Index: 80% ✓

5. **Quality Verification**
   - System validates: All fingerprints above 70% threshold
   - Biometric enrollment: SUCCESS ✓
   - Biometric ID assigned: BIO-2024-0458

6. **Enable Biometric Features**
   - Attendance: Enabled ✓
   - Library Access: Enabled ✓
   - Exam Hall Entry: Enabled ✓
   - Canteen: Enabled ✓

7. **Test Biometric**
   - Student tests fingerprint on attendance device
   - System recognizes: "Rohan Kumar - Grade 6-B" ✓
   - Verification successful

8. **Update Status**
   - System updates: biometric_enrolled = TRUE
   - Registration status: COMPLETE
   - Notification sent to parents:
     ```
     "Biometric enrollment completed for Rohan.
      ID card will be ready in 2 days."
     ```

**Expected Outcome:**
- Failed biometric enrollment recovered
- Quality threshold met on retry
- All biometric features enabled
- Student can use fingerprint for attendance/access
- Registration completed successfully

**Common Failure Reasons & Solutions:**
- **Dry Fingers**: Apply hand cream, wait 5 minutes
- **Wet Fingers**: Dry thoroughly, retry
- **Dirty Scanner**: Clean scanner glass
- **Poor Positioning**: Guide student on proper finger placement
- **Cut/Injury**: Use alternate finger (backup enrollment)

---

## 📱 PARENT PORTAL INTEGRATION

### Self-Service Registration

**Features:**
- **Online Form**: Parents fill registration form from home
- **Document Upload**: Upload scanned documents directly
- **Payment Integration**: Pay registration fee online
- **Status Tracking**: Track registration progress
- **Appointment Booking**: Book slot for biometric enrollment

**Workflow:**
1. Parent receives admission confirmation email
2. Email contains: Registration portal link + credentials
3. Parent logs in, completes online form
4. Uploads documents
5. Pays registration fee
6. Books appointment for biometric enrollment
7. Visits school only for biometric enrollment and ID card collection

---

## 📊 REPORTS & ANALYTICS

### Registration Reports

1. **Daily Registration Report**
   - New registrations today
   - Pending registrations
   - Document verification status

2. **Sibling Discount Report**
   - Students with sibling discounts
   - Discount amount by family
   - Total discount given

3. **Incomplete Registration Report**
   - Students with pending documents
   - Days since registration
   - Follow-up status

4. **Admission Test Analysis**
   - Average scores by subject
   - Pass/fail ratio
   - Top performers

5. **Biometric Enrollment Status**
   - Enrolled vs pending
   - Quality check failures
   - Re-enrollment required

---

## 🔒 SECURITY & COMPLIANCE

### Data Privacy
- **GDPR/DPDPA Compliance**: Parental consent for data collection
- **Aadhaar Security**: Encrypted storage, masked display
- **Biometric Data**: Encrypted, consent-based
- **Access Control**: Role-based access to sensitive data

### Audit Trail
- All registration activities logged
- User, timestamp, action recorded
- Document upload/verification tracked
- Changes to student data audited

---

**Status:** Fully Documented - Production Ready  
**Last Updated:** January 17, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade  
**Compliance:** CBSE Guidelines, GDPR, DPDPA, Aadhaar Act

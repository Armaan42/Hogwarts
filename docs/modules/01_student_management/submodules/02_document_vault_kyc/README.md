# DOCUMENT VAULT & KYC - COMPLETE DOCUMENTATION

**Module:** Student Management  
**Submodule Code:** STD-DOC-002  
**Category:** Core Foundation  
**Priority:** Critical (P0)  
**Owner:** Document Management Team

---

## 📋 OVERVIEW

The Document Vault & KYC submodule serves as a centralized, secure repository for all student-related documents including admission papers, academic records, medical certificates, consent forms, and identity proofs. It implements advanced features like version control, expiry tracking, digital verification, OCR, blockchain verification, and AI-powered document validation.

### Purpose

Securely store, manage, verify, and track all student documents throughout their academic lifecycle, ensure regulatory compliance, maintain document authenticity, automate expiry alerts, and provide seamless access to authorized stakeholders.

### Scope

- **Document Storage**: Secure cloud-based storage with encryption
- **Document Verification**: Manual and AI-powered verification
- **Version Control**: Track document updates and history
- **Expiry Management**: Automated alerts and renewal tracking
- **Access Control**: Role-based document access
- **Compliance**: KYC, GDPR, data retention policies

---

## 🎯 KEY FEATURES

### 1. Document Categories & Types

#### 1.1 Mandatory Admission Documents

**Required at Admission (Grade 2+):**
- **Birth Certificate**: Government-issued, original verification required
- **Transfer Certificate (TC)**: From previous school with seal and signature
- **Aadhaar Card**: Student (mandatory for Indian nationals)
- **Passport Size Photos**: 4 recent color photographs (white/blue background)
- **Address Proof**: Electricity bill / Gas bill / Ration card (< 3 months old)
- **Parent ID Proofs**: Aadhaar cards of both parents
- **Caste Certificate**: If claiming SC/ST/OBC reservation (government-issued)
- **Income Certificate**: For EWS/scholarship (< 6 months old)
- **Medical Fitness Certificate**: From registered medical practitioner
- **Vaccination Records**: Complete immunization card

**Required for Grade 1/KG (First-time joiners):**
- **Birth Certificate**: Mandatory
- **Aadhaar Card**: Student + Parents
- **Passport Size Photos**: 4 copies
- **Address Proof**: Current residence proof
- **Parent ID Proofs**: Aadhaar cards
- **Medical Fitness Certificate**: Required
- **Vaccination Records**: Complete immunization
- **Note**: No TC required for first-time school joiners

**Document Verification Example:**
```yaml
Document: Birth Certificate
Student: Aarav Patel (2024/06/0456)
Upload Date: 05/04/2024
Uploaded By: Parent (Mr. Rajesh Patel)
File: birth_cert_aarav_patel.pdf (1.2 MB)
Verification Status: VERIFIED
Verified By: Mrs. Anjali Gupta (Admissions Officer)
Verification Date: 06/04/2024
Verification Method: Original sighted, copy retained
Notes: DOB matches application - 15/08/2012
Issuing Authority: Municipal Corporation, Ahmedabad
Certificate Number: MC/AHM/2012/45678
Validity: Lifetime (no expiry)
```

#### 1.2 Academic Records

**Previous School Documents:**
- **Report Cards**: Last 2 academic years
- **Progress Reports**: Term-wise performance
- **Character Certificate**: From previous school principal
- **School Leaving Certificate**: Official TC with seal
- **Board Exam Certificates**: Class 10/12 marksheets (if applicable)
- **Migration Certificate**: For board change (CBSE to ICSE, etc.)
- **Conduct Certificate**: Behavior and discipline record

**Current School Records (Auto-generated):**
- **Term Report Cards**: Quarterly/semester reports
- **Assessment Records**: Unit tests, assignments
- **Project Submissions**: Digital copies of major projects
- **Competition Certificates**: Academic olympiads, contests
- **Achievement Awards**: Merit certificates, prizes
- **Attendance Records**: Monthly attendance summary
- **Discipline Records**: Behavior incidents (if any)

**Example:**
```yaml
Academic Year: 2023-24
Student: Aarav Patel
Grade: 5 (Previous School)

Documents:
  - Report Card Q1: 92.5% (Uploaded: 10/07/2023)
  - Report Card Q2: 90.8% (Uploaded: 15/10/2023)
  - Report Card Q3: 94.2% (Uploaded: 20/01/2024)
  - Final Report: 92.5% Annual (Uploaded: 30/03/2024)
  - Character Certificate: Excellent (Uploaded: 02/04/2024)
  - TC: DPS/AHM/2024/0456 (Uploaded: 02/04/2024)
```

#### 1.3 Medical Documents

**Health Records:**
- **Medical Fitness Certificate**: Annual renewal required
- **Vaccination Card**: Complete immunization record
- **Blood Test Reports**: If required for specific conditions
- **Allergy Test Results**: Food/environmental allergies
- **Chronic Condition Documentation**: Asthma, diabetes, epilepsy
- **Doctor's Prescription**: For regular medication
- **Health Insurance Policy**: Copy with policy number
- **COVID-19 Vaccination**: Certificate with doses
- **Disability Certificate**: If applicable (for special accommodation)
- **Mental Health Assessment**: If recommended

**Medical Document Example:**
```yaml
Student: Rohan Sharma (2024/06/0457)

Medical Fitness Certificate:
  Issue Date: 01/04/2024
  Expiry Date: 31/03/2025
  Issued By: Dr. Suresh Mehta (MBBS, MD)
  Clinic: Mehta Medical Center, Noida
  Registration No: DMC/2024/12345
  Status: FIT for all school activities
  Restrictions: None
  
Vaccination Record:
  COVID-19: 2 doses + 1 booster (Last: 15/01/2024)
  Hepatitis B: Complete (3 doses)
  MMR: Complete
  Tetanus: Up to date (Last: 10/03/2023)
  
Known Allergies:
  - Peanuts (Severe - Anaphylaxis risk)
  - Dust mites (Mild - Seasonal)
  
Chronic Conditions:
  - Asthma (Mild, well-controlled)
  - Medication: Salbutamol Inhaler (as needed)
  
Health Insurance:
  Provider: Star Health Insurance
  Policy Number: SH/2024/123456789
  Coverage: ₹5,00,000
  Validity: 01/04/2024 - 31/03/2025
  Cashless: Yes
```

#### 1.4 Consent Forms & Agreements

**Parent Consent Documents:**
- **Photo/Video Consent**: School events, website, social media, yearbook
- **Medical Treatment Consent**: Emergency medical procedures
- **Field Trip Consent**: Annual blanket + per-trip specific
- **Data Privacy Consent**: GDPR/DPDPA compliance
- **Transport Consent**: School bus usage and safety
- **Hostel Consent**: Boarding facility (if applicable)
- **Extra-curricular Consent**: Sports, clubs, activities
- **Swimming Pool Consent**: If school has pool
- **Online Learning Consent**: Virtual classes, recordings
- **Biometric Consent**: Fingerprint/facial recognition
- **Third-party Sharing Consent**: Educational platforms

**Digital Consent Tracking:**
```yaml
Consent Type: Photo/Video Usage
Student: Aarav Patel (2024/06/0456)
Granted By: Mr. Rajesh Patel (Father)
Relationship: Father
Consent Date: 01/04/2024
Valid From: 01/04/2024
Valid Till: 31/03/2025
Renewal: Annual

Scope:
  - School website: YES
  - Social media (Facebook, Instagram): YES
  - Yearbook: YES
  - Newspaper/Media: NO
  - Commercial use: NO
  
Revocable: Yes (can be withdrawn anytime)
Status: ACTIVE
Digital Signature: Captured via Parent Portal
IP Address: 103.x.x.x
Timestamp: 01/04/2024 10:30:45 AM
```

#### 1.5 Identity Proofs & KYC Documents

**Student Identity Documents:**
- **Aadhaar Card**: 12-digit unique ID (mandatory for Indian students)
- **School ID Card**: With photo, admission number, QR code
- **Library Card**: For book issue/return
- **Transport Pass**: Bus route and stop details
- **Hostel ID**: Room number and block (if boarding)
- **Exam Hall Admit Card**: For board exams
- **Passport**: For international students

**Parent/Guardian ID Documents:**
- **Father's Aadhaar**: Mandatory
- **Mother's Aadhaar**: Mandatory
- **Guardian Aadhaar**: If applicable
- **PAN Card**: For fee payment, tax deduction
- **Driving License**: Alternative ID proof
- **Passport**: For foreign nationals
- **Voter ID Card**: Alternative ID proof
- **Office ID Card**: For employment verification

**KYC Verification:**
```yaml
Student: Aarav Patel
KYC Status: VERIFIED

Student Documents:
  Aadhaar: 1234-5678-9012
    Status: Verified via UIDAI API
    Name Match: 100%
    DOB Match: 15/08/2012 ✓
    
Father Documents:
  Name: Rajesh Kumar Patel
  Aadhaar: 9876-5432-1098
    Status: Verified
  PAN: ABCDE1234F
    Status: Verified via Income Tax API
    Name Match: 100%
    
Mother Documents:
  Name: Priya Rajesh Patel
  Aadhaar: 9876-5432-1099
    Status: Verified
  PAN: ABCDE1235F
    Status: Verified

KYC Completion: 100%
Verification Date: 06/04/2024
Next Review: 01/04/2025
```

---

### 2. Advanced Document Features

#### 2.1 Version Control & History

**Document Versioning System:**
```yaml
Document: Medical Fitness Certificate
Student: Rohan Sharma (2024/06/0457)

Version History:
  Version 1:
    Upload Date: 01/04/2023
    Issue Date: 01/04/2023
    Expiry Date: 31/03/2024
    Status: EXPIRED
    File: medical_cert_v1.pdf
    Uploaded By: Parent
    
  Version 2:
    Upload Date: 28/03/2024
    Issue Date: 01/04/2024
    Expiry Date: 31/03/2025
    Status: CURRENT (Active)
    File: medical_cert_v2.pdf
    Uploaded By: Parent
    Changes: New certificate for 2024-25
    
  Version 3:
    Status: PENDING (Due: 01/04/2025)
    
Change Log:
  - 01/04/2023: Initial upload (v1)
  - 31/03/2024: Document expired, alert sent
  - 28/03/2024: New version uploaded (v2)
  - 29/03/2024: Version 2 verified
  - 01/04/2024: Version 2 activated
```

**Version Control Features:**
- **Automatic Versioning**: New upload creates new version
- **History Preservation**: All versions retained for audit
- **Rollback Capability**: Revert to previous version if needed
- **Change Tracking**: Log all modifications
- **Comparison Tool**: Compare versions side-by-side

#### 2.2 Expiry Tracking & Alerts

**Expiry Management:**
```yaml
Document Expiry Schedule:

Medical Fitness Certificate:
  Current Expiry: 31/03/2025
  Days Remaining: 165
  Alert Schedule:
    - 30 days before (01/03/2025): Email to parent
    - 15 days before (16/03/2025): SMS reminder
    - 7 days before (24/03/2025): Email + SMS warning
    - On expiry (31/03/2025): Document marked EXPIRED
    - 7 days after (07/04/2025): Final notice
    
Vaccination Certificate:
  COVID Booster Due: 15/07/2024
  Days Remaining: 90
  Alert: Reminder sent 30 days before
  
Health Insurance:
  Policy Expiry: 31/03/2025
  Days Remaining: 165
  Alert: Renewal reminder at 60 days
  
Consent Forms:
  Photo Consent: 31/03/2025 (Annual renewal)
  Field Trip: 31/03/2025 (Annual renewal)
  Transport: 31/03/2025 (Annual renewal)
```

**Alert Automation:**
- **Email Alerts**: Detailed renewal instructions
- **SMS Alerts**: Quick reminders with deadlines
- **Portal Notifications**: In-app alerts
- **WhatsApp Messages**: Instant notifications
- **Dashboard Warnings**: Red flags for expired docs

#### 2.3 OCR & AI Document Verification

**Optical Character Recognition (OCR):**
```yaml
Document: Birth Certificate (Scanned PDF)

OCR Extraction:
  Name: AARAV KUMAR PATEL
  Date of Birth: 15/08/2012
  Place of Birth: Ahmedabad, Gujarat
  Father's Name: RAJESH KUMAR PATEL
  Mother's Name: PRIYA RAJESH PATEL
  Certificate Number: MC/AHM/2012/45678
  Issuing Authority: Municipal Corporation, Ahmedabad
  Issue Date: 20/08/2012
  
AI Validation:
  Name Match with Application: 100% ✓
  DOB Match: 15/08/2012 ✓
  Parent Names Match: 100% ✓
  Document Authenticity: VERIFIED ✓
  Tampering Detection: No tampering detected ✓
  Quality Score: 95/100 (Excellent)
  
Auto-Population:
  Student Name: Auto-filled from OCR
  DOB: Auto-filled and validated
  Parent Names: Auto-filled
  Birth Certificate Number: Extracted and stored
```

**AI-Powered Features:**
- **Text Extraction**: Auto-extract data from documents
- **Data Validation**: Cross-verify with application
- **Fraud Detection**: Identify fake/tampered documents
- **Quality Check**: Assess document clarity
- **Auto-Population**: Fill forms from scanned docs

#### 2.4 Digital Signature & Blockchain Verification

**Digital Signature Integration:**
```yaml
Document: Transfer Certificate
Student: Aarav Patel
School: Delhi Public School, Ahmedabad

Digital Signature:
  Signed By: Dr. Ramesh Kumar (Principal)
  Designation: Principal, DPS Ahmedabad
  Signature Type: DSC (Digital Signature Certificate)
  Certificate Authority: eMudhra
  Signing Date: 02/04/2024
  Signature Valid: YES ✓
  Certificate Valid Till: 15/03/2026
  
Verification:
  Signature Verified: YES ✓
  Document Integrity: Not tampered ✓
  Timestamp: 02/04/2024 03:45:30 PM
  Hash: a3f5b2c8d9e1f4g7h8i9j0k1l2m3n4o5
```

**Blockchain Verification:**
```yaml
Document: Final Marksheet (Grade 12)
Student: Priya Sharma
Board: CBSE

Blockchain Record:
  Transaction ID: 0x7f3a9b2c5d8e1f4a6b9c2d5e8f1a4b7c
  Block Number: 1234567
  Blockchain: Ethereum (Private Network)
  Timestamp: 15/05/2024 10:30:00 AM
  
Document Hash: SHA-256
  Hash: b4c7d2e5f8a1b4c7d2e5f8a1b4c7d2e5
  
Verification:
  Document Authentic: YES ✓
  Tamper-Proof: YES ✓
  Publicly Verifiable: YES
  QR Code: Generated for instant verification
  
Verification URL:
  https://verify.hogwarts.edu/doc/0x7f3a9b2c5d8e1f4a6b9c2d5e8f1a4b7c
```

---

### 3. Document Storage & Security

#### 3.1 Secure Cloud Storage

**Storage Architecture:**
```yaml
Storage Provider: AWS S3 / Google Cloud Storage
Encryption: AES-256 (at rest)
Transfer: TLS 1.3 (in transit)
Backup: Daily automated backups
Redundancy: Multi-region replication
Access: Role-based access control (RBAC)

Storage Structure:
/documents
  /2024-25
    /grade-06
      /2024-06-0456_aarav_patel
        /admission
          - birth_certificate.pdf
          - transfer_certificate.pdf
          - aadhaar_card.pdf
          - photos.zip
        /medical
          - fitness_cert_2024.pdf
          - vaccination_card.pdf
          - insurance_policy.pdf
        /academic
          - report_card_q1.pdf
          - report_card_q2.pdf
        /consent
          - photo_consent_2024.pdf
          - transport_consent_2024.pdf
```

**Security Features:**
- **Encryption**: All files encrypted
- **Access Logs**: Track who accessed what
- **Virus Scanning**: Auto-scan all uploads
- **Watermarking**: Add school watermark
- **Download Tracking**: Log all downloads

#### 3.2 Access Control & Permissions

**Role-Based Access:**
```yaml
Document: Medical Fitness Certificate
Student: Aarav Patel

Access Permissions:
  Parents (Mr. & Mrs. Patel):
    - View: YES
    - Download: YES
    - Upload: YES
    - Delete: NO
    - Share: NO
    
  Student (Aarav):
    - View: YES (Age 10+)
    - Download: YES
    - Upload: NO
    - Delete: NO
    
  Class Teacher (Mrs. Singh):
    - View: YES
    - Download: YES
    - Upload: NO
    - Delete: NO
    
  School Nurse:
    - View: YES (Medical docs only)
    - Download: YES
    - Upload: YES
    - Delete: NO
    
  Admissions Officer:
    - View: YES
    - Download: YES
    - Upload: YES
    - Verify: YES
    - Delete: NO
    
  Principal:
    - View: YES
    - Download: YES
    - Upload: YES
    - Verify: YES
    - Delete: YES (with approval)
    
  System Admin:
    - Full Access: YES
    - Audit Logs: YES
```

---

## 📊 DATABASE SCHEMA

### Documents Table

```sql
CREATE TABLE student_documents (
  -- Primary Key
  document_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Student Reference
  student_id INT NOT NULL,
  admission_number VARCHAR(20),
  
  -- Document Classification
  document_type ENUM(
    'BIRTH_CERTIFICATE', 'TRANSFER_CERTIFICATE', 'AADHAAR_CARD',
    'MEDICAL_FITNESS', 'VACCINATION_CARD', 'REPORT_CARD',
    'CHARACTER_CERT', 'CASTE_CERT', 'INCOME_CERT',
    'CONSENT_FORM', 'INSURANCE_POLICY', 'PASSPORT',
    'PARENT_ID', 'ADDRESS_PROOF', 'OTHER'
  ) NOT NULL,
  
  document_category ENUM(
    'ADMISSION', 'ACADEMIC', 'MEDICAL', 
    'CONSENT', 'IDENTITY', 'FINANCIAL'
  ) NOT NULL,
  
  document_subcategory VARCHAR(100),
  
  -- File Information
  file_name VARCHAR(255) NOT NULL,
  file_path VARCHAR(500) NOT NULL,
  file_size_bytes BIGINT,
  file_format VARCHAR(10),
  file_hash VARCHAR(64), -- SHA-256 hash
  
  -- Upload Details
  upload_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  uploaded_by INT,
  upload_source ENUM('PARENT_PORTAL', 'ADMIN_PANEL', 'MOBILE_APP', 'EMAIL', 'SCAN') DEFAULT 'ADMIN_PANEL',
  
  -- Verification
  verification_status ENUM('PENDING', 'VERIFIED', 'REJECTED', 'EXPIRED') DEFAULT 'PENDING',
  verified_by INT,
  verification_date TIMESTAMP,
  verification_method ENUM('MANUAL', 'OCR', 'AI', 'BLOCKCHAIN') DEFAULT 'MANUAL',
  verification_notes TEXT,
  rejection_reason TEXT,
  
  -- Version Control
  version_number INT DEFAULT 1,
  is_current_version BOOLEAN DEFAULT TRUE,
  previous_version_id INT,
  superseded_by_id INT,
  
  -- Validity Period
  issue_date DATE,
  expiry_date DATE,
  is_lifetime_valid BOOLEAN DEFAULT FALSE,
  
  -- Document Status
  status ENUM('ACTIVE', 'EXPIRED', 'SUPERSEDED', 'DELETED') DEFAULT 'ACTIVE',
  
  -- OCR & AI Data
  ocr_extracted_text TEXT,
  ocr_confidence_score DECIMAL(5,2),
  ai_validation_score DECIMAL(5,2),
  tampering_detected BOOLEAN DEFAULT FALSE,
  
  -- Digital Signature
  digitally_signed BOOLEAN DEFAULT FALSE,
  signature_certificate_id VARCHAR(100),
  signature_timestamp TIMESTAMP,
  
  -- Blockchain
  blockchain_hash VARCHAR(66),
  blockchain_transaction_id VARCHAR(100),
  blockchain_verified BOOLEAN DEFAULT FALSE,
  
  -- Access Control
  access_level ENUM('PUBLIC', 'RESTRICTED', 'CONFIDENTIAL', 'HIGHLY_CONFIDENTIAL') DEFAULT 'RESTRICTED',
  
  -- Metadata
  tags VARCHAR(500),
  description TEXT,
  remarks TEXT,
  
  -- Audit
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  deleted_date TIMESTAMP,
  deleted_by INT,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_document_type (document_type),
  INDEX idx_verification_status (verification_status),
  INDEX idx_expiry_date (expiry_date),
  INDEX idx_status (status),
  INDEX idx_current_version (is_current_version),
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  FOREIGN KEY (uploaded_by) REFERENCES users(user_id),
  FOREIGN KEY (verified_by) REFERENCES users(user_id)
);
```

### Document Access Log Table

```sql
CREATE TABLE document_access_log (
  log_id BIGINT PRIMARY KEY AUTO_INCREMENT,
  document_id INT NOT NULL,
  student_id INT NOT NULL,
  
  -- Access Details
  accessed_by INT NOT NULL,
  access_type ENUM('VIEW', 'DOWNLOAD', 'UPLOAD', 'EDIT', 'DELETE', 'SHARE') NOT NULL,
  access_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Session Info
  ip_address VARCHAR(45),
  user_agent TEXT,
  device_type VARCHAR(50),
  
  -- Result
  access_granted BOOLEAN,
  denial_reason VARCHAR(255),
  
  -- Metadata
  duration_seconds INT,
  
  -- Foreign Keys
  FOREIGN KEY (document_id) REFERENCES student_documents(document_id) ON DELETE CASCADE,
  FOREIGN KEY (student_id) REFERENCES students(student_id),
  FOREIGN KEY (accessed_by) REFERENCES users(user_id),
  
  -- Indexes
  INDEX idx_document_id (document_id),
  INDEX idx_accessed_by (accessed_by),
  INDEX idx_timestamp (access_timestamp)
);
```

### Document Expiry Alerts Table

```sql
CREATE TABLE document_expiry_alerts (
  alert_id INT PRIMARY KEY AUTO_INCREMENT,
  document_id INT NOT NULL,
  student_id INT NOT NULL,
  
  -- Alert Details
  alert_type ENUM('30_DAYS', '15_DAYS', '7_DAYS', 'EXPIRED', 'OVERDUE') NOT NULL,
  alert_date DATE NOT NULL,
  expiry_date DATE NOT NULL,
  days_to_expiry INT,
  
  -- Notification
  notification_sent BOOLEAN DEFAULT FALSE,
  notification_method ENUM('EMAIL', 'SMS', 'WHATSAPP', 'PORTAL', 'ALL'),
  sent_to VARCHAR(255),
  sent_timestamp TIMESTAMP,
  
  -- Response
  acknowledged BOOLEAN DEFAULT FALSE,
  acknowledged_by INT,
  acknowledged_timestamp TIMESTAMP,
  
  -- Document Renewed
  renewed BOOLEAN DEFAULT FALSE,
  new_document_id INT,
  
  -- Foreign Keys
  FOREIGN KEY (document_id) REFERENCES student_documents(document_id) ON DELETE CASCADE,
  FOREIGN KEY (student_id) REFERENCES students(student_id),
  
  -- Indexes
  INDEX idx_alert_date (alert_date),
  INDEX idx_notification_sent (notification_sent)
);
```

---

## ⚙️ BUSINESS RULES & LOGIC

### Rule 1: Document Upload Validation

```javascript
FUNCTION upload_document(student, document_type, file, metadata) {
  // Step 1: Validate file format
  allowed_formats = ["PDF", "JPG", "JPEG", "PNG", "DOC", "DOCX"]
  file_extension = GET_FILE_EXTENSION(file.name).toUpperCase()
  
  IF file_extension NOT IN allowed_formats {
    RETURN {
      success: FALSE,
      error: "Invalid file format. Allowed: PDF, JPG, PNG, DOC, DOCX"
    }
  }
  
  // Step 2: Validate file size (max 10MB)
  max_size = 10 * 1024 * 1024  // 10 MB
  IF file.size > max_size {
    RETURN {
      success: FALSE,
      error: "File too large. Maximum size: 10 MB"
    }
  }
  
  // Step 3: Virus scan
  scan_result = VIRUS_SCAN(file)
  IF scan_result.infected {
    LOG_SECURITY_ALERT("Virus detected in upload", {
      student_id: student.id,
      file_name: file.name,
      virus: scan_result.virus_name
    })
    RETURN {
      success: FALSE,
      error: "File contains malware. Upload blocked."
    }
  }
  
  // Step 4: Check for existing document
  existing_doc = QUERY("
    SELECT * FROM student_documents 
    WHERE student_id = ? 
    AND document_type = ? 
    AND is_current_version = TRUE
  ", [student.id, document_type])
  
  version_number = 1
  IF existing_doc.exists() {
    // Mark old version as superseded
    UPDATE student_documents SET
      is_current_version = FALSE,
      status = 'SUPERSEDED',
      superseded_by_id = NULL  // Will be updated after new doc created
    WHERE document_id = existing_doc.document_id
    
    version_number = existing_doc.version_number + 1
  }
  
  // Step 5: Generate secure file path
  year = GET_ACADEMIC_YEAR()
  grade = student.current_grade
  admission_no = student.admission_number.replace('/', '_')
  category = GET_DOCUMENT_CATEGORY(document_type)
  timestamp = UNIX_TIMESTAMP()
  
  file_path = `/documents/${year}/grade-${grade}/${admission_no}/${category}/${timestamp}_${file.name}`
  
  // Step 6: Calculate file hash (for integrity)
  file_hash = SHA256(file.content)
  
  // Step 7: Upload to cloud storage
  upload_result = UPLOAD_TO_CLOUD(file, file_path)
  IF NOT upload_result.success {
    RETURN {
      success: FALSE,
      error: "Failed to upload file to storage"
    }
  }
  
  // Step 8: Perform OCR (if PDF/image)
  ocr_data = NULL
  IF file_extension IN ["PDF", "JPG", "JPEG", "PNG"] {
    ocr_data = PERFORM_OCR(file)
  }
  
  // Step 9: Create document record
  document = INSERT INTO student_documents (
    student_id,
    admission_number,
    document_type,
    document_category,
    file_name,
    file_path,
    file_size_bytes,
    file_format,
    file_hash,
    uploaded_by,
    upload_source,
    version_number,
    is_current_version,
    previous_version_id,
    issue_date,
    expiry_date,
    is_lifetime_valid,
    ocr_extracted_text,
    ocr_confidence_score,
    status
  ) VALUES (
    student.id,
    student.admission_number,
    document_type,
    GET_DOCUMENT_CATEGORY(document_type),
    file.name,
    file_path,
    file.size,
    file_extension,
    file_hash,
    CURRENT_USER_ID,
    metadata.upload_source || 'ADMIN_PANEL',
    version_number,
    TRUE,
    existing_doc.document_id || NULL,
    metadata.issue_date,
    metadata.expiry_date,
    metadata.is_lifetime_valid || FALSE,
    ocr_data.text,
    ocr_data.confidence,
    'ACTIVE'
  )
  
  // Step 10: Update superseded document reference
  IF existing_doc.exists() {
    UPDATE student_documents SET
      superseded_by_id = document.document_id
    WHERE document_id = existing_doc.document_id
  }
  
  // Step 11: Trigger AI verification (async)
  IF ocr_data {
    QUEUE_JOB("ai_document_verification", {
      document_id: document.document_id,
      ocr_data: ocr_data
    })
  }
  
  // Step 12: Notify verification team
  SEND_NOTIFICATION("admissions_team", {
    title: "New Document Uploaded",
    message: `${document_type} uploaded for ${student.full_name}`,
    document_id: document.document_id,
    priority: "NORMAL"
  })
  
  // Step 13: Log access
  LOG_DOCUMENT_ACCESS(document.document_id, "UPLOAD", CURRENT_USER_ID)
  
  // Step 14: Setup expiry alerts (if applicable)
  IF metadata.expiry_date {
    SETUP_EXPIRY_ALERTS(document.document_id, metadata.expiry_date)
  }
  
  RETURN {
    success: TRUE,
    document_id: document.document_id,
    version: version_number,
    message: "Document uploaded successfully. Pending verification."
  }
}
```

### Rule 2: Expiry Alert System

```javascript
FUNCTION check_document_expiry() {
  // Run daily at 6:00 AM
  today = CURRENT_DATE()
  
  // Get all active documents with expiry dates
  expiring_docs = QUERY("
    SELECT d.*, s.full_name, s.admission_number,
           f.father_email, f.father_mobile, f.mother_email, f.mother_mobile,
           DATEDIFF(d.expiry_date, ?) as days_to_expiry
    FROM student_documents d
    JOIN students s ON d.student_id = s.student_id
    JOIN families f ON s.family_id = f.family_id
    WHERE d.status = 'ACTIVE'
    AND d.is_current_version = TRUE
    AND d.expiry_date IS NOT NULL
    AND d.expiry_date >= ?
    AND d.expiry_date <= DATE_ADD(?, INTERVAL 30 DAY)
  ", [today, today, today])
  
  FOR each doc IN expiring_docs {
    days_remaining = doc.days_to_expiry
    
    // Determine alert type
    alert_type = NULL
    send_alert = FALSE
    
    IF days_remaining == 30 {
      alert_type = "30_DAYS"
      send_alert = TRUE
    } ELSE IF days_remaining == 15 {
      alert_type = "15_DAYS"
      send_alert = TRUE
    } ELSE IF days_remaining == 7 {
      alert_type = "7_DAYS"
      send_alert = TRUE
    } ELSE IF days_remaining == 0 {
      alert_type = "EXPIRED"
      send_alert = TRUE
      
      // Mark document as expired
      UPDATE student_documents SET
        status = 'EXPIRED',
        verification_status = 'EXPIRED'
      WHERE document_id = doc.document_id
    }
    
    IF send_alert {
      // Check if alert already sent
      existing_alert = QUERY("
        SELECT * FROM document_expiry_alerts
        WHERE document_id = ?
        AND alert_type = ?
        AND alert_date = ?
      ", [doc.document_id, alert_type, today])
      
      IF NOT existing_alert.exists() {
        // Create alert record
        alert = INSERT INTO document_expiry_alerts (
          document_id,
          student_id,
          alert_type,
          alert_date,
          expiry_date,
          days_to_expiry
        ) VALUES (
          doc.document_id,
          doc.student_id,
          alert_type,
          today,
          doc.expiry_date,
          days_remaining
        )
        
        // Send notifications
        IF days_remaining >= 7 {
          // Email notification
          SEND_EMAIL(doc.father_email, {
            subject: `Document Expiring: ${doc.document_type}`,
            template: "document_expiry_reminder",
            data: {
              student_name: doc.full_name,
              admission_number: doc.admission_number,
              document_type: doc.document_type,
              expiry_date: doc.expiry_date,
              days_remaining: days_remaining,
              renewal_instructions: GET_RENEWAL_INSTRUCTIONS(doc.document_type)
            }
          })
          
          // Also send to mother
          IF doc.mother_email != doc.father_email {
            SEND_EMAIL(doc.mother_email, same_email_params)
          }
        }
        
        IF days_remaining <= 15 {
          // SMS notification (more urgent)
          sms_message = `
            URGENT: ${doc.document_type} for ${doc.full_name} 
            expires in ${days_remaining} days (${doc.expiry_date}). 
            Please renew immediately. - Hogwarts School
          `
          
          SEND_SMS(doc.father_mobile, sms_message)
          IF doc.mother_mobile != doc.father_mobile {
            SEND_SMS(doc.mother_mobile, sms_message)
          }
        }
        
        IF days_remaining <= 7 {
          // WhatsApp notification (most urgent)
          whatsapp_message = `
            🚨 URGENT ALERT 🚨
            
            Document: ${doc.document_type}
            Student: ${doc.full_name} (${doc.admission_number})
            Expires: ${doc.expiry_date} (${days_remaining} days)
            
            Please upload renewed document immediately via Parent Portal.
            
            Login: https://portal.hogwarts.edu
          `
          
          SEND_WHATSAPP(doc.father_mobile, whatsapp_message)
        }
        
        IF days_remaining == 0 {
          // Document expired - critical alert
          SEND_EMAIL(doc.father_email, {
            subject: `EXPIRED: ${doc.document_type} - Immediate Action Required`,
            template: "document_expired",
            priority: "HIGH"
          })
          
          SEND_SMS(doc.father_mobile, 
            `EXPIRED: ${doc.document_type} for ${doc.full_name}. Upload renewed document urgently.`)
          
          // Notify school admin
          SEND_NOTIFICATION("admissions_team", {
            title: "Document Expired",
            message: `${doc.document_type} expired for ${doc.full_name}`,
            priority: "HIGH",
            student_id: doc.student_id
          })
        }
        
        // Update alert record
        UPDATE document_expiry_alerts SET
          notification_sent = TRUE,
          notification_method = 'ALL',
          sent_to = doc.father_email,
          sent_timestamp = NOW()
        WHERE alert_id = alert.alert_id
      }
    }
  }
  
  LOG_SYSTEM("Document expiry check completed", {
    documents_checked: expiring_docs.length,
    alerts_sent: COUNT(alerts_sent)
  })
}
```

### Rule 3: AI Document Verification

```javascript
FUNCTION ai_verify_document(document_id) {
  // Get document details
  doc = QUERY("
    SELECT d.*, s.full_name, s.date_of_birth, 
           f.father_name, f.mother_name
    FROM student_documents d
    JOIN students s ON d.student_id = s.student_id
    JOIN families f ON s.family_id = f.family_id
    WHERE d.document_id = ?
  ", [document_id])
  
  IF NOT doc.ocr_extracted_text {
    RETURN {success: FALSE, error: "No OCR data available"}
  }
  
  verification_result = {
    document_id: document_id,
    checks_passed: 0,
    checks_failed: 0,
    overall_score: 0,
    details: []
  }
  
  // Check 1: Name Matching
  IF doc.document_type IN ['BIRTH_CERTIFICATE', 'AADHAAR_CARD', 'TRANSFER_CERTIFICATE'] {
    extracted_name = EXTRACT_NAME_FROM_OCR(doc.ocr_extracted_text)
    similarity = CALCULATE_STRING_SIMILARITY(extracted_name, doc.full_name)
    
    IF similarity >= 0.85 {  // 85% match threshold
      verification_result.checks_passed++
      verification_result.details.push({
        check: "Name Match",
        status: "PASS",
        confidence: similarity * 100,
        extracted: extracted_name,
        expected: doc.full_name
      })
    } ELSE {
      verification_result.checks_failed++
      verification_result.details.push({
        check: "Name Match",
        status: "FAIL",
        confidence: similarity * 100,
        extracted: extracted_name,
        expected: doc.full_name,
        action: "Manual verification required"
      })
    }
  }
  
  // Check 2: Date of Birth Matching
  IF doc.document_type IN ['BIRTH_CERTIFICATE', 'AADHAAR_CARD'] {
    extracted_dob = EXTRACT_DOB_FROM_OCR(doc.ocr_extracted_text)
    
    IF extracted_dob == doc.date_of_birth {
      verification_result.checks_passed++
      verification_result.details.push({
        check: "DOB Match",
        status: "PASS",
        extracted: extracted_dob,
        expected: doc.date_of_birth
      })
    } ELSE {
      verification_result.checks_failed++
      verification_result.details.push({
        check: "DOB Match",
        status: "FAIL",
        extracted: extracted_dob,
        expected: doc.date_of_birth,
        action: "Manual verification required"
      })
    }
  }
  
  // Check 3: Parent Name Matching
  IF doc.document_type == 'BIRTH_CERTIFICATE' {
    extracted_father = EXTRACT_FATHER_NAME_FROM_OCR(doc.ocr_extracted_text)
    extracted_mother = EXTRACT_MOTHER_NAME_FROM_OCR(doc.ocr_extracted_text)
    
    father_match = CALCULATE_STRING_SIMILARITY(extracted_father, doc.father_name)
    mother_match = CALCULATE_STRING_SIMILARITY(extracted_mother, doc.mother_name)
    
    IF father_match >= 0.85 AND mother_match >= 0.85 {
      verification_result.checks_passed++
      verification_result.details.push({
        check: "Parent Names Match",
        status: "PASS",
        father_confidence: father_match * 100,
        mother_confidence: mother_match * 100
      })
    } ELSE {
      verification_result.checks_failed++
      verification_result.details.push({
        check: "Parent Names Match",
        status: "FAIL",
        father_confidence: father_match * 100,
        mother_confidence: mother_match * 100,
        action: "Manual verification required"
      })
    }
  }
  
  // Check 4: Document Tampering Detection
  tampering_score = DETECT_TAMPERING(doc.file_path)
  
  IF tampering_score < 0.3 {  // Low tampering probability
    verification_result.checks_passed++
    verification_result.details.push({
      check: "Tampering Detection",
      status: "PASS",
      tampering_probability: tampering_score * 100,
      message: "No signs of tampering detected"
    })
  } ELSE {
    verification_result.checks_failed++
    verification_result.details.push({
      check: "Tampering Detection",
      status: "FAIL",
      tampering_probability: tampering_score * 100,
      message: "Possible tampering detected",
      action: "Flag for manual review"
    })
    
    // Update document record
    UPDATE student_documents SET
      tampering_detected = TRUE
    WHERE document_id = document_id
  }
  
  // Calculate overall score
  total_checks = verification_result.checks_passed + verification_result.checks_failed
  verification_result.overall_score = (verification_result.checks_passed / total_checks) * 100
  
  // Update document with AI validation score
  UPDATE student_documents SET
    ai_validation_score = verification_result.overall_score,
    verification_method = 'AI'
  WHERE document_id = document_id
  
  // Auto-verify if score is high enough
  IF verification_result.overall_score >= 90 AND verification_result.checks_failed == 0 {
    UPDATE student_documents SET
      verification_status = 'VERIFIED',
      verification_date = NOW(),
      verification_notes = 'Auto-verified by AI (Score: ' + verification_result.overall_score + '%)'
    WHERE document_id = document_id
    
    // Notify parent
    SEND_EMAIL(doc.parent_email, {
      subject: "Document Verified",
      message: `${doc.document_type} has been verified successfully.`
    })
  } ELSE {
    // Flag for manual verification
    SEND_NOTIFICATION("admissions_team", {
      title: "Document Requires Manual Verification",
      message: `AI verification inconclusive for ${doc.document_type} (Score: ${verification_result.overall_score}%)`,
      document_id: document_id,
      priority: "NORMAL"
    })
  }
  
  RETURN verification_result
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Student Registration Module**
   - Sends: Document verification status
   - Purpose: Complete registration only when all docs verified

2. **Fee Management Module**
   - Sends: Income certificate, caste certificate
   - Purpose: Apply fee concessions/scholarships

3. **Health & Wellness Module**
   - Sends: Medical fitness, vaccination records
   - Purpose: Health profile creation

4. **Communication Module**
   - Sends: Document expiry alerts
   - Purpose: Notify parents via email/SMS

5. **Compliance Module**
   - Sends: All document records
   - Purpose: Regulatory compliance reporting

### Inbound Integrations

1. **Parent Portal**
   - Receives: Document uploads from parents
   - Trigger: Self-service document submission

2. **Admissions Module**
   - Receives: Scanned documents during admission
   - Trigger: New student admission

3. **Medical Team**
   - Receives: Updated health certificates
   - Trigger: Annual medical checkup

---

## 👥 USER WORKFLOWS

### Workflow 1: Parent Uploads Document via Portal

**Actor:** Parent (Mr. Rajesh Patel)  
**Duration:** 5-10 minutes  
**Device:** Mobile/Desktop

**Steps:**

1. **Login to Parent Portal**
   - URL: https://portal.hogwarts.edu
   - Username: rajesh.patel@gmail.com
   - Password: ********
   - 2FA: OTP sent to mobile

2. **Navigate to Documents**
   - Dashboard → My Child → Aarav Patel
   - Click "Documents" tab
   - View current documents list

3. **Check Expiring Documents**
   - System shows: "Medical Fitness Certificate expires in 15 days"
   - Alert banner: Red warning

4. **Upload New Document**
   - Click "Upload New Document"
   - Select Document Type: "Medical Fitness Certificate"
   - Choose file: medical_cert_2024.pdf (2.3 MB)
   - Enter Issue Date: 01/04/2024
   - Enter Expiry Date: 31/03/2025
   - Add Notes: "Annual checkup completed"

5. **System Validation**
   - File format: PDF ✓
   - File size: 2.3 MB ✓ (under 10 MB limit)
   - Virus scan: Clean ✓

6. **Upload Processing**
   - Progress bar: 100%
   - File uploaded to cloud storage
   - OCR processing started
   - Document ID generated: DOC-2024-12345

7. **Version Management**
   - System detects existing certificate (v1)
   - Marks old version as "SUPERSEDED"
   - Creates new version (v2) as "CURRENT"

8. **Verification Queue**
   - Status: "Pending Verification"
   - Notification sent to admissions team
   - Expected verification: Within 24 hours

9. **Confirmation**
   - Success message: "Document uploaded successfully"
   - Email confirmation sent
   - SMS: "Medical certificate uploaded. Verification pending."

10. **AI Verification (Background)**
    - OCR extracts: Student name, DOB, doctor details
    - AI validates: Name match 98%, DOB match 100%
    - Tampering check: No tampering detected
    - AI Score: 95% (Auto-verified)

11. **Auto-Verification**
    - System auto-verifies (AI score > 90%)
    - Status updated: "VERIFIED"
    - Parent notified: "Document verified successfully"

12. **Expiry Alert Setup**
    - System schedules alerts:
      - 01/03/2025: 30-day reminder
      - 16/03/2025: 15-day reminder
      - 24/03/2025: 7-day warning

**Expected Outcome:**
- New medical certificate uploaded and verified
- Old certificate superseded
- Expiry alerts scheduled
- Parent notified of successful verification

---

### Workflow 2: Bulk Document Upload by Admin

**Actor:** Admissions Officer (Mrs. Anjali Gupta)  
**Duration:** 30-45 minutes  
**Scenario:** 50 new admissions, bulk document upload

**Steps:**

1. **Prepare Documents**
   - Collect physical documents from 50 students
   - Scan all documents (batch scanning)
   - Organize files by student admission number

2. **Login to Admin Panel**
   - Navigate: Admin Panel → Documents → Bulk Upload

3. **Select Bulk Upload Mode**
   - Choose: "Bulk Upload via CSV"
   - Download CSV template

4. **Prepare CSV File**
   ```csv
   admission_number,document_type,file_name,issue_date,expiry_date
   2024/06/0456,BIRTH_CERTIFICATE,2024_06_0456_birth.pdf,15/08/2012,
   2024/06/0456,TRANSFER_CERTIFICATE,2024_06_0456_tc.pdf,02/04/2024,
   2024/06/0456,AADHAAR_CARD,2024_06_0456_aadhaar.pdf,10/03/2020,
   2024/06/0457,BIRTH_CERTIFICATE,2024_06_0457_birth.pdf,20/05/2012,
   ...
   ```

5. **Upload CSV + Files**
   - Upload CSV file
   - Upload ZIP file with all scanned documents
   - System validates CSV format

6. **Processing**
   - System extracts ZIP file
   - Matches files with CSV entries
   - Validates each file
   - Progress: 45/50 processed

7. **Review Errors**
   - 5 files failed:
     - File not found: 2
     - Invalid format: 1
     - File too large: 2
   - Download error report

8. **Fix and Re-upload**
   - Fix errors
   - Re-upload failed files individually
   - All 50 students now have documents

9. **Trigger Verification**
   - Click "Send for Verification"
   - AI verification runs for all documents
   - 40 auto-verified, 10 flagged for manual review

10. **Manual Verification**
    - Review 10 flagged documents
    - Verify manually
    - Mark as "VERIFIED"

**Expected Outcome:**
- 50 students' documents uploaded in bulk
- 40 auto-verified by AI
- 10 manually verified
- All documents organized and accessible

---

### Workflow 3: Document Expiry Alert & Renewal

**Actor:** System (Automated) + Parent  
**Duration:** Ongoing  
**Trigger:** Document approaching expiry

**Steps:**

1. **Daily Expiry Check (6:00 AM)**
   - System scans all documents
   - Finds: Medical certificate expires in 30 days
   - Student: Rohan Sharma (2024/06/0457)

2. **30-Day Alert**
   - Email sent to parent:
     ```
     Subject: Document Expiring Soon
     
     Dear Mr. Sharma,
     
     The Medical Fitness Certificate for Rohan Sharma 
     will expire on 31/03/2025 (30 days from now).
     
     Please renew and upload the new certificate via 
     Parent Portal: https://portal.hogwarts.edu
     
     Regards,
     Hogwarts School
     ```

3. **15-Day Alert**
   - Email + SMS sent:
     ```
     SMS: Medical certificate for Rohan expires in 15 days (31/03/2025). 
     Please renew urgently. - Hogwarts
     ```

4. **7-Day Alert**
   - Email + SMS + WhatsApp:
     ```
     🚨 URGENT: Medical certificate expires in 7 days!
     Upload renewed certificate immediately.
     ```

5. **Parent Action**
   - Parent takes child for medical checkup
   - Doctor issues new certificate
   - Parent logs into portal
   - Uploads new certificate

6. **System Processing**
   - New certificate uploaded
   - Old certificate marked "SUPERSEDED"
   - New expiry: 31/03/2026
   - Status: "Pending Verification"

7. **Verification**
   - AI verifies document
   - Auto-approved (95% confidence)
   - Status: "VERIFIED"

8. **Alert Closure**
   - Expiry alert marked "RENEWED"
   - New alert schedule created for 2026
   - Parent notified: "Certificate renewed successfully"

**Expected Outcome:**
- Proactive expiry management
- Parent reminded multiple times
- Document renewed before expiry
- No gap in valid documentation

---

## 📊 REPORTS & ANALYTICS

### Document Status Dashboard

```yaml
Overall Statistics:
  Total Documents: 12,450
  Verified: 11,890 (95.5%)
  Pending Verification: 450 (3.6%)
  Rejected: 60 (0.5%)
  Expired: 50 (0.4%)

By Category:
  Admission Documents: 4,200 (100% verified)
  Academic Records: 3,800 (98% verified)
  Medical Documents: 2,100 (92% verified - 8% pending renewal)
  Consent Forms: 1,850 (95% verified)
  Identity Proofs: 500 (100% verified)

Expiry Alerts:
  Expiring in 30 days: 45 documents
  Expiring in 15 days: 22 documents
  Expiring in 7 days: 8 documents
  Expired: 12 documents (action required)

Upload Sources:
  Parent Portal: 6,200 (50%)
  Admin Panel: 5,800 (46%)
  Mobile App: 450 (4%)

Verification Methods:
  AI Auto-verified: 8,900 (71%)
  Manual Verification: 3,550 (29%)
```

---

## 🔒 SECURITY & COMPLIANCE

### Data Security

- **Encryption**: AES-256 at rest, TLS 1.3 in transit
- **Access Control**: Role-based permissions
- **Audit Logging**: All access logged
- **Virus Scanning**: All uploads scanned
- **Backup**: Daily encrypted backups
- **Retention**: 7 years post-graduation

### Compliance

- **GDPR**: Right to access, rectify, delete
- **DPDPA**: Indian data protection compliance
- **KYC Norms**: Identity verification standards
- **Document Retention**: As per education regulations

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 17, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade  
**Compliance:** GDPR, DPDPA, KYC Norms, Education Regulations

# DOCUMENT VAULT & KYC

**Module:** Student Management  
**Submodule Code:** STD-DOC-002  
**Category:** Core Foundation  
**Priority:** Critical (P0)

## Overview

Centralized repository for all student-related documents including mandatory admission documents, academic records, medical certificates, consent forms, and identity proofs. Implements version control, expiry tracking, and digital verification.

## Purpose

Store, manage, and verify all student documents in a secure digital vault, track document validity, maintain version history, and ensure compliance with KYC (Know Your Customer) norms.

## Key Features

### 2.1 Mandatory Admission Documents

**Required at Admission:**
- Birth Certificate (Government issued)
- Previous School Transfer Certificate (TC)
- Aadhaar Card (Student + Parents)
- Passport Size Photos (4 copies)
- Address Proof (Electricity bill/Ration card)
- Caste Certificate (if claiming reservation)
- Income Certificate (for scholarship/fee concession)
- Medical Fitness Certificate
- Vaccination Records

**Document Verification:**
```
Document: Birth Certificate
Uploaded: 05/04/2024
Verified by: Mrs. Gupta (Admissions)
Verification Date: 06/04/2024
Status: VERIFIED
Notes: Original sighted, copy retained
```

### 2.2 Academic Records

**Previous School Documents:**
- Report Cards (Last 2 years)
- Progress Reports
- Character Certificate
- School Leaving Certificate
- Board Exam Certificates (if applicable)

**Current School Records:**
- Term-wise Report Cards
- Assessment Records
- Project Submissions
- Competition Certificates
- Achievement Awards

### 2.3 Medical Documents

**Health Records:**
- Medical Fitness Certificate (annual renewal)
- Vaccination Card (updated)
- Blood Test Reports (if required)
- Allergy Test Results
- Chronic Condition Documentation
- Doctor's Prescription (for regular medication)
- Health Insurance Policy Copy

**Example:**
```
Student: Rohan Sharma
Medical Fitness: Valid till 31/03/2025
Vaccinations: Up to date (last: COVID-19 booster, 15/01/2024)
Known Allergies: Peanuts, Dust
Chronic Conditions: Asthma (mild)
Regular Medication: Inhaler (as needed)
Insurance: Star Health, Policy: 123456789
```

### 2.4 Consent Forms

**Parent Consent Documents:**
- Photo/Video Consent (for school events, website)
- Medical Treatment Consent (emergency situations)
- Field Trip Consent (annual, per-trip)
- Data Privacy Consent (GDPR compliance)
- Transport Consent (if using school bus)
- Hostel Consent (if boarding)
- Extra-curricular Activity Consent

**Digital Consent Tracking:**
```
Consent Type: Photo/Video Usage
Granted by: Mr. Rajesh Sharma (Father)
Date: 01/04/2024
Valid till: 31/03/2025
Scope: School website, social media, yearbook
Revocable: Yes
Status: ACTIVE
```

### 2.5 Identity Proofs

**Student ID Documents:**
- Aadhaar Card (mandatory)
- School ID Card
- Library Card
- Transport Pass
- Hostel ID (if applicable)

**Parent ID Documents:**
- Aadhaar Card (both parents)
- PAN Card (for fee payment)
- Driving License / Passport
- Voter ID Card

### 2.6 Version Control & Expiry Tracking

**Document Versioning:**
```
Document: Medical Fitness Certificate
Version 1: 01/04/2023 - 31/03/2024 (Expired)
Version 2: 01/04/2024 - 31/03/2025 (Current)
Version 3: (Pending renewal)
```

**Expiry Alerts:**
- 30 days before expiry: Email to parent
- 15 days before expiry: SMS reminder
- 7 days before expiry: Final warning
- On expiry: Document marked EXPIRED, parent notified

## Data Fields

```
document_id (PK)
student_id (FK)
document_type (ENUM: BIRTH_CERT, TC, AADHAAR, MEDICAL, CONSENT, etc.)
document_category (ADMISSION/ACADEMIC/MEDICAL/CONSENT/IDENTITY)
file_name
file_path (secure storage)
file_size
file_format (PDF/JPG/PNG)
upload_date
uploaded_by
verified_by
verification_date
verification_status (PENDING/VERIFIED/REJECTED)
version_number
issue_date
expiry_date
is_current_version (boolean)
notes
status (ACTIVE/EXPIRED/SUPERSEDED)
```

## Business Rules

### Document Upload Validation
```
FUNCTION upload_document(student, document_type, file):
  // Validate file format
  allowed_formats = ["PDF", "JPG", "JPEG", "PNG"]
  IF file.format NOT IN allowed_formats:
    RETURN ERROR "Invalid format. Allowed: PDF, JPG, PNG"
  END IF
  
  // Validate file size (max 5MB)
  IF file.size > 5 * 1024 * 1024:
    RETURN ERROR "File too large. Max size: 5MB"
  END IF
  
  // Check for existing document
  existing_doc = GET document WHERE student_id = student.id AND document_type = document_type AND is_current_version = TRUE
  
  IF existing_doc EXISTS:
    // Mark old version as superseded
    existing_doc.is_current_version = FALSE
    existing_doc.status = "SUPERSEDED"
    version_number = existing_doc.version_number + 1
  ELSE:
    version_number = 1
  END IF
  
  // Create new document record
  document = NEW Document
  document.student_id = student.id
  document.document_type = document_type
  document.file_path = SECURE_STORAGE_PATH + "/" + student.admission_number + "/" + file.name
  document.version_number = version_number
  document.upload_date = TODAY
  document.verification_status = "PENDING"
  document.is_current_version = TRUE
  
  // Save file to secure storage
  SAVE_FILE(file, document.file_path)
  
  // Notify verifier
  SEND_NOTIFICATION(admissions_team, "New document uploaded for verification: " + student.name)
  
  RETURN document
END FUNCTION
```

### Expiry Alert System
```
FUNCTION check_document_expiry():
  // Run daily job
  expiring_docs = GET documents WHERE expiry_date BETWEEN TODAY AND (TODAY + 30 days) AND status = "ACTIVE"
  
  FOR each doc IN expiring_docs:
    days_to_expiry = DAYS_BETWEEN(TODAY, doc.expiry_date)
    
    IF days_to_expiry = 30:
      SEND_EMAIL(doc.student.parent_email, "Document expiring in 30 days: " + doc.document_type)
    ELSE IF days_to_expiry = 15:
      SEND_SMS(doc.student.parent_mobile, "Document expiring in 15 days: " + doc.document_type)
    ELSE IF days_to_expiry = 7:
      SEND_EMAIL_AND_SMS(doc.student.parent, "URGENT: Document expiring in 7 days")
    ELSE IF days_to_expiry = 0:
      doc.status = "EXPIRED"
      SEND_ALERT(doc.student.parent, "Document EXPIRED: " + doc.document_type)
    END IF
  END FOR
END FUNCTION
```

## Integration Points

### Connects TO:
1. **Admissions Module** - Verify mandatory documents before admission confirmation
2. **Student Registration** - Link documents to student profile
3. **Health & Wellness Module** - Access medical documents
4. **Compliance Module** - Ensure regulatory document compliance
5. **Parent Portal** - Allow parents to upload/view documents

### Receives FROM:
1. **Parent Portal** - Document uploads from parents
2. **Admissions Team** - Scanned documents during admission
3. **Medical Team** - Updated health certificates

## User Workflows

### Workflow: Parent Uploads Document via Portal

**Actor:** Parent (Mr. Rajesh Sharma)

**Steps:**
1. Parent logs into Parent Portal
2. Navigate to "My Child" > "Documents"
3. Click "Upload New Document"
4. Select Document Type: "Medical Fitness Certificate"
5. Choose file from computer (PDF, 2.3 MB)
6. Enter Issue Date: 01/04/2024
7. Enter Expiry Date: 31/03/2025
8. Click "Upload"
9. System validates file format and size
10. System uploads to secure storage
11. System creates document record (Version 2)
12. System marks old certificate as "SUPERSEDED"
13. System notifies admissions team for verification
14. Parent sees: "Document uploaded successfully. Pending verification."
15. Admissions officer verifies document
16. System updates status: "VERIFIED"
17. Parent receives email: "Medical certificate verified"

**Expected Outcome:** Document uploaded, versioned, and verified successfully.

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

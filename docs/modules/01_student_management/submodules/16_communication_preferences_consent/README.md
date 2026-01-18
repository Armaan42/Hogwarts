# COMMUNICATION PREFERENCES & CONSENT - COMPLETE DOCUMENTATION

**Module:** Student Management  
**Submodule Code:** STD-COMM-016  
**Category:** Data Privacy & Communication  
**Priority:** High (P1)  
**Owner:** Communication Department & Data Privacy Officer

---

## 📋 OVERVIEW

The Communication Preferences & Consent submodule manages parent/student communication preferences, consent for various school activities, marketing opt-ins, data sharing permissions, and ensures GDPR/DPDPA compliance for all communications.

### Purpose

Manage communication channels, track consent for activities, ensure privacy compliance, respect opt-out preferences, maintain consent records, and enable personalized communication based on preferences.

### Scope

- **Communication Channels**: SMS, Email, WhatsApp, App
- **Activity Consents**: Field trips, photos, medical treatment
- **Marketing Preferences**: Newsletter, promotional content
- **Data Sharing**: Third-party sharing consent
- **Opt-out Management**: Unsubscribe handling
- **Compliance**: GDPR, DPDPA, TCPA

---

## 🎯 KEY FEATURES

### 1. Communication Preferences

#### 1.1 Channel Preferences

**Preference Settings:**
```yaml
Student: Aarav Patel (2024/06/0456)
Parent: Mr. Rajesh Patel (Father)

Communication Channels:

SMS:
  Enabled: Yes
  Mobile: +91-9876543210
  Preferences:
    - Attendance alerts: Yes
    - Fee reminders: Yes
    - Event notifications: Yes
    - Emergency alerts: Yes (Cannot opt-out)
    - Marketing: No
  Frequency: Real-time
  
Email:
  Enabled: Yes
  Email: rajesh.patel@gmail.com
  Preferences:
    - Academic reports: Yes
    - Monthly newsletter: Yes
    - Event invitations: Yes
    - Fee invoices: Yes
    - Marketing: No
  Frequency: Daily digest
  
WhatsApp:
  Enabled: Yes
  Number: +91-9876543210
  Preferences:
    - Daily updates: Yes
    - Photo/video sharing: Yes
    - Group messages: Yes
    - Marketing: No
  Frequency: Real-time
  
Mobile App:
  Enabled: Yes
  Push Notifications: Yes
  Preferences:
    - All notifications: Yes
    - Marketing: No

Preferred Channel: WhatsApp
Preferred Time: 9:00 AM - 9:00 PM
Language: English
```

---

### 2. Activity Consents

#### 2.1 Consent Management

**Consent Records:**
```yaml
Student: Aarav Patel (2024/06/0456)
Academic Year: 2024-25

Consent 1: Field Trips
  Consent Given: Yes
  Consent By: Mr. Rajesh Patel (Father)
  Consent Date: 01/04/2024
  Consent Method: Digital signature
  Valid Till: 31/03/2025
  Conditions: Parent to be notified 7 days in advance
  
Consent 2: Photography & Videography
  Consent Given: Yes
  Consent By: Mr. Rajesh Patel
  Consent Date: 01/04/2024
  Usage Allowed:
    - School website: Yes
    - Social media: Yes
    - Promotional materials: No
    - Yearbook: Yes
    - Internal use: Yes
  Valid Till: 31/03/2025
  
Consent 3: Medical Treatment (Emergency)
  Consent Given: Yes
  Consent By: Mr. Rajesh Patel
  Consent Date: 01/04/2024
  Conditions:
    - Emergency situations only
    - Parent to be informed immediately
    - Preferred hospital: Max Hospital, Noida
  Valid Till: 31/03/2025
  
Consent 4: Data Sharing with Third Parties
  Consent Given: No
  Consent By: Mr. Rajesh Patel
  Consent Date: 01/04/2024
  Restrictions:
    - No sharing with educational platforms
    - No sharing with vendors
    - School use only
  
Consent 5: Biometric Data Collection
  Consent Given: Yes
  Consent By: Mr. Rajesh Patel
  Consent Date: 01/04/2024
  Data Types: Fingerprint, Photo
  Purpose: Attendance, Security
  Storage: Encrypted, School servers only
  Valid Till: 31/03/2025
```

---

### 3. Marketing & Newsletter Preferences

#### 3.1 Subscription Management

**Marketing Preferences:**
```yaml
Parent: Mr. Rajesh Patel
Student: Aarav Patel (2024/06/0456)

Newsletter Subscriptions:
  Monthly School Newsletter:
    Subscribed: Yes
    Frequency: Monthly
    Last Sent: 01/01/2026
    Open Rate: 85%
    
  Academic Updates:
    Subscribed: Yes
    Frequency: Weekly
    Last Sent: 15/01/2026
    
  Event Invitations:
    Subscribed: Yes
    Frequency: As needed
    
  Alumni Newsletter:
    Subscribed: No (Not applicable)

Marketing Communications:
  Promotional Offers: No
  Partner Offers: No
  Surveys: Yes
  Feedback Requests: Yes

Unsubscribe History:
  - Unsubscribed from "Promotional Offers" on 15/05/2024
  - Reason: Too frequent
```

---

## 📊 DATABASE SCHEMA

### Communication Preferences Table

```sql
CREATE TABLE communication_preferences (
  preference_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  parent_id INT NOT NULL,
  
  -- Channel Preferences
  sms_enabled BOOLEAN DEFAULT TRUE,
  sms_attendance BOOLEAN DEFAULT TRUE,
  sms_fee BOOLEAN DEFAULT TRUE,
  sms_events BOOLEAN DEFAULT TRUE,
  sms_marketing BOOLEAN DEFAULT FALSE,
  
  email_enabled BOOLEAN DEFAULT TRUE,
  email_academic BOOLEAN DEFAULT TRUE,
  email_newsletter BOOLEAN DEFAULT TRUE,
  email_events BOOLEAN DEFAULT TRUE,
  email_marketing BOOLEAN DEFAULT FALSE,
  
  whatsapp_enabled BOOLEAN DEFAULT FALSE,
  whatsapp_updates BOOLEAN DEFAULT FALSE,
  whatsapp_photos BOOLEAN DEFAULT FALSE,
  whatsapp_marketing BOOLEAN DEFAULT FALSE,
  
  app_notifications BOOLEAN DEFAULT TRUE,
  app_marketing BOOLEAN DEFAULT FALSE,
  
  -- Preferences
  preferred_channel VARCHAR(50),
  preferred_time_start TIME,
  preferred_time_end TIME,
  preferred_language VARCHAR(50),
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id)
);
```

### Consent Records Table

```sql
CREATE TABLE consent_records (
  consent_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Consent Details
  consent_type ENUM(
    'FIELD_TRIP', 'PHOTOGRAPHY', 'MEDICAL_TREATMENT',
    'DATA_SHARING', 'BIOMETRIC', 'MARKETING', 'OTHER'
  ) NOT NULL,
  
  consent_description TEXT NOT NULL,
  
  -- Consent Status
  consent_given BOOLEAN NOT NULL,
  consent_by INT NOT NULL,
  consent_date DATE NOT NULL,
  consent_method ENUM('DIGITAL_SIGNATURE', 'WRITTEN', 'VERBAL', 'ONLINE_FORM') NOT NULL,
  
  -- Validity
  valid_from DATE NOT NULL,
  valid_till DATE,
  
  -- Conditions
  conditions TEXT,
  restrictions TEXT,
  
  -- Revocation
  revoked BOOLEAN DEFAULT FALSE,
  revoked_date DATE,
  revoked_reason TEXT,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  modified_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
  
  -- Indexes
  INDEX idx_student_id (student_id),
  INDEX idx_consent_type (consent_type),
  INDEX idx_valid_till (valid_till)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Consent Validation

```javascript
FUNCTION validate_consent(student_id, consent_type, activity_date) {
  // Get consent record
  consent = QUERY("
    SELECT * FROM consent_records
    WHERE student_id = ?
    AND consent_type = ?
    AND consent_given = TRUE
    AND revoked = FALSE
    AND valid_from <= ?
    AND (valid_till IS NULL OR valid_till >= ?)
  ", [student_id, consent_type, activity_date, activity_date])
  
  IF NOT consent {
    RETURN {
      valid: FALSE,
      error: "Consent not found or expired",
      action: "Request parent consent"
    }
  }
  
  RETURN {
    valid: TRUE,
    consent_id: consent.consent_id,
    consent_date: consent.consent_date
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Communication Module**: Channel preferences
2. **SMS Gateway**: SMS delivery
3. **Email Service**: Email delivery
4. **WhatsApp Business API**: WhatsApp messages

### Inbound Integrations

1. **Parent Portal**: Preference management
2. **Consent Forms**: Digital consent collection

---

## 👥 USER WORKFLOWS

### Workflow: Update Communication Preferences

**Actor:** Parent  
**Duration:** 5-10 minutes

**Steps:**

1. Login to Parent Portal
2. Navigate to: Settings → Communication Preferences
3. Review current preferences
4. Update channel preferences
5. Set preferred time
6. Save changes
7. Receive confirmation email
8. Preferences applied immediately

**Expected Outcome:** Communication preferences updated

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade  
**Compliance:** GDPR, DPDPA, TCPA

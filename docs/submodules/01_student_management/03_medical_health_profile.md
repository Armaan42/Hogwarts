# MEDICAL & HEALTH PROFILE

**Module:** Student Management  
**Submodule Code:** STD-HEALTH-003  
**Category:** Student Services  
**Priority:** Critical (P0)

## Overview

Comprehensive health tracking system capturing physical details, chronic conditions, allergies, vaccination records, mental health status, insurance information, and blood donation eligibility.

## Purpose

Maintain complete health profile for each student to ensure safety, enable emergency response, track medical history, manage chronic conditions, and facilitate health insurance claims.

## Key Features

### 3.1 Physical Details

**Basic Measurements:**
```
Student: Rohan Sharma, Grade 6
Height: 145 cm (4'9")
Weight: 38 kg
BMI: 18.1 (Normal range)
Vision: Left: 6/6, Right: 6/6 (Normal)
Hearing: Normal
Dental: Regular checkup due (6 months)
Last Updated: 15/04/2024
```

**Growth Tracking:**
- Annual health checkup
- Height/Weight recorded
- BMI calculated
- Growth chart maintained
- Alerts for abnormal growth patterns

### 3.2 Chronic Conditions

**Medical Conditions:**
```
Condition: Asthma (Mild)
Diagnosed: 2018
Severity: Mild
Triggers: Dust, cold weather, exercise
Medication: Salbutamol inhaler (as needed)
Doctor: Dr. Amit Kumar, Max Hospital
Last Episode: 15/01/2024
Management Plan: Inhaler kept in school clinic, avoid dust exposure
```

**Other Common Conditions:**
- Diabetes (Type 1/Type 2)
- Epilepsy
- Heart conditions
- ADHD
- Autism Spectrum Disorder
- Dyslexia

### 3.3 Allergies

**Allergy Profile:**
```
Food Allergies:
- Peanuts (Severe - Anaphylaxis risk)
- Shellfish (Moderate)

Environmental Allergies:
- Dust mites (Mild)
- Pollen (Seasonal)

Medication Allergies:
- Penicillin (Severe)

Emergency Action Plan:
- EpiPen available in school clinic
- Nurse trained in administration
- Parents notified immediately
- Ambulance called if severe reaction
```

### 3.4 Vaccination Records

**Immunization Status:**
```
Vaccine: COVID-19
Dose 1: 15/05/2021 (Covaxin)
Dose 2: 15/06/2021 (Covaxin)
Booster: 15/01/2024 (Covishield)
Next Due: Annual booster (15/01/2025)

Other Vaccinations:
- BCG: ✓ Complete
- DPT: ✓ Complete (3 doses)
- Polio: ✓ Complete (4 doses)
- MMR: ✓ Complete (2 doses)
- Hepatitis B: ✓ Complete (3 doses)
- Typhoid: ✓ Due for booster (2025)
```

### 3.5 Mental Health

**Psychological Profile:**
```
Counseling Sessions: 3 sessions (Oct-Dec 2023)
Reason: Exam anxiety
Counselor: Ms. Priya Mehta
Status: Resolved
Follow-up: Quarterly check-in

Behavioral Observations:
- Social skills: Good
- Peer relationships: Positive
- Academic stress: Moderate
- Family support: Strong
```

### 3.6 Insurance Details

**Health Insurance:**
```
Insurance Provider: Star Health
Policy Number: 123456789
Policy Holder: Mr. Rajesh Sharma (Father)
Coverage Amount: ₹5,00,000
Validity: 01/04/2024 - 31/03/2025
Cashless Hospitals: Max, Apollo, Fortis
TPA: Medi Assist
Claim Process: Cashless + Reimbursement
```

### 3.7 Blood Donation Eligibility

**Blood Donor Profile:**
```
Student: Rohan Sharma
Age: 11 years (Not eligible - min age 18)
Blood Group: B+
Weight: 38 kg (Not eligible - min weight 45 kg)
Chronic Conditions: Asthma (May affect eligibility)
Eligibility Status: NOT ELIGIBLE
Eligible From: 2031 (when turns 18)

Note: Profile maintained for future reference
```

## Data Fields

```
health_profile_id (PK)
student_id (FK)
blood_group
height_cm
weight_kg
bmi
vision_left
vision_right
hearing_status
dental_status
last_checkup_date
chronic_conditions (JSON array)
allergies (JSON array)
vaccinations (JSON array)
mental_health_notes
insurance_provider
insurance_policy_number
insurance_validity
blood_donation_eligible (boolean)
created_date
updated_date
```

## Business Rules

### BMI Calculation & Alert
```
FUNCTION calculate_bmi(student):
  height_m = student.height_cm / 100
  bmi = student.weight_kg / (height_m * height_m)
  
  // WHO BMI categories for children
  IF bmi < 18.5:
    category = "Underweight"
    alert = TRUE
  ELSE IF bmi >= 18.5 AND bmi < 25:
    category = "Normal"
    alert = FALSE
  ELSE IF bmi >= 25 AND bmi < 30:
    category = "Overweight"
    alert = TRUE
  ELSE:
    category = "Obese"
    alert = TRUE
  END IF
  
  student.bmi = bmi
  student.bmi_category = category
  
  IF alert:
    SEND_NOTIFICATION(school_nurse, "BMI alert for " + student.name + ": " + category)
    SEND_EMAIL(student.parent_email, "Health checkup recommended")
  END IF
  
  RETURN student
END FUNCTION
```

## Integration Points

### Connects TO:
1. **Health & Wellness Module** - Share health profile for clinic visits
2. **Canteen Module** - Flag food allergies for meal planning
3. **Sports Module** - Check fitness for sports participation
4. **Emergency Response** - Quick access to medical info

### Receives FROM:
1. **School Clinic** - Updated health records
2. **Parent Portal** - Insurance details, vaccination updates

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

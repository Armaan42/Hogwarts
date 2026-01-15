# BOARD COMPLIANCE (CBSE/ICSE/IB)

**Module:** Academic Curriculum  
**Submodule Code:** CURR-COMPLIANCE-006  
**Category:** Compliance  
**Priority:** Critical (P0)

## Overview

Ensures curriculum compliance with board requirements (CBSE, ICSE, IB, Cambridge), tracks mandatory components, and manages board audits.

## Purpose

Maintain compliance with board guidelines, implement board-specific assessment models, and prepare for board inspections.

## Key Features

### 6.1 CBSE Compliance

**Mandatory Requirements:**
```
1. CCE Implementation:
   - Formative Assessment (FA): 40%
     - FA1: 10% (April-June)
     - FA2: 10% (July-September)
     - FA3: 10% (October-December)
     - FA4: 10% (January-March)
   
   - Summative Assessment (SA): 60%
     - SA1: 30% (September)
     - SA2: 30% (March)

2. Scholastic Areas:
   - All core subjects covered
   - Minimum passing: 33%

3. Co-scholastic Areas:
   - Life Skills
   - Work Education
   - Art Education
   - Physical & Health Education

4. Value Education:
   - Integrated in curriculum
   - Separate periods allocated
```

### 6.2 IB Compliance

**IB DP Requirements:**
```
1. Core Components:
   - Theory of Knowledge (TOK): 100 hours
   - Extended Essay (EE): 4,000 words
   - CAS (Creativity, Activity, Service): 150 hours

2. Subject Groups (6 groups):
   - 3 Higher Level (HL): 240 hours each
   - 3 Standard Level (SL): 150 hours each

3. Assessment:
   - Internal Assessment: 20-30%
   - External Examination: 70-80%

4. Learner Profile:
   - Inquirers, Knowledgeable, Thinkers
   - Communicators, Principled, Open-minded
   - Caring, Risk-takers, Balanced, Reflective
```

## Data Fields

```
compliance_record_id (PK)
board (CBSE/ICSE/IB/CAMBRIDGE)
academic_year
compliance_checklist (JSON)
audit_date
audit_status
deficiencies (JSON)
action_plan
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

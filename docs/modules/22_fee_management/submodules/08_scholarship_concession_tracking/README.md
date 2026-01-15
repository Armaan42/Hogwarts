# SCHOLARSHIP & CONCESSION TRACKING

**Module:** Fee Management  
**Submodule Code:** FEE-SCHOLARSHIP-008  
**Category:** Financial Core  
**Priority:** High (P1)

## Overview

Manages scholarship awards, concession applications, approval workflows, renewal processes, and revocation handling.

## Key Features

### Scholarship Types
- **Merit:** Top 10 students, 25% waiver, annual renewal
- **Sports:** State/National athletes, 50% waiver
- **Need-based:** Income <₹3L/year, 75-100% waiver
- **Alumni:** Funded by alumni, variable amount

### Application & Approval
```
FUNCTION apply_scholarship(student, type):
  application = NEW ScholarshipApplication
  application.student = student
  application.type = type
  application.status = "PENDING"
  
  // Document verification
  IF type = "NEED_BASED":
    REQUIRE income_certificate
    REQUIRE bank_statements
  ELSE IF type = "MERIT":
    REQUIRE academic_transcripts
    VERIFY student.percentage >= 90
  ELSE IF type = "SPORTS":
    REQUIRE achievement_certificates
  END IF
  
  // Committee review
  committee_decision = REVIEW(application)
  
  IF approved:
    application.status = "APPROVED"
    UPDATE student.scholarship_percentage
    RECALCULATE student_fee(student)
    SEND EMAIL(student.parent, "Scholarship approved!")
  ELSE:
    application.status = "REJECTED"
  END IF
  
  RETURN application
END FUNCTION
```

### Annual Renewal
- Scholarship expires end of year
- Student must reapply
- Performance reviewed
- Auto-renewal if criteria met

### Revocation
- Performance dropped (<75%)
- Disciplinary issues
- Fraudulent documents
- Warning issued first

## Data Fields
```
scholarship_id (PK)
student_id (FK)
scholarship_type
percentage_discount
amount
academic_year
application_date
approved_by
approval_date
renewal_status
revocation_date
status
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

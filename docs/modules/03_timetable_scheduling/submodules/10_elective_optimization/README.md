# ELECTIVE OPTIMIZATION - COMPLETE DOCUMENTATION

**Module:** Timetable & Scheduling  
**Submodule Code:** TIME-ELECTIVE-010  
**Category:** Advanced Scheduling  
**Priority:** High (P1)  
**Owner:** Academic Planning Team

---

## 📋 OVERVIEW

Optimizes elective subject scheduling for senior grades (11-12), manages student choice allocation, balances section sizes, and creates feasible timetables accommodating diverse subject combinations.

### Purpose

Handle student elective choices, create balanced elective sections, optimize timetable for multiple subject combinations, ensure teacher availability for electives, and maximize student preference satisfaction.

---

## �� KEY FEATURES

### 1. Elective Choice Management

**Grade 11 Science Stream:**
```yaml
Compulsory Subjects:
  - English (4 periods)
  - Physics (5 periods)
  - Chemistry (5 periods)
  - Mathematics (6 periods)

Elective Options (Choose 1):
  - Biology (5 periods)
  - Computer Science (5 periods)
  - Physical Education (5 periods)

Student Preferences:
  - 60 students choose Biology
  - 45 students choose Computer Science
  - 15 students choose Physical Education

Section Formation:
  - Biology: 2 sections (30 each)
  - Computer Science: 2 sections (22-23 each)
  - Physical Education: 1 section (15)

Timetable Challenge:
  - All 5 sections need different elective periods
  - Common periods for compulsory subjects
  - Teacher availability for each elective
```

### 2. Optimization Algorithm

**Constraint Satisfaction:**
```javascript
FUNCTION optimize_elective_schedule(students, electives) {
  // Group students by elective choice
  groups = GROUP_BY_ELECTIVE(students)
  
  // Create balanced sections
  sections = CREATE_BALANCED_SECTIONS(groups, min_size=15, max_size=35)
  
  // Find common free periods for all sections
  common_periods = FIND_COMMON_FREE_PERIODS(sections)
  
  // Allocate elective periods
  FOR each section IN sections {
    elective_periods = ALLOCATE_PERIODS(
      section.elective,
      common_periods,
      section.teacher
    )
    
    section.schedule = elective_periods
  }
  
  RETURN sections
}
```

---

## 📊 DATABASE SCHEMA

```sql
CREATE TABLE elective_allocation (
  allocation_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  elective_subject_id INT NOT NULL,
  preference_rank INT,
  allocated_section VARCHAR(10),
  allocation_status ENUM('PENDING', 'ALLOCATED', 'WAITLIST') DEFAULT 'PENDING',
  academic_year VARCHAR(9) NOT NULL,
  FOREIGN KEY (student_id) REFERENCES students(student_id),
  FOREIGN KEY (elective_subject_id) REFERENCES subjects(subject_id)
);
```

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 1.0

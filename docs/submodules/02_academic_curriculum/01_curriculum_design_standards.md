# CURRICULUM DESIGN & STANDARDS

**Module:** Academic Curriculum  
**Submodule Code:** CURR-DESIGN-001  
**Category:** Core Academic  
**Priority:** Critical (P0)

## Overview

Defines and manages curriculum frameworks, learning standards, competency mapping, and alignment with national/international education boards (CBSE, ICSE, IB, Cambridge).

## Purpose

Design comprehensive curriculum aligned with educational standards, define learning outcomes, map competencies, and ensure board compliance for all grades and subjects.

## Key Features

### 1.1 Curriculum Framework

**Framework Types:**
- **CBSE (Central Board of Secondary Education)**
  - NCF 2005 (National Curriculum Framework)
  - Competency-based learning
  - Continuous & Comprehensive Evaluation (CCE)
  
- **ICSE (Indian Certificate of Secondary Education)**
  - CISCE curriculum
  - Analytical & application-based learning
  - Internal assessment + Board exams

- **IB (International Baccalaureate)**
  - PYP (Primary Years Programme): Grades 1-5
  - MYP (Middle Years Programme): Grades 6-10
  - DP (Diploma Programme): Grades 11-12
  
- **Cambridge (IGCSE/A-Level)**
  - Cambridge Primary: Grades 1-5
  - Cambridge Lower Secondary: Grades 6-8
  - IGCSE: Grades 9-10
  - A-Level: Grades 11-12

**Example:**
```
School: XYZ International School
Board: CBSE (Grades 1-10) + IB DP (Grades 11-12)

Grade 6 Curriculum:
- Framework: CBSE NCF 2005
- Learning Approach: Competency-based
- Assessment: CCE (Formative + Summative)
- Subjects: 8 core subjects
- Language Policy: English medium, Hindi/Sanskrit as 2nd language
```

### 1.2 Learning Standards

**Bloom's Taxonomy Levels:**
1. **Remember:** Recall facts, terms, concepts
2. **Understand:** Explain ideas, concepts
3. **Apply:** Use information in new situations
4. **Analyze:** Draw connections, compare/contrast
5. **Evaluate:** Justify decisions, critique
6. **Create:** Produce new work, design solutions

**Grade-wise Standards:**
```
Grade 6 - Mathematics
Standard: Number Systems

Learning Outcomes:
- Remember: Recall properties of whole numbers, integers
- Understand: Explain place value system up to crores
- Apply: Solve real-world problems using integers
- Analyze: Compare and order integers
- Evaluate: Justify solutions to word problems
- Create: Design number puzzles using integers

Assessment Criteria:
- 60% Application & Analysis
- 30% Understanding
- 10% Remembering
```

### 1.3 Competency Mapping

**21st Century Skills:**
- Critical Thinking
- Creativity & Innovation
- Collaboration
- Communication
- Digital Literacy
- Social & Cultural Awareness
- Problem Solving
- Self-directed Learning

**Subject-wise Competencies:**
```
Subject: Science (Grade 8)
Topic: Photosynthesis

Competencies Developed:
1. Scientific Inquiry (Critical Thinking)
   - Design experiments to test photosynthesis factors
   
2. Data Analysis (Problem Solving)
   - Analyze plant growth data under different light conditions
   
3. Communication (Presentation Skills)
   - Present findings using charts, graphs
   
4. Collaboration (Teamwork)
   - Group lab experiments
   
5. Digital Literacy (Technology Integration)
   - Use simulation software for virtual experiments
```

### 1.4 Vertical & Horizontal Alignment

**Vertical Alignment (Grade Progression):**
```
Topic: Fractions

Grade 3: Introduction to fractions (1/2, 1/4)
Grade 4: Equivalent fractions, comparing fractions
Grade 5: Adding/subtracting fractions (same denominator)
Grade 6: Adding/subtracting fractions (different denominators)
Grade 7: Multiplying/dividing fractions
Grade 8: Rational numbers, operations
```

**Horizontal Alignment (Cross-subject):**
```
Grade 7 - Theme: "Sustainability"

Mathematics: Calculate carbon footprint, analyze data
Science: Study ecosystems, climate change
Social Studies: Environmental policies, geography
English: Read articles, write essays on sustainability
Art: Create posters on environmental awareness
```

### 1.5 Board Compliance

**CBSE Compliance Checklist:**
- ✓ Curriculum aligned with NCF 2005
- ✓ CCE implementation (FA1, FA2, SA1, SA2)
- ✓ Scholastic & Co-scholastic areas covered
- ✓ Life Skills integrated
- ✓ Value Education included
- ✓ Activity-based learning
- ✓ Continuous assessment (40% weightage)

**IB Compliance:**
- ✓ Learner Profile attributes (Inquirers, Thinkers, Communicators...)
- ✓ Approaches to Learning (ATL) skills
- ✓ Transdisciplinary themes (PYP)
- ✓ Subject groups (MYP/DP)
- ✓ CAS (Creativity, Activity, Service) - DP
- ✓ Extended Essay - DP
- ✓ Theory of Knowledge (TOK) - DP

## Data Fields

```
curriculum_id (PK)
curriculum_name
board (ENUM: CBSE, ICSE, IB, CAMBRIDGE, STATE)
framework_version
academic_year
grade_range_start
grade_range_end
learning_approach (COMPETENCY_BASED/TRADITIONAL)
assessment_model (CCE/TRADITIONAL/IB_CRITERIA)
language_policy
created_date
approved_by
approval_date
status (DRAFT/APPROVED/ACTIVE/ARCHIVED)
```

### Learning Standards Table
```
standard_id (PK)
curriculum_id (FK)
subject_id (FK)
grade
topic
learning_outcome
bloom_level (REMEMBER/UNDERSTAND/APPLY/ANALYZE/EVALUATE/CREATE)
competency_mapped
assessment_criteria
```

## Business Rules

### Curriculum Approval Workflow
```
FUNCTION approve_curriculum(curriculum):
  // Validate curriculum completeness
  IF NOT has_all_subjects(curriculum):
    RETURN ERROR "All mandatory subjects must be defined"
  END IF
  
  IF NOT has_learning_outcomes(curriculum):
    RETURN ERROR "Learning outcomes must be defined for all topics"
  END IF
  
  // Board-specific validation
  IF curriculum.board = "CBSE":
    IF NOT ccee_implemented(curriculum):
      RETURN ERROR "CCE model must be implemented for CBSE"
    END IF
  ELSE IF curriculum.board = "IB":
    IF NOT atl_skills_mapped(curriculum):
      RETURN ERROR "ATL skills must be mapped for IB"
    END IF
  END IF
  
  // Approval workflow
  curriculum.status = "PENDING_APPROVAL"
  ASSIGN_TO academic_committee
  
  IF approved_by_committee:
    curriculum.status = "APPROVED"
    curriculum.approved_by = committee_head
    curriculum.approval_date = TODAY
    
    // Activate for next academic year
    IF curriculum.academic_year = NEXT_YEAR:
      SCHEDULE_ACTIVATION(curriculum, NEXT_YEAR_START_DATE)
    END IF
    
    NOTIFY teachers, "New curriculum approved for " + curriculum.academic_year
  ELSE:
    curriculum.status = "REJECTED"
  END IF
  
  RETURN curriculum
END FUNCTION
```

### Vertical Alignment Validation
```
FUNCTION validate_vertical_alignment(subject, grade):
  // Get previous grade curriculum
  previous_grade = grade - 1
  previous_topics = GET topics WHERE subject = subject AND grade = previous_grade
  current_topics = GET topics WHERE subject = subject AND grade = grade
  
  // Check for logical progression
  FOR each current_topic IN current_topics:
    prerequisite_topics = current_topic.prerequisites
    
    FOR each prerequisite IN prerequisite_topics:
      IF prerequisite NOT IN previous_topics:
        RETURN WARNING "Prerequisite topic '" + prerequisite + "' not covered in Grade " + previous_grade
      END IF
    END FOR
  END FOR
  
  // Check for appropriate difficulty progression
  previous_bloom_avg = AVG(bloom_level WHERE grade = previous_grade)
  current_bloom_avg = AVG(bloom_level WHERE grade = grade)
  
  IF current_bloom_avg <= previous_bloom_avg:
    RETURN WARNING "Bloom's taxonomy level not progressing appropriately"
  END IF
  
  RETURN SUCCESS
END FUNCTION
```

## Integration Points

### Connects TO:
1. **Subject Management** - Define subjects for curriculum
2. **Timetable Module** - Allocate periods based on curriculum requirements
3. **Assessment Module** - Align assessments with learning outcomes
4. **Teacher Management** - Assign teachers based on curriculum expertise
5. **Textbook Module** - Map textbooks to curriculum topics

### Receives FROM:
1. **Board Authorities** - Official curriculum guidelines
2. **Academic Committee** - Curriculum approval decisions

## User Workflows

### Workflow: Design New Curriculum for Academic Year

**Actor:** Academic Coordinator

**Steps:**
1. Navigate to Academic Curriculum > Curriculum Design
2. Click "Create New Curriculum"
3. **Select Parameters:**
   - Academic Year: 2025-26
   - Board: CBSE
   - Grade Range: 6-10
   - Framework: NCF 2005
4. **Define Subjects:**
   - Core: English, Hindi, Mathematics, Science, Social Studies
   - Electives: Computer Science, French
5. **Map Learning Outcomes:**
   - For each subject, define grade-wise topics
   - For each topic, define learning outcomes
   - Map to Bloom's taxonomy levels
6. **Map Competencies:**
   - Link topics to 21st century skills
   - Define assessment criteria
7. **Ensure Board Compliance:**
   - System validates CCE implementation
   - System checks mandatory subjects
   - System verifies life skills integration
8. **Submit for Approval:**
   - Academic committee reviews
   - Committee approves
9. **System activates** curriculum for 2025-26
10. **System notifies** all teachers

**Expected Outcome:** New curriculum designed, approved, and ready for implementation.

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026  
**Documentation Standard:** Production-Ready

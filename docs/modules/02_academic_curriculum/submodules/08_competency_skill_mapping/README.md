# COMPETENCY & SKILL MAPPING - COMPLETE DOCUMENTATION

**Module:** Academic Curriculum  
**Submodule Code:** CURR-COMPETENCY-008  
**Category:** Skills Development  
**Priority:** High (P1)  
**Owner:** Curriculum Development Team

---

## 📋 OVERVIEW

The Competency & Skill Mapping submodule maps 21st-century skills and competencies to curriculum topics, tracks skill development across grades, aligns with NEP 2020 competency-based education, and ensures holistic skill development beyond academic knowledge.

### Purpose

Map competencies to curriculum, track 21st-century skills development, implement competency-based assessment, align with NEP 2020 framework, ensure skill progression across grades, and prepare students for future readiness.

### Scope

- **21st Century Skills**: Critical thinking, creativity, collaboration, communication
- **NEP 2020 Competencies**: Foundational literacy, numeracy, scientific temper
- **Skill Progression**: Grade-wise skill development tracking
- **Cross-curricular Skills**: Skills developed across multiple subjects
- **Assessment**: Competency-based evaluation

---

## 🎯 KEY FEATURES

### 1. 21st Century Skills Framework

#### 1.1 Core Competencies

**Framework (4Cs + Digital Literacy):**
```yaml
1. Critical Thinking
   Definition: Ability to analyze, evaluate, and synthesize information
   
   Grade 6-8 Development:
     - Analyzing data and information
     - Evaluating arguments and evidence
     - Making logical conclusions
     - Identifying biases and assumptions
   
   Subject Mapping:
     Mathematics: Problem-solving, logical reasoning
     Science: Scientific inquiry, hypothesis testing
     Social Studies: Analyzing historical events, evaluating sources
     English: Literary analysis, argument evaluation
   
   Assessment:
     - Case study analysis
     - Problem-solving tasks
     - Critical essays
     - Debate participation

2. Creativity & Innovation
   Definition: Ability to generate novel ideas and solutions
   
   Grade 6-8 Development:
     - Divergent thinking
     - Design thinking
     - Artistic expression
     - Innovation mindset
   
   Subject Mapping:
     Art: Creative projects, original artwork
     Science: Experiment design, innovation projects
     English: Creative writing, storytelling
     Mathematics: Alternative problem-solving methods
   
   Assessment:
     - Project-based assessments
     - Innovation challenges
     - Creative portfolios
     - Design thinking projects

3. Collaboration & Teamwork
   Definition: Ability to work effectively in teams
   
   Grade 6-8 Development:
     - Team communication
     - Shared responsibility
     - Conflict resolution
     - Collective problem-solving
   
   Subject Mapping:
     Science: Group experiments, lab work
     Social Studies: Group projects, presentations
     Sports: Team sports, group activities
     Arts: Group performances, collaborative art
   
   Assessment:
     - Group project evaluation
     - Peer assessment
     - Team presentation
     - Collaborative tasks

4. Communication Skills
   Definition: Ability to express ideas effectively
   
   Grade 6-8 Development:
     - Verbal communication
     - Written communication
     - Presentation skills
     - Active listening
   
   Subject Mapping:
     English: Essay writing, presentations, debates
     All Subjects: Oral presentations, written reports
     Drama: Public speaking, expression
   
   Assessment:
     - Presentations
     - Written assignments
     - Debates
     - Communication rubrics

5. Digital Literacy
   Definition: Ability to use technology effectively
   
   Grade 6-8 Development:
     - Computer skills
     - Internet research
     - Digital citizenship
     - Coding basics
   
   Subject Mapping:
     Computer Science: Programming, digital tools
     All Subjects: Research, presentations, digital projects
   
   Assessment:
     - Digital projects
     - Coding assignments
     - Research tasks
     - Technology integration
```

---

### 2. NEP 2020 Competency Framework

#### 2.1 Foundational Competencies

**NEP 2020 Competency Mapping:**
```yaml
Foundational Stage (Grades 1-2):
  Literacy:
    - Letter recognition
    - Phonics
    - Basic reading
    - Simple writing
    
  Numeracy:
    - Number recognition (1-100)
    - Basic operations (+, -)
    - Shapes and patterns
    
  Socio-emotional:
    - Self-awareness
    - Social interaction
    - Emotional regulation

Preparatory Stage (Grades 3-5):
  Literacy:
    - Reading comprehension
    - Creative writing
    - Grammar basics
    
  Numeracy:
    - All operations (×, ÷)
    - Fractions, decimals
    - Measurement
    
  Scientific Temper:
    - Observation skills
    - Questioning
    - Basic experiments

Middle Stage (Grades 6-8):
  Advanced Literacy:
    - Critical reading
    - Analytical writing
    - Research skills
    
  Advanced Numeracy:
    - Algebra basics
    - Geometry
    - Data handling
    
  Scientific Inquiry:
    - Hypothesis formation
    - Experimentation
    - Data analysis
    
  Computational Thinking:
    - Logical reasoning
    - Algorithm design
    - Problem decomposition

Secondary Stage (Grades 9-10):
  Subject Mastery:
    - Deep conceptual understanding
    - Application skills
    - Interdisciplinary connections
    
  Critical Thinking:
    - Analysis and evaluation
    - Synthesis
    - Problem-solving
    
  Life Skills:
    - Decision making
    - Stress management
    - Career awareness
```

---

### 3. Skill Progression Tracking

#### 3.1 Grade-wise Skill Development

**Critical Thinking Progression:**
```yaml
Grade 6:
  Level: Developing
  Skills:
    - Identify main ideas
    - Compare and contrast
    - Make simple inferences
  
  Assessment Criteria:
    - Can identify key information: 70%
    - Can compare two items: 65%
    - Can make basic inferences: 60%

Grade 7:
  Level: Proficient
  Skills:
    - Analyze complex information
    - Evaluate arguments
    - Draw logical conclusions
  
  Assessment Criteria:
    - Can analyze data: 75%
    - Can evaluate evidence: 70%
    - Can form conclusions: 70%

Grade 8:
  Level: Advanced
  Skills:
    - Synthesize information from multiple sources
    - Critique arguments
    - Solve complex problems
  
  Assessment Criteria:
    - Can synthesize information: 80%
    - Can critique effectively: 75%
    - Can solve complex problems: 75%

Progression Indicators:
  Developing → Proficient → Advanced → Expert
  
  Developing: Basic understanding, needs guidance
  Proficient: Independent application, consistent performance
  Advanced: Complex application, creative solutions
  Expert: Mastery, can teach others
```

---

## 📊 DATABASE SCHEMA

### Competency Mapping Table

```sql
CREATE TABLE competency_mapping (
  mapping_id INT PRIMARY KEY AUTO_INCREMENT,
  
  -- Curriculum Link
  subject_id INT NOT NULL,
  chapter_id INT,
  topic VARCHAR(300),
  grade VARCHAR(10) NOT NULL,
  
  -- Competency Details
  competency_type ENUM(
    'CRITICAL_THINKING', 'CREATIVITY', 'COLLABORATION',
    'COMMUNICATION', 'DIGITAL_LITERACY', 'PROBLEM_SOLVING',
    'SCIENTIFIC_TEMPER', 'COMPUTATIONAL_THINKING'
  ) NOT NULL,
  
  competency_description TEXT,
  
  -- Skill Level
  skill_level ENUM('DEVELOPING', 'PROFICIENT', 'ADVANCED', 'EXPERT') NOT NULL,
  
  -- Assessment
  assessment_method VARCHAR(500),
  success_criteria TEXT,
  
  -- Metadata
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  -- Foreign Keys
  FOREIGN KEY (subject_id) REFERENCES subjects(subject_id),
  
  -- Indexes
  INDEX idx_subject_grade (subject_id, grade),
  INDEX idx_competency (competency_type)
);
```

### Student Competency Assessment Table

```sql
CREATE TABLE student_competency_assessment (
  assessment_id INT PRIMARY KEY AUTO_INCREMENT,
  student_id INT NOT NULL,
  
  -- Academic Year
  academic_year VARCHAR(9) NOT NULL,
  grade VARCHAR(10) NOT NULL,
  
  -- Competencies (1-5 rating)
  critical_thinking_rating INT,
  creativity_rating INT,
  collaboration_rating INT,
  communication_rating INT,
  digital_literacy_rating INT,
  problem_solving_rating INT,
  
  -- Overall
  overall_competency_score DECIMAL(3,1),
  teacher_remarks TEXT,
  
  -- Assessment
  assessed_by INT,
  assessment_date DATE,
  
  -- Foreign Keys
  FOREIGN KEY (student_id) REFERENCES students(student_id),
  
  -- Indexes
  INDEX idx_student_year (student_id, academic_year)
);
```

---

## ⚙️ BUSINESS RULES

### Rule 1: Competency Progression Validation

```javascript
FUNCTION validate_competency_progression(student_id, grade, competency) {
  // Get previous grade competency level
  previous_grade = grade - 1
  previous_level = GET_COMPETENCY_LEVEL(student_id, previous_grade, competency)
  current_level = GET_COMPETENCY_LEVEL(student_id, grade, competency)
  
  // Define progression levels
  levels = ['DEVELOPING', 'PROFICIENT', 'ADVANCED', 'EXPERT']
  
  previous_index = levels.indexOf(previous_level)
  current_index = levels.indexOf(current_level)
  
  // Check if progression is appropriate
  IF current_index < previous_index {
    WARN(`${competency} level decreased from ${previous_level} to ${current_level}`)
    RECOMMEND_INTERVENTION(student_id, competency)
  }
  
  IF current_index > previous_index + 1 {
    WARN(`${competency} level jumped from ${previous_level} to ${current_level}`)
    VERIFY_ASSESSMENT(student_id, competency)
  }
  
  RETURN {
    valid: TRUE,
    progression: current_index - previous_index
  }
}
```

---

## 🔗 INTEGRATION POINTS

### Outbound Integrations

1. **Assessment Module**: Competency-based assessment
2. **Reports Module**: Skill development reports
3. **Student Portfolio**: Competency tracking

### Inbound Integrations

1. **Curriculum Design**: Competency requirements
2. **Subject Management**: Subject-wise skills

---

## 👥 USER WORKFLOWS

### Workflow: Map Competencies to Curriculum

**Actor:** Curriculum Designer  
**Duration:** 2-3 hours per subject

**Steps:**

1. Login to Curriculum Portal
2. Navigate to: Curriculum → Competency Mapping
3. Select subject and grade
4. For each chapter/topic:
   - Identify competencies developed
   - Define skill level expected
   - Set assessment criteria
5. Review cross-curricular competencies
6. Submit for approval
7. Academic committee reviews
8. Mapping activated

**Expected Outcome:** Competencies mapped to curriculum

---

**Status:** Production-Ready Documentation  
**Last Updated:** January 18, 2026  
**Version:** 2.0  
**Documentation Standard:** Enterprise Grade

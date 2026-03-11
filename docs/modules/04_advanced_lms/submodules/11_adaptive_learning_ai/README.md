# ADAPTIVE LEARNING AI

**Module:** Advanced LMS  
**Submodule Code:** LMS-ADAPT-011  
**Category:** AI-Powered Personalization  
**Priority:** Medium (P2)  
**Owners:** Academic Coordinator, Data Science Team, EdTech Lead

---

## OVERVIEW

The Adaptive Learning AI submodule uses machine learning and data analytics to personalize each student's learning journey within the LMS. Instead of a one-size-fits-all curriculum delivery, this submodule analyzes a student's performance patterns, learning speed, strengths, weaknesses, and preferred content types to dynamically adjust what content is shown, at what difficulty level, and in what sequence. It is the "intelligent tutor" that scales personalized education to every student simultaneously.

### Purpose

To ensure that every student receives content at the right difficulty level at the right time. A student struggling with "Quadratic Equations" should not be pushed to "Polynomials" until the foundation is solid. Conversely, a student who masters a topic quickly should not be forced to sit through basic exercises they have already outgrown.

### Scope

-   **Knowledge Graph Mapping:** Mapping curriculum topics into a dependency graph (prerequisites and progressions).
-   **Diagnostic Assessment:** Identifying gaps in a student's understanding at a topic level.
-   **Personalized Learning Pathways:** Dynamically generating the optimal sequence of content for each student.
-   **Difficulty Calibration:** Adjusting quiz and practice problem difficulty in real-time.
-   **Intelligent Content Recommendation:** Suggesting videos, readings, and exercises based on learning patterns.
-   **Teacher Intervention Alerts:** Flagging students who are stuck and need human help.

---

## KEY FEATURES

### 1. Curriculum Knowledge Graph

**Feature Description:**
A directed graph representing topic dependencies.

**Example (Mathematics Grade 8):**
```
Number Systems -> Integers -> Fractions -> Decimals -> Percentages
                                        -> Ratios (parallel)
Linear Equations -> [requires: Integers, Fractions]
Geometry Basics -> Angles -> Triangles -> Quadrilaterals
                                       -> Circle Theorems (Grade 9)
```

*   Each node is a "Learning Objective" (LO) with: ID, Topic Name, Difficulty Level, Estimated Learning Time.
*   Edges represent prerequisites. A student CANNOT unlock "Linear Equations" until they demonstrate proficiency in "Integers" AND "Fractions."

### 2. Diagnostic Pre-Assessment

**Feature Description:**
Mapping a student's current knowledge state.
*   At the start of each term (or when a student enrolls), the system administers a short adaptive diagnostic quiz (15-20 questions across all topics).
*   Based on responses, it plots the student's "Knowledge Map" -- green (mastered), yellow (partial), red (needs work).
*   This map drives the personalized learning pathway.

### 3. Dynamic Learning Pathway Generator

**Feature Description:**
The core personalization engine.

**Algorithm:**
1.  Start from the student's Knowledge Map (diagnostic results).
2.  Identify the "frontier" -- topics where the student has partial knowledge or is about to enter.
3.  Generate a sequence: Remediate Red topics -> Strengthen Yellow topics -> Introduce new Green-adjacent topics.
4.  At each step, serve content in the student's preferred modality: Video (for visual learners), Reading (for text-preference), Interactive Simulation (for kinesthetic).
5.  After each content piece, administer a quick 3-question check. If 3/3 correct, move forward. If < 2/3, re-present the topic with alternative content.

### 4. Real-Time Difficulty Calibration

**Feature Description:**
Practice problems that adjust to the student's level.
*   If a student answers 5 "Easy" problems correctly in a row, the system serves "Medium" problems.
*   If they get 3 "Medium" problems wrong, the system drops back to "Easy" and presents explanatory content.
*   This keeps the student in the "Zone of Proximal Development" -- challenging enough to learn, but not so hard as to frustrate.
*   The calibration follows Item Response Theory (IRT) -- each question has a pre-computed difficulty parameter.

---

## DATABASE SCHEMA

### 1. Knowledge Graph Nodes (`lms_knowledge_graph`)

```sql
CREATE TABLE lms_knowledge_graph (
    node_id INT PRIMARY KEY AUTO_INCREMENT,
    subject_id INT NOT NULL,
    grade_id INT NOT NULL,
    
    topic_name VARCHAR(200),
    learning_objective TEXT,
    
    difficulty_level ENUM('FOUNDATIONAL', 'INTERMEDIATE', 'ADVANCED'),
    estimated_learning_hours DECIMAL(4,1),
    
    prerequisite_node_ids JSON, -- [12, 15] (must master these first)
    
    content_ids JSON -- [101, 102, 103] (linked content items)
);
```

### 2. Student Knowledge State (`lms_student_knowledge_state`)

```sql
CREATE TABLE lms_student_knowledge_state (
    state_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    node_id INT NOT NULL,
    
    mastery_level DECIMAL(5,2), -- 0.00 (no knowledge) to 100.00 (mastered)
    
    last_assessed_at DATETIME,
    questions_attempted INT DEFAULT 0,
    questions_correct INT DEFAULT 0,
    
    status ENUM('NOT_STARTED', 'IN_PROGRESS', 'MASTERED', 'NEEDS_REVIEW'),
    
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (node_id) REFERENCES lms_knowledge_graph(node_id)
);
```

### 3. Learning Pathway (`lms_learning_pathways`)

```sql
CREATE TABLE lms_learning_pathways (
    pathway_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    subject_id INT NOT NULL,
    
    current_node_id INT, -- Where the student is right now
    recommended_sequence JSON, -- [node_5, node_8, node_12, ...]
    
    pathway_generated_at DATETIME,
    pathway_version INT DEFAULT 1, -- Increments each time pathway is recalculated
    
    completion_percentage DECIMAL(5,2),
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

### 4. Intervention Alerts (`lms_ai_interventions`)

```sql
CREATE TABLE lms_ai_interventions (
    alert_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    student_id INT NOT NULL,
    subject_id INT NOT NULL,
    node_id INT NOT NULL,
    
    alert_type ENUM('STUCK', 'DECLINING_PERFORMANCE', 'DISENGAGEMENT', 'RAPID_MASTERY'),
    
    alert_message TEXT, -- "Student has attempted Topic X 5 times with <40% accuracy"
    
    teacher_action ENUM('PENDING', 'ACKNOWLEDGED', 'INTERVENTION_DONE', 'DISMISSED'),
    teacher_notes TEXT,
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## BUSINESS RULES

### Rule 1: Prerequisite Enforcement
*   A student CANNOT access content for a topic unless all prerequisite topics show `mastery_level >= 70%`. The locked topic displays: "Complete [Prerequisite Topic] first to unlock this section."

### Rule 2: Pathway Recalculation Triggers
*   The learning pathway is recalculated when: (a) A new diagnostic test is taken, (b) The student's mastery level changes significantly (>15% in any topic), or (c) The teacher manually overrides a node status.

### Rule 3: Teacher Override
*   Although the AI generates the pathway, the teacher has full authority to override. For example, if the AI recommends remediating "Fractions" but the teacher knows the student understands fractions (just had a bad test day), the teacher can manually set `mastery_level = 80%` and unlock the next topic.

### Rule 4: Stuck Student Escalation
*   If a student attempts the same topic more than 5 times with accuracy below 40%, the system creates a `STUCK` intervention alert for the teacher. The alert suggests: "Consider scheduling a 1-on-1 session or assigning peer tutoring."

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Student Progress (06):** Feeds mastery levels and learning trajectory data.
*   **To Psychometric Analysis (Module 05, Sub 10):** Provides learning pattern data for aptitude correlation.
*   **To Parent Portal:** Shows "Your child's personalized learning progress" with the Knowledge Map visualization.

### Inbound Relationships
*   **From Course Content (01):** Reads available content items (videos, readings, exercises) to recommend.
*   **From Online Assessments (03):** Uses quiz/test results to update `mastery_level`.
*   **From Academic Curriculum (Module 02):** Reads the syllabus to build the Knowledge Graph.

---

## USER WORKFLOWS

### Workflow 1: Student's Self-Paced Learning Session
**Actor:** Student

1.  **Login:** Opens LMS > "My Learning Path" for Mathematics.
2.  **Dashboard:** Sees a visual pathway: "Fractions (Mastered) -> Decimals (In Progress) -> Percentages (Locked)."
3.  **Learn:** Clicks "Decimals." System shows a 5-minute video explanation.
4.  **Practice:** After the video, system serves 5 easy problems. Student gets 4/5 correct.
5.  **Level Up:** System now serves 5 medium problems. Student gets 3/5. System identifies "decimal division" as the weak sub-topic.
6.  **Remediate:** Shows a targeted 3-minute video specifically on decimal division.
7.  **Retry:** Student gets 5/5 on retry. Mastery level jumps to 82%. "Percentages" topic is now unlocked.

### Workflow 2: Teacher Reviews AI Alerts
**Actor:** Mathematics Teacher

1.  **Dashboard:** Sees "3 Intervention Alerts for Grade 8B Mathematics."
2.  **Alert 1:** "Rohan is STUCK on Linear Equations (Attempted 6 times, accuracy 35%)."
3.  **Action:** Teacher schedules a 15-minute one-on-one during the free period. Marks alert as "Intervention Done."
4.  **Alert 2:** "Priya shows RAPID MASTERY. Completed 3 topics in 1 week (expected: 3 weeks)."
5.  **Action:** Teacher assigns Priya advanced enrichment material and nominates her as a Peer Tutor for the topic.

---

## EDGE CASES

### Edge Case 1: Knowledge Graph Circular Dependency
*   **Scenario:** An incorrectly configured knowledge graph has "Topic A requires Topic B" and "Topic B requires Topic A" -- creating a deadlock.
*   **Resolution:** The system runs a cycle detection algorithm during graph validation. Circular dependencies are flagged with an error: "Cycle detected: A -> B -> A. Please resolve before publishing."

### Edge Case 2: Student Bypasses Prerequisites via Teacher Override
*   **Scenario:** Teacher unlocks "Calculus" for a student who hasn't mastered "Pre-Calculus." The student struggles badly.
*   **Resolution:** The system tracks that this was a manual override. If the student's mastery drops below 30% in the unlocked topic, an alert is sent: "Teacher override may have been premature. Student struggling in Calculus (mastery: 25%). Consider re-locking."

### Edge Case 3: Insufficient Content for a Topic
*   **Scenario:** The AI wants to show alternative content for "Trigonometric Identities" (because the student didn't understand the first video), but there's only one video in the content library.
*   **Resolution:** System flags: "Content Deficit for Topic: Trigonometric Identities. Only 1 content item available. Recommendation engine cannot provide alternatives." The Academic Coordinator is notified to upload additional content.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `ai_mastery_threshold_pct` | 70% | Mastery level to unlock next topic |
| `ai_stuck_attempt_limit` | 5 | Attempts before flagging student as stuck |
| `ai_stuck_accuracy_threshold` | 40% | Accuracy below which "stuck" alert triggers |
| `ai_pathway_recalc_trigger_pct` | 15% | Mastery change that triggers pathway recalculation |
| `ai_difficulty_calibration_model` | `IRT_2PL` | Item Response Theory model for difficulty adjustment |
| `ai_teacher_override_allowed` | `true` | Can teachers override AI recommendations? |
| `ai_content_modality_preference` | `AUTO` | Options: `AUTO` (AI decides), `VIDEO`, `TEXT`, `INTERACTIVE` |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

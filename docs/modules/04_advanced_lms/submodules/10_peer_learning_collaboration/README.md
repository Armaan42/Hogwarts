# PEER LEARNING & COLLABORATION

**Module:** Advanced LMS  
**Submodule Code:** LMS-PEER-010  
**Category:** Social & Collaborative Learning  
**Priority:** Medium (P2)  
**Owners:** Academic Coordinator, Class Teachers, Student Council

---

## OVERVIEW

The Peer Learning & Collaboration submodule enables structured student-to-student learning within the LMS. It provides tools for study groups, peer tutoring, collaborative document editing, project teams, and peer feedback/review. Research consistently shows that students who teach concepts to peers retain knowledge more effectively (the "Protege Effect"), and this submodule operationalizes that principle within the school's digital ecosystem.

### Purpose

To foster a collaborative learning culture where students learn FROM and WITH each other, not just from teachers. It provides supervised digital spaces for group study, peer tutoring (where a strong student helps a struggling one), joint projects, and peer review of assignments -- all tracked and visible to teachers for monitoring and guidance.

### Scope

-   **Study Groups:** Student-created or teacher-assigned small groups for specific subjects.
-   **Peer Tutoring Program:** Matching academically strong students with those needing help.
-   **Collaborative Documents:** Google Docs-style real-time co-editing within the LMS.
-   **Project Teams:** Team formation, task assignment, and deliverable tracking for group projects.
-   **Peer Review:** Students reviewing and grading each other's work using teacher-defined rubrics.
-   **Knowledge Sharing Forum:** Student-authored study notes, solved examples, and tips.

---

## KEY FEATURES

### 1. Study Group Management

**Feature Description:**
Organized spaces for collaborative studying.
*   Students can create a study group (e.g., "Physics Olympiad Prep - Grade 10") with up to 8 members.
*   Teacher approval required for creation (prevents misuse).
*   Each group gets: a private chat channel, a shared file repository, and a collaborative whiteboard.
*   Groups can schedule "Study Sessions" (synced to a shared calendar) with optional video call via Virtual Classroom integration.

### 2. Peer Tutoring Matchmaking

**Feature Description:**
Smart pairing of tutors and learners.
*   Teachers nominate "Peer Tutors" -- students who scored A1/A2 in a subject.
*   Students needing help can request a peer tutor via the LMS.
*   System suggests matches based on: Subject, Availability (timetable free periods), Language preference.
*   Tutoring sessions are logged. The tutor receives "Community Service Hours" credit.

### 3. Collaborative Document Editor

**Feature Description:**
Real-time co-authoring within the LMS.
*   Teams can create shared documents (notes, lab reports, presentations).
*   Supports simultaneous editing with cursor presence ("Aarav is editing paragraph 3").
*   Version history with rollback capability.
*   Teacher can view the document, add comments, and track individual student contributions (word count per student, edit frequency).

### 4. Peer Review System

**Feature Description:**
Structured peer-to-peer feedback.
*   Teacher creates an assignment with "Peer Review" enabled.
*   After submission, each student's work is anonymously assigned to 2-3 peers for review.
*   Reviewers use a teacher-provided rubric (e.g., "Content: 5pts, Grammar: 3pts, Creativity: 2pts").
*   The original student sees aggregated peer feedback alongside the teacher's marks.
*   Peer review marks contribute a configurable percentage (e.g., 20%) to the final grade.

---

## DATABASE SCHEMA

### 1. Study Groups (`lms_study_groups`)

```sql
CREATE TABLE lms_study_groups (
    group_id INT PRIMARY KEY AUTO_INCREMENT,
    group_name VARCHAR(150),
    subject_id INT,
    
    created_by_student_id INT,
    approved_by_teacher_id INT,
    
    max_members INT DEFAULT 8,
    
    status ENUM('PENDING_APPROVAL', 'ACTIVE', 'ARCHIVED'),
    
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### 2. Group Members (`lms_study_group_members`)

```sql
CREATE TABLE lms_study_group_members (
    member_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    group_id INT NOT NULL,
    student_id INT NOT NULL,
    
    role ENUM('LEADER', 'MEMBER'),
    joined_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (group_id) REFERENCES lms_study_groups(group_id)
);
```

### 3. Peer Tutoring Sessions (`lms_peer_tutoring`)

```sql
CREATE TABLE lms_peer_tutoring (
    tutoring_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    tutor_student_id INT NOT NULL,
    learner_student_id INT NOT NULL,
    subject_id INT NOT NULL,
    
    session_date DATE,
    duration_minutes INT,
    
    tutor_feedback TEXT, -- "Good understanding of basics, struggles with derivations"
    learner_rating INT, -- 1-5 stars
    
    community_hours_credited DECIMAL(3,1) DEFAULT 0,
    
    status ENUM('REQUESTED', 'MATCHED', 'COMPLETED', 'CANCELLED')
);
```

### 4. Peer Reviews (`lms_peer_reviews`)

```sql
CREATE TABLE lms_peer_reviews (
    review_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    assignment_id BIGINT NOT NULL,
    
    author_student_id INT NOT NULL, -- Who wrote the original work
    reviewer_student_id INT NOT NULL, -- Who is reviewing
    
    rubric_scores JSON, -- {"content": 4, "grammar": 3, "creativity": 2}
    total_peer_score DECIMAL(5,1),
    written_feedback TEXT,
    
    is_anonymous BOOLEAN DEFAULT TRUE,
    reviewed_at DATETIME,
    
    FOREIGN KEY (assignment_id) REFERENCES lms_assignments(assignment_id)
);
```

---

## BUSINESS RULES

### Rule 1: Supervision Requirement
*   All peer interactions are monitored. Chat messages in study groups pass through a keyword filter (flagging profanity, bullying language). Flagged messages are sent to the Class Teacher for review.

### Rule 2: Peer Tutor Eligibility
*   A student can be a "Peer Tutor" in a subject ONLY if their most recent exam grade in that subject is A1 or A2. If their grade drops below B1 in the next exam, tutor status is auto-revoked.

### Rule 3: Peer Review Anonymity
*   Peer reviewers are always anonymous to the author. The author sees "Reviewer 1 said..." not "Priya said..." This prevents social pressure from influencing honest feedback.

### Rule 4: Contribution Tracking
*   In collaborative documents, the system tracks each student's contribution percentage. If a group project has 4 members and "Arjun" contributed 5% while others contributed 30%+ each, the teacher is alerted: "Potential free-rider detected in Group 7."

---

## INTEGRATION POINTS

### Outbound Relationships
*   **To Gamification (07):** Study group activity earns "Collaboration Points" and badges.
*   **To Student Progress (06):** Peer tutoring sessions and group participation feed engagement metrics.
*   **To Virtual Classroom (09):** Study groups can launch video sessions.

### Inbound Relationships
*   **From Assignment Module (02):** Receives assignment details for peer review workflows.
*   **From Grade Calculation:** Reads student grades for peer tutor eligibility checks.

---

## USER WORKFLOWS

### Workflow 1: Student Creates a Study Group
**Actor:** Student (Group Leader)

1.  **Request:** Student navigates to LMS > Peer Learning > "Create Study Group."
2.  **Details:** Names it "Chemistry Olympiad Prep." Selects subject: Chemistry. Invites 5 classmates.
3.  **Approval:** Class Teacher receives notification. Reviews and approves.
4.  **Activity:** Group is now ACTIVE. Members chat, share notes, and schedule weekly study sessions.
5.  **Outcome:** At term-end, the group's average Chemistry score improved by 12% compared to non-group peers.

### Workflow 2: Peer Review Assignment Cycle
**Actor:** English Teacher

1.  **Create:** Teacher creates "Write an Essay on Climate Change" with Peer Review enabled.
2.  **Submit:** 40 students submit their essays by the deadline.
3.  **Assign:** System randomly assigns each essay to 3 anonymous peer reviewers.
4.  **Review:** Over 3 days, reviewers read and score the essay using the rubric.
5.  **Results:** Author sees: Peer Average Score (8.2/10), Teacher Score (7.5/10). Final Grade = 0.2 * Peer + 0.8 * Teacher = 7.64.
6.  **Feedback:** Author reads constructive peer comments and improves their writing.

---

## EDGE CASES

### Edge Case 1: Cyberbullying in Study Group Chat
*   **Scenario:** A student sends hurtful messages targeting another student in a study group.
*   **Resolution:** The keyword filter flags the message immediately. The Class Teacher is notified. The offending student's chat access is temporarily suspended. The incident is logged in the Discipline Module.

### Edge Case 2: Peer Reviewer Gives Unfair Scores
*   **Scenario:** A student gives all peers 1/10 out of spite.
*   **Resolution:** The system detects "Outlier Reviewers" -- if a reviewer's average score deviates by more than 2 standard deviations from other reviewers' averages for the same assignment, their reviews are flagged. The teacher can exclude their scores from the final calculation.

### Edge Case 3: Student in Multiple Study Groups
*   **Scenario:** An enthusiastic student joins 6 study groups, overloading their schedule.
*   **Resolution:** The system enforces `max_groups_per_student` (default: 3). The student must leave an existing group before joining a new one.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `peer_study_group_max_members` | 8 | Max students per study group |
| `peer_max_groups_per_student` | 3 | Max simultaneous group memberships |
| `peer_tutor_min_grade` | `A2` | Minimum grade to be eligible as peer tutor |
| `peer_review_reviewers_per_submission` | 3 | Number of peer reviewers per assignment |
| `peer_review_weight_in_grade_pct` | 20% | Peer review contribution to final grade |
| `peer_chat_profanity_filter` | `true` | Enable keyword-based content filtering? |
| `peer_contribution_alert_threshold_pct` | 10% | Below this % contribution triggers free-rider alert |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

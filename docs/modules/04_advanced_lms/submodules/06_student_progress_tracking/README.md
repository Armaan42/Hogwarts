# STUDENT PROGRESS TRACKING

**Module:** Advanced LMS  
**Submodule Code:** LMS-PROGRESS-006  
**Category:** Analytics  
**Priority:** High (P1)

## Overview

Comprehensive tracking of student learning progress including content completion, assignment submission, quiz performance, and engagement metrics.

## Purpose

Monitor student learning journey, identify struggling students, provide personalized recommendations, and generate progress reports.

## Key Features

### 6.1 Progress Dashboard

**Student Dashboard:**
```
Student: Rohan Sharma (Grade 9A)
Subject: Mathematics

Overall Progress: 65%

Content Completion:
- Unit 1: Number Systems - 100% ✓
- Unit 2: Algebra - 80% (in progress)
- Unit 3: Geometry - 20%
- Unit 4: Statistics - 0%

Assignments:
- Submitted: 8/10 (80%)
- Average Score: 75%
- Pending: 2

Quizzes:
- Attempted: 6/8 (75%)
- Average Score: 78%

Video Lectures:
- Watched: 12/15 (80%)
- Avg Completion: 85%

Engagement Score: 72/100
```

### 6.2 Learning Path

**Personalized Recommendations:**
```
Based on your progress, we recommend:

1. Complete pending assignments:
   - Algebra Worksheet (Due: 25-Apr)
   - Geometry Problems (Due: 28-Apr)

2. Watch missed lectures:
   - "Factoring Quadratic Equations"
   - "Graphing Parabolas"

3. Revise weak areas:
   - Quiz score low in "Rational Numbers" (50%)
   - Suggested: Rewatch lecture, practice worksheet

4. Next milestone:
   - Complete Unit 2 by 30-Apr to stay on track
```

### 6.3 Teacher View

**Class Progress Overview:**
```
Subject: Mathematics (Grade 9A)
Teacher: Mrs. Sharma

Class Average: 68%

Top Performers (>80%):
1. Priya Singh - 92%
2. Amit Kumar - 87%
3. Rohan Sharma - 85%

At Risk (<50%):
1. Rahul Verma - 45%
2. Sneha Patel - 42%

Content Completion:
- Unit 1: 95% (class average)
- Unit 2: 60%
- Unit 3: 25%

Assignment Submission Rate: 75%
Quiz Participation: 80%
```

## Data Fields

```
progress_id (PK)
student_id (FK)
subject_id (FK)
content_completion_percentage
assignments_submitted_count
assignments_total_count
quizzes_attempted_count
quizzes_total_count
average_quiz_score
average_assignment_score
videos_watched_count
videos_total_count
engagement_score
last_updated
```

---


## EDGE CASES

### Edge Case 1: Transfer Student with No Historical Data
*   **Scenario:** A student joins mid-year from another school. The LMS has zero activity history for them.
*   **Resolution:** The system initializes the student's profile with baseline data from their Transfer Certificate (previous grade, marks). The progress dashboard shows "Data available from [Join Date] onwards." Year-over-year comparisons are suppressed until a full year of data is accumulated.

### Edge Case 2: Teacher Deletes Course Content After Student Completed It
*   **Scenario:** Teacher removes an outdated module. The student's progress was 100% but now the denominator changes.
*   **Resolution:** Completed milestones are preserved as "Archived Completions." The system shows: "Completed: 5/5 modules (1 archived)." The overall completion percentage is recalculated based on current active modules only, but a footnote acknowledges the archived work.

### Edge Case 3: Parent Compares Siblings' Progress
*   **Scenario:** A parent has 2 children. They see one child's progress at 85% and the other at 40% in the same subject. They escalate to the Principal.
*   **Resolution:** The system provides context alongside raw numbers. The 40% student may be in a section with a harder teacher or different course pace. The progress report includes "Section Average: 42%" which contextualizes the individual score.

---

## REAL-WORLD SCENARIOS

### Scenario A: Teacher Identifies At-Risk Students Early
*   3 weeks into the term, the progress tracking dashboard shows 5 students with < 15% engagement (no video views, no quiz attempts, no assignment submissions).
*   Teacher schedules individual check-ins. Discovers: 2 had family issues, 1 had tech problems (no internet at home), 2 were disengaged. Tailored interventions are applied.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `progress_engagement_low_threshold_pct` | 20% | Below this = "At Risk" alert |
| `progress_streak_reset_after_days` | 3 | Days of inactivity before streak resets |
| `progress_parent_view_enabled` | `true` | Can parents see child's progress dashboard? |
| `progress_comparison_mode` | `SELF_ONLY` | Options: `SELF_ONLY`, `SECTION_AVERAGE`, `GRADE_AVERAGE` |
| `progress_auto_certificate_on_completion` | `true` | Auto-issue certificate when course is 100% complete? |
| `progress_milestone_notifications` | `true` | Send notifications at 25%, 50%, 75%, 100% milestones? |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

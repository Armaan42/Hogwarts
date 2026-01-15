# ASSIGNMENT & HOMEWORK

**Module:** Advanced LMS  
**Submodule Code:** LMS-ASSIGNMENT-002  
**Category:** Digital Learning  
**Priority:** Critical (P0)

## Overview

Digital assignment creation, distribution, submission, grading, and feedback system with plagiarism detection and late submission tracking.

## Purpose

Eliminate paper-based homework, enable online submissions, automate grading for objective questions, provide instant feedback, and track completion rates.

## Key Features

### 2.1 Assignment Types

**Assignment Categories:**
```
1. Written Assignment:
   - Essay, Report, Project
   - File upload (PDF, Word)
   - Word limit: 500-2000 words

2. Problem Solving:
   - Mathematics problems
   - Science numerical
   - Upload solution (handwritten scan or typed)

3. Programming Assignment:
   - Code submission
   - Auto-graded (test cases)
   - Languages: Python, Java, C++

4. Group Assignment:
   - Collaborative work
   - Multiple students submit together
   - Peer evaluation

5. Presentation:
   - PPT/Video submission
   - Oral presentation (recorded)
```

### 2.2 Assignment Creation

**Teacher Creates Assignment:**
```
Subject: English (Grade 9)
Assignment: "Essay on Climate Change"

Details:
- Title: Climate Change Essay
- Description: Write 800-1000 words on impact of climate change
- Type: Written Assignment
- Due Date: 25-Apr-2024, 11:59 PM
- Total Marks: 20
- Submission Format: PDF only
- Late Submission: Allowed (penalty: -2 marks/day)
- Plagiarism Check: Enabled
- Rubric:
  - Content (10 marks)
  - Grammar (5 marks)
  - Structure (5 marks)

Assigned to: Grade 9A, 9B (60 students)
```

### 2.3 Student Submission

**Submission Process:**
```
Student: Rohan Sharma (Grade 9A)
Assignment: Climate Change Essay

Steps:
1. Student logs into LMS
2. Navigate to Assignments > Pending
3. Click "Climate Change Essay"
4. View assignment details, rubric
5. Click "Submit Assignment"
6. Upload file: "Climate_Essay_Rohan.pdf" (1.2 MB)
7. System validates:
   - File format: PDF ✓
   - File size: <10 MB ✓
   - Submission before deadline: ✓
8. Run plagiarism check (30 seconds)
9. Plagiarism score: 12% (Acceptable, <20%)
10. Confirm submission
11. System sends confirmation email
12. Status: SUBMITTED (on time)
```

### 2.4 Auto-Grading

**Objective Question Auto-Grading:**
```
Assignment: Mathematics Quiz (10 MCQs)

Question 1: What is 2 + 2?
Student Answer: 4
Correct Answer: 4
Result: ✓ Correct (1 mark)

Question 2: Solve: x + 5 = 10
Student Answer: x = 5
Correct Answer: x = 5
Result: ✓ Correct (1 mark)

...

Total: 8/10 (80%)
Auto-graded in 2 seconds
Instant feedback to student
```

### 2.5 Manual Grading & Feedback

**Teacher Grades Essay:**
```
Assignment: Climate Change Essay
Student: Rohan Sharma

Teacher Review:
- Content: 8/10 (Good research, relevant examples)
- Grammar: 4/5 (Minor spelling errors)
- Structure: 4/5 (Clear introduction, conclusion)

Total: 16/20 (80%)

Feedback:
"Excellent work, Rohan! Your essay demonstrates good understanding of the topic. The examples from India (floods, droughts) were particularly relevant. Work on proofreading to avoid spelling mistakes. Overall, well done!"

Graded by: Mrs. Kumar
Graded on: 26-Apr-2024
```

### 2.6 Late Submission Handling

**Late Submission:**
```
Assignment: Climate Change Essay
Due Date: 25-Apr-2024, 11:59 PM
Student Submission: 27-Apr-2024, 10:30 AM
Days Late: 2 days

Late Penalty: -2 marks/day × 2 days = -4 marks

Original Score: 16/20
After Penalty: 12/20 (60%)

Status: SUBMITTED_LATE
Student notified of penalty
```

## Data Fields

```
assignment_id (PK)
subject_id (FK)
title
description
assignment_type (WRITTEN/PROBLEM_SOLVING/PROGRAMMING/GROUP/PRESENTATION)
due_date
total_marks
submission_format (PDF/WORD/CODE/VIDEO)
late_submission_allowed (boolean)
late_penalty_per_day
plagiarism_check_enabled (boolean)
rubric (JSON)
assigned_to_sections (JSON)
created_by (teacher_id)
created_date
status (DRAFT/PUBLISHED/CLOSED)
```

### Submission Table
```
submission_id (PK)
assignment_id (FK)
student_id (FK)
submission_date
file_path
plagiarism_score
is_late (boolean)
days_late
marks_obtained
feedback
graded_by (teacher_id)
graded_date
status (PENDING/GRADED/RESUBMISSION_REQUIRED)
```

## Business Rules

### Plagiarism Detection
```
FUNCTION check_plagiarism(submission):
  // Upload to plagiarism detection API
  result = CALL_PLAGIARISM_API(submission.file_path)
  
  plagiarism_score = result.similarity_percentage
  
  IF plagiarism_score > 50:
    submission.status = "FLAGGED_HIGH_PLAGIARISM"
    NOTIFY_TEACHER("High plagiarism detected: " + student.name)
    NOTIFY_STUDENT("Your submission has high similarity with existing content")
  ELSE IF plagiarism_score > 20:
    submission.status = "FLAGGED_MODERATE_PLAGIARISM"
    NOTIFY_TEACHER("Moderate plagiarism: " + student.name)
  ELSE:
    submission.status = "SUBMITTED"
  END IF
  
  submission.plagiarism_score = plagiarism_score
  SAVE(submission)
  
  RETURN submission
END FUNCTION
```

## Integration Points

### Connects TO:
1. **Student Management** - Get student list for assignment distribution
2. **Grading Module** - Post marks to gradebook
3. **Communication Module** - Send notifications
4. **Plagiarism API** - Check content originality
5. **Cloud Storage** - Store submissions

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

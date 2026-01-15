# ONLINE ASSESSMENTS & QUIZZES

**Module:** Advanced LMS  
**Submodule Code:** LMS-QUIZ-003  
**Category:** Digital Learning  
**Priority:** High (P1)

## Overview

Create and conduct online quizzes, tests, and assessments with multiple question types, auto-grading, time limits, and anti-cheating measures.

## Purpose

Enable digital assessments, provide instant results, reduce teacher grading workload, and maintain question banks for reuse.

## Key Features

### 3.1 Question Types

**Supported Formats:**
```
1. Multiple Choice (MCQ):
   - Single correct answer
   - Auto-graded
   - Example: What is 2+2? (a) 3 (b) 4 (c) 5 (d) 6

2. Multiple Select:
   - Multiple correct answers
   - Partial marking
   - Example: Select prime numbers: (a) 2 (b) 4 (c) 5 (d) 6

3. True/False:
   - Binary choice
   - Auto-graded

4. Fill in the Blanks:
   - Text input
   - Auto-graded (exact match)
   - Example: The capital of India is _____.

5. Short Answer:
   - Text input (50-100 words)
   - Manual grading required

6. Essay:
   - Long text (500+ words)
   - Manual grading

7. Matching:
   - Match items from two lists
   - Auto-graded
```

### 3.2 Quiz Creation

**Teacher Creates Quiz:**
```
Subject: Mathematics (Grade 9)
Quiz: "Quadratic Equations - Unit Test"

Settings:
- Duration: 30 minutes
- Total Questions: 20
- Total Marks: 20
- Passing Marks: 8 (40%)
- Attempts Allowed: 1
- Shuffle Questions: Yes
- Shuffle Options: Yes
- Show Correct Answers: After submission
- Availability: 20-Apr-2024, 9:00 AM to 9:30 AM

Questions:
Q1. (MCQ, 1 mark) Solve: x² - 5x + 6 = 0
Q2. (Fill blank, 1 mark) The roots of x² - 4 = 0 are ___ and ___.
...
Q20. (Short answer, 1 mark) Explain the quadratic formula.
```

### 3.3 Anti-Cheating Measures

**Proctoring Features:**
```
1. Randomization:
   - Questions shuffled for each student
   - Options shuffled
   - Different question sets from pool

2. Time Limit:
   - Strict timer (30 minutes)
   - Auto-submit when time expires

3. Browser Lockdown:
   - Full-screen mode enforced
   - Tab switching detected
   - Copy-paste disabled

4. IP Tracking:
   - Record student IP address
   - Flag multiple IPs (sharing account)

5. Webcam Monitoring (Optional):
   - Record student during quiz
   - AI detects suspicious behavior
```

### 3.4 Auto-Grading

**Instant Results:**
```
Student: Rohan Sharma
Quiz: Quadratic Equations

Submitted: 20-Apr-2024, 9:28 AM
Time Taken: 28 minutes

Auto-Graded Results:
MCQs (15 questions): 12/15 (80%)
Fill Blanks (3 questions): 2/3 (67%)
Short Answer (2 questions): Pending manual grading

Current Score: 14/18 (78%)
Final Score: Pending (after manual grading)
```

## Data Fields

```
quiz_id (PK)
subject_id (FK)
title
description
duration_minutes
total_marks
passing_marks
attempts_allowed
shuffle_questions (boolean)
show_correct_answers (NEVER/AFTER_SUBMISSION/AFTER_DUE_DATE)
availability_start
availability_end
created_by (teacher_id)
status (DRAFT/PUBLISHED/CLOSED)
```

### Question Bank Table
```
question_id (PK)
question_type (MCQ/MULTIPLE_SELECT/TRUE_FALSE/FILL_BLANK/SHORT_ANSWER/ESSAY)
question_text
options (JSON - for MCQ)
correct_answer
marks
difficulty_level (EASY/MEDIUM/HARD)
tags (JSON)
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

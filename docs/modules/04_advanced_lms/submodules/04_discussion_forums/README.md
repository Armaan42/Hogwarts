# DISCUSSION FORUMS

**Module:** Advanced LMS  
**Submodule Code:** LMS-FORUM-004  
**Category:** Collaboration  
**Priority:** Medium (P2)

## Overview

Online discussion boards for students and teachers to ask questions, share knowledge, discuss topics, and collaborate on learning.

## Purpose

Foster collaborative learning, enable peer-to-peer help, provide platform for doubt clarification, and build learning community.

## Key Features

### 4.1 Forum Structure

**Forum Categories:**
```
Subject: Mathematics (Grade 9)

Forums:
1. General Discussion
   - Announcements
   - Class updates

2. Doubt Clarification
   - Post questions
   - Peer/teacher answers

3. Study Groups
   - Collaborative learning
   - Group discussions

4. Resources Sharing
   - Share useful links
   - Study materials
```

### 4.2 Discussion Thread

**Sample Thread:**
```
Title: "How to solve quadratic equations by factoring?"
Posted by: Rohan Sharma (Student)
Date: 15-Apr-2024, 4:30 PM
Category: Doubt Clarification

Question:
"I understand the quadratic formula, but I'm confused about factoring method. Can someone explain with an example?"

Replies:
1. Priya Singh (Student) - 15-Apr-2024, 5:00 PM:
   "I can help! For x² - 5x + 6 = 0, find two numbers that multiply to 6 and add to -5. That's -2 and -3. So (x-2)(x-3) = 0"

2. Mrs. Sharma (Teacher) - 15-Apr-2024, 6:00 PM:
   "Great explanation, Priya! Rohan, here's a video that might help: [link]"

3. Rohan Sharma (Student) - 15-Apr-2024, 7:00 PM:
   "Thank you! I understand now."

Status: RESOLVED
Upvotes: 5
Views: 23
```

### 4.3 Moderation

**Content Moderation:**
```
- Inappropriate language filtered
- Spam detection
- Teacher can pin important threads
- Teacher can close/lock threads
- Students can report posts
```

## Data Fields

```
thread_id (PK)
subject_id (FK)
category
title
posted_by (user_id)
post_date
content
status (OPEN/RESOLVED/CLOSED)
is_pinned (boolean)
upvotes
views
```

---


## EXTENDED DATABASE SCHEMA

### 3. Forum Moderation Queue (`lms_forum_moderation`)

```sql
CREATE TABLE lms_forum_moderation (
    mod_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    post_id BIGINT NOT NULL,
    
    flag_reason ENUM('PROFANITY', 'SPAM', 'BULLYING', 'OFF_TOPIC', 'INAPPROPRIATE'),
    flagged_by INT, -- Student or AI system
    
    moderator_action ENUM('PENDING', 'APPROVED', 'HIDDEN', 'DELETED'),
    moderator_id INT,
    moderator_notes TEXT,
    
    flagged_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    resolved_at DATETIME
);
```

---

## EDGE CASES

### Edge Case 1: Student Posts Homework Answer in Forum
*   **Scenario:** A student posts the complete solution to an active assignment in a discussion thread, allowing others to copy.
*   **Resolution:** Teacher marks the post as "Spoiler/Answer Leak". The post is hidden with the message: "This reply has been hidden by the teacher as it contains assignment answers." The student receives a warning.

### Edge Case 2: Excessive Thread Necromancy
*   **Scenario:** A student replies to a thread from 6 months ago, causing it to jump to the top of active discussions.
*   **Resolution:** Threads older than `forum_thread_lock_days` (default: 90) are auto-locked. The student sees "This thread is archived. Create a new discussion if you have a related question."

### Edge Case 3: Anonymous Posting Abuse
*   **Scenario:** If anonymous posting is enabled, students use it to make inappropriate comments.
*   **Resolution:** While posts appear anonymous to other students, the system logs the actual author. Teachers can "Reveal Identity" on flagged anonymous posts for disciplinary purposes. This is disclosed in the forum rules.

---

## REAL-WORLD SCENARIOS

### Scenario A: Flipped Classroom Discussion
*   Teacher posts a 5-minute video on "Photosynthesis" and creates a discussion: "What would happen if plants could only photosynthesize at night?"
*   Students respond with hypotheses. Teacher engages, asks follow-up questions. The discussion becomes the foundation for the next class.

### Scenario B: Cross-Section Q&A During Exams
*   Before the Mid-Term, the teacher creates a "Doubt Clearing Forum" open to all sections of Grade 10.
*   Students post questions. Both teachers and peer tutors can answer. The thread is pinned for 2 weeks.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `forum_moderation_mode` | `POST_MODERATION` | Options: `PRE_MODERATION`, `POST_MODERATION`, `NONE` |
| `forum_profanity_filter` | `true` | Auto-flag posts with inappropriate language? |
| `forum_anonymous_posting` | `false` | Allow anonymous posts? |
| `forum_thread_lock_days` | 90 | Days after which threads auto-lock |
| `forum_max_post_length_chars` | 5000 | Maximum characters per post |
| `forum_file_upload_enabled` | `true` | Allow image/file attachments? |
| `forum_upvote_enabled` | `true` | Allow students to upvote helpful answers? |

---


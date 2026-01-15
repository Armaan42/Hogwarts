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

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

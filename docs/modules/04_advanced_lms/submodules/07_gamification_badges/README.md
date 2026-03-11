# GAMIFICATION & BADGES

**Module:** Advanced LMS  
**Submodule Code:** LMS-GAMIFY-007  
**Category:** Engagement  
**Priority:** Medium (P2)

## Overview

Gamification system with points, badges, leaderboards, and achievements to motivate students and increase engagement.

## Purpose

Increase student motivation, reward learning milestones, foster healthy competition, and make learning fun.

## Key Features

### 7.1 Points System

**Earning Points:**
```
Activity                    Points
Complete video lecture      10
Submit assignment on time   20
Score >80% in quiz         30
Help peer in forum         15
Daily login streak (7 days) 50
Complete unit              100
```

**Student Points:**
```
Student: Rohan Sharma
Total Points: 1,250

Breakdown:
- Content Completion: 450 points
- Assignments: 320 points
- Quizzes: 380 points
- Forum Participation: 100 points

Level: 5 (Expert Learner)
Next Level: 1,500 points (250 points to go)
```

### 7.2 Badges

**Achievement Badges:**
```
Badge: "Early Bird"
Criteria: Submit 5 assignments before deadline
Rarity: Common
Earned by: 45% of students

Badge: "Quiz Master"
Criteria: Score 100% in 3 quizzes
Rarity: Rare
Earned by: 12% of students

Badge: "Helping Hand"
Criteria: Answer 10 forum questions
Rarity: Uncommon
Earned by: 25% of students

Badge: "Perfect Attendance"
Criteria: Login daily for 30 days
Rarity: Epic
Earned by: 5% of students
```

### 7.3 Leaderboard

**Class Leaderboard:**
```
Subject: Mathematics (Grade 9A)
Period: April 2024

Rank  Student          Points  Badges
1     Priya Singh      2,450   12
2     Amit Kumar       2,120   10
3     Rohan Sharma     1,850   8
4     Sneha Patel      1,650   7
5     Rahul Verma      1,420   6
```

## Data Fields

```
gamification_id (PK)
student_id (FK)
total_points
level
badges_earned (JSON)
leaderboard_rank
last_activity_date
```

---


## EXTENDED DATABASE SCHEMA

### 3. Leaderboard Snapshots (`lms_leaderboard`)

```sql
CREATE TABLE lms_leaderboard (
    snapshot_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    leaderboard_type ENUM('CLASS', 'GRADE', 'SCHOOL'),
    scope_id INT, -- Class ID, Grade ID, or NULL for school-wide
    
    period ENUM('WEEKLY', 'MONTHLY', 'TERM', 'ANNUAL'),
    period_start DATE,
    period_end DATE,
    
    rankings JSON, -- [{"student_id":101,"xp":450,"rank":1}, {"student_id":102,"xp":430,"rank":2}]
    
    generated_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

---

## EDGE CASES

### Edge Case 1: Gaming the XP System
*   **Scenario:** A student discovers that posting 100 low-effort forum replies earns more XP than solving 10 challenging problems.
*   **Resolution:** The system implements "Diminishing Returns." The first 5 forum posts per day earn full XP (10 each). Posts 6-10 earn 50% XP. Posts 11+ earn 0 XP. This prevents spam-grinding while encouraging genuine participation.

### Edge Case 2: Student Demoralized by Low Rank
*   **Scenario:** A struggling student is consistently ranked last on the leaderboard, causing discouragement.
*   **Resolution:** Configurable display options:
    *   `TOP_N_ONLY`: Only show Top 10; other students see their own rank privately.
    *   `PERSONAL_GROWTH`: Show "Your XP increased by 120 this week (15% improvement)" instead of comparative rank.
    *   `OPT_OUT`: Students can choose to hide from public leaderboards.

### Edge Case 3: Badge Exploit via Account Sharing
*   **Scenario:** Two students share login credentials. One completes activities on behalf of the other to earn badges.
*   **Resolution:** The system tracks device fingerprints and IP addresses. If two accounts consistently log in from the same device within short intervals, a "Shared Account Suspicion" flag is raised for admin review.

---

## REAL-WORLD SCENARIOS

### Scenario A: House Points Integration
*   The school's 4 Houses (Red, Blue, Green, Yellow) compete via the LMS gamification system.
*   Every XP earned by a student contributes to their House's total.
*   Monthly "House Cup" updated on the school digital signboard (integrated via the Communication Module).

### Scenario B: Year-End Awards from Gamification Data
*   At the Annual Day, awards are given: "Digital Learner of the Year" (highest XP), "Badge Master" (most badges), "Quiz Champion" (highest quiz scores).
*   Data is pulled directly from the gamification tables, replacing subjective teacher nominations with data-driven recognition.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `gamification_xp_per_assignment` | 20 | XP earned for submitting an assignment |
| `gamification_xp_per_quiz_correct` | 5 | XP per correct quiz answer |
| `gamification_xp_per_forum_post` | 10 | XP per forum post (subject to diminishing returns) |
| `gamification_daily_xp_cap` | 200 | Max XP earnable per day |
| `gamification_leaderboard_display` | `TOP_10` | Options: `ALL`, `TOP_10`, `PERSONAL_ONLY` |
| `gamification_diminishing_returns_threshold` | 5 | Actions per type per day before XP reduction |
| `gamification_house_integration` | `true` | Aggregate XP by school Houses? |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

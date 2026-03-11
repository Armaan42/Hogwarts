# VIDEO LECTURES & RESOURCES

**Module:** Advanced LMS  
**Submodule Code:** LMS-VIDEO-005  
**Category:** Digital Learning  
**Priority:** High (P1)

## Overview

Video hosting, streaming, and management platform for recorded lectures, tutorials, and educational videos with playback tracking and interactive features.

## Purpose

Deliver video-based learning, enable flipped classroom, provide on-demand access to lectures, and track video engagement.

## Key Features

### 5.1 Video Library

**Video Categories:**
```
1. Recorded Lectures:
   - Full class recordings
   - Topic-specific lectures
   - Duration: 15-45 minutes

2. Tutorials:
   - Step-by-step guides
   - Problem-solving videos
   - Duration: 5-15 minutes

3. Demonstrations:
   - Lab experiments
   - Practical demonstrations
   - Duration: 10-20 minutes

4. Guest Lectures:
   - Industry experts
   - Alumni talks
   - Duration: 30-60 minutes
```

### 5.2 Video Player Features

**Interactive Player:**
```
Features:
- Play/Pause, Seek
- Playback Speed (0.5x, 1x, 1.25x, 1.5x, 2x)
- Quality Selection (360p, 480p, 720p, 1080p)
- Subtitles/Captions
- Bookmarks (save timestamp)
- Notes (add notes at specific time)
- Quiz Integration (pop-up quiz at intervals)
```

### 5.3 Video Analytics

**Engagement Tracking:**
```
Video: "Introduction to Quadratic Equations"
Student: Rohan Sharma

Watch History:
- First Watch: 15-Apr-2024, 4:00 PM (watched 80%)
- Second Watch: 16-Apr-2024, 10:00 AM (watched 100%)

Engagement Metrics:
- Total Watch Time: 22 minutes (of 15 min video)
- Completion Rate: 100%
- Rewatched Sections: 3:20-5:40 (quadratic formula explanation)
- Quiz Score: 8/10 (embedded quiz)
```

## Data Fields

```
video_id (PK)
subject_id (FK)
title
description
duration_seconds
file_path (cloud storage)
thumbnail_url
upload_date
uploaded_by (teacher_id)
view_count
average_completion_rate
has_subtitles (boolean)
has_quiz (boolean)
```

---


## EXTENDED DATABASE SCHEMA

### 3. Video Analytics (`lms_video_analytics`)

```sql
CREATE TABLE lms_video_analytics (
    analytics_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    video_id BIGINT NOT NULL,
    student_id INT NOT NULL,
    
    total_watch_time_seconds INT,
    completion_percentage DECIMAL(5,2),
    
    replay_segments JSON, -- [{"start":120,"end":180,"replays":3}] (student rewatched 2:00-3:00 three times)
    playback_speed DECIMAL(3,1) DEFAULT 1.0,
    
    last_watched_position INT, -- Resume from here
    watched_at DATETIME,
    device_type ENUM('DESKTOP', 'TABLET', 'MOBILE'),
    
    FOREIGN KEY (video_id) REFERENCES lms_videos(video_id),
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## EDGE CASES

### Edge Case 1: Video Plays But Audio Fails
*   **Scenario:** A student reports "I can see the teacher but there's no sound" for a recorded lecture.
*   **Resolution:** The system stores videos with separate audio and video tracks. If the primary audio codec fails on certain browsers, the player offers a fallback: "Having audio issues? Click here for audio-only version." The system also provides a downloadable transcript (auto-generated via speech-to-text).

### Edge Case 2: Student Plays Video at 4x Speed to Game Completion
*   **Scenario:** Student plays a 20-minute video at 4x speed (5 min real time) just to mark it as "Completed" without actually learning.
*   **Resolution:** The system logs `playback_speed`. If average speed > 2x for more than 50% of the video, the completion is flagged as "Speed-Watched." Teacher can configure `video_speed_completion_cap` (default: 2x) -- playback above this speed does not count toward completion percentage.

### Edge Case 3: Copyright Content in Uploaded Videos
*   **Scenario:** A teacher uploads a YouTube documentary clip as "their lecture" -- potentially violating copyright.
*   **Resolution:** The system provides a "Content Declaration" checkbox during upload: "I confirm this content is original or I have the rights to distribute it." Additionally, the admin can enable an optional third-party copyright scan (integration with content identification APIs).

---

## REAL-WORLD SCENARIOS

### Scenario A: Flipped Classroom Model
*   Teacher records a 15-minute "Introduction to Quadratic Equations" video with embedded quizzes at 5:00, 10:00, and 14:00 marks.
*   Students watch at home. The quiz responses tell the teacher which concepts students understood and where they struggled.
*   Classroom time is then spent on problem-solving (the "hard" part), not on explanation.

### Scenario B: Teacher-to-Teacher Knowledge Sharing
*   The best English teacher records a masterclass on "Teaching Shakespeare to Grade 9."
*   This recording is shared internally with all English teachers across branches as a professional development resource.

---

## CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `video_max_upload_size_mb` | 500 | Maximum file size for video uploads |
| `video_supported_formats` | `['MP4','WEBM','MOV']` | Accepted video file types |
| `video_auto_transcode` | `true` | Auto-convert to adaptive streaming (HLS)? |
| `video_speed_completion_cap` | 2.0 | Max playback speed that counts toward completion |
| `video_auto_caption` | `true` | Auto-generate subtitles via speech-to-text? |
| `video_retention_months` | 24 | Months before old videos are archived |
| `video_download_allowed` | `false` | Can students download videos for offline viewing? |

---

**Status:** Production-Ready Documentation  
**Version:** 3.0  
**Last Updated:** March 2026

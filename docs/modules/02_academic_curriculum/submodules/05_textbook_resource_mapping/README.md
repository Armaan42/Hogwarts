# TEXTBOOK & RESOURCE MAPPING

**Module:** Academic Curriculum  
**Submodule Code:** CURR-RESOURCE-005  
**Category:** Academic Support  
**Priority:** High (P1)

## Overview

Maps textbooks, reference books, digital resources, and learning materials to curriculum topics for each grade and subject.

## Purpose

Ensure appropriate learning resources are available for all topics, maintain resource library, and track resource usage.

## Key Features

### 5.1 Textbook Mapping

**Grade 6 - Mathematics:**
```
Prescribed Textbook:
- NCERT Mathematics Class 6
- ISBN: 978-81-7450-635-5
- Publisher: NCERT
- Edition: 2024

Chapter Mapping:
Chapter 1: Knowing Our Numbers → Unit 1: Number Systems
Chapter 2: Whole Numbers → Unit 1: Number Systems
Chapter 3: Playing with Numbers → Unit 1: Number Systems
Chapter 4: Basic Geometrical Ideas → Unit 3: Geometry

Reference Books:
- RD Sharma Mathematics Class 6
- RS Aggarwal Mathematics Class 6
```

### 5.2 Digital Resources

**Online Resources:**
```
Topic: Photosynthesis

Videos:
- Khan Academy: Photosynthesis (15 min)
- BYJU'S: Process of Photosynthesis (10 min)

Simulations:
- PhET: Photosynthesis Lab Simulation
- Labster: Virtual Photosynthesis Experiment

Interactive:
- Quizlet: Photosynthesis Flashcards
- Kahoot: Photosynthesis Quiz
```

## Data Fields

```
resource_id (PK)
resource_type (TEXTBOOK/REFERENCE/DIGITAL/VIDEO)
title
author
publisher
isbn
url
topic_id (FK)
grade
subject_id (FK)
```

---

**Status:** Fully Documented  
**Last Updated:** January 15, 2026

# PLAGIARISM DETECTION

**Module:** Advanced LMS  
**Submodule Code:** LMS-PLAG-012  
**Category:** Academic Integrity  
**Priority:** High (P1)  
**Owner:** Academic Quality & LMS Team

---

## 📋 OVERVIEW

The Plagiarism Detection submodule provides comprehensive content originality verification for student submissions — including assignments, essays, projects, research papers, and thesis documents. It uses multi-engine similarity checking against institutional repositories, internet sources, published journals, and peer submissions to ensure academic integrity across all grade levels.

### Purpose

Safeguard academic integrity by detecting copied, paraphrased, or AI-generated content in student submissions; educate students about proper citation and referencing; provide teachers with detailed originality reports; and maintain an institutional content repository that grows with each submission to catch intra-school plagiarism.

### Scope

- **Similarity Detection**: Text-matching against internet, journals, institutional database
- **AI Content Detection**: GPT/AI-generated content identification
- **Paraphrase Detection**: Semantic similarity analysis beyond word-matching
- **Citation Verification**: Reference format validation (APA, MLA, Chicago, IEEE)
- **Institutional Repository**: Growing database of all student submissions
- **Report Generation**: Detailed originality reports with highlighted matches
- **Integration**: Direct integration with Assignment & Homework module

---

## 🎯 KEY FEATURES

### 12.1 Multi-Source Comparison Engine

**Detection Sources:**
```
Source Tier 1: Institutional Repository (HIGHEST PRIORITY)
  - All previous student submissions (current + past 10 years)
  - Teacher-uploaded reference materials
  - Internal research papers and projects
  - Previous year question paper answers
  
Source Tier 2: Internet & Web Content
  - 60+ billion web pages indexed
  - Wikipedia, educational websites
  - Blog posts, forum discussions
  - Open-access publications

Source Tier 3: Academic Databases
  - JSTOR, Google Scholar, PubMed
  - IEEE Xplore, ACM Digital Library
  - SHODHGANGA (Indian thesis repository)
  - INFLIBNET, DELNET (Indian library networks)

Source Tier 4: AI Content Detection
  - GPT-3.5/GPT-4 generated text patterns
  - Gemini, Claude, other LLM signatures
  - Statistical analysis: perplexity, burstiness
  - Vocabulary distribution analysis
```

### 12.2 Submission & Scanning Workflow

**Teacher-Initiated Check:**
```
Step 1: Teacher creates assignment in LMS
        → "Essay on Indian Independence Movement" (Grade 10 History)
        → Word limit: 1,000-1,500 words
        → Plagiarism check: ENABLED (auto-scan on submission)
        → AI detection: ENABLED
        → Threshold: 15% maximum similarity allowed

Step 2: Student Aarav Patel submits essay (1,247 words, PDF)

Step 3: System processes submission:
        [1] Extract text from PDF/DOCX → plain text conversion
        [2] Pre-process: remove headers, footers, bibliography
        [3] Segment into sentences and n-grams
        [4] Compare against Tier 1 sources (institutional) → 3 seconds
        [5] Compare against Tier 2 sources (internet) → 8 seconds
        [6] Compare against Tier 3 sources (academic) → 5 seconds
        [7] Run AI content detection → 4 seconds
        [8] Generate originality report → 2 seconds
        Total processing: ~22 seconds

Step 4: Report generated:
        Overall Similarity: 12% ✅ (below 15% threshold)
        Sources matched: 4
        AI-generated content: 0% ✅
        
Step 5: Teacher reviews report before grading
```

### 12.3 Originality Report

**Sample Report:**
```
╔══════════════════════════════════════════════════════════════════╗
║              ORIGINALITY REPORT                                  ║
╠══════════════════════════════════════════════════════════════════╣
║ Student: Aarav Patel (Grade 10, Section A)                       ║
║ Assignment: Essay on Indian Independence Movement                ║
║ Submitted: 15-Mar-2025, 11:30 PM                                ║
║ Word Count: 1,247 words                                          ║
║ Scanned: 15-Mar-2025, 11:30:22 PM                                ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║ OVERALL SIMILARITY: 12% ✅                                       ║
║ Threshold: 15% | Status: PASSED                                  ║
║                                                                  ║
║ SOURCE BREAKDOWN:                                                ║
║ ┌──────────────────────────────────────────────┐                  ║
║ │ 1. Wikipedia - "Quit India Movement"    → 5% │                  ║
║ │ 2. NCERT Textbook (Grade 10 History)    → 3% │                  ║
║ │ 3. Priya Sharma (Grade 10B, 2024)       → 2% │                  ║
║ │ 4. britannica.com                       → 2% │                  ║
║ └──────────────────────────────────────────────┘                  ║
║                                                                  ║
║ AI CONTENT DETECTION:                                            ║
║ ┌──────────────────────────────────────────────┐                  ║
║ │ AI-Generated Content: 0% ✅                   │                  ║
║ │ Perplexity Score: 78.4 (Human range: 60-100) │                  ║
║ │ Burstiness: 0.72 (Human range: 0.5-1.0)     │                  ║
║ └──────────────────────────────────────────────┘                  ║
║                                                                  ║
║ CITATION CHECK:                                                  ║
║ References cited: 6                                              ║
║ References verified: 5 ✅                                         ║
║ Missing citation: 1 ⚠️ (paragraph 3, line 2)                     ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

### 12.4 AI Content Detection

**Detection Methodology:**
```
FUNCTION detect_ai_content(text):
  // Method 1: Statistical Analysis
  perplexity = CALCULATE_PERPLEXITY(text)
  burstiness = CALCULATE_BURSTINESS(text)
  
  // Human text: High perplexity (60-100), high burstiness (0.5-1.0)
  // AI text: Low perplexity (10-40), low burstiness (0.1-0.3)
  
  // Method 2: Vocabulary Analysis
  vocab_diversity = UNIQUE_WORDS(text) / TOTAL_WORDS(text)
  // AI text tends to have lower vocabulary diversity
  
  // Method 3: Sentence Pattern Analysis
  sentence_lengths = GET_SENTENCE_LENGTHS(text)
  length_variance = VARIANCE(sentence_lengths)
  // AI text has more uniform sentence lengths
  
  // Method 4: Classifier Model
  classifier_score = AI_CLASSIFIER.predict(text)
  // Trained on 100K+ labeled human vs AI samples
  
  // Weighted score
  ai_probability = (
    normalize(perplexity) * 0.25 +
    normalize(burstiness) * 0.20 +
    normalize(vocab_diversity) * 0.15 +
    normalize(length_variance) * 0.10 +
    classifier_score * 0.30
  )
  
  IF ai_probability > 0.85:
    RETURN {verdict: "LIKELY_AI", confidence: ai_probability}
  ELIF ai_probability > 0.50:
    RETURN {verdict: "POSSIBLY_AI", confidence: ai_probability}
  ELSE:
    RETURN {verdict: "LIKELY_HUMAN", confidence: 1 - ai_probability}
  END IF
END FUNCTION
```

### 12.5 Threshold Configuration by Grade

**Grade-Wise Similarity Thresholds:**
```
Grade 1-5 (Primary):
  Plagiarism Check: DISABLED (age-appropriate)
  Teacher can manually scan if needed

Grade 6-8 (Middle School):
  Similarity Threshold: 25% (lenient - students learning citation)
  AI Detection: DISABLED
  Action on violation: Warning + citation guidance

Grade 9-10 (Secondary):
  Similarity Threshold: 15%
  AI Detection: ENABLED (advisory only)
  Action on violation: Grade penalty + resubmission required

Grade 11-12 (Senior Secondary):
  Similarity Threshold: 10%
  AI Detection: ENABLED (strict)
  Action on violation: Zero marks + disciplinary committee referral

College/University Level:
  Similarity Threshold: 8%
  AI Detection: ENABLED (strict)
  Thesis threshold: 5%
  Action on violation: Course failure + academic misconduct hearing
```

---

## 📊 DATA FIELDS

### Plagiarism Scan Record

```sql
CREATE TABLE lms_plagiarism_scans (
    scan_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    submission_id BIGINT NOT NULL,
    student_id INT NOT NULL,
    assignment_id BIGINT NOT NULL,
    
    -- Scan Details
    scan_timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    scan_duration_seconds INT,
    word_count INT,
    
    -- Similarity Results
    overall_similarity_pct DECIMAL(5,2),
    internet_similarity_pct DECIMAL(5,2),
    institutional_similarity_pct DECIMAL(5,2),
    academic_similarity_pct DECIMAL(5,2),
    sources_matched INT,
    source_details JSON,       -- [{url, pct, matched_text}]
    
    -- AI Detection
    ai_detection_enabled BOOLEAN DEFAULT TRUE,
    ai_probability DECIMAL(5,4),
    ai_verdict ENUM('LIKELY_HUMAN', 'POSSIBLY_AI', 'LIKELY_AI'),
    perplexity_score DECIMAL(8,2),
    burstiness_score DECIMAL(5,4),
    
    -- Citation Check
    references_cited INT DEFAULT 0,
    references_verified INT DEFAULT 0,
    missing_citations INT DEFAULT 0,
    
    -- Threshold & Decision
    threshold_pct DECIMAL(5,2),
    status ENUM('PASSED', 'WARNING', 'FAILED', 'MANUAL_REVIEW'),
    teacher_reviewed BOOLEAN DEFAULT FALSE,
    teacher_override ENUM('ACCEPT', 'REJECT', 'RESUBMIT'),
    teacher_notes TEXT,
    
    -- Report
    report_url VARCHAR(255),
    
    INDEX idx_student (student_id),
    INDEX idx_assignment (assignment_id),
    INDEX idx_status (status)
);
```

---

## 📋 BUSINESS RULES

### Rule 1: Auto-Scan on Submission
```
FUNCTION on_assignment_submitted(submission):
  assignment = GET_ASSIGNMENT(submission.assignment_id)
  
  IF assignment.plagiarism_check_enabled:
    scan = QUEUE_PLAGIARISM_SCAN(submission)
    SET submission.status = "PENDING_SCAN"
    
    // Process scan
    result = RUN_SCAN(submission.content)
    
    IF result.overall_similarity > assignment.threshold:
      SET submission.status = "FLAGGED"
      NOTIFY teacher: "Submission by {student.name} flagged: {result.similarity}% similarity"
      NOTIFY student: "Your submission requires review. Similarity: {result.similarity}%"
    ELSE:
      SET submission.status = "SCAN_PASSED"
    END IF
  END IF
END FUNCTION
```

### Rule 2: Repeat Offender Escalation
```
FUNCTION check_repeat_offender(student_id):
  violations = COUNT(scans WHERE student_id = student_id AND status = 'FAILED' AND academic_year = current_year)
  
  SWITCH violations:
    CASE 1: SEND_WARNING(student, "First citation warning")
    CASE 2: NOTIFY_CLASS_TEACHER(student, "Second plagiarism violation")
    CASE 3: ESCALATE_TO_COORDINATOR(student, "Third violation - disciplinary review needed")
    CASE 4+: ESCALATE_TO_PRINCIPAL(student, "Repeat offender - academic misconduct hearing")
  END SWITCH
END FUNCTION
```

---

## 🔄 INTEGRATION POINTS

| Connected Module | Data Exchanged | Direction |
|---|---|---|
| LMS - Assignments (02) | Submission content, assignment settings | Inbound |
| Assessment & Exams (05) | Exam answer scripts for plagiarism check | Inbound |
| Discipline & Behavior (10) | Academic misconduct incidents | Outbound |
| Communication (30) | Plagiarism alerts, citation guidance | Outbound |
| Document & Certificate (28) | Originality certificates for thesis/projects | Outbound |

---

## 👤 USER WORKFLOWS

### Workflow: Research Project Submission (Grade 12)

```
Context: Priya Singh submits her Grade 12 research project on "Impact of Social Media on Adolescent Mental Health" (4,500 words).

Step 1: Priya uploads project document (DOCX, 4,500 words)
Step 2: System auto-scans (processing: 45 seconds for long document)
Step 3: Results:
        Similarity: 18% ⚠️ (threshold: 10%)
        - 8% from Wikipedia articles
        - 5% from a published journal (cited but improperly formatted)
        - 3% from peer submission (Rohan's project, same topic)
        - 2% from NCERT Psychology textbook
        AI Detection: 4% possibly AI (within acceptable range)
Step 4: System flags submission: "Similarity exceeds threshold"
Step 5: Teacher Mrs. Gupta reviews the detailed report
Step 6: Teacher notes: "Wikipedia citations need proper formatting. Journal citation format incorrect (use APA). Overlap with Rohan's project appears to be common knowledge — acceptable."
Step 7: Teacher action: RESUBMIT with proper citations
Step 8: Priya resubmits with corrected citations
Step 9: Rescan: Similarity: 7% ✅ (properly cited sources excluded)
Step 10: Submission accepted and graded
```

---

## ⚠️ EDGE CASES

### Edge Case 1: Common Knowledge False Positive
- **Scenario:** Multiple students write "The Indian Constitution was adopted on 26th January 1950" — flagged as plagiarism.
- **Resolution:** System maintains a "Common Knowledge Dictionary" of facts, dates, formulas that are excluded from similarity scoring. Teachers can add phrases to the exclusion list.

### Edge Case 2: Self-Plagiarism
- **Scenario:** Student reuses content from their own previous assignment in a new submission.
- **Resolution:** Self-plagiarism is flagged separately (labeled "Self-Citation"). Teacher decides if acceptable based on context. Config: `allow_self_citation = false` (default).

### Edge Case 3: Non-English Submissions
- **Scenario:** Student submits an essay in Hindi or regional language.
- **Resolution:** System supports Hindi, Tamil, Telugu, Marathi, Bengali, Gujarati detection through transliteration-aware matching. Coverage may be lower than English (40-60% internet coverage for Hindi vs 90%+ for English).

### Edge Case 4: Code Plagiarism (Computer Science)
- **Scenario:** Two students submit identical Python code for a programming assignment.
- **Resolution:** Code plagiarism uses structure-aware comparison (AST matching) instead of text matching. Variable renaming, comment changes, and whitespace changes don't evade detection. Similarity considers logic flow, not just syntax.

---

## ⚙️ CONFIGURATION PARAMETERS

| Parameter | Default | Description |
|---|---|---|
| `plagiarism_auto_scan` | `true` | Auto-scan all submissions on upload |
| `default_similarity_threshold` | 15 | Default max similarity % allowed |
| `ai_detection_enabled` | `true` | Enable AI-generated content detection |
| `ai_detection_threshold` | 50 | AI probability % to flag as "POSSIBLY_AI" |
| `scan_institutional_db` | `true` | Compare against institutional repository |
| `scan_internet` | `true` | Compare against internet sources |
| `scan_academic_db` | `true` | Compare against academic databases |
| `exclude_bibliography` | `true` | Exclude reference section from similarity |
| `exclude_quotes` | `true` | Exclude properly quoted text from similarity |
| `allow_self_citation` | `false` | Allow reuse of own previous submissions |
| `code_plagiarism_engine` | `AST_MATCHING` | Code comparison method |
| `supported_languages` | `['en','hi','ta','te','mr','bn','gu']` | Languages supported for detection |
| `report_retention_years` | 5 | Years to retain plagiarism reports |
| `max_file_size_mb` | 25 | Maximum file size for scanning |

---

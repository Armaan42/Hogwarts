# [MODULE NAME] MODULE - COMPLETE DEPENDENCY ANALYSIS

## MODULE OVERVIEW

**Name:** [Module Name] Module 
**Role:** [Brief description of module's purpose - 1-2 sentences] 
**Type:** [Core/Academic/Operations/Finance/Student Services/etc.] 
**Dependencies:** [X]+ modules [depend on/provide data to] this module 

**Primary Functions:**
- [Function 1]
- [Function 2]
- [Function 3]
- [Function 4]
- [Function 5]
- [Add more as needed]

---

## OUTBOUND CONNECTIONS ([This Module] → Other Modules)

### 1. TO [TARGET MODULE NAME]

**WHY This Connection Exists:**
[Explain the business rationale - Why must these modules communicate? What problem does this solve? What would break if this connection didn't exist?]

**DATA FLOW:**
- **[Data Category 1]:**
 - [Specific field 1] ([data type/format])
 - [Specific field 2] ([data type/format])
 - [Specific field 3] ([data type/format])
- **[Data Category 2]:**
 - [Specific field 1]
 - [Specific field 2]
- **Data Volume:** [X records/day, Y MB/month]
- **Frequency:** [Real-time/Hourly/Daily/Weekly/Monthly/On-demand]
- **Direction:** [One-way/Bidirectional]

**TRIGGER EVENT:**
- [User action that initiates transfer]
- [System-generated trigger]
- [Scheduled event]
- **Timing:** [Real-time/Batch processing at HH:MM/Event-driven]

**IMPACT:**
- **[Specific Scenario Name]:**
 - [Step 1 with actual data/amounts/dates]
 - [Step 2 with actual data/amounts/dates]
 - [Step 3 with actual data/amounts/dates]
 - **Result:** [Concrete outcome with numbers]
- **Business Outcome:** [What changes for users/business]
- **User Experience:** [How this affects end users]
- **Downstream Effects:** [What other modules are impacted]

**BUSINESS LOGIC:**
```
FUNCTION [function_name]([parameters]):
 // [Brief description of what this function does]
 
 // Validation checks
 IF [condition]:
 [validation logic]
 RETURN {error: "[error message]"}
 END IF
 
 // Main processing
 [variable] = [operation]
 
 FOR each [item] IN [collection]:
 [processing logic]
 END FOR
 
 // Decision rules
 IF [condition]:
 [action]
 ELSE IF [condition]:
 [action]
 ELSE:
 [default action]
 END IF
 
 // Error handling
 TRY:
 [risky operation]
 CATCH [error]:
 LOG_ERROR([error])
 NOTIFY [stakeholder]
 RETURN {status: "FAILED", reason: [error]}
 END TRY
 
 // Notify dependent modules
 NOTIFY [target_module]([data])
 
 RETURN {status: "SUCCESS", data: [result]}
END FUNCTION
```

**EXAMPLE:**
- **[Real-world Scenario Name]:**
 - **Context:** [Setup the scenario]
 - **Step 1:** [Action with specific data - e.g., "Rohan (Student ID: 2024/06/123) submits fee payment ₹25,000 on Jan 15, 2024"]
 - **Step 2:** [System processing - e.g., "System validates payment, updates fee record"]
 - **Step 3:** [Data transfer - e.g., "Fee module sends payment confirmation to Student Management"]
 - **Step 4:** [Downstream impact - e.g., "Student status updated to 'Fees Paid', Parent receives SMS"]
 - **Expected Outcome:** [Final result with concrete data]
 - **Edge Case 1:** [What if payment fails? - Include failure scenario]
 - **Edge Case 2:** [What if partial payment? - Include alternative scenario]

---

### 2. TO [SECOND TARGET MODULE]

[Repeat the same structure as above for each outbound connection]

---

### 3. TO [THIRD TARGET MODULE]

[Continue for all outbound connections - typically 6-10 connections]

---

## INBOUND CONNECTIONS (Other Modules → [This Module])

### FROM [SOURCE MODULE 1]

**WHY:** [Brief explanation of why this module sends data here]

**DATA RECEIVED:**
- [Data field 1]
- [Data field 2]
- [Data field 3]

**IMPACT:**
- **[Scenario]:**
 - [How received data is used]
 - [What actions are triggered]
 - [Example with concrete data]

**TRIGGER:** [What causes this data transfer]

---

### FROM [SOURCE MODULE 2]

[Repeat for all inbound connections - typically 3-5 connections]

---

## SUMMARY

**[Module Name] connects to [X]+ modules**

**[Category Name] Breakdown:**
- **[Subcategory 1]:** [Details with numbers]
- **[Subcategory 2]:** [Details with numbers]
- **[Subcategory 3]:** [Details with numbers]

**Key Metrics:**
- **Total [Items]:** [X] items
- **[Metric 1]:** [Value] ([unit])
- **[Metric 2]:** [Value] ([unit])
- **[Metric 3]:** [Value] ([unit])
- **Annual [Volume]:** [Value]

**[Process Name] Workflow:**
1. **[Phase 1]:** [Description]
2. **[Phase 2]:** [Description]
3. **[Phase 3]:** [Description]
4. **[Phase 4]:** [Description]
5. **[Phase 5]:** [Description]

**Technology Integration:**
- **[Tool/System 1]:** [Purpose]
- **[Tool/System 2]:** [Purpose]
- **[Tool/System 3]:** [Purpose]
- **[Tool/System 4]:** [Purpose]

**Real-world Example Scenarios:**

**Scenario 1: [Scenario Name]**
- **[Phase/Time 1]:**
 - [Detailed step with dates, amounts, names]
 - [Detailed step with dates, amounts, names]
- **[Phase/Time 2]:**
 - [Detailed step]
 - [Detailed step]
- **[Phase/Time 3]:**
 - [Detailed step]
 - [Result with concrete data]

**Scenario 2: [Scenario Name]**
[Repeat structure]

**Analytics & Performance:**
- **[Metric Category 1]:**
 - [Specific metric]: [Value]
 - [Specific metric]: [Value]
- **[Metric Category 2]:**
 - [Specific metric]: [Value]
 - [Specific metric]: [Value]

**Compliance & Audit:**
- **[Compliance Area 1]:** [Details]
- **[Compliance Area 2]:** [Details]
- **[Audit Frequency]:** [Schedule]

**Challenges:**
- **[Challenge 1]:** [Description]
- **[Challenge 2]:** [Description]
- **[Challenge 3]:** [Description]

**Best Practices:**
- **[Practice 1]:** [Description]
- **[Practice 2]:** [Description]
- **[Practice 3]:** [Description]

**Data Freshness:**
- **Real-time:** [What data is real-time]
- **Daily:** [What updates daily]
- **Weekly:** [What updates weekly]
- **Monthly:** [What updates monthly]
- **Annually:** [What updates annually]

**Future Enhancements:**
- **[Enhancement 1]:** [Description]
- **[Enhancement 2]:** [Description]
- **[Enhancement 3]:** [Description]

This module is the **"[descriptive nickname]"** - [comprehensive description of module's role, impact, and value proposition, emphasizing how it enables other modules, ensures compliance, optimizes operations, or enhances user experience].

---

## TEMPLATE USAGE GUIDELINES

**Target Length:** 1,200-1,500 lines for full detailed modules

**Required Elements Checklist:**
- [ ] MODULE OVERVIEW (Name, Role, Type, Dependencies, Primary Functions)
- [ ] WHY This Connection Exists (for EACH outbound connection)
- [ ] DATA FLOW (specific fields, volume, frequency, direction)
- [ ] TRIGGER EVENT (user actions, system triggers, timing)
- [ ] IMPACT (scenarios with concrete data, outcomes, user experience)
- [ ] BUSINESS LOGIC (pseudo-code with validation, error handling)
- [ ] EXAMPLE (real-world scenarios, step-by-step, edge cases)
- [ ] INBOUND CONNECTIONS (reverse flows, feedback loops)

**Quality Standards:**
- Use **concrete data**: Names (Rohan, Priya, Aarav), amounts (₹25,000), dates (Jan 15, 2024), IDs (2024/06/123)
- Include **Indian context**: Currency (₹), boards (CBSE, ICSE), exams (JEE, NEET), cities (Delhi, Mumbai)
- Provide **step-by-step workflows**: Phase 1 → Phase 2 → Phase 3 with dates and actions
- Add **metrics and analytics**: Actual numbers, percentages, volumes
- Include **edge cases**: What happens when things fail or deviate from normal flow
- Show **error handling**: Validation checks, failure scenarios, recovery procedures

**Content Guidelines:**
- **Be specific, not generic**: "40 computers × ₹50,000 = ₹20,00,000" NOT "purchased equipment"
- **Use real examples**: "Rohan scored 85% in admission test" NOT "student performed well"
- **Include timing**: "Real-time updates, Batch processing at 11:59 PM daily" NOT "periodic updates"
- **Show calculations**: "Monthly depreciation: ₹33,333 (₹20,00,000 × 20% ÷ 12)" NOT "depreciation calculated"
- **Provide context**: "180 students in Grade 6 → Need 6 sections (30 students each)" NOT "sections created"

**Avoid:**
- Vague statements without numbers
- Generic examples without names/dates/amounts
- Missing error handling in business logic
- Incomplete workflows without all phases
- Summary without concrete metrics

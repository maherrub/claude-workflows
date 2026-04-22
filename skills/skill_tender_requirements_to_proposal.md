# Skill: Tender Requirements to Proposal Conversion (Optimized)

## Metadata
- **Skill ID**: skill_tender_requirements_to_proposal
- **Version**: 2.0 (Token-Optimized & Section-Based)
- **Author Role**: Solutions Architect
- **Use Case**: Converting RFP/tender documents into structured proposal responses with token efficiency
- **Last Updated**: 2026-04-22

---

## 1. Purpose & Trigger Conditions

### When to Use This Skill
Trigger this skill when:
- User uploads a tender document (RFP, ITT, or similar procurement document)
- User provides a Word template for proposal response
- User explicitly requests: "Convert tender to proposal", "Generate proposal response", "Map tender requirements"
- User mentions creating a proposal response maintaining tender structure
- Task involves responding to every section, subsection, and point in original order

### What This Skill Does
- Extracts requirements from tender document
- Maps each requirement to corresponding template section
- Generates proposal responses in original order
- **NEW**: Processes section-by-section to optimize token usage
- **NEW**: Maintains state across multiple conversations
- **NEW**: Returns only response text (no requirement header)

---

## 2. Token Optimization Strategy

### The Problem
Large tender documents (50+ pages) + detailed responses = high token consumption = hitting limits mid-document

### The Solution: Section-by-Section Processing

#### **2.1 Initial Setup (Conversation 1)**

**Step 1: Extract & Organize Structure**

USER INSTRUCTION:
```
"Initialize proposal conversion:
- TENDER: [upload document]
- TEMPLATE: [upload document]  
- TENDERER: [Company Name]
- OUTPUT: Section index only (no responses yet)"
```

CLAUDE OUTPUT:
```
Provide structured breakdown ONLY:

SECTION 1: General Information [pages 1-5]
  1.1 Company Profile [requirement count: 3]
  1.2 Experience [requirement count: 2]
  1.3 Team [requirement count: 4]

SECTION 2: Technical Approach [pages 6-12]
  2.1 Architecture [requirement count: 5]
  2.2 Implementation [requirement count: 6]
  2.3 Security [requirement count: 4]

SECTION 3: Commercial Terms [pages 13-18]
  3.1 Pricing [requirement count: 2]
  3.2 Timeline [requirement count: 3]

TOTAL REQUIREMENTS: [X]
TOKEN ESTIMATE: [X] tokens (for full conversion)
```

**Tokens Used**: ~500-1000 tokens

---

#### **2.2 Process Each Section Separately (Conversations 2, 3, 4...)**

**For Each Section, Use This Format:**

USER INSTRUCTION:
```
"RESUME PROPOSAL CONVERSION
- Tenderer: [Company Name]
- Template: [Reference or re-upload if needed]
- Process: SECTION 2.1 ONLY
- Requirements count: 5
- Template language: 'The Tenderer proposes...', 'The Tenderer confirms...'
- Format: Only responses (no requirement headers)
- Previous completion: Completed through Section 1.3
- Status: Continue from Section 2.1"
```

CLAUDE OUTPUT FORMAT:
```
SECTION 2: Technical Approach
2.1 Architecture

[Direct response 1 - no requirement header]
[Direct response 2]
[Direct response 3]
[Direct response 4]
[Direct response 5]

---

STATUS: ✓ Section 2.1 Complete
NEXT: Section 2.2 Implementation
TOKENS USED THIS SECTION: [X]
TOKENS REMAINING: [X]
```

**Tokens Used**: ~1500-3000 tokens per section (depends on complexity)

---

### 2.3 Resumption Protocol

**When resuming a section that was partially completed:**

USER INSTRUCTION:
```
"RESUME PROPOSAL CONVERSION (PARTIAL)
- Tenderer: [Company Name]
- Section: 2.1 Architecture
- Previously completed: Requirements 1-3 (of 5)
- Resume from: Requirement 4
- Action: Provide responses 4-5 only

PREVIOUSLY COMPLETED RESPONSES:
[Paste the 3 completed responses from previous conversation]

NOW PROVIDE:
Response 4: [new content]
Response 5: [new content]
"
```

CLAUDE OUTPUT:
```
[Response 4 only]
[Response 5 only]

STATUS: ✓ Section 2.1 Complete
NEXT: Section 2.2
```

---

## 3. Response Format (Token-Optimized)

### OLD Format (Removed):
```
REQUIREMENT: [text]
PROPOSED RESPONSE: [response]
```
**Problem**: Extra text = wasted tokens

### NEW Format (Optimized):
```
[Direct response only]
```

**Example - Old (Wasteful):**
```
REQUIREMENT: The solution must support multi-region deployment
PROPOSED RESPONSE: The Tenderer proposes a distributed cloud architecture...
```

**Example - New (Optimized):**
```
The Tenderer proposes a distributed cloud architecture leveraging [Cloud Provider] 
regions across [specified geographies] to ensure data residency compliance and 
optimal latency. The Tenderer confirms that the infrastructure supports real-time 
synchronization across regions with automated failover orchestration.
```

**Token Savings**: ~30% per response

---

## 4. State Management Across Conversations

### Tracking Progress

**Create a Progress File** (User maintains this):
```markdown
# Proposal Conversion Progress

**Tenderer**: [Company Name]
**Tender**: [Document Name & Date]
**Template**: [Template Name]
**Started**: [Date]

## Completion Status

| Section | Subsection | Requirements | Status | Conversation |
|---------|-----------|--------------|--------|--------------:|
| 1 | 1.1 Company Profile | 3 | ✓ Complete | Conv #1 |
| 1 | 1.2 Experience | 2 | ✓ Complete | Conv #1 |
| 1 | 1.3 Team | 4 | ✓ Complete | Conv #1 |
| 2 | 2.1 Architecture | 5 | ⏳ In Progress | Conv #2 |
| 2 | 2.2 Implementation | 6 | ⬜ Not Started | - |
| 2 | 2.3 Security | 4 | ⬜ Not Started | - |
| 3 | 3.1 Pricing | 2 | ⬜ Not Started | - |
| 3 | 3.2 Timeline | 3 | ⬜ Not Started | - |

## Completed Responses (Paste Below for Reference)

### SECTION 1.1 Company Profile
[Completed response 1]
[Completed response 2]
[Completed response 3]

## Next Steps
- Process Section 2.1 Architecture (5 requirements)
- Estimated tokens: 2000-2500
```

---

## 5. Recommended Workflow

### Workflow A: Small Documents
- Single conversation: Initialize + Process all sections
- Token budget: 8000-12000 tokens

### Workflow B: Medium Documents
- Conversation 1: Initialize
- Conversation 2-4: Process sections
- Conversation 5: Compile
- Token budget: ~2500 tokens/conversation

### Workflow C: Large Documents
- Conversation 1: Initialize
- Conversation 2-N: One section per conversation
- Final: Compile
- Token budget: 3000-5000 tokens/conversation

### Workflow D: Very Large Documents
- Conversation 1: Structure
- Conversation 2-N: One subsection per conversation
- Final: Compile
- Token budget: 2000-3000 tokens/conversation

---

## 6. Quick Reference: How to Use

### First Time:
1. Upload tender + template
2. Say: "Initialize proposal conversion using skill_tender_requirements_to_proposal"
3. I'll provide section structure

### Each Section:
1. Say: "Process SECTION [X.Y] with [requirement count]"
2. Optionally paste progress file
3. I'll provide responses
4. Save to your progress file

### Final:
1. Say: "Compile proposal - here are all responses"
2. Provide template
3. I'll create final DOCX

---

**End of FILE 1**

---

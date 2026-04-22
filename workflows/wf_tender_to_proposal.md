# Workflow: Tender to Proposal

## Metadata
- **Workflow ID**: WF-001
- **File**: `wf_tender_to_proposal.md`
- **Version**: 1.0
- **Author Role**: Solutions Architect
- **Purpose**: End-to-end process for converting tender/RFP documents into structured proposal responses
- **Last Updated**: 2026-04-22

---

## Quick Start

To trigger this workflow, say any of the following:

- "Run workflow tender to proposal"
- "Convert this tender to a proposal"
- "Start proposal conversion"
- "I have an RFP I need to respond to"

---

## Prerequisites

Before starting, ensure you have:

1. **Tender document** — the RFP, ITT, or procurement document (PDF or DOCX)
2. **Proposal template** — a DOCX template for the response (selected from `templates/` or uploaded)
3. **Company name** — the tendering company name to use in responses

---

## Skills Used

| Skill | File | Purpose |
|-------|------|---------|
| Tender Requirements to Proposal | `skills/sk_tender_requirements_to_proposal.md` | Extracts requirements, generates section-by-section responses |

---

## Templates

This workflow supports multiple proposal templates. Available templates are stored in `templates/` with prefix `tp_`.

### Current Templates

| ID | File | Description |
|----|------|-------------|
| — | — | No templates yet — user will upload during first run |

> **Note**: When templates are added to the repo, update this table. During Step 1, Claude will ask the user to select a template if more than one is available.

---

## Workflow Steps

### Step 1: Gather Inputs

**Claude asks the user for:**

1. **Tender document** — "Please upload the tender/RFP document."
2. **Template selection** — Claude checks `templates/` for available `tp_` files:
   - If **no templates exist**: "Please upload the DOCX template you'd like to use for the proposal response."
   - If **one template exists**: "I found `[template name]` in templates. Would you like to use this, or upload a different one?"
   - If **multiple templates exist**: "I found the following templates — which one would you like to use?"
     - List each template with its description from the table above
     - Allow the user to pick one or upload a new one
3. **Company name** — "What is the tendering company name? (This will be used as 'The Tenderer' in responses.)"

**Output**: All three inputs confirmed.

---

### Step 2: Initialize — Extract Section Structure

**Invoke skill**: `sk_tender_requirements_to_proposal` — Section 2.1 (Initial Setup)

**Claude action**:
- Read the tender document
- Extract all sections, subsections, and requirements
- Determine document size and recommend a workflow type (A/B/C/D)
- Present the section index to the user

**Output**:
```
SECTION 1: [Title] [pages X-Y]
  1.1 [Subsection] [requirement count: N]
  1.2 [Subsection] [requirement count: N]
  ...

TOTAL REQUIREMENTS: [X]
RECOMMENDED WORKFLOW: [A/B/C/D]
```

**Checkpoint**: Claude provides an initialized `pt_` tracker for the user to commit to `projects/<project_name>/`.

---

### Step 3: Process Sections

**Invoke skill**: `sk_tender_requirements_to_proposal` — Section 2.2 (Section Processing)

**Claude action**:
- Process sections according to the recommended workflow type:
  - **Type A (Small)**: All sections in a single conversation
  - **Type B (Medium)**: Multiple sections per conversation
  - **Type C (Large)**: One section per conversation
  - **Type D (Very Large)**: One subsection per conversation
- For each section, generate responses using the template's language patterns
- Responses use direct format — no requirement headers, only the proposal text

**Per-section output**:
```
SECTION [X]: [Title]
[X.Y] [Subsection]

[Response 1]
[Response 2]
...

STATUS: ✓ Section [X.Y] Complete
NEXT: Section [X.Z]
```

**Checkpoint after each session**: Claude provides updated `pt_` tracker content with completed sections marked and next action identified.

---

### Step 4: Resume (if needed)

**When**: The conversation was interrupted or a new session is started.

**Claude action**:
1. Pull the repo and read the relevant `pt_` tracker
2. Identify last completed section and next pending item
3. Resume from the next pending section
4. If a section was partially completed, follow the resumption protocol in the skill (Section 2.3)

**User prompt to resume**: "Resume proposal conversion" or "Continue tender to proposal"

---

### Step 5: Compile Final Document

**When**: All sections are marked ✓ in the project tracker.

**Claude action**:
1. Confirm all sections are complete by checking the `pt_` tracker
2. Compile all responses into the selected DOCX template
3. Preserve template formatting, section numbering, and structure
4. Generate the final proposal document

**Output**: A DOCX file ready for download.

**Checkpoint**: Claude updates the `pt_` tracker status to ✓ Complete and notes the compilation date.

---

### Step 6: Quality Assurance

**Claude action**:
- Walk through the QA checklist from the project tracker
- Flag any gaps: missing sections, thin responses, numbering mismatches
- User confirms or requests revisions

**On completion**: Project can be moved to `archived/` when no longer active.

---

## Workflow Diagram

```
┌─────────────────────────────────────────────────────────┐
│                    USER TRIGGERS WORKFLOW                │
│  "Convert this tender to a proposal"                    │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
              ┌────────────────────────┐
              │  STEP 1: Gather Inputs │
              │  • Tender document     │
              │  • Select template     │
              │  • Company name        │
              └───────────┬────────────┘
                          │
                          ▼
              ┌────────────────────────┐
              │  STEP 2: Initialize    │
              │  • Extract structure   │
              │  • Recommend workflow  │
              │  • Create pt_ tracker  │
              └───────────┬────────────┘
                          │
                          ▼
              ┌────────────────────────┐
              │  STEP 3: Process       │◄──────┐
              │  • Section by section  │       │
              │  • Generate responses  │       │
              │  • Checkpoint after    │       │
              │    each session        │       │
              └───────────┬────────────┘       │
                          │                    │
                          ▼                    │
                   ┌──────────────┐            │
                   │ All sections │──No───────►│
                   │  complete?   │   STEP 4:  │
                   └──────┬───────┘   Resume   │
                          │                    
                         Yes                   
                          │
                          ▼
              ┌────────────────────────┐
              │  STEP 5: Compile       │
              │  • Merge into DOCX     │
              │  • Apply template      │
              └───────────┬────────────┘
                          │
                          ▼
              ┌────────────────────────┐
              │  STEP 6: QA Review     │
              │  • Checklist review    │
              │  • Final sign-off      │
              └────────────────────────┘
```

---

## Error Handling

| Scenario | Action |
|----------|--------|
| Token limit approaching mid-section | Save progress, checkpoint, resume in next session |
| Template not provided | Ask user to upload or select from `templates/` |
| Ambiguous requirements in tender | Flag to user with specific section reference, ask for clarification |
| Tender document unreadable | Ask user to re-upload in a different format (PDF → DOCX or vice versa) |
| Section responses too thin | Flag during QA, offer to regenerate specific sections |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-04-22 | Initial workflow definition |

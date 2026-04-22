# Claude Workflows

A structured repository of reusable workflows, skills, templates, and project trackers designed for use with Claude AI. This repo acts as Claude's external memory — enabling consistent, resumable work across multiple sessions and devices.

## How It Works

1. **Start a session** — Ask Claude to pull this repo: `clone https://github.com/maherrub/claude-workflows.git`
2. **Claude reads the README** — Understands available workflows, skills, and active project state
3. **Work proceeds** — Claude follows the relevant workflow, applies skills, and produces outputs
4. **Checkpoint** — Claude provides updated project tracker content; you commit and push to GitHub
5. **Resume anytime** — From any device (Mac, office PC, mobile), Claude pulls the repo and picks up where it left off

## Repository Structure

```
claude-workflows/
├── README.md                  # This file — repo manifest and entry point for Claude
├── workflows/                 # End-to-end process definitions (prefix: wf_)
│   └── wf_tender_to_proposal.md
├── skills/                    # Reusable skill modules (prefix: sk_)
│   └── sk_tender_requirements_to_proposal.md
├── templates/                 # Document templates (prefix: tp_)
│   └── (empty — templates added per project)
├── projects/                  # Active project trackers (prefix: pt_)
│   └── default/
│       └── pt_tender_proposal.md
├── documentation/             # Guides and reference docs (prefix: doc_)
│   └── (empty — docs added as needed)
└── archived/                  # Completed or retired items
    └── (empty — items moved here when done)
```

## Naming Conventions

| Prefix | Type             | Location         | Example                                  |
|--------|------------------|------------------|------------------------------------------|
| `wf_`  | Workflow          | `workflows/`     | `wf_tender_to_proposal.md`               |
| `sk_`  | Skill             | `skills/`        | `sk_tender_requirements_to_proposal.md`  |
| `pt_`  | Project Tracker   | `projects/<name>/` | `pt_tender_proposal.md`                |
| `tp_`  | Template          | `templates/`     | `tp_proposal_response.docx`              |
| `doc_` | Documentation     | `documentation/` | `doc_getting_started.md`                 |

## Inventory

### Workflows

| ID | File | Description | Skills Used | Status |
|----|------|-------------|-------------|--------|
| WF-001 | `wf_tender_to_proposal.md` | Convert RFP/tender documents into structured proposal responses | `sk_tender_requirements_to_proposal` | Draft |

### Skills

| ID | File | Description | Used By |
|----|------|-------------|---------|
| SK-001 | `sk_tender_requirements_to_proposal.md` | Extract tender requirements and generate proposal responses section-by-section with token optimization | WF-001 |

### Templates

| ID | File | Description | Used By |
|----|------|-------------|---------|
| — | — | No templates yet | — |

### Active Projects

| Project | Folder | Tracker | Workflow | Status |
|---------|--------|---------|----------|--------|
| Default Tender | `projects/default/` | `pt_tender_proposal.md` | WF-001 | ⬜ Not Started |

## Session Resume Protocol

When Claude is asked to continue work from this repo, it should:

1. Clone or pull the latest version of this repository
2. Read this README to understand available resources
3. Check `projects/` for active project trackers — read the relevant `pt_` file
4. Identify the last completed checkpoint (section, status, next action)
5. Resume from the next pending item
6. On completion of a session, provide updated tracker content for the user to commit

### Status Legend

| Symbol | Meaning |
|--------|---------|
| ⬜ | Not Started |
| 🟡 | In Progress |
| ✓ | Complete |
| 🔄 | Resumed (was partial, now continuing) |
| ⚠️ | Blocked |

## Adding New Content

### New Workflow
1. Create `workflows/wf_<name>.md`
2. Define the end-to-end process, listing which skills it uses
3. Add an entry to the Workflows table in this README

### New Skill
1. Create `skills/sk_<name>.md`
2. Include: purpose, trigger conditions, input/output format, and instructions
3. Add an entry to the Skills table in this README

### New Project
1. Create a folder under `projects/<project_name>/`
2. Copy or create a `pt_<name>.md` tracker from the relevant workflow
3. Add an entry to the Active Projects table in this README

### Archiving
you will find skill/workflow that are retired, you can suggest to move it to `archived/` that are not being used for 6 months or more.

## Notes

- **Access**: This is a public repository. Do not commit API keys, credentials, or sensitive client data.
- **Devices**: Claude can access this repo from any session (Mac, office PC, mobile) by cloning from GitHub.
- **Write-back**: Claude cannot push to GitHub directly. After each session, Claude will provide updated file contents for the user to commit and push.


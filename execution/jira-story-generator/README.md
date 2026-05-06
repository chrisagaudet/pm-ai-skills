# Jira Story Generator

A Claude skill that breaks a Product Requirements Document (PRD) into Jira-ready user stories with epics, acceptance criteria, and release versions.

## What It Does

Provide a PRD — uploaded as a `.docx`, pasted as text, or referenced from an earlier conversation — and this skill generates a complete backlog of user stories organized by Epics and release versions. Each story follows the standard user story format with checklist-style acceptance criteria written from the user's perspective.

The skill produces two outputs: a `.csv` file you can import directly into Jira, and a `.docx` reference document for review with engineering before import.

## How Stories Are Structured

### Epic Organization
- Each release version (V0, V1, V2) gets its own set of Epics
- Epics represent cohesive, independently releasable features
- Related requirements are grouped into the same Epic
- A single release can have multiple Epics

### Story Format
```
As a [persona from PRD], I'd like to be able to [action], so that [benefit].

Acceptance Criteria:
☐ I can [verifiable outcome from user perspective]
☐ I see [expected behavior]
☐ The system [expected system behavior]

Source: REQ-001
Release: V0
```

### What's Included vs. Not
- **Included:** Story summary, full description, acceptance criteria, priority, epic assignment, requirement traceability (REQ-ID as label), release version
- **Not included:** Story points (left for engineering to estimate during sprint planning)

## Output Files

| File | Purpose | Format |
|------|---------|--------|
| `Jira_Stories_{Feature}_{Date}.csv` | Direct Jira import | CSV with columns: IssueType, Summary, Description, Priority, Epic Name, Labels, Fix Version |
| `Jira_Stories_{Feature}_{Date}.docx` | Human review before import | Formatted Word doc organized by release version and epic |

## Installation

1. Download the `jira-story-generator.skill` file
2. In Claude, go to **Settings → Skills** and upload the `.skill` file
3. The skill activates when you mention Jira stories, user stories, epics, or ask to break down a PRD

## Usage

**Example prompts:**

- *"Here's our PRD — break it into Jira stories."* (with uploaded .docx)
- *"Use the PRD we just created and generate user stories for V0."*
- *"Turn these requirements into epics and stories for sprint planning."*
- *"Create Jira tickets from this product spec."*

### Importing the CSV into Jira

1. In Jira, go to **Settings → System → External system import → CSV**
2. Upload the generated `.csv` file
3. Map the columns to Jira fields (they should auto-match)
4. Review the preview and click **Import**

## Priority Mapping

| PRD Priority | Jira Priority |
|-------------|---------------|
| P0 | Highest |
| P1 | High |
| P2 | Medium |
| P3 | Low |

## Customization

- **Story format:** Edit the "Story Format" section in `SKILL.md` to change the user story template
- **CSV columns:** Modify the CSV column list in `SKILL.md` to match your Jira field configuration
- **Epic naming:** Adjust the "Epic Structure" section to change how Epics are named and grouped
- **Acceptance criteria style:** Currently checklist-style from user perspective; can be changed to Given/When/Then in `SKILL.md`

## Related Skills

This skill works best paired with:

- **[PRD Generator](../prd-generator/)** — Create the PRD that feeds into this skill
- **Release Notes Writer** *(planned)* — Generate release notes from completed stories
- **GTM Communications Drafter** *(planned)* — Draft launch emails and announcements

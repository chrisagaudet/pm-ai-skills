---
name: jira-story-generator
description: "Break a Product Requirements Document (PRD) into Jira-ready user stories with acceptance criteria, epics, and release versions. Use this skill whenever the user mentions 'Jira stories', 'user stories', 'break this into stories', 'sprint planning', 'backlog', 'epics', or asks to turn a PRD, product spec, or set of requirements into actionable development work. Also trigger when the user uploads a PRD and asks to 'create tickets', 'write stories for this', 'break this down for engineering', or 'plan the sprint'. Works with uploaded .docx PRDs, pasted text, or references to PRDs from earlier in the conversation. Even casual requests like 'ok now let's turn this into Jira tickets' or 'what stories do we need for this?' should trigger this skill."
---

# Jira Story Generator

## Purpose

Transform a Product Requirements Document (PRD) into a structured set of Jira-ready user stories organized by Epics and release versions. The output gives product managers a complete backlog they can import directly into Jira or share with engineering for sprint planning.

## When This Skill Triggers

- User has a PRD and wants to break it into stories
- User mentions "Jira stories", "user stories", "tickets", "backlog", "epics"
- User asks to "break this down for engineering" or "plan the sprint"
- User wants to turn requirements into actionable dev work
- User uploads or references a PRD and asks for stories

## Inputs

The user will provide a PRD through one of these methods:
- **Upload a .docx file** — read it using `extract-text` from the docx skill
- **Paste text directly** — requirements, notes, or a full PRD pasted into chat
- **Reference an earlier conversation** — "use the PRD we just created" or similar

If the input is a full PRD, focus primarily on the Requirements table (with IDs, descriptions, release versions, and priorities) and the User Flows section. These are the primary sources for story generation.

Also draw context from: Problem Statement (for the "so that" clause), Target Audience (for the "As a" clause), Goals/Objectives (for acceptance criteria validation), and Solutions Considered (for implementation context).

## Output

Two files, both saved to `/mnt/user-data/outputs/`:

### 1. Jira Import CSV (`.csv`)
Filename: `Jira_Stories_{Feature_Name}_{Date}.csv`

This is the primary deliverable — a CSV file that can be imported directly into Jira.

**CSV columns** (in this exact order):
```
IssueType,Summary,Description,Priority,Epic Name,Labels,Fix Version
```

Column details:
- **IssueType**: "Epic" or "Story"
- **Summary**: For Epics, a short feature name. For Stories, the user story title (concise, action-oriented)
- **Description**: For Stories, the full user story in format plus acceptance criteria (see Story Format below). For Epics, a brief description of the feature scope.
- **Priority**: Mapped from PRD priority — P0 → "Highest", P1 → "High", P2 → "Medium", P3 → "Low"
- **Epic Name**: The name of the parent Epic this story belongs to (must exactly match the Epic's Summary value)
- **Labels**: Comma-separated labels. Always include the source requirement ID (e.g., "REQ-001") as a label for traceability
- **Fix Version**: The release version from the PRD (e.g., "V0", "V1", "V2")

**CSV formatting rules** (critical for Jira import):
- Use commas as delimiters
- Wrap any field containing commas, newlines, or quotes in double quotes
- Escape double quotes within fields by doubling them ("")
- First row must be the header row
- UTF-8 encoding
- Epic rows must appear before the Story rows that reference them
- **Use ASCII-only characters** — no unicode symbols like ☐ or ✓. Use `[ ]` for unchecked items and `[x]` for checked items in acceptance criteria. Unicode characters render as garbled text when the CSV is opened in Excel.

### 2. Reference Document (`.docx`)
Filename: `Jira_Stories_{Feature_Name}_{Date}.docx`

A formatted Word document for human review before import. This is for the PM to review, annotate, and share with engineering before importing the CSV. It should include all Epics and Stories with full detail, organized by release version.

Use the docx skill's JavaScript approach (`npm install -g docx`) to create the document.

## Epic Structure

Epics represent releasable features within a release version. The skill should create Epics following these rules:

- **Each release version (V0, V1, V2, etc.) has its own set of Epics**
- **An Epic should represent a cohesive, independently releasable feature** — not a single requirement and not an entire release
- **Group related requirements into the same Epic** when they contribute to a single feature
- **Name Epics descriptively** — e.g., "Guided Onboarding Tour (V0)" not "V0 Requirements"
- If a release version has enough requirements to warrant multiple features, create multiple Epics for that version
- Include the release version in the Epic name for clarity

Example: If a PRD has V0 requirements for both "guided tour" and "analytics instrumentation," those become two separate V0 Epics, not one.

## Story Format

Every story must follow this exact format:

### Summary (title)
Short, action-oriented. Should make sense in a Jira board column.
Example: "Display guided onboarding tour on first login"

### Description
```
*User Story:*
As a [user persona from the PRD], I'd like to be able to [action], so that [benefit tied to PRD goals].

*Acceptance Criteria:*
☐ [Criterion specifying which user, exact text/behavior, and verifiable outcome]
☐ [Criterion specifying which user, exact text/behavior, and verifiable outcome]
☐ [Criterion specifying which user, exact text/behavior, and verifiable outcome]

*Designs:* [Link to Figma/design file, or "[TO BE ADDED]" if not yet available]
*Source:* [REQ-ID from PRD]
*Release:* [V0/V1/V2]
```

### Acceptance Criteria Rules

Acceptance criteria must be specific enough that a developer can build from them and QA can write a test case without asking follow-up questions. Vague criteria create ambiguity and rework.

**Specify which user or role can take the action.** Don't say "the user" — say "a new trial user" or "an SMB admin." If multiple roles have different permissions or experiences, write separate criteria for each.

**Spell out exact text and copy.** If the UI displays a message, heading, button label, or notification, include the specific text. Don't say "a welcome message" — say "the heading reads 'Welcome to [Product Name], [First Name]!' and the body text reads 'Let us show you around. This quick tour will help you get set up in under 5 minutes.'"

**Include design references for UI/UX stories.** If designs exist, include a link to the Figma file or frame. If a screenshot is available, reference it. If designs are not yet ready, include `*Designs:* [TO BE ADDED — pending design review]` so it's clear the story is not fully specced.

**Spell out exact event details for analytics stories.** If the story involves tracking an event, the AC must specify: the exact event name, every property/field being captured with its data type and expected values, and the trigger condition. Don't say "track step completion" — say "fire event `onboarding_step_completed` with properties: `user_id` (string), `step_number` (integer, 1-based), `step_name` (string, e.g. 'create_first_project'), `duration_seconds` (integer, time spent on this step)."

**Include edge cases and error states.** What happens if the user is offline? What if the API call fails? What does the empty state look like? These belong in AC, not as separate stories.

**General rules:**
- Write from the user's perspective using checklist style
- Each criterion should be independently verifiable
- Use phrasing like "A new trial user sees..." or "An SMB admin can..."
- Aim for 4-8 criteria per story — fewer means the story is underspecified, more means it should be split
- Describe what the user experiences, not how it's implemented

Good example:
```
☐ A new trial user sees a welcome modal immediately after their first login
☐ The modal heading reads "Welcome to [Product Name], [First Name]!"
☐ The modal body text reads "Let us show you around. This quick tour will help you get set up in under 5 minutes."
☐ The modal displays two buttons: "Start Tour" (primary) and "Skip for Now" (secondary)
☐ A new trial user who clicks "Skip for Now" is returned to the dashboard and the modal does not reappear on subsequent logins
☐ A returning user who has already dismissed or completed the tour does not see the modal
☐ Designs: [Link to Figma frame]
```

Bad example:
```
☐ I see a welcome modal when I log in for the first time
☐ I can dismiss the modal and return to the dashboard
☐ The modal does not appear on subsequent logins
☐ I see my name in the welcome message
```
The bad example is too vague — it doesn't specify the user type, the exact text, the button labels, or what "dismiss" means.

### Story Points
Do NOT assign story points. Leave this blank — engineering estimates during sprint planning. Never include a story points column in the CSV.

## Story Decomposition Guidelines

The goal is to create stories that are small enough to complete in a single sprint but large enough to deliver meaningful value. Follow these principles:

- **One requirement may become multiple stories** — if REQ-001 says "Display a guided product tour on first login," that could become: "Show welcome modal on first login," "Highlight first feature with tooltip overlay," "Track tour step completion," etc.
- **Don't create stories for non-functional requirements** unless they have user-facing implications. If a requirement is purely technical (e.g., "lazy-load assets"), it becomes acceptance criteria on a related story, not its own story.
- **Each story should be independently demoable** — you should be able to show it to a stakeholder and they understand the value
- **Include edge cases as acceptance criteria**, not separate stories. "User can skip at any point" is an AC on the tour story, not its own story (unless skipping has complex behavior worth its own work).

## Document Formatting (.docx)

### Style
- **Font:** Arial throughout
- **Page size:** US Letter (12240 x 15840 DXA)
- **Margins:** 1 inch all sides

### Structure
```
Title Page: "Jira Stories — {Feature Name}"
  Author, Date, Source PRD reference

For each Release Version (V0, V1, V2...):
  Heading 1: "Release {Version}"
  
  For each Epic in this version:
    Heading 2: Epic Name
    Brief Epic description
    
    For each Story in this Epic:
      Heading 3: Story Summary
      User Story text
      Acceptance Criteria (checklist)
      Priority, Source Requirement ID
      
Summary:
  Total Epics: X
  Total Stories: X
  Stories by Version: V0: X, V1: X, V2: X
  Stories by Priority: P0: X, P1: X, P2: X
```

### Table Formatting (for summary)
- Use `WidthType.DXA` (never percentage)
- Set both `columnWidths` on the table AND `width` on each cell
- Use `ShadingType.CLEAR` for header shading
- Light blue header: #D5E8F0, borders: #CCCCCC
- Cell margins: `{ top: 80, bottom: 80, left: 120, right: 120 }`

## Generation Process

1. **Read the PRD** — extract text from uploaded .docx, parse pasted text, or pull from conversation context
2. **Identify the requirements** — find the requirements table, user flows, and any other actionable items
3. **Group into Epics** — cluster related requirements into cohesive features per release version
4. **Decompose into stories** — break each requirement into one or more user stories with acceptance criteria
5. **Generate both outputs** — create the .csv and .docx files
6. **Validate the .docx** — run `python scripts/office/validate.py`
7. **Present both files** — save to `/mnt/user-data/outputs/` and share with the user

## Important Behaviors

- **Trace every story back to a requirement** — include the REQ-ID as a label and in the description. No orphan stories.
- **Use the PRD's language for personas** — if the PRD says "New Trial Users," use that in the "As a" clause, not generic "user."
- **Don't invent requirements** — only create stories for what's in the PRD. If something seems missing, note it but don't add stories for it.
- **Maintain priority mapping** — P0 requirements become Highest priority stories, P1 → High, P2 → Medium.
- **Keep the CSV clean** — it must import into Jira without errors. Test mentally: would Jira accept this row?
- **Order matters in the CSV** — Epic rows must come before the Story rows that reference them.

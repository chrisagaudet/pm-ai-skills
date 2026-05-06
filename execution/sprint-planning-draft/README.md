# Sprint Planning Draft

A Claude skill that generates sprint planning documents from Jira stories, PRDs, backlog lists, planning meeting notes, or any combination of inputs.

## What It Does

Takes whatever you've got — a PRD, a list of Jira stories, rough notes from a planning meeting — and produces a clear, organized sprint plan (.docx) that serves as the shared reference for you and your eng lead.

## Inputs

Feed it whatever you have:
- **Jira stories or CSV export** — candidate stories for the sprint
- **A PRD (.docx or pasted text)** — the source requirements driving priorities
- **Planning meeting notes** — decisions, concerns, scope discussions
- **Capacity / velocity info** — team size, availability, historical velocity
- **Any combination of the above**

## Output

A `.docx` sprint plan with professional formatting, organized by workstream.

## Document Sections

| Section | Always Included? | Purpose |
|---------|-----------------|---------|
| Sprint Goal | Yes | 1-3 outcome-oriented goals for the sprint |
| Capacity & Velocity | If provided | Team availability, velocity, capacity risks |
| Committed Stories | Yes | Stories grouped by Epic/workstream, with priority, assignee, key criteria |
| Stretch Goals | If applicable | Stories to pull in if capacity allows |
| Not This Sprint | If discussed | Deferred work with rationale — prevents mid-sprint re-litigation |
| Risks & Dependencies | Yes | Technical, dependency, and people risks with owners and mitigations |
| Key Decisions & Open Questions | If applicable | Planning decisions and unresolved questions with owners |
| Carryover from Last Sprint | If applicable | Stories carried over with context on why |

## Sprint Goal Writing

Goals are outcome-oriented, not task lists:
- **Good:** "New trial users can complete a guided onboarding flow with progress tracking on first login"
- **Bad:** "Complete ONBOARD-101 through ONBOARD-108"

## Story Table Format

Stories are organized by Epic/workstream, with each table containing:

| Column | Description |
|--------|-------------|
| Story ID | Jira ticket ID or sequential (S-001) |
| Story Title | Brief, descriptive title |
| Priority | P0 / P1 / P2 |
| Assignee | Team member or [TBD] |
| Points | Story points or — |
| Key Criteria | 1-2 line acceptance criteria summary |

## Detail Levels

The skill adapts depth based on your inputs:

- **Light Plan:** Just a story list or rough bullets → Sprint Goal + Stories table + Risks
- **Standard Plan:** Stories plus context (PRD, notes, capacity) → All relevant sections
- **Detailed Plan:** Extensive inputs or explicit request → Full sections with expanded capacity analysis, decision log, carryover

Defaults to **Standard Plan**.

## Smart Behaviors

- **Triages inputs first** — assesses what you provided and tells you which sections will be strong vs. thin before generating
- **Asks sprint context questions** — confirms team availability, capacity, carryover, Epic groupings, stretch vs. committed, deferrals, and key decisions in one efficient message
- **Never fabricates** — won't invent risks, decisions, deferrals, or stretch goals it doesn't have evidence for
- **Flags overcommitment** — if stories exceed stated capacity, it calls it out
- **Groups by workstream** — stories organized by Epic (confirmed with you), not just priority
- **Captures deferrals** — documents what's NOT in the sprint and why
- **Auto-detects PRD** — if a PRD exists in the conversation, it uses it to enrich goals, risks, and traceability
- **Marks unknowns** — uses [TBD] for missing assignees, — for missing points, never guesses estimates

## Example Usage

**From a PRD:**
> "Here's the onboarding PRD. Plan a sprint for the V0 scope — our team is 2 FE, 1 BE, 1 QA."

**From rough notes:**
> "We met today and decided to tackle the guided tour, skip button, progress indicator, and all the analytics events. Push role-based personalization to next sprint."

**From a story list:**
> "Here are the 15 stories in our backlog. We have 35 points of capacity. Help me figure out what fits."

## Installation

Upload `sprint-planning-draft.skill` to your Claude project's skills, or copy the `SKILL.md` file into your skills directory.

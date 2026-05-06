---
name: sprint-planning-draft
description: "Generate a sprint planning document as a .docx file from Jira stories, PRDs, backlog lists, planning meeting notes, or any combination of inputs. Use this skill whenever the user mentions 'sprint plan', 'sprint planning', 'plan the sprint', 'sprint goals', 'what are we working on this sprint', 'sprint scope', or asks to organize stories into a sprint. Also trigger when the user has a backlog of stories and wants to figure out what fits in the next sprint, when they have planning meeting notes to formalize, or when they want to communicate sprint commitments to their eng lead. Even casual requests like 'help me plan the next two weeks', 'what should we tackle next sprint', 'organize these stories into a sprint', or 'write up what we committed to' should trigger this skill."
---

# Sprint Planning Draft

## Purpose

Transform mixed sprint planning inputs — Jira stories, PRDs, backlog lists, rough notes from planning meetings — into a clear, organized sprint plan (.docx) that serves as a working reference for the PM and eng lead. This is not a presentation or a Jira import; it's the shared source of truth for what the team is doing, why, and what to watch out for.

## When This Skill Triggers

- User wants to plan a sprint or organize stories for a sprint
- User mentions "sprint plan", "sprint planning", "sprint goals", "sprint scope"
- User has Jira stories or a backlog and wants to scope a sprint
- User has planning meeting notes to formalize
- User wants to communicate sprint commitments
- User asks "what should we work on next sprint?"

## Inputs

The user will provide some combination of:
- **Jira stories or CSV export** — the backlog or candidate stories for the sprint
- **A PRD (.docx or pasted text)** — the source requirements guiding priorities
- **Rough notes from a planning meeting** — decisions made, stories discussed, concerns raised
- **Capacity or velocity info** — team size, availability, historical velocity, PTO
- **Sprint metadata** — sprint name/number, dates, team name

It's common for the input to be incomplete. The skill should work with whatever is provided, but it must be transparent about what it can and can't generate from the available inputs.

### Input Triage (Required First Step)

Before generating anything, assess what was provided and determine what each section of the sprint plan will look like. This is the most important step — it prevents the skill from fabricating content it doesn't have evidence for.

**Classify what you received into these input types:**

| Input Type | What It Unlocks |
|------------|----------------|
| **Stories** (Jira list, CSV, bullet points) | Committed Stories table, basic story grouping |
| **PRD** (.docx or pasted text) | Sprint Goal (tied to hypothesis/metrics), Epic descriptions, Risks & Dependencies, Not This Sprint (V1/V2 items), REQ-ID traceability |
| **Planning meeting notes** | Sprint Goal (team-aligned), Stretch Goals, Not This Sprint (with rationale), Key Decisions & Open Questions, Carryover |
| **Capacity/velocity info** | Capacity & Velocity section, overcommitment warnings |
| **Sprint metadata** (name, dates, team) | Title page |

**Then assess each section's quality level:**

| Section | Stories Only | + PRD | + Meeting Notes | + All Three |
|---------|-------------|-------|-----------------|-------------|
| Sprint Goal | Thin — summarizes story themes only | Strong — ties to PRD hypothesis, metrics, and business context | Strong — captures what team aligned on | Best |
| Capacity & Velocity | Skip | Skip | Only if discussed | Only if data provided |
| Committed Stories | Full — this is the core table | Full + REQ-ID traceability + richer Epic descriptions | Full + committed vs. stretch distinction | Best |
| Stretch Goals | Cannot distinguish — include all as committed | Partial — can infer from P1/P2 priority | Strong — planning decisions clarify this | Best |
| Not This Sprint | Cannot generate — no visibility into what was considered | Partial — can list V1/V2 items from PRD as deferred | Strong — captures explicit deferral decisions | Best |
| Risks & Dependencies | Weak — can only guess from story content | Strong — PRD has risks, dependencies, and technical concerns | Strong — surfaces concerns raised in planning | Best |
| Key Decisions & Open Questions | Cannot generate | Partial — can flag PRD open items | Strong — primary source for this section | Best |
| Carryover | Cannot generate unless user mentions it | Cannot generate | Only if discussed | Only if discussed |

### Communicate Gaps to the User

After triage, **before generating the document**, tell the user what you can produce and what would be stronger with additional input. Be specific and actionable:

**Example — stories only:**
> "I have your 12 stories and can build the story table and group them by theme. Here's what I'll need to keep some sections thin:
> - **Sprint Goal** will summarize what the stories cover, but I can't tie it to business metrics or the product hypothesis without the PRD.
> - **Risks & Dependencies** will be basic — I can flag obvious dependency risks from story content, but the PRD would give me the full risk picture.
> - **Not This Sprint** and **Stretch Goals** I can't generate from stories alone — I don't know what was considered and cut. If you have planning notes or can tell me what was deferred, I'll add those.
>
> Want me to proceed with what I have, or can you share the PRD or planning notes?"

**Example — stories + PRD:**
> "I have your stories and the PRD. I can generate a strong plan with sprint goals tied to your PRD metrics, full risk assessment, and a 'Not This Sprint' section covering V1/V2 deferrals. The only sections I'll keep thin are Key Decisions and Stretch Goals — if you have planning meeting notes, I can strengthen those."

**Example — stories + PRD + meeting notes:**
> "I have everything I need to generate a detailed sprint plan. Let me confirm the sprint name and dates, then I'll build it."

**When to skip the triage message:**
- If the user provided all three core inputs (stories + PRD + meeting notes), just confirm sprint metadata and proceed.
- If the user explicitly says "just use what I gave you" or "don't worry about the gaps," proceed and mark thin sections with `[Could be strengthened with PRD / planning notes]` placeholders.

### What to Infer vs. What to Ask

- **Stories and scope:** Work with whatever is provided. If the user provides a PRD but no stories, extract the V0/P0 requirements and frame them as sprint candidates.
- **Priority and ordering:** If stories don't have explicit priorities, infer from PRD priority (P0 > P1 > P2), business context, or dependencies. Note assumptions.
- **PRD context:** If available in the conversation (e.g., from an earlier message or an uploaded file), use it. You don't need the user to explicitly say "use the PRD" — if it's there, reference it.

### Sprint Context Questions (Required Before Generating)

After triage and gap communication, ask the user to confirm the following. These are things the skill cannot reliably infer from documents alone — they are sprint-specific and change every cycle. Present them as a quick checklist the user can answer in one message.

**Always ask:**

1. **Sprint metadata:** "What's the sprint name/number, dates, and team name?"
2. **Team availability:** "Who's available this sprint? Any PTO, on-call rotations, or partial allocations? What's the team's total capacity (story points or number of dev-days)?"
3. **Carryover:** "Are there any stories in progress from last sprint that will carry over? If so, which ones and what's their status?"
4. **Epic groupings:** "I'm planning to group these stories under the following Epics: [list inferred groupings]. Does this look right, or should I adjust?" (Only ask if the stories don't already have explicit Epic associations from Jira.)

**Ask if no meeting notes provided:**

5. **Stretch vs. committed:** "Are all of these stories committed, or are some stretch goals? If so, which ones?"
6. **Deferrals:** "Was anything discussed and explicitly deferred to a future sprint? If so, what and why?"
7. **Key decisions:** "Were any key decisions made during planning that should be documented? Any open questions that need answers during the sprint?"

**How to ask efficiently:**

Don't fire 7 questions one at a time. Combine them into a single message grouped by what you already know vs. what you need. If the user provided meeting notes that answer some of these, skip those questions.

**Example — stories only, no PRD or notes:**
> "Before I generate the plan, a few things I need to confirm:
>
> **Sprint info:** What's the sprint name/number and dates? What team is this for?
>
> **Team:** Who's available this sprint? Any PTO or partial allocations? What's the total capacity (points or dev-days)?
>
> **Carryover:** Any stories in progress from last sprint carrying over?
>
> **Scope:** I'm grouping stories under these Epics: [Guided Tour, Skip & Progress, Analytics]. Look right? Also, are all of these committed or are some stretch? Was anything discussed and cut from this sprint?
>
> **Decisions:** Any planning decisions or open questions I should capture?"

**Example — stories + PRD, no notes:**
> "I have the stories and the PRD, so I can pull risks, goals, and deferrals from there. Just need to confirm:
>
> **Sprint info:** Sprint name/number and dates?
>
> **Team:** Who's available and what's capacity this sprint?
>
> **Carryover:** Anything carrying over from last sprint?
>
> **Scope:** Are all stories committed, or are some stretch? Any planning decisions to capture?"

**If the user says "just generate it":** Proceed with what you have. Use `[TBD]` for team availability, skip capacity section, note inferred Epic groupings, include all stories as committed (no stretch distinction), and skip Key Decisions & Open Questions entirely rather than fabricating them.

## Output

A `.docx` file saved to `/mnt/user-data/outputs/`.

Filename format: `Sprint_Plan_{Sprint_Name}_{Date}.docx`

## Sprint Plan Structure

The structure is flexible — not every sprint needs every section. The skill should include sections that are relevant based on the inputs provided, and skip sections that would be empty. However, the Sprint Goal, Committed Stories, and Risks/Dependencies sections should always be present.

```
Title Page:
  "Sprint Plan — {Sprint Name}"
  Team, Dates, Author (default: "Christopher Gaudet")

1. Sprint Goal
   - 1-3 clear goal statements for the sprint
   - What does "done" look like at the end of these two weeks?
   - Tie goals to the broader product objective (e.g., PRD hypothesis, quarterly OKR)
   - Keep it outcome-oriented, not task-oriented
   - Good: "New users can complete a guided onboarding tour on first login"
   - Bad: "Finish onboarding tickets"

2. Capacity & Velocity (include if user provides this info via sprint context questions)
   - Team members and availability this sprint (PTO, partial allocations, on-call)
   - Total capacity for this sprint (story points or dev-days)
   - Historical velocity for context (average story points per sprint, if known)
   - Capacity risks (key person on PTO, new team member ramping, etc.)
   - If total capacity is provided and stories have point estimates, include a utilization note: "X points committed out of Y capacity (Z%)"

3. Committed Stories
   - The core of the document — the stories the team is committing to
   - Organized by Epic or workstream, not by priority alone
   - Epic groupings: use Jira Epic field if available; otherwise infer from story themes and confirm with user via sprint context questions before generating
   - Each story includes:
     - Story title and ID (if available)
     - Priority (P0/P1/P2 or Highest/High/Medium)
     - Assignee (if known — use [TBD] if not)
     - Story points / estimate (if available — leave blank if not)
     - Brief description of what the story delivers
     - Acceptance criteria summary (1-2 key criteria, not the full list)
   - Present as a table grouped under Epic/workstream headings
   - Order within each group: P0 first, then P1, then P2

4. Stretch Goals (include only if user distinguishes committed vs. stretch)
   - Stories that are in scope if capacity allows
   - Same format as committed stories but clearly labeled as stretch
   - These are the first things to cut if the sprint gets tight
   - Source: user's answer to "are all stories committed or are some stretch?"
   - If user did not distinguish, include all stories as committed — do not guess

5. Not This Sprint (include if user shares deferrals, or if PRD has V1/V2 items)
   - Stories or work that was explicitly discussed and deferred
   - Brief reason for deferral (e.g., "blocked by API dependency", "deprioritized to V1")
   - This prevents re-litigation of planning decisions mid-sprint
   - Sources: user's answer to "was anything deferred?", planning meeting notes, or PRD V1/V2 requirements that are explicitly not in this sprint's scope
   - If PRD is the only source, frame as: "The following V1/V2 items from the PRD are not in scope for this sprint" — don't present them as planning decisions unless the user confirmed they were discussed

6. Risks & Dependencies
   - Always include this section, even if just to say "No significant risks identified"
   - Technical risks: complex stories, unfamiliar tech, performance concerns
   - Dependency risks: blocked by another team, waiting on design, external API
   - People risks: key person on PTO, new team member
   - Each risk should have an owner and mitigation if possible
   - Present as a table: Risk, Likelihood (High/Med/Low), Impact (High/Med/Low), Owner, Mitigation

7. Key Decisions & Open Questions (only if provided by user — never fabricated)
   - Decisions made during planning that should be documented
   - Open questions that need answers during the sprint
   - Who owns resolving each question and by when
   - Source: planning meeting notes or user's answers to sprint context questions
   - If no decisions or questions were shared, SKIP this section entirely — do not include a placeholder

8. Carryover from Last Sprint (only if user confirms carryover stories exist)
   - Stories that weren't completed last sprint
   - Current status (in progress, blocked, not started)
   - Brief note on why they carried over
   - Whether they're re-committed or deprioritized this sprint
```

## Detail Levels

The sprint plan's depth is determined by the **input triage** above, not by an arbitrary setting. The more context the user provides, the richer each section becomes.

### Light Plan (Stories only, no PRD or notes)
Output includes:
- Sprint Goal (theme-based summary — thin without business context)
- Committed Stories table (full: title, priority, assignee, key criteria)
- Risks & Dependencies (basic — inferred from story content only)
- Thin sections are marked with `[Could be strengthened with PRD / planning notes]`

### Standard Plan (Stories + PRD or meeting notes)
Output includes all relevant sections from the full structure above, with:
- Sprint Goal tied to PRD metrics or planning alignment
- Committed Stories with REQ-ID traceability (if PRD available)
- Risks & Dependencies from PRD or meeting discussion
- Not This Sprint with V1/V2 deferrals (if PRD) or explicit deferrals (if notes)
- Remaining thin sections marked with appropriate placeholders

### Detailed Plan (Stories + PRD + meeting notes, or user requests thorough plan)
Output includes all sections with full depth:
- Sprint Goal with business context, success criteria, and connection to broader objectives
- Committed Stories with full Epic descriptions, REQ-ID traceability, and acceptance criteria
- Stretch Goals clearly separated from committed work
- Not This Sprint with explicit rationale for each deferral
- Detailed risk assessment with owners and mitigations
- Key Decisions & Open Questions with owners and deadlines
- Capacity analysis (if data provided)
- Carryover analysis (if mentioned)

The skill should **never fabricate content for a section it doesn't have evidence for.** If a section would require invention rather than synthesis, either skip it or include it with a clear placeholder explaining what input would populate it.

## Story Table Formatting

The Committed Stories table is the heart of the document. Format it clearly:

### Column Definitions
| Column | Width (DXA) | Description |
|--------|-------------|-------------|
| Story ID | 1200 | Jira ticket ID if available (e.g., "ONBOARD-123"), otherwise sequential (S-001) |
| Story Title | 3360 | Brief, descriptive title |
| Priority | 900 | P0, P1, or P2 |
| Assignee | 1500 | Team member name or [TBD] |
| Points | 700 | Story points if available, otherwise "—" |
| Key Criteria | 1700 | 1-2 line summary of the most important acceptance criteria |

Total width: 9360 DXA (US Letter with 1-inch margins)

### Grouping
- Group stories under Epic or workstream headings (Heading 2)
- Within each group, order by priority (P0 first)
- Use a shaded row for the Epic/workstream header within the table, or use a heading above the table if there are multiple distinct tables

## Sprint Goal Writing

Sprint goals should follow these principles:
- **Outcome-oriented:** Describe what will be true at the end of the sprint, not what tasks will be done
- **Specific enough to evaluate:** At sprint review, the team should be able to say "yes, we achieved this" or "no, we didn't"
- **Connected to the bigger picture:** Reference the PRD, quarterly goal, or customer need this sprint serves
- **1-3 goals max:** More than 3 means the sprint lacks focus. If the stories span many areas, find the common thread

### Examples
- Good: "New trial users can complete a guided onboarding flow with progress tracking on first login, with full analytics instrumentation to measure completion rates."
- Good: "Reduce P95 API latency for the search endpoint from 800ms to under 200ms."
- Bad: "Complete ONBOARD-101 through ONBOARD-108."
- Bad: "Work on onboarding and fix some bugs."

## Document Formatting

Use the docx skill's JavaScript approach (`npm install -g docx`) to create the document.

### Style Guidelines
- **Font:** Arial throughout
- **Title:** 28pt, bold, centered
- **Heading 1:** 24pt, bold (section headers like "Sprint Goal", "Committed Stories")
- **Heading 2:** 18pt, bold (Epic/workstream names within Committed Stories)
- **Body:** 12pt
- **Page size:** US Letter (12240 x 15840 DXA)
- **Margins:** 1 inch all sides (1440 DXA)
- **Header:** "Sprint Plan" on the left, "CONFIDENTIAL" on right
- **Footer:** Page numbers centered

### Table Formatting
- All tables use `WidthType.DXA` (never percentage)
- Set both `columnWidths` on the table AND `width` on each cell
- Use `ShadingType.CLEAR` for header shading
- Header row shading: #D5E8F0 (light blue, consistent with PRD and release notes skills)
- Borders: #CCCCCC, single, size 1
- Cell margins: `{ top: 80, bottom: 80, left: 120, right: 120 }`

## Generation Process

1. **Parse all inputs** — read uploaded files (.docx, .csv), parse pasted text, pull from conversation context. Also check whether a PRD exists in the conversation history (e.g., uploaded earlier or generated by the PRD Generator skill).
2. **Run input triage** — classify what was received (stories, PRD, meeting notes, capacity, metadata) and assess what each section will look like. See the Input Triage section above.
3. **Communicate gaps** — tell the user what the plan will cover well and what will be thin. Offer to proceed or wait for additional input. Skip this step if all core inputs are present.
4. **Ask sprint context questions** — ask the user to confirm sprint metadata, team availability/capacity, carryover stories, Epic groupings (if not from Jira), stretch vs. committed distinction, deferrals, and key decisions. Combine into a single efficient message. Skip questions already answered by meeting notes or prior conversation. See Sprint Context Questions section above.
5. **Wait for user answers** — do not generate the document until the user responds to the sprint context questions. Their answers directly populate Capacity, Stretch Goals, Not This Sprint, Key Decisions, and Carryover sections.
6. **Extract and organize stories** — categorize by confirmed Epic groupings, assign priorities, separate committed from stretch based on user's answers
7. **Synthesize sprint goals** — write 1-3 outcome-oriented goals. Quality depends on available context (see triage table).
8. **Populate remaining sections** — each section draws from the inputs identified in triage plus the user's sprint context answers. Mark thin sections with appropriate placeholders.
9. **Generate the .docx** — using the docx npm package with the formatting above
10. **Validate** — run `python scripts/office/validate.py` on the output
11. **Present** — save to `/mnt/user-data/outputs/` and share with the user. In the response, note which sections are thin and what input would strengthen them, so the user can iterate if they choose.

## Important Behaviors

- **Triage first, generate second.** Always assess what inputs you have before generating anything. The quality of each section depends on the inputs available — don't pretend you have more context than you do. A thin section with a clear placeholder is far more useful than a fabricated one.
- **Never fabricate risks, decisions, or deferrals.** These sections require real information from a PRD or planning discussion. If you don't have evidence for a risk, don't invent one. If you don't know what was deferred, don't guess. Include the section header with a placeholder like "[No planning notes provided — add deferral decisions here]" so the user knows to fill it in.
- **Use the PRD when it's available.** If a PRD exists anywhere in the conversation — uploaded, pasted, or generated by another skill — use it to enrich the sprint goal, populate risks, provide REQ-ID traceability, and identify V1/V2 deferrals. You don't need the user to explicitly say "use the PRD."
- **Synthesize goals, don't just list stories.** The sprint goal should tell a story about what the team will accomplish, not regurgitate Jira titles. "Enable new users to experience value within their first session" is a goal; "Do ONBOARD-101, 102, 103" is a task list. With a PRD, tie the goal to metrics and hypothesis. Without one, synthesize from story themes but acknowledge the goal is thinner.
- **Be practical about capacity.** If the user provides velocity data and the stories clearly exceed capacity, flag it. "These 45 points of stories may exceed your 3-sprint average of 32 points. Consider moving P1 stories to stretch." Don't silently overcommit.
- **Group by workstream, not priority.** Priorities matter within a group, but the primary organization should be by Epic or workstream so it reads logically. A reader should be able to scan the document and understand "what are we doing for onboarding, what are we doing for search, what are we doing for infra."
- **Capture what's NOT in the sprint.** The "Not This Sprint" section is just as valuable as the committed stories. It prevents mid-sprint scope creep and documents the rationale for deferral. But only include it if you have evidence for what was deferred — from meeting notes or PRD V1/V2 items.
- **Flag risks early.** If a story depends on another team's work, or if a key engineer is on PTO, call it out explicitly. Don't bury risks in story descriptions. With a PRD, pull from the Risks and Dependencies sections. Without one, flag only what's obvious from the story content.
- **Mark unknowns clearly.** Use `[TBD]` for missing assignees, `—` for missing story points, and `[Could be strengthened with PRD / planning notes]` for thin sections. Don't make up estimates.
- **Keep it a working doc, not a presentation.** This is for PM + eng lead day-to-day reference. It should be scannable, practical, and easy to update. No fluff, no executive summaries, no marketing language.
- **Connect to the PRD when possible.** If the user provided a PRD, reference requirement IDs (REQ-001) in story descriptions so the eng lead can trace stories back to requirements.

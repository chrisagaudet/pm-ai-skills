---
name: release-notes-generator
description: "Generate professional release notes as a .docx file from Jira stories, PRDs, rough bullet points, or any combination of inputs. Use this skill whenever the user mentions 'release notes', 'changelog', 'what shipped', 'what's new', 'release summary', or asks to document what was built in a sprint or release. Also trigger when the user uploads Jira exports, PRDs, or pastes bullet points and wants them turned into polished release notes. Works for both internal release notes (engineering, CS, sales audiences) and external release notes (customer-facing). Even casual requests like 'write up what we shipped' or 'I need to tell the team what went out' or 'summarize this release for customers' should trigger this skill."
---

# Release Notes Generator

## Purpose

Transform mixed release inputs — Jira stories, PRDs, sprint summaries, rough bullets — into polished, well-organized release notes (.docx) ready to share with the appropriate audience. The skill auto-detects the audience tone or lets the user specify it, and organizes changes into logical categories.

## When This Skill Triggers

- User wants to write release notes for a sprint or release
- User mentions "release notes", "changelog", "what shipped", "what's new"
- User has Jira stories or a PRD and wants to summarize what was built
- User wants to communicate a release to internal teams or external customers
- User pastes bullet points about shipped features and wants them polished

## Inputs

The user will provide some combination of:
- **Jira stories or CSV export** — the stories that were completed in the release
- **A PRD (.docx or pasted text)** — the source requirements document
- **Rough bullet points** — quick notes about what shipped
- **Sprint or release name/number** — for titling
- **Target audience** — internal (eng, CS, sales) or external (customers), or both

It's common for the input to be messy or incomplete. The skill should synthesize whatever is provided into clear, organized release notes.

### Audience Detection

If the user doesn't specify the audience, infer it from context:
- References to "customers", "users", or "what's new page" → external tone
- References to "the team", "engineering", "sales", "CS" → internal tone
- If unclear, **ask the user** — audience dramatically changes the tone and detail level

## Output

A `.docx` file saved to `/mnt/user-data/outputs/`.

Filename format: `Release_Notes_{Release_Name}_{Date}.docx`

If the user asks for both internal and external versions, generate two separate files:
- `Release_Notes_Internal_{Release_Name}_{Date}.docx`
- `Release_Notes_External_{Release_Name}_{Date}.docx`

## Release Notes Structure

### For Internal Audiences (engineering, CS, sales)

Internal release notes are detailed and technical. They help CS respond to customer questions, help sales demo new features, and give engineering a record of what shipped.

```
Title Page:
  "Release Notes — {Release Name}"
  Date, Version, Author (default: "Christopher Gaudet")

1. Release Summary
   - 2-3 sentence overview of what shipped and why it matters
   - Call out the most impactful change upfront

2. What's New (Features)
   - Each feature gets a subsection with:
     - Feature name (heading)
     - **What it is:** A clear description of the feature and how it works from the user's perspective (2-3 sentences)
     - **Why we built it:** The problem or insight that motivated this feature — reference customer research, data, or business context (1-2 sentences)
     - **Why it's valuable:** The expected impact on the user, the business, or both — tie to metrics where possible (1-2 sentences)
     - **Screenshots:** A placeholder for 1-3 screenshots showing the feature in action. Use `[Insert screenshot: {description of what to capture}]` with a brief caption describing each screenshot. Be specific about what the screenshot should show (e.g., "[Insert screenshot: Welcome modal as seen by a new trial user on first login]" not just "[Insert screenshot]")
     - Source requirement or Jira epic reference
     - Known limitations or caveats (if any)
   - Order by impact, not by requirement ID

3. Improvements
   - Enhancements to existing functionality
   - Performance improvements
   - UX refinements
   - Each gets a brief description and the "before vs. after" if applicable

4. Bug Fixes
   - Brief description of what was fixed
   - Only include bugs that customers or internal teams would notice
   - Skip purely technical/infra fixes unless they affected users

5. Breaking Changes (if any)
   - What changed that could disrupt existing workflows
   - What users or teams need to do differently
   - Migration steps if applicable

6. Known Issues
   - Issues identified but not yet resolved
   - Workarounds if available
   - Expected fix timeline if known

7. What's Coming Next
   - Brief preview of V1/V2 items or next sprint priorities
   - Helps CS and sales set expectations with customers

8. Technical Notes (optional)
   - Architecture changes, API updates, infrastructure changes
   - Only include if relevant to the audience
   - Deployment notes or rollback procedures if applicable
```

### For External Audiences (customers)

External release notes are concise, benefit-focused, and jargon-free. They tell customers what changed and why they should care.

```
Title Page:
  "What's New — {Release Name}"
  Date

1. Overview
   - 1-2 sentence summary written for customers
   - Focus on the benefit, not the feature

2. New Features
   - Each feature gets:
     - Benefit-oriented heading (e.g., "Get started faster with guided onboarding" not "Interactive Guided Tour")
     - **What it is:** 1-2 sentences explaining what the user can now do, in plain language
     - **Why it matters to you:** 1 sentence on why this makes their life better
     - **Screenshot:** Placeholder for a polished screenshot showing the feature from the customer's perspective. Use `[Insert screenshot: {description}]` with a caption. Focus on the happy path — show the feature working, not edge cases.
     - No technical jargon, no requirement IDs
   - Order by customer impact

3. Improvements
   - Brief, benefit-focused descriptions
   - "Faster load times" not "Reduced P95 latency by 40ms"

4. Bug Fixes
   - Only customer-visible fixes
   - Brief, plain language

5. Coming Soon
   - 1-2 sentence teaser of upcoming improvements
```

## Categorization Logic

When processing inputs, categorize each item using these rules:

- **New Feature**: A capability that didn't exist before. Usually maps to a new PRD requirement or a Jira Epic.
- **Improvement**: An enhancement to an existing feature. Look for words like "improve", "enhance", "optimize", "update", "refine" in the source material.
- **Bug Fix**: Something that was broken and is now fixed. Look for words like "fix", "resolve", "correct", "patch", "address" in Jira stories.
- **Breaking Change**: Anything that changes existing behavior in a way that could disrupt users. Look for API changes, removed features, changed defaults, or migration requirements.
- **Known Issue**: Problems identified but not resolved in this release. Often mentioned in PRD risks or sprint retro notes.

If an item doesn't clearly fit a category, default to "Improvement" for enhancements and "New Feature" for net-new functionality. When in doubt, ask the user.

## Tone Guidelines

### Internal Tone
- Professional but direct — write like you're briefing a colleague
- Include technical context where helpful
- Reference Jira tickets, PRD sections, and requirement IDs
- It's OK to mention tradeoffs, known issues, and future plans
- Use active voice: "Users now see..." not "A feature has been added that..."

### External Tone
- Warm, clear, benefit-focused
- No internal jargon (no "REQ-001", no "V0", no "epic")
- No apologies for bugs — just state what was fixed
- Frame everything as a positive: "You can now..." or "We've improved..."
- Keep sentences short and scannable

## Document Formatting

Use the docx skill's JavaScript approach (`npm install -g docx`) to create the document.

### Style Guidelines
- **Font:** Arial throughout
- **Title:** 28pt, bold, centered
- **Heading 1:** 24pt, bold (section headers like "What's New")
- **Heading 2:** 18pt, bold (individual feature names)
- **Body:** 12pt
- **Page size:** US Letter (12240 x 15840 DXA)
- **Margins:** 1 inch all sides (1440 DXA)
- **Header:** "Release Notes" on the left, "CONFIDENTIAL" on right (internal only; omit for external)
- **Footer:** Page numbers centered

### Formatting Details
- Use bullet lists (LevelFormat.BULLET with numbering config) for multi-point descriptions
- Use bold TextRuns for emphasis on key terms within body text
- Add spacing between sections for readability (spacing.after: 200 on paragraphs)
- If a "Before vs. After" comparison is needed, use a simple two-column table
- Tables: use `WidthType.DXA`, set both `columnWidths` and cell `width`, use `ShadingType.CLEAR` for headers

## Generation Process

1. **Parse all inputs** — read uploaded files (.docx, .csv), parse pasted text, pull from conversation context
2. **Identify the release scope** — what version/sprint is this for? What shipped?
3. **Categorize items** — sort into New Features, Improvements, Bug Fixes, Breaking Changes, Known Issues
4. **Determine audience** — internal, external, or both. Ask if unclear.
5. **Synthesize and write** — don't just list items. Write coherent descriptions that explain the "what" and the "why."
6. **Generate the .docx** — using the docx npm package with the formatting above
7. **Validate** — run `python scripts/office/validate.py` on the output
8. **Present** — save to `/mnt/user-data/outputs/` and share with the user

## Important Behaviors

- **Use the What / Why / Value framework for every feature.** Each new feature must answer three questions: What is it? Why did we build it? Why is it valuable? This structure helps readers quickly understand the feature without having to piece it together themselves. For internal audiences, "why we built it" should reference customer research, data, or strategic goals. For external audiences, "why it matters" should be framed as a direct benefit to the customer.
- **Include screenshot placeholders for every new feature.** Screenshots help readers visualize the feature far better than text alone. For each feature, include 1-3 specific screenshot placeholders with descriptive captions that tell the PM exactly what to capture. Be specific: "[Insert screenshot: Tooltip overlay highlighting the 'Create Project' button during onboarding step 3]" not "[Insert screenshot]". For internal notes, include screenshots of key states (initial view, mid-flow, completion). For external notes, one polished happy-path screenshot per feature is usually enough.
- **If the user provides actual screenshot images,** embed them directly in the document using ImageRun. If no images are provided, use the descriptive placeholder text so the PM knows exactly what screenshots to add before distributing.
- **Synthesize, don't copy-paste.** The user's raw inputs (Jira titles, PRD requirements) are source material, not the final product. Rewrite everything into clear, polished prose appropriate for the audience.
- **Lead with impact.** Order features by how much they matter to the audience, not by requirement ID or Jira priority. The most exciting change goes first.
- **Be specific about behavior.** "Improved onboarding" is too vague. "New users now see a step-by-step guided tour on first login that walks them through creating their first project" is useful.
- **Connect features to benefits.** Don't just describe what changed — explain why it matters. "This reduces time-to-first-value from 4 days to under 1 day" gives the reader context.
- **Mark unknowns clearly.** If the inputs don't cover a section (e.g., no bug fixes mentioned), include the section header with "[None in this release]" rather than omitting it. This makes it clear the omission was intentional.
- **Keep external notes short.** Customers want to scan, not read an essay. 2-3 sentences per feature is the target for external notes.
- **Cross-reference when internal.** For internal notes, include Jira ticket references and REQ-IDs so readers can trace back to the source.

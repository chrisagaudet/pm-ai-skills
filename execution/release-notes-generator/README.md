# Release Notes Generator

A Claude skill that transforms mixed release inputs into polished, professional release notes (.docx).

## What It Does

Takes any combination of inputs — PRDs, Jira stories, rough bullet points, sprint summaries — and produces well-organized release notes ready to share with your team or customers.

## Inputs

Feed it whatever you have:
- **PRD (.docx or pasted text)** — the source requirements document
- **Jira stories or CSV export** — completed stories from the sprint
- **Rough bullet points** — quick notes about what shipped
- **Any combination of the above**

## Output

A `.docx` file with professional formatting, organized by category.

### Audience Modes

The skill generates different output depending on who's reading:

| Audience | Tone | Detail Level | Includes |
|----------|------|-------------|----------|
| **Internal** (eng, CS, sales) | Professional, direct | High — technical context included | REQ-IDs, Jira references, analytics events, known issues, rollout plan |
| **External** (customers) | Warm, benefit-focused | Concise — no jargon | Benefit-oriented headings, plain language, "Coming Soon" teaser |

If you ask for both, it generates two separate files.

## Feature Structure: What / Why / Value

Every new feature in the release notes follows a three-part framework:

- **What it is:** Clear description of the feature from the user's perspective
- **Why we built it:** The research, data, or customer insight that motivated it
- **Why it's valuable:** Expected impact tied to metrics or business outcomes

This gives CS and sales the full story without needing to read the PRD.

## Screenshot Placeholders

Each feature includes descriptive screenshot placeholders like:

> *[Insert screenshot: Tooltip overlay highlighting the 'Create Project' button during onboarding step 2, with the progress indicator visible]*

The placeholders tell you exactly what to capture. If you provide actual images, the skill will embed them directly.

## Document Sections

### Internal Release Notes
1. Release Summary
2. What's New (with What/Why/Value + screenshots)
3. Improvements
4. Bug Fixes
5. Breaking Changes
6. Analytics & Instrumentation (event details)
7. Known Issues (with workarounds and fix timelines)
8. Rollout Plan
9. Success Metrics (table with baseline/target/timeframe)
10. What's Coming Next

### External Release Notes
1. Overview
2. New Features (benefit-oriented, with screenshots)
3. Improvements
4. Bug Fixes
5. Coming Soon

## Categorization

The skill automatically sorts items into categories:

| Category | What Goes Here |
|----------|---------------|
| New Feature | Capabilities that didn't exist before |
| Improvement | Enhancements to existing features |
| Bug Fix | Things that were broken and are now fixed |
| Breaking Change | Changes that could disrupt existing workflows |
| Known Issue | Problems identified but not yet resolved |

## Example Usage

**From a PRD:**
> "Here's the PRD for our onboarding feature. We just shipped V0 — can you write up internal release notes?"

**From rough bullets:**
> "We shipped these this sprint: new guided tour on first login, skip button for the tour, progress indicator showing step count, and full analytics instrumentation. Write release notes for the team."

**From Jira stories:**
> "Here's the CSV export from our sprint board. Write release notes — both internal and customer-facing versions."

## Document Formatting

- **Font:** Arial throughout
- **Page size:** US Letter
- **Tables:** Light blue headers (#D5E8F0), thin borders
- **Header:** "Release Notes" + "CONFIDENTIAL" (internal only)
- **Footer:** Page numbers centered
- **Screenshots:** Dashed-border placeholder boxes with gray background

## Installation

Upload `release-notes-generator.skill` to your Claude project's skills, or copy the `SKILL.md` file into your skills directory.

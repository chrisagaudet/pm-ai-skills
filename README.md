# PRD Generator

A Claude skill that transforms rough product notes into a polished, professional Product Requirements Document (.docx).

## What It Does

Paste your stakeholder meeting notes, customer feedback, or high-level product ideas into Claude, and this skill generates a fully formatted PRD as a downloadable Word document. It synthesizes messy inputs into clear, structured sections — rewriting vague ideas into specific, testable requirements with traceable IDs.

Sections that can't be inferred from your input are included with `[TO BE DEFINED]` placeholders so the document is always complete and ready for review.

## PRD Structure

The generated document includes 17 sections:

| # | Section | Key Content |
|---|---------|-------------|
| 1 | Title Page | Product name, author, date, version, status |
| 2 | Problem Statement | What's broken, who it affects, why it matters now |
| 3 | Hypothesis | "We believe [X] for [Y] will achieve [Z] because [reasoning]" |
| 4 | Goals / Objectives | Measurable, outcome-oriented targets |
| 5 | Target Audience | Personas, pain points, current workarounds |
| 6 | Solutions Considered | 3 options compared (pros, cons, effort, impact) with selected solution rationale |
| 7 | Requirements | Table with ID, description, release version (V0/V1/V2), priority (P0/P1/P2) |
| 8 | Out-of-Scope | Explicit exclusions to prevent scope creep |
| 9 | Go-to-Market Plan | Launch strategy, comms plan, enablement |
| 10 | Risks | Technical, business, and adoption risks with mitigations |
| 11 | Dependencies | Teams, projects, and external blockers (table format) |
| 12 | Resources | Roles, headcount, allocation, and phase (table format) |
| 13 | User Flows | Step-by-step journeys with wireframe placeholders |
| 14 | Mockups / Wireframes | Placeholder section for design artifacts |
| 15 | Success Metrics (KPIs) | Baseline → target with timeframes (table format) |
| 16 | Events to Track | Analytics events, triggers, properties (table format) |
| 17 | FAQs | Anticipated stakeholder and engineering questions |

## Installation

1. Download the `prd-generator.skill` file
2. In Claude, go to **Settings → Skills** and upload the `.skill` file
3. The skill activates automatically when you mention PRDs or ask to write up product ideas

## Usage

Just provide your rough notes in a conversation with Claude. The more context you give, the better the output — but even sparse inputs will produce a complete document with placeholders for what's missing.

**Example prompts:**

- *"Here are my notes from today's stakeholder meeting. Turn these into a PRD."*
- *"I want to build a feature that lets users export dashboards as PDFs. Write up a PRD."*
- *"Turn these customer feedback themes into a product spec."*

**What to include in your input (any or all):**

- Problem you're solving and for whom
- Your hypothesis about the root cause
- High-level requirements or feature ideas
- Known constraints, risks, or dependencies
- Target metrics or KPIs
- Team or resource information

## Output

- Format: `.docx` (Word document)
- Styling: Arial font, US Letter, 1-inch margins, professional table formatting
- Header: Document title + "CONFIDENTIAL"
- Footer: Page numbers

## Customization

- **Author**: Defaults to "Christopher Gaudet" — update in `SKILL.md` section 1
- **Output format**: Currently `.docx`. The skill is designed so switching to Notion later requires changing only the output section of `SKILL.md`
- **Section order**: Edit the numbered sections in `SKILL.md` to reorder
- **Adding sections**: Add a new numbered section to the PRD Structure in `SKILL.md`

## Related Skills

This skill is part of a broader PM automation toolkit. Planned skills include:

- **Jira Story Generator** — Break a PRD into user stories with acceptance criteria
- **Release Notes Writer** — Generate release notes from completed stories
- **GTM Communications Drafter** — Draft launch emails and internal announcements
- **Executive Pitch Deck Generator** — Create investment approval decks

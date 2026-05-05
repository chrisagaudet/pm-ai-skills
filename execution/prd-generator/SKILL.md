---
name: prd-generator
description: "Generate a Product Requirements Document (PRD) as a .docx file from rough stakeholder notes, customer feedback, hypotheses, or high-level requirements. Use this skill whenever the user mentions 'PRD', 'product requirements document', 'product spec', 'requirements doc', or asks to turn meeting notes, stakeholder feedback, or product ideas into a structured document. Also trigger when the user wants to create a document with sections like problem statement, goals, requirements by release version, go-to-market plan, success metrics, or user flows. Works for both new PRDs from scratch and restructuring existing notes into PRD format. Even if the user says something casual like 'I had a meeting about a new feature, help me write it up' or 'turn these notes into something I can share with engineering', this skill applies."
---

# PRD Generator

## Purpose

Transform rough product inputs (stakeholder notes, customer feedback, hypotheses, high-level requirements) into a polished, professional Product Requirements Document (.docx) that is ready to share with engineering, design, and leadership.

## When This Skill Triggers

- User provides rough notes and wants a PRD
- User mentions "PRD", "product requirements", "product spec"
- User wants to structure product ideas into a shareable document
- User has meeting notes they want formalized into requirements
- User asks to "write up" a feature or product idea

## Inputs

The user will typically provide some combination of:
- Rough notes from stakeholder or customer meetings
- A hypothesis about the problem to solve
- High-level requirements or feature ideas
- Context about the target audience or customer
- Any known constraints, risks, or dependencies

It's OK if the input is messy or incomplete. The skill should do its best with what's provided and clearly mark sections that need the user's input with `[TO BE DEFINED]` placeholders.

## Output

A `.docx` file with professional formatting, following the PRD structure below.

### Output Location

Write the final `.docx` to `/mnt/user-data/outputs/`. The filename should be descriptive, e.g., `PRD_Feature_Name_YYYY-MM-DD.docx`.

> **Future note:** The user plans to migrate to Notion. When that happens, update only this output section — the PRD structure and generation logic remain the same.

## PRD Structure

Every PRD must include ALL of the following sections in this order. If the user's input doesn't cover a section, include it anyway with `[TO BE DEFINED]` placeholder text and a brief note about what should go there.

### 1. Title Page
- Product/Feature name
- Author (default: "Christopher Gaudet")
- Date
- Version (default: "Draft v0.1")
- Status (default: "Draft")

### 2. Problem Statement
- Clear articulation of the problem being solved
- Who experiences this problem and how
- Why solving it matters now

### 3. Hypothesis
- The product leader's hypothesis about the root cause and the believed solution direction
- Frame as: "We believe that [doing X] for [audience Y] will achieve [outcome Z] because [reasoning]"
- This is distinct from the solution — the hypothesis captures the underlying belief being tested

### 4. Goals / Objectives
- Measurable goals tied to the problem
- What success looks like
- Use bullet points, keep them specific and outcome-oriented

### 5. Target Audience / Customer
- Primary user personas or segments
- Their pain points and needs
- How they currently solve (or don't solve) this problem

### 6. Solutions Considered
- Present 3 potential solutions that could address the problem
- For each solution, include: a brief description, key pros, key cons, estimated effort level (Low / Medium / High), and estimated impact level (Low / Medium / High)
- Clearly identify which solution is being selected and explain why
- The reasoning should reference the hypothesis, the goals, and practical constraints (timeline, resources, risk)
- Present as a comparison table followed by a "Selected Solution" subsection with the rationale

### 7. Requirements
- **Presented as a table** with columns: Requirement ID, Description, Release Version (V0, V1, V2, etc.), Priority (P0, P1, P2)
- Group requirements by release version
- V0 = MVP / minimum viable release
- V1 = fast-follow improvements
- V2+ = future enhancements
- Each requirement should be specific and testable

### 8. Out-of-Scope
- What this PRD explicitly does NOT cover
- Features or use cases intentionally excluded
- This prevents scope creep and sets clear boundaries

### 9. Go-to-Market Plan
- Launch strategy (phased rollout, beta, GA, etc.)
- Communication plan (internal + external)
- Training or enablement needs
- Key dates or milestones if known

### 10. Risks
- Technical risks
- Business risks
- User adoption risks
- Mitigations for each risk where possible

### 11. Dependencies
- Teams involved and their responsibilities
- Related projects or features this depends on
- External dependencies (APIs, vendors, partners)
- Present as a table: Dependency, Team/Owner, Status, Impact if Delayed

### 12. Resources
- What people and roles are needed to execute this project
- Present as a table: Role, Team, Count Needed, Allocation (Full-time / Part-time), Phase (V0, V1, V2, or All)
- Common roles to consider: Product Manager, Engineering (Frontend), Engineering (Backend), Engineering (QA), UX Designer, UX Researcher, Data Analyst, Marketing, Customer Success, Technical Writer
- If the user's input specifies team members or headcount, use that. Otherwise, infer reasonable staffing from the scope of the requirements and mark specifics as `[TO BE DEFINED]`
- Include a brief note on any resource constraints or hiring needs if mentioned

### 13. User Flows
- Step-by-step description of key user journeys
- Numbered steps for each flow
- Note where mockups or wireframes should be inserted
- Include placeholder text: `[Insert wireframe/mockup here]`

### 14. Mockups / Wireframes
- Placeholder section with note: `[Attach mockups and wireframes here]`
- Reference any design tools or links the user mentions

### 15. Success Metrics (KPIs)
- Specific, measurable KPIs tied to goals
- Baseline values if known
- Target values and timeframes
- Present as a table: Metric, Baseline, Target, Timeframe

### 16. Events to Track
- Analytics events needed to measure success
- Event name, trigger condition, properties to capture
- Present as a table: Event Name, Trigger, Properties, Associated KPI

### 17. FAQs
- Anticipated questions from stakeholders, engineering, or customers
- If none are obvious from the input, include 3-5 common PRD questions as starters with `[TO BE DEFINED]` answers

## Document Formatting

Use the docx skill's JavaScript approach (`npm install -g docx`) to create the document.

### Style Guidelines
- **Font:** Arial throughout
- **Title:** 28pt, bold, centered
- **Heading 1:** 24pt, bold (section headers)
- **Heading 2:** 18pt, bold (subsections)
- **Body:** 12pt
- **Page size:** US Letter (12240 x 15840 DXA)
- **Margins:** 1 inch all sides
- **Tables:** Light gray header row (#D5E8F0), thin borders (#CCCCCC), cell padding
- **Header:** Document title and "CONFIDENTIAL" on right
- **Footer:** Page numbers centered

### Table Formatting
- All tables must use `WidthType.DXA` (never percentage)
- Set both `columnWidths` on the table AND `width` on each cell
- Use `ShadingType.CLEAR` for header shading
- Add cell margins for readability: `{ top: 80, bottom: 80, left: 120, right: 120 }`

## Generation Process

1. **Parse the user's input** — identify anything that maps to the PRD sections above
2. **Ask clarifying questions only if critical information is missing** — like the product/feature name. Otherwise, proceed and use `[TO BE DEFINED]` placeholders
3. **Generate the .docx** — using the docx npm package and the structure/formatting defined above
4. **Validate** — run `python scripts/office/validate.py` on the output
5. **Present** — save to `/mnt/user-data/outputs/` and share with the user

## Important Behaviors

- **Be opinionated:** Don't just parrot the user's notes back. Synthesize them into clear, professional language. Rewrite vague requirements into specific, testable ones.
- **Fill gaps intelligently:** If the user mentions "we need better onboarding," expand that into specific requirements like "Display a guided tour on first login" with appropriate release versions.
- **Maintain traceability:** Every requirement should have an ID (e.g., REQ-001) so it can be referenced in Jira stories later.
- **Mark unknowns clearly:** Use `[TO BE DEFINED]` for anything you can't reasonably infer. Don't make up metrics, dates, or team assignments.
- **Keep it scannable:** Executives and engineers should both be able to quickly find what they need. Use tables, bullet points, and clear headings.

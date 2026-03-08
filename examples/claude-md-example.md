# CLAUDE.md

<!-- This file is loaded into every Claude Code conversation automatically.
     Treat it as onboarding docs for a team member who starts fresh each session.
     Be specific – the more context here, the less you repeat yourself. -->

This file provides guidance to Claude Code (claude.ai/code) when working with this repository.

## Context

- **Current year**: 2026. All dates, filenames, and references should use 2026 unless explicitly told otherwise.

## Repository Overview

This is a Product Management documentation repository for Relay, containing feature specifications, project context, and product documentation. Relay builds sales engagement tools for mid-market B2B teams.

## Repository Structure

<!-- Numbered prefixes keep directories sorted by usage frequency. Adjust to taste. -->

- **00_daily/**: Daily workflow – daily-notes, tasks, meeting-notes (gitignored)
- **01_products/**: Product documentation organised by product area (see Team Structure below)
- **02_planning/**: Planning activities – prioritization, initiatives, OKRs
- **03_reference/**: Stable knowledge – context/, frameworks/, resources/
- **.claude/**: Claude Code configuration and skills

## Key Product Context

### Relay Platform

- **Core Products**: Sequences (multi-channel email sequences), Signals (buyer intent data), Connect (power dialer + call intelligence)
- **Target Market**: 50-500 seat B2B sales teams, primarily SaaS and professional services
- **Primary Users**: SDRs, AEs, Revenue Operations
- **CRM Integration**: Salesforce and HubSpot – bidirectional sync via REST APIs
- **Signals Note**: Signals ingests data from multiple third-party intent providers (Bombora, G2) and combines it with first-party engagement data from Sequences and Connect

### Team Structure & PM Ownership

<!-- This table tells Claude who owns what. When you say "write a spec for Sequences,"
     it knows that's your product. When you reference Connect, it knows that's Priya's. -->

| Team | PM | Products | Notes |
|------|-----|----------|-------|
| Outbound | Sarah (primary) | Sequences, Sequences Analytics | Sarah's main focus |
| Intelligence | Sarah (interim) | Signals | Dedicated PM being hired |
| Voice | Priya | Connect, Call Intelligence | |

### Strategic Priorities

- Unified activity timeline across all three products
- Signals-driven prioritisation in Sequences (intent data triggers sequence enrollment)

## Working with This Repository

When creating or editing documentation:

- Follow the spec template in `03_reference/templates/spec-template.md` for feature specifications
- Include relevant Jira tickets and Figma design links
- Include "Out of scope" sections to clarify boundaries
- Consider CRM sync constraints – Salesforce API limits, HubSpot workflow triggers
- Reference company context in `03_reference/context/` for strategic alignment

### Working with Data and Analysis

When analyzing data (JSON exports, CSV files, API responses, etc.):

- **Create an `analysis/` folder** within the relevant product or feature directory
- All `analysis/` folders are automatically gitignored (see `.gitignore`)
- Example: `01_products/sequences/a-b-testing/analysis/`

### Search Strategy

- **Grep** – exact terms, patterns, or strings you know exist in the repo
- **Glob** – finding files by name or path pattern

## Jira

### Instance

- **URL**: `relay-eng.atlassian.net`
- **Guide**: See `03_reference/resources/guides/atlassian.md`

### Ticket Creation

<!-- Project keys route tickets to the right board. Getting this wrong
     means engineers never see the ticket. -->

- **Project Routing**:
  - `SEQ` – Sequences
  - `SIG` – Signals
  - `CON` – Connect
  - `PLAT` – Platform / shared infrastructure
- **Assign correctly**: Use full email addresses (e.g., `marcos@relay.io`)
- **Include context**: Video links, business justification, acceptance criteria
- **Structure content**: Problem > Solution > Context > Acceptance Criteria

## Writing Preferences

<!-- These rules prevent Claude from writing in a style you'll need to rewrite.
     Be explicit about words and patterns you dislike. -->

- **Dashes**: Don't use em dashes (—).
- **Default tone**: Direct, matter-of-fact prose. State things plainly.
- **Avoid emphasis words**: Don't use "key insight", "importantly", "crucially". If something matters, the content will show it.
- **Avoid marketing verbs**: Words like "unlocks", "enables", "empowers", "drives" are for customer-facing copy, not internal docs.
- **Active voice**: "CRM sync requires a valid API token" not "It should be noted that a valid API token is required for the CRM sync to function."
- **Trust the reader**: Assume product and domain knowledge. Don't explain what an SDR is or how email sequences work.

## MCP Servers

<!-- MCP servers connect Claude to external systems. Configure these in .mcp.json
     at the root of your project. Each entry here tells Claude what it can do
     with each server and any quirks to watch for. -->

### Slack

- Read public channels, search messages, draft announcements
- Private channels require `search_public_and_private` – `read_channel` returns empty results on private channels
- Thread replies don't appear in `read_channel` – use `read_thread` with the parent message timestamp

### Feedback Tool (Productboard)

- Search customer requests, pull quotes, check existing feature requests
- Use the Productboard MCP server for feature evidence gathering during spec work

### Task Manager (Todoist)

- Create tasks, move between projects, read inbox
- Default project: Work

## Workflow Rules

- **Git safety**: Never assume which repo, org, or destination to push to. Always ask before any git push or remote operation.
- **Preserve structure**: When updating documents, preserve empty or placeholder sections rather than deleting them. Leave unfilled sections with "TBD" – never remove them.
- **Destructive operations**: Before running multi-file renames, directory restructures, or any operation that modifies many files at once – present a plan and wait for approval.

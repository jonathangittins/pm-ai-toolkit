---
name: weekly-feedback-review
description: >
  Weekly batch review of customer feedback. Scans the feedback tool for new
  ideas and momentum, surfaces clusters and trends, and generates validation
  assessments for promising ideas. Two-phase: generate a triage file
  asynchronously, then execute after review.
---

# Weekly Feedback Review

Weekly batch review of customer feedback – surfaces patterns and identifies ideas worth validating.

## Workflow

Two-phase, async:

1. **Generate** – scan the feedback tool, write a triage file with proposed actions. The PM reviews the file in their notes app and fills in the "Actual" column.
2. **Execute** – the PM re-invokes the skill. Claude reads the file, picks up triage decisions, and executes actions (account lookups, task creation, validation assessments).

Phase 1 can run unattended (e.g. via cron on a server). Phase 2 requires the PM.

## Prerequisites

- Feedback tool MCP configured (e.g. Canny, Productboard, or similar)
- Task manager MCP for capturing follow-ups
- CRM CLI or API for account lookups (optional, for "book a call" actions)
- A daily notes system

## Phase 1: Generate

### Step 1: Scan Feedback Tool (subagent)

**Delegate to a subagent** to keep raw feedback data out of the main context.

The subagent should:

1. **Fetch recent ideas** – last 7 days, sorted by creation date
2. **Filter to the PM's product groups** – configure which product areas to include
3. **Enrich with evidence** – for each matching idea, pull:
   - Source (sales call recordings, review sites, support tickets)
   - Customer priority classification
   - Evidence text (what the customer said)
   - Company name (for account lookups later)
4. **Check momentum** – existing ideas that gained new evidence this week but were created earlier. These are often more interesting than brand-new ideas.
5. **Note other groups** – ideas outside the PM's ownership that mention their products

**Return a structured summary:**
```
### New Ideas This Week (X in your groups)

1. **[Idea title]** – [Group name]
   [One-line description]
   Source: [type] ([link]) · ARR: $X · Evidence: N items
   Companies: [list]

### Momentum (X existing ideas with new activity)

1. **[Idea title]** – [Group name]
   New evidence this week: N (total: M)
   Latest: "[quote]" – [company]
   ARR: $X

### Other Groups (FYI)
- [Idea title] – [Group name] – $ARR – N evidence items
```

### Step 2: Write Triage File

Write the review to a weekly file (e.g. `weekly-feedback/YYYY-WNN.md`).

**File structure:**

```markdown
# Weekly Feedback Review – YYYY-WNN

Date: YYYY-MM-DD

## [Product Area]

| # | Idea | ARR | Evidence | Proposed | Actual |
|---|------|-----|----------|----------|--------|
| 1 | [Idea title](link-to-feedback-tool) | $X | N | Review | |
| 2 | [Idea title](link-to-feedback-tool) | $X | N | Ignore | |

**Notes:**
- Context about clusters, related ideas, connections to existing work.

## Other Groups (FYI)

| Idea | Group | ARR | Why it's here |
|------|-------|-----|---------------|
| Title | Group | $X | Relevance note |

## Trends

- **[Theme]**: Description with combined ARR and idea references.

## Validation Candidates

*Pending triage.*

## Actions Taken

*Pending triage.*
```

**Table conventions:**
- **Idea links** should go directly to the idea in the feedback tool
- **Proposed column** uses full words: Review, Ignore, Book a Call, Validation Candidate
- **Actual column** left blank for the PM to fill in
- **Group by product area** with separate tables
- **Notes below each table** for clusters, same-source groupings, and context
- **Numbered sequentially** across all tables

After writing, add a link to the daily note so the PM sees it during their review.

Phase 1 is complete.

## Phase 2: Execute

When the PM returns after reviewing the file, read it and parse the Actual column.

### Step 3: Process Triage Decisions

For each idea where Actual is filled in:

- **Review** – no action needed, PM has already reviewed
- **Ignore** – no action needed
- **Book a Call** – look up the customer's account in CRM, create a task with account owner names and context
- **Validation Candidate** – generate a validation assessment (Step 4) and create a task

### Step 4: Validation Assessments

For each idea marked as Validation Candidate, generate:

1. **What's the assumption?** – The core assumption that needs to be true. One sentence.
2. **Who already does this?** – Do customers have workarounds today? Check the evidence text for clues.
3. **Cheapest test** – The simplest way to test this assumption this week:
   - A prompt prototype (paste into an AI chat and test the interaction)
   - A direct message to 2-3 customers asking about the problem
   - A CRM query to see how many accounts hit this pattern
   - A config change to an existing feature that approximates the idea
   - A mock/wireframe to put in front of the next customer call
4. **Signal to watch for** – What would tell you this is worth pursuing vs. not?

Keep each assessment to 4-5 lines. The goal is "can I test this today?" not "let me plan a research programme."

Write the assessments into the Validation Candidates section. Create a task for each with the cheapest test as the description and the full assessment in notes.

### Step 5: Update Actions Taken

Update the file with everything executed:

```markdown
## Actions Taken

- [Book a Call] [idea] – [company] – AM: [name], CSM: [name] → task created
- [Validation Candidate] [idea] – testing via [method] → task created
- [Review] [idea] – reviewed in feedback tool
- [Ignore] [idea] – [brief reason]
```

## Architecture Notes

**Why async triage:** Interactive triage works well for 5-10 items (like the EOD review's Slack saved items). For 20+ feedback ideas that each need clicking through to read customer quotes, async is better. You make considered decisions in your own time rather than snap judgements mid-conversation.

**Why weekly, not daily:** Customer feedback from sources like sales call recordings accumulates slowly – a few new ideas per day. Daily scanning adds overhead without much signal. A weekly batch lets you see clusters that daily scans miss, and the trend analysis improves with more data points.

**Why validation assessments:** Most feedback tools show you what customers want but not how to test whether the idea is viable. The validation step bridges the gap between "customers asked for X" and "is X worth building?" – borrowing from lightweight product validation methodology to generate the cheapest possible test for each promising idea.

**Separation of concerns with EOD review:** The EOD review's discovery lens still catches product signals in real-time Slack conversations and cross-references them with the feedback tool. This weekly skill is the deeper, batch-level review. They complement each other: daily signals → weekly patterns → monthly themes.

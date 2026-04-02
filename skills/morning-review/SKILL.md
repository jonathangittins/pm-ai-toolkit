---
name: morning-review
description: >
  Morning review skill. Closes yesterday's daily note, triages Slack saved
  items and channel threads interactively, extracts meeting follow-ups,
  reshuffles the task list, and generates today's daily note with priorities
  mapped to goals.
---

# Morning Review

Closes yesterday and sets up today. Runs interactively when the PM sits down in the morning.

## Prerequisites

- Slack MCP configured
- Feedback tool MCP configured (e.g. Canny, Productboard) for discovery lookups
- A task file system: an open-tasks file and a completed-tasks file (e.g. markdown with inline fields)
- A daily notes system (e.g. Obsidian, markdown files)
- Calendar access (script, API, or MCP)
- Optional: project tracker CLI or API (e.g. Jira, Linear) for ticket digests

## Task System

Tasks live in a single open-tasks file with inline metadata fields.

**Format:**
```
- [ ] Task title [due:: 2026-03-14] [project:: Web] [tags:: Slack] [added:: 2026-03-14] [waiting:: Alex]
  Context notes, indented with 2 spaces. Slack-sourced tasks MUST include a permalink here.
  e.g. From Dana in #product-questions: https://yourco.slack.com/archives/C051TG5E1CG/p1710900000000000
```

**Sections:** Today, This Week, then by project area (configure for your team's structure).

**Sub-sections within each project (### level):** Each project section uses action-based sub-sections. Only include sub-sections that have tasks -- omit empty ones.

- `### Respond` -- quick replies, yes/no/maybe answers, short clarifications. Minutes not hours.
- `### Review` -- watch videos, read documents, give feedback. Consumption-mode.
- `### Discuss` -- topics to raise with someone (`[with:: Name]`). Not blocked -- conversation items for 1:1s or syncs.
- `### Waiting` -- genuinely blocked on someone (`[waiting:: Name]`). Can't act until they deliver something.
- `### Do` -- active work: build, investigate, write, create, coordinate.
- `### Someday` -- aspirational, ideas, "check this out". No urgency, no commitment.

**Classification guidance for new tasks:**
- Slack thread needing a quick reply -> Respond
- Slack thread that kicks off real work (spec, POC, investigation) -> Do
- Slack thread linking a video/doc to review -> Review
- `[with:: Name]` -> Discuss
- `[waiting:: Name]` -> Waiting
- "Check out X", "Explore Y", ideas -> Someday
- When ambiguous, prefer Do over Someday

**Within each sub-section:** chronological order (oldest first).

**Fields:**
- `[project:: X]` -- required. Configure your project names.
- `[due:: YYYY-MM-DD]` -- deadline
- `[added:: YYYY-MM-DD]` -- date the task was added. Required on all new tasks. Enables staleness detection -- tasks with no due date and an `[added::]` older than ~2 weeks are candidates for removal.
- `[tags:: X]` -- origin/type marker: Slack, Email, Ticket, Watch, etc.
- `[with:: Name]` -- discussion topic for a specific person
- `[waiting:: Name]` -- genuinely blocked on someone

**Slack permalink rule (HARD REQUIREMENT):** Every task with `[tags:: Slack]` MUST have a permalink URL in its context line. No exceptions. If you have a channel name but no permalink, search Slack to find the message and build the link. A channel name alone (e.g. "#product-questions") is NOT sufficient. Format: `https://yourco.slack.com/archives/{channel_id}/p{ts_without_dot}`. If after searching you genuinely cannot find the message, hold the item and flag it rather than writing the task without a link.

**Completing a task:** Remove the line from the open-tasks file and append it to the completed-tasks file with `- [x]` and a `[done:: YYYY-MM-DD]` field. Most recent first, under a date heading.

## Triage: Interactive Approval

All triage is interactive -- items are presented for the PM's approval before execution.

### LNO Classification

Per the LNO Effectiveness framework:
- **L** -- Leverage: multiplies effectiveness 10x
- **N** -- Neutral: important but incremental
- **O** -- Overhead: necessary but low-impact

### Guardrails

Apply these before proposing any task:

1. **Task attribution** -- verify who actually owns the action. Meeting notes and all-hands often mention you in context but the action belongs to someone else. If the owner isn't clearly the PM, don't propose it.

2. **Calendar items don't need tasks** -- if something is already on the calendar (demos, meetings, events), don't duplicate it as a task.

3. **Saved Slack items are deliberate** -- every saved item gets a task. Never dismiss a saved item as informational or FYI-only. If the PM saved it, they want to come back to it. Use Review for reads/watches, Respond for replies, Do for actions.

4. **Project routing** -- configure clear rules for which project label to apply. When the team home is ambiguous, use a catch-all label.

### Proposed actions
- **T(Project/SubSection)** -- add to the open-tasks file under the correct project section AND sub-section, with context as indented notes (include Slack permalink). Example: `T(Web/Respond)` = quick Slack reply, `T(Web/Do)` = real work item.
- **I** -- ignore, no action needed
- **P** -- save context to person file (a per-person markdown file where you track context about colleagues, stakeholders, and contacts)

### Interaction model

Present all items in a numbered list with proposed action and LNO classification. The PM approves, modifies, or rejects each. Cheat sheet:

```
Approve all · 4 5 = approve those · 3->I = change to ignore
T(Project) = Task · I = Ignore · P = People file
Dates: tomorrow = due date · >fri = deadline
LNO override: 4->T(Web) L
e.g., approve all  or  4 5  3->I  4->T(Backend) >fri
```

After the PM responds, execute the approved actions (add to task file, update person files, etc.).

## Process

### Step 1: Staleness Check

Read the open-tasks file and check the "Last updated" timestamp.

- If >24h old, warn: "Task list is stale." Skip the reshuffle (Step 6). Only append new tasks; don't reorganise sections.
- If fresh, proceed normally.

### Step 2: Close Yesterday

Determine yesterday's date (Friday -> use Friday's date on Monday morning).

1. Read yesterday's daily note
2. If the end-of-day section is already populated, skip this step
3. If empty, populate it:

**Done today** -- compile from:
- Completed-tasks file -- tasks with `[done:: YESTERDAY]`
- Meetings attended -- from yesterday's calendar section
- Notes in yesterday's daily note
- Task manager logbook (if available) -- for completions logged elsewhere

**Blocked** -- infer from yesterday's notes and open questions.

**Priority for today** -- will be generated in Step 7 (today's morning brief).

**Exclude from Done today:** "Daily Review" itself, triage counts, snapshot exports -- meta-tasks that don't represent real work.

### Step 3: Triage Slack Saved Items

1. Search for saved Slack messages (`is:saved`, sorted by most recent)
2. If no results, note "Slack saved items is clear" and move on
3. Apply triage logic -- propose actions for each item and present for approval. Apply guardrails before proposing any task.

### Step 4: Slack Channel Scan (subagent)

**Delegate to a subagent** to keep raw Slack data out of the main context window.

The subagent should:

1. **Read configured channels** -- the PM's key channels. Configure these for your context -- typically a support/questions channel, a cross-team product channel, and any team-specific channels you own.
2. **Filter to yesterday's messages** -- skip anything from earlier days
3. **Identify threads needing attention:**
   - Threads with no human replies (bot auto-replies don't count as answered)
   - Threads where a human replied but the question is still open
   - Pattern detection (same question repeated = documentation gap)
4. **Filter by product ownership** -- only flag threads relevant to the PM's products
5. **Deduplicate** -- skip threads already triaged as saved items in Step 3

**Return a structured summary** -- not raw message content:
```
### #channel-name (X threads yesterday)
- **Unanswered**: [question summary] -- from [person] at [time]
- **Needs PM input**: [question summary] -- [person] asked about [topic]
```

**Discovery lens** (apply after the attention scan):

Re-scan all threads for product signals: feature requests, pain points, workflow frustrations, or integration asks. A thread can be "answered" from a support perspective but still contain a discovery signal.

For each signal found:
1. Extract the request in one sentence
2. Note who asked
3. Search your feedback tool for existing requests
4. Classify as **Match** (existing request), **Partial** (related but distinct), or **New**

Append a `### Discovery` section:
```
### Discovery
- **[Match]** "customer wants X" -- from [person] in #channel
  -> Matches: [Existing request title] (URL) -- N requests, $ARR
- **[New]** "customer wants Y" -- from [person] in #channel
  -> No match in feedback tool
```

**After receiving the subagent summary**, present attention items and discovery items separately for triage:

For **attention items:**
- **Draft a reply** -- create via Slack message draft with thread context
- **Capture to task file** -- add task under correct project section
- **Ignore** -- already aware or not actionable

For **discovery items:**
- **C** -- send to feedback tool (manually or via API)
- **C+** -- create a new idea in the feedback tool directly
- **B** -- book a call: CRM lookup, add task with account manager info
- **I** -- ignore

### Step 5: Meeting Follow-Up Scan (subagent)

**Delegate to a subagent.**

Read yesterday's daily note calendar entries. Extract meetings that happened.

The subagent should:

1. **Find meeting notes** for yesterday's meetings (e.g. from a meeting notes tool like Granola, Otter, or a shared notes folder)
2. **Read summaries only** -- not full transcripts
3. **Extract:**
   - Decisions made
   - Action items assigned to the PM
   - Open questions needing further discussion
   - Commitments made to others

**Return a structured summary:**
```
### [Meeting Name] (HH:MM)
- **Action**: [extracted action] -- [context]
- **Decision**: [what was decided]
- **Open**: [unresolved question]

### Meetings without notes:
- [Meeting name] (HH:MM)
```

**After receiving the summary**, present follow-ups for approval:
- **Capture to task file** -- add task with meeting context
- **Note for daily note** -- add to yesterday's Done today or Blocked
- **Draft a Slack message** -- create a follow-up message draft
- **Ignore** -- already handled

### Step 6: Reshuffle Task List

Update the "Last updated" timestamp.

1. **Move completed tasks** -- any `- [x]` items in open-tasks -> move to completed-tasks with `[done:: TODAY]`
2. **Promote due items** -- tasks with `[due:: TODAY]` in project sections -> copy to Today section
3. **Demote overdue** -- tasks in Today section with past due dates that are still open -> leave in Today for visibility
4. **Skip if stale** -- if Step 1 flagged staleness, only append new tasks from Steps 3-5, don't reorganise

### Step 7: Generate Today's Daily Note

Read these inputs:

1. **Calendar** -- fetch today's events. On Mondays, also fetch the full week.
2. **Goals** -- quarterly goals and ownership areas (e.g. GOALS.md or equivalent)
3. **Current context** -- current priorities, risks, blockers
4. **Open-tasks file** -- current task list (just reshuffled)
5. **Yesterday's daily note** -- for context continuity
6. **Person files** -- context files for anyone in today's meetings
7. **Meeting notes** -- scan recent meeting notes (last 2 working days) for context
8. **Project tracker digest** -- recent ticket activity (shipped, needs PM attention, in review)
9. **Initiatives board** -- read your planning Kanban for the snapshot section

Write today's daily note with:

```markdown
## Today's Calendar
[from calendar fetch]

## Triage Outcome
[Items triaged during the interactive session. List each approved action.
Format: "- **Task title** -> T(Project/SubSection) -- [sender in #channel](slack-permalink)"
EVERY Slack-sourced item MUST have a clickable permalink. Never use bare channel names.
If nothing was triaged, show "No new items."]

## Kanban Snapshot
[Show the Now and Ship Prep columns from your initiatives board -- just card names.
Cross-reference with today's calendar: if a meeting involves an initiative in the Next column,
flag it as a potential kickoff.]

## Morning Brief
[2-3 sentence summary: what's urgent, what can wait, context from yesterday.
Factor in triage results -- if something hot came in overnight, the brief should call it out
and the priority tasks below should reflect it.
Cross-reference task priorities against the Kanban Now column -- if effort is going
somewhere that isn't on the Kanban (or vice versa), call it out.]

## Priority Tasks
[Top 5, ranked by which quarterly goals they support. Must account for triage -- if a hot
item came in overnight, it may displace a lower-priority task.]
Every task sourced from Slack MUST include a clickable Slack permalink.

## Unmapped Tasks
[Tasks not mapped to goals -- flag for conscious decision]

## Ticket Activity (24h)
[From project tracker digest: needs PM attention, shipped/resolved, in review]

## Discovery Signals (from channel scan)
[New signals from Step 4 -- include feedback tool match status]
Every signal MUST include a clickable Slack permalink to the source message.

## Next Steps
[What to do first]

## Person File Updates
[Update person files from recent meeting notes]

## Notes

---

## End of Day Review

### Done today

### Blocked

### Priority for tomorrow
```

On Mondays, add "This Week's Calendar" and "Meeting Prep Needed" sections.

### Step 8: Verification (subagent)

**Delegate to a subagent** for cold-read checking.

Collect inputs into a scratch file:
- Yesterday's completed-task entries
- Channel scan summary
- Meeting follow-ups
- Yesterday's end-of-day section (written in Step 2)
- Today's morning brief (written in Step 7)

The verifier checks:
1. **Completeness** -- every completed task appears in "Done today"
2. **Date accuracy** -- day-of-week labels match actual dates; Friday -> "Priority for Monday"
3. **Product ownership** -- items attributed to the correct product areas
4. **Deduplication** -- no item appears in both channel scan and Done today without explanation
5. **Omissions** -- meeting follow-up actions appear somewhere (Done today, Priority for tomorrow, or as a captured task)

Returns `PASS` or a list of issues. Fix any issues before proceeding.

### Step 9: Summary

Print a summary after all steps complete:
- Items triaged (Slack saved + channels + meetings)
- Tasks added to task file
- Tasks moved to completed
- Daily note status (yesterday closed, today generated)

## Monday Variant

On Monday mornings:
- Yesterday = Friday (close Friday's daily note)
- Slack scan covers Friday's activity -- filter for threads that may have been resolved over the weekend
- Include "This Week's Calendar" with full work week
- Include "Meeting Prep Needed" section for non-recurring meetings with guests
- Flag the 5 oldest tasks per section for staleness review

## Architecture Notes

**Why parallel subagents:** Steps 4, 5 are independent data-gathering operations. Running them in parallel cuts review time compared to sequential execution. The main thread stays clean because raw Slack messages and meeting transcripts never enter the primary context window.

**Why interactive triage:** Presenting items as a numbered list with shortcodes and accepting `4 5 3->I` in a single line minimises back-and-forth. The PM processes 15-20 items in one response instead of triaging each individually.

**Why cold-read verification:** The verifier subagent receives raw source data and the proposed output with no conversation context. It catches errors the main thread is blind to -- missing items, date mistakes, misattributions -- because it has no memory of the drafting process.

**Why a discovery lens:** The channel scan serves two purposes: (1) find threads needing a reply, and (2) mine for product signals. A thread can be fully answered from a support perspective but still contain a feature request or pain point worth capturing in your feedback tool.

**Why staleness checks:** If the task list hasn't been updated in >24h, reorganising sections risks moving stale data around. Better to append only and let the PM do a manual cleanup.

**Morning vs EOD:** This skill pairs with the [EOD Review](../eod-review/) skill. The EOD review exports a task snapshot and closes the day; the morning review picks up that state, triages overnight activity, and generates the daily note. Together they create a continuous loop -- nothing falls through the cracks between sessions.

---
name: daily-review
description: Daily review (also fires on "morning review" / "morning-review"). Closes yesterday, triages Slack interactively, reshuffles tasks, generates today's daily note.
user_invocable: true
---

# Daily Review

Closes yesterday and sets up today. Runs interactively when the PM sits down in the morning.

## First Run

On first invocation, check the Slack Channel Scan subagent prompt (Step 4) for `<!-- CONFIGURE -->` placeholders. If any remain:

1. Read `CLAUDE.local.md` (or `CLAUDE.md`) for the PM's product ownership
2. Ask: "Which Slack channels should I scan daily? I need the channel name and ID for each." Suggest common ones based on their product area (e.g. a support channel, a product questions channel, and any team-specific channels).
3. Ask: "Which products do you own? I'll use this to filter threads to your area."
4. Replace the `<!-- CONFIGURE -->` blocks with their answers
5. Write the updated SKILL.md
6. Proceed with the review

This only happens once -- subsequent runs skip straight to Step 1.

## Prerequisites

- Slack MCP configured
- Feedback tool MCP configured (e.g. Canny, Productboard) for discovery lookups
- Task files: an `active.md` (open tasks) and `done.md` (completed tasks) in your workspace
- Daily notes directory in your workspace
- Calendar access -- a script or MCP that can return today's events as structured text. If you have a calendar fetch script, configure the path below. Otherwise, paste your calendar manually when prompted.
- An initiatives Kanban -- a markdown file or board (e.g. Obsidian Kanban plugin, Linear, Notion) with columns like Now, Next, Ship Prep, Later. The skill reads this for the Kanban Snapshot and Morning Brief sections. If you don't have one, skip those sections or create a simple markdown file with `## Now` / `## Next` / `## Ship Prep` headings.
- Optional: project tracker CLI or API (e.g. Jira, Linear) for ticket digests
- Optional: CRM CLI (e.g. Salesforce CLI) for account lookups on discovery items

## Task System

Tasks live in `active.md` with inline Dataview fields.

**Format:**
```
- [ ] Task title [due:: 2026-03-14] [project:: Web] [tags:: Slack] [added:: 2026-03-14] [waiting:: Alex]
  Context notes, indented with 2 spaces. Slack-sourced tasks MUST include a permalink here.
  e.g. From Dana in #product-questions: https://yourco.slack.com/archives/C051TG5E1CG/p1710900000000000
```

**Sections in active.md:** Today, This Week, then by project area. Configure project sections for your team's ownership areas (see Team Structure in CLAUDE.md).

**Sub-sections within each project (### level):** Each project section uses action-based sub-sections. Only include sub-sections that have tasks -- omit empty ones.

- `### Respond` -- quick replies, yes/no/maybe answers, short clarifications. Minutes not hours.
- `### Review` -- watch videos, read documents, give feedback. Consumption-mode. Items with `[tags:: Watch]` go here.
- `### Discuss` -- topics to raise with someone (`[with:: Name]`). Not blocked -- conversation items for 1:1s or syncs.
- `### Waiting` -- genuinely blocked on someone (`[waiting:: Name]`). Can't act until they deliver something.
- `### Do` -- active work: build, investigate, write, create, coordinate.
- `### Someday` -- aspirational, ideas, "check this out". No urgency, no commitment.

**Classification guidance for new tasks:**
- Slack thread needing a quick reply -> Respond
- Slack thread that kicks off real work (spec, POC, investigation) -> Do
- Slack thread linking a video/doc to review -> Review
- `[tags:: Watch]` -> Review
- `[with:: Name]` -> Discuss
- `[waiting:: Name]` -> Waiting
- "Check out X", "Explore Y", ideas -> Someday
- When ambiguous, prefer Do over Someday

**Within each sub-section:** chronological order (oldest first).

**Fields:**
- `[project:: X]` -- required. Match your team ownership areas.
- `[due:: YYYY-MM-DD]` -- deadline
- `[added:: YYYY-MM-DD]` -- date the task was added to active.md. Required on all new tasks added by triage. Enables staleness detection -- tasks with no due date and an `[added::]` older than ~2 weeks are candidates for removal.
- `[tags:: X]` -- origin/type marker: Slack, Email, Ticket, Watch, etc.
- `[with:: Name]` -- discussion topic for a specific person
- `[waiting:: Name]` -- genuinely blocked on someone

**Adding a task:** Place under the correct project section AND the correct sub-section based on action type. Always include `[added:: YYYY-MM-DD]` with today's date. If it's due today or this week, also place it in Today/This Week. Create the sub-section heading if it doesn't exist yet (respect the order: Respond -> Review -> Waiting -> Do -> Someday).

**Slack permalink rule (HARD REQUIREMENT):** Every task with `[tags:: Slack]` MUST have a permalink URL in its context line. No exceptions. If you have a channel name but no permalink -- search Slack to find the message and build the link before writing the task. A channel name alone (e.g. "#product-questions") is NOT sufficient. Format: `https://yourco.slack.com/archives/{channel_id}/p{ts_without_dot}`. If after searching you genuinely cannot find the message, hold the item instead of writing the task without a link.

**Completing a task:** Remove the line from `active.md` and append it to `done.md` with `- [x]` and a `[done:: YYYY-MM-DD]` field. Most recent first, under a date heading.

**done.md format:**
```
## 2026-03-14

- [x] Task title [project:: Web] [done:: 2026-03-14]
```

## Triage: Interactive Approval

All triage is interactive -- items are presented for the PM's approval before execution.

### LNO Classification
Per the LNO Effectiveness framework:
- **L** -- Leverage: multiplies effectiveness 10x
- **N** -- Neutral: important but incremental
- **O** -- Overhead: necessary but low-impact

### Guardrails

Apply these before proposing any task:

1. **Task attribution** -- verify who actually owns the action. Meeting notes and all-hands often mention people in context but the action belongs to someone else. If the owner isn't clearly the PM running the review, don't propose it.

2. **Calendar items don't need tasks** -- if something is already on the calendar (demos, meetings, events), don't duplicate it as an active.md task.

3. **Saved Slack items are deliberate** -- every saved item gets a task on active.md. Never dismiss a saved item as informational or FYI-only. If the PM saved it, they want to come back to it. Use Review for reads/watches, Respond for replies, Do for actions.

4. **Project routing** -- route tasks to the correct team/product area based on the PM's ownership. When ambiguous, use a general catch-all label.

### Proposed actions
- **T(Project/SubSection)** -- add to `active.md` under the correct project section AND sub-section, with context as indented notes (include Slack permalink). Example: `T(Web/Respond)` = quick Slack reply, `T(Web/Do)` = real work item.
- **I** -- ignore, no action needed
- **P** -- save context to person file (a per-person markdown file for tracking context about colleagues and stakeholders)

### Interaction model

Present all items in a numbered list with proposed action and LNO classification. The PM approves, modifies, or rejects each. Cheat sheet:

```
Approve all = approve all · 4 5 = approve those · 3->I = change to ignore
T(Project) = active.md · I = Ignore · P = People file
Dates: tomorrow = due date · >fri = deadline
LNO override: 4->T(Web) L
e.g., approve all  or  4 5  3->I  4->T(Backend) >fri
```

After the PM responds, execute the approved actions (add to active.md, update person files, etc.).

## Process

### Step 1: Staleness Check

Read `active.md` and check the "Last updated" timestamp.

- If >24h old, warn: "active.md is stale." Skip the reshuffle (Step 6). Only append new tasks; don't reorganise sections.
- If fresh, proceed normally.

### Step 2: Close Yesterday

Determine yesterday's date (Friday -> use Friday's date on Monday morning).

1. Read yesterday's daily note
2. If the EOD section is already populated, skip this step
3. If empty, populate it:

**Done today** -- compile from:
- `done.md` -- tasks with `[done:: YESTERDAY]`
- Meetings attended -- from yesterday's calendar section
- Notes in yesterday's daily note

**Blocked** -- infer from yesterday's notes and open questions.

**Priority for today** -- will be generated in Step 7 (today's morning brief).

**Exclude from Done today:** "Daily Review", triage counts, snapshot exports.

**Ticket links** -- render as `[TICKET-KEY](https://your-instance.atlassian.net/browse/TICKET-KEY)`. Configure your project tracker URL in CLAUDE.md.

### Step 3: Triage Slack Saved Items

1. Search for saved Slack messages (`is:saved`, sorted by most recent)
2. If no results, note "Slack Later is clear" and move on
3. Apply triage logic -- propose actions for each item and present for approval. Apply guardrails before proposing any task.

### Step 4: Slack Channel Scan (subagent)

**Delegate to a subagent** to keep raw Slack data out of the main context window.

Before launching, compute `UNIX_TS_START`: run `date -u -v-1d -j -f "%Y-%m-%d %H:%M:%S" "$(date -u -v-1d +%Y-%m-%d) 07:00:00" +%s` and substitute the result. This gives a full 24-hour scan window.

Pass the subagent these instructions (replace YYYY-MM-DD, UNIX_TS_START, and configure your channels):

> Today's date is YYYY-MM-DD. Scan these Slack channels for threads needing the PM's attention. Read all channels in parallel:
>
> <!-- CONFIGURE: Add your channels here. Example format: -->
> 1. `slack_read_channel(channel_id="YOUR_CHANNEL_ID", oldest="UNIX_TS_START", limit=30, response_format="detailed")` -- #your-team-questions
> 2. `slack_read_channel(channel_id="YOUR_CHANNEL_ID", oldest="UNIX_TS_START", limit=30, response_format="detailed")` -- #support-channel
> 3. `slack_read_channel(channel_id="YOUR_CHANNEL_ID", oldest="UNIX_TS_START", limit=15, response_format="detailed")` -- #product-questions
>
> **Hard date filter**: The `oldest` parameter limits results server-side, but as a safety net, also **skip any message where the Slack timestamp (the `ts` field, e.g. `1774000000.123456`) is less than UNIX_TS_START**. Slack timestamps are Unix epoch seconds before the dot. Messages older than UNIX_TS_START MUST be excluded.
>
> For any message with replies, read the thread with `slack_read_thread`.
>
> <!-- CONFIGURE: For each channel, describe what's relevant to your product area. Example: -->
> **#your-team-questions** (your product -- all threads relevant):
> - Flag threads with no human replies -- support bots auto-reply to every question, so bot-only replies = unanswered
> - Flag threads where a human replied but the question is still open
> - Note patterns (same question repeated = documentation gap or product issue)
>
> **#support-channel** (cross-product -- filter to your products):
> - Only flag threads about your product areas
> - Same bot logic: bot replies don't count as answered
> - Skip threads about products owned by other PMs unless you're mentioned
>
> **#product-questions** (cross-product, lower volume):
> - Same filtering -- only your product area threads
> - Skip threads already answered by another PM or engineer
>
> **Bot detection:** Bot messages come from app/bot users (check for bot indicators in detailed response).
>
> **Deduplication:** Skip any threads with these timestamps (already triaged as saved items): [INSERT TIMESTAMPS FROM STEP 3]
>
> **Slack permalinks are required.** For every flagged thread, include the Slack permalink URL. Build it from the channel ID and message timestamp: `https://yourco.slack.com/archives/{channel_id}/p{ts_without_dot}`. Every item in the return must have a clickable link.
>
> **Return ONLY this summary** -- do not include raw message content:
> ```
> ### #channel-name (X threads yesterday)
> - **Unanswered**: [question summary] -- from [person] ([slack permalink])
> - **Needs PM input**: [question summary] -- [person] asked about [topic] ([slack permalink])
> ```
>
> **Discovery lens** (apply after the attention scan):
>
> Re-scan all threads for product signals: feature requests, pain points, workflow frustrations, or integration asks. A thread can be "answered" from a support perspective but still contain a discovery signal. Skip pure support/troubleshooting with no product signal.
>
> For each signal found:
> 1. Extract the request in one sentence
> 2. Note who asked (name + company if visible from Slack profile or context)
> 3. Search your feedback tool for existing requests matching this signal
> 4. Classify:
>    - **Match** -- existing idea covers this. Return idea title, URL, and request count.
>    - **Partial** -- related idea exists but this is a distinct angle. Return closest idea.
>    - **New** -- nothing matches.
>
> Append a `### Discovery` section. **Every signal must include its Slack permalink.**
> ```
> ### Discovery
> - **[Match]** "customer wants X" -- from [person] in #channel ([slack permalink])
>   -> Matches: [Idea title] ([URL]) -- N requests
> - **[New]** "customer wants Y" -- from [person] in #channel ([slack permalink])
>   -> No match in feedback tool
> ```
> If no product signals found, return "### Discovery\nNo product signals today."

**After receiving the subagent summary**, triage attention items and discovery items:

For **attention items** -- present with proposed action:
- **Draft a reply** -- create via Slack message draft with thread context
- **Capture to active.md** -- add task under correct project section. When adding to Respond, tag the type in the context note: `[PM decision]` (product call needed), `[bug -- should ticket]` (has evidence, should create ticket), or `[support -- redirect]` (how-does-this-work, can self-serve).
- **Ignore** -- already aware or not actionable

For **discovery items**:
- **C** -- PM sends to feedback tool manually
- **C+** -- create a new idea in the feedback tool via API
- **B** -- book a call: CRM lookup, add task to active.md with account manager info
- **I** -- ignore

Apply guardrails before adding any task.

> **Note:** Full feedback review is a separate weekly skill. This step does lightweight lookups only when a thread contains a product signal.

### Step 5: Meeting Follow-Up Scan (subagent)

**Delegate to a subagent.**

Read yesterday's daily note calendar entries. Extract meetings that happened.

The subagent should:

1. **Find meeting notes** for yesterday's meetings (e.g. from Granola, Otter, or a shared notes folder)
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

**After receiving the subagent summary**, present follow-ups for approval:
- **Capture to active.md** -- add task with meeting context as indented notes
- **Note for daily note** -- add to yesterday's Done today or Blocked
- **Draft a Slack message** -- create a follow-up message draft
- **Ignore** -- already handled

### Step 6: Reshuffle active.md

Update the "Last updated" timestamp.

1. **Move completed tasks** -- any `- [x]` items in active.md -> move to done.md with `[done:: TODAY]`
2. **Promote due items** -- tasks with `[due:: TODAY]` in project sections -> copy to Today section
3. **Demote overdue** -- tasks in Today section with `[due:: PAST_DATE]` that are still open -> leave in Today but they'll be visible
4. **Skip if stale** -- if Step 1 flagged staleness, only append new tasks from Steps 3-5, don't reorganise

### Step 7: Generate Today's Daily Note

Read these inputs:

1. **Calendar** -- fetch today's events using your calendar script or MCP. If no automated access, ask the PM to paste their calendar. On Mondays, also fetch the full week.
2. **Goals** -- quarterly goals and ownership areas (GOALS.md or equivalent)
3. **Current context** -- current priorities, risks, blockers
4. **Initiatives Kanban** -- read your Kanban file/board. The Kanban Snapshot section shows what's in Now and Ship Prep. The Morning Brief cross-references active.md against the Now column to flag misalignment (effort going somewhere not on the Kanban, or a Now item getting no attention).
5. **active.md** -- current task list (just reshuffled)
6. **Yesterday's daily note** -- for context continuity
7. **Person files** -- context files for anyone in today's meetings
8. **Meeting notes** -- scan recent meeting notes (last 2 working days) for context
9. **Ticket digest** -- recent ticket activity if available (shipped, needs PM attention, in review)

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
Cross-reference active.md priorities against the Kanban Now column -- if effort is going
somewhere that isn't on the Kanban (or vice versa), call it out.]

## Priority Tasks
[Top 5, ranked by which quarterly goals they support. Must account for triage -- if a hot
item came in overnight, it may displace a lower-priority task.]
Every task sourced from Slack MUST include a clickable Slack permalink to the thread/message.

## Unmapped Tasks
[Tasks not mapped to goals -- flag for conscious decision]

## Ticket Activity (24h)
[From digest: needs PM attention, shipped/resolved, in review]

**Shipped/resolved tickets MUST include clickable links and summaries**, not bare ticket numbers.
Format: `- [TICKET-KEY](https://your-instance.atlassian.net/browse/TICKET-KEY) -- summary text`

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

Collect the raw inputs into a scratch file:
- Yesterday's done.md entries (tasks with `[done:: YESTERDAY]`)
- Channel scan summary from Step 4
- Meeting follow-ups from Step 5
- Yesterday's EOD section (written in Step 2)
- Today's morning brief (written in Step 7)

Pass the subagent these instructions:

> Read the verification file. This contains raw source data and proposed daily note content. Verify the following:
>
> 1. **Done completeness** -- Count done.md entries for yesterday. Count "Done today" entries in yesterday's EOD section. Every done.md entry should appear in the EOD section (except: "Daily Review", Slack triage -- these are excluded by convention). Flag any missing items by name.
> 2. **Date accuracy** -- Verify the day-of-week label matches the actual date. Check "Priority for tomorrow" vs "Priority for Monday" (Friday -> Monday).
> 3. **Product context** -- For any item attributed to a product area, verify it matches the PM's ownership (see Team Structure in CLAUDE.md). Flag misattributions.
> 4. **Deduplication** -- Flag any item appearing in both channel scan and Done today without explanation.
> 5. **Omissions** -- Flag any meeting follow-up actions from Step 5 that don't appear in Done today, Priority for tomorrow, or as a task in active.md.
> 6. **Morning brief coherence** -- Check that today's priority tasks reference items actually in active.md. Flag any phantom tasks.
> 7. **Slack permalink completeness** -- Check every item in the Triage Outcome and every new task in active.md with `[tags:: Slack]`. Each MUST have a full Slack permalink URL, not just a bare channel name. Flag any item missing a permalink.
>
> **Return ONLY:**
> ```
> PASS -- no issues found
> ```
> or
> ```
> ISSUES:
> - [issue description]
> - [issue description]
> ```

Fix any issues before proceeding.

### Step 9: Summary

Print a summary after all steps complete:
- Items triaged (Slack saved + channels + meetings)
- Tasks added to active.md
- Tasks moved to done.md
- Daily note status (yesterday closed, today generated)

## Monday Variant

On Monday mornings:
- Yesterday = Friday (close Friday's daily note)
- Slack scan covers Friday's activity -- filter for threads that may have been resolved over the weekend
- Include "This Week's Calendar" with full work week
- Include "Meeting Prep Needed" section for non-recurring meetings with guests
- Flag the 5 oldest tasks per section in active.md for staleness review

## Notes

- Slack MCP cannot unsave items -- the PM unsaves manually.
- Feedback tool discovery: lightweight lookups in Step 4. Full review via a separate weekly skill.
- On Fridays, "Priority for tomorrow" becomes "Priority for Monday".

## Architecture Notes

**Why parallel subagents:** Steps 4, 5 are independent data-gathering operations. Running them in parallel cuts review time compared to sequential execution. The main thread stays clean because raw Slack messages and meeting transcripts never enter the primary context window.

**Why interactive triage:** Presenting items as a numbered list with shortcodes and accepting `4 5 3->I` in a single line minimises back-and-forth. The PM processes 15-20 items in one response instead of triaging each individually.

**Why cold-read verification:** The verifier subagent receives raw source data and the proposed output with no conversation context. It catches errors the main thread is blind to -- missing items, date mistakes, misattributions -- because it has no memory of the drafting process.

**Why a discovery lens:** The channel scan serves two purposes: (1) find threads needing a reply, and (2) mine for product signals. A thread can be fully answered from a support perspective but still contain a feature request or pain point worth capturing in your feedback tool.

**Why staleness checks:** If the task list hasn't been updated in >24h, reorganising sections risks moving stale data around. Better to append only and let the PM do a manual cleanup.

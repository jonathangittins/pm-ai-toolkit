---
name: eod-review
description: >
  End-of-day review skill. Triages Slack saved items and task inbox,
  scans channels for unanswered questions, extracts meeting follow-ups,
  and exports a task snapshot for the next morning.
---

# EOD Review

End-of-day triage, channel scan, and task export.

## Prerequisites

- Task manager MCP configured (e.g. Things 3, Todoist, Linear)
- Slack MCP configured
- A daily notes system (e.g. Obsidian, markdown files)

## Process

### Step 1: Triage Slack Saved Items

Search for saved Slack messages using `is:saved` via search.

1. Search for saved items, sorted by most recent
2. If results found, present them as a numbered list with channel, sender, preview text, and date
3. If no saved items, note that and move on

**Shortcode triage:** Present all items at once. User responds with shortcodes in a single line:

- **T(Project)** – capture to task manager with context in the notes field (include the Slack permalink)
- **I** – ignore, no action needed
- **P** – save context to the relevant person file (a per-person markdown file where you track context about colleagues, stakeholders, and contacts – e.g. `people/jane-smith.md`)

**Include a cheat sheet when presenting items:**

```
T(Project) = Task · I = Ignore · P = People file
Dates: tomorrow = start date · >fri = deadline · both: tomorrow >fri
e.g., 1 T(Web) tomorrow >fri  2 I  3 P  4 T(Backend)
```

Parse the entire response and execute all actions in one pass.

### Step 2: Triage Task Inbox

1. Read inbox from task manager
2. If empty, note that and move on
3. If items exist, present them as a numbered list

**Same shortcode triage:**

- **T(Project)** – move to project
- **I** – ignore, leave in inbox
- **P** – save context to a person file
- **D** – mark done (already handled)

User may also append scheduling, deadlines, or tags inline.

```
T(Project) = Task · I = Ignore · P = People file · D = Done
Dates: tomorrow = start date · >fri = deadline · both: tomorrow >fri
e.g., 1 T(Web) tomorrow >fri  2 I  3 D  4 T(Backend) #Ticket
```

Parse the entire response and execute all actions in one pass.

### Steps 3, 4, 5: Parallel Data Gathering

**Launch Steps 3, 4, and 5 as parallel sub-agents.** These are independent – none needs the output of another. Present the results from Steps 3 and 4 for triage while Step 5 runs in the background.

### Step 3: Slack Channel Scan (subagent)

**Delegate this to a subagent** to keep raw Slack data out of the main context window.

The subagent should:

1. **Read configured channels** – read recent messages from the PM's key channels. Configure these for your context – typically a support/questions channel for your product area, a cross-team product questions channel, and any team-specific channels you own.
2. **Filter to today's messages** – skip anything from previous days
3. **Identify threads needing attention:**
   - Threads with no human replies (bot auto-replies don't count as answered)
   - Threads where a human replied but the question is still open
   - Pattern detection (same question repeated = documentation gap)
4. **Filter by product ownership** – only flag threads relevant to the PM's products
5. **Deduplicate** – skip threads already triaged as saved items in Step 1

**Return a structured summary** – not raw message content:
```
### #channel-name (X threads today)
- **Unanswered**: [question summary] – from [person] at [time]
- **Needs PM input**: [question summary] – [person] asked about [topic]
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
- **[Match]** "customer wants X" – from [person] in #channel
  → Matches: [Existing request title] (URL) – N requests, $ARR
- **[New]** "customer wants Y" – from [person] in #channel
  → No match in feedback tool
```

**After receiving the subagent summary**, present attention items and discovery items separately with triage options.

### Step 4: Meeting Follow-Up Scan (subagent)

**Delegate this to a subagent** to keep meeting note content out of the main context window.

First, read today's calendar from the daily note to get the list of meetings that have already happened.

The subagent should:

1. **Find meeting notes** for today's meetings
2. **Read summaries only** – not full transcripts
3. **Extract:**
   - Decisions made
   - Action items assigned to the PM
   - Open questions needing further discussion
   - Commitments made to others

**Return a structured summary:**
```
### [Meeting Name] (HH:MM)
- **Action**: [extracted action] – [context]
- **Decision**: [what was decided]
- **Open**: [unresolved question]

### Meetings without notes:
- [Meeting name] (HH:MM)
```

**After receiving the summary**, present it with triage options:
- Capture to task manager
- Add to daily note
- Draft a Slack follow-up message
- Ignore

### Step 5: Export Task Snapshot (subagent)

**Delegate this to a subagent** to keep task data out of the main context window.

The subagent reads all tasks, formats them, writes the snapshot file, and returns only a count summary.

**Snapshot structure:**
```markdown
# Task Snapshot

Exported: YYYY-MM-DD HH:MM

## Today
- [ ] Task name  (Project · Tags · due YYYY-MM-DD)

## Upcoming
- [ ] Task name  (Project · due YYYY-MM-DD)

## Active Work
### Project A
- [ ] Task in Project A

### Project B
- [ ] Task in Project B
```

**Snapshot rules:**
- Only include work-related content – skip personal areas
- Truncate task notes to 300 characters max
- Include checklist items with completion status
- Include tags and deadlines in parenthetical metadata
- Skip empty projects

The subagent returns only: `Snapshot written. Today: X tasks · Upcoming: X tasks · Active Work: X tasks`

### Step 5b: Verification (subagent)

**Delegate verification to a subagent** so it checks the output cold – without the conversation context.

Collect raw inputs into a scratch file:
- Completed tasks from today
- Channel scan summary from Step 3
- Meeting follow-ups from Step 4
- The drafted daily note sections

The verifier checks:
1. **Completeness** – every completed task appears in "Done today"
2. **Date accuracy** – day-of-week labels match actual dates; Friday → "Priority for Monday"
3. **Product ownership** – items attributed to the correct product areas
4. **Deduplication** – no item appears in both channel scan and Done today without explanation
5. **Omissions** – meeting follow-up actions appear somewhere (Done today, Priority for tomorrow, or as a captured task)

Returns `PASS` or a list of issues. Fix any issues before presenting.

### Step 6: Update Daily Note

Update the daily note with:

- **Done today** – compiled from completed tasks, meetings attended, notes written, Slack threads responded to. Exclude routine meta-tasks (daily review, triage, snapshot export).
- **Blocked** – items waiting on someone else or an external dependency
- **Priority for tomorrow** – based on tomorrow's calendar and open items. Use "Priority for Monday" on Fridays.

Present the drafted sections for review before writing.

### Step 7: Confirm

After writing:
1. Print a summary: items triaged, threads flagged, total tasks exported
2. Note remaining inbox items if any

## Architecture Notes

**Why parallel sub-agents:** Steps 3, 4, and 5 are independent data-gathering operations. Running them in parallel cuts the EOD review time roughly in half compared to sequential execution. The main thread stays clean because raw Slack messages, meeting transcripts, and task lists never enter the primary context window.

**Why shortcode triage:** Presenting items as a numbered list and accepting `1 T(Web) 2 I 3 D` in a single line minimises back-and-forth. The PM processes 15-20 items in one response instead of triaging each individually.

**Why cold-read verification:** The verifier sub-agent receives raw source data and the proposed output with no conversation context. It catches errors the main thread is blind to – missing items, date mistakes, misattributions – because it has no memory of the drafting process.

**Why a discovery lens:** The channel scan serves two purposes: (1) find threads needing a reply, and (2) mine for product signals. A thread can be fully answered from a support perspective but still contain a feature request or pain point worth capturing in your feedback tool.

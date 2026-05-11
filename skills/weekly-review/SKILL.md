---
name: weekly-review
description: Weekly review checkpoint. Runs Monday morning between the daily morning review and the monthly review. Owns backlog hygiene, roadmap delta vs last week, signal carryover from the daily review, stale-tasks-per-project, a long-context blind-spots prompt, and by-team support-ticket clusters. Proposal-only — never edits live task files. Trigger manually (typically Monday or Tuesday after a holiday).
---

# Weekly Review

The missing layer between a daily morning review and a monthly review. Catches things that fall through the daily lens but don't need to wait a month.

## Cadence

- Monday morning, or Tuesday after a public holiday.
- Manual trigger only – no cron, no auto-write.
- Output is a single file at `weekly-reviews/YYYY-MM-DD.md` (path is configurable; adapt to your repo), scannable in 10 minutes.

## Constraints (apply to every step)

- **Proposal-only.** Never edit your live task list, roadmap file, or project files. You apply changes manually.
- **Reference, don't duplicate.** If the daily review already surfaced a signal cluster this week, reference it and add new context. Don't re-list.
- **One bullet per item, no sub-details.** Standup pattern: done / open / blocked.
- **Obsidian callouts** for visual hierarchy if your output renders in Obsidian: `[!warning]` for stale, `[!info]` for proposals, `[!danger]` for blockers. Plain markdown also works.
- **Dates not week numbers.** Filenames and references use `YYYY-MM-DD`, never `YYYY-Www`.

## Process

### Step 1: Setup and staleness checks

1. Determine `TODAY = YYYY-MM-DD`.
2. Determine `LAST_WEEK = TODAY - 7 days` (cutoff for "last 7 days" windows).
3. Confirm output path: `weekly-reviews/TODAY.md` (or wherever your weekly outputs live).
4. If you maintain a support-ticket index (Step 6), check its freshness. If the index is missing or older than 7 days, hold a `[!warning]` callout for Step 6 and skip the cluster work. Don't auto-refresh – that's the index pipeline's job.
5. If you keep a long-context blind-spots history (Step 5), check the most recent dated file and hold its path for Step 5's carryover detection.

### Step 2: Backlog hygiene proposal

Read your active task list and surface candidates to action manually. **Pure-text proposal – do not edit the task file.**

Identify four categories:

1. **Stale candidates** – tasks added more than 14 days ago with no recent activity touching the same path or topic. Cross-reference recent daily-note mentions to confirm absence of movement.
2. **Likely duplicates** – task lines that semantically overlap (same person, same topic, same project section).
3. **Likely-done** – tasks that appear closed by other evidence: matching done-log entry, recent commit/PR, resolved Slack thread. Surface for you to tick off.
4. **Cluster suggestions** – repeating task shapes within one project section (e.g. "5 watch-loom tasks – consider blocking 30 min on these together").

**Output format** – one callout per category. Skip a category if it's empty.

```markdown
> [!info] Stale candidates (3)
> - Task line — last touched YYYY-MM-DD — suggested: drop / reschedule / archive
> - ...
```

Bullet structure: `task name — evidence — suggested action`. One line. No sub-bullets.

### Step 3: Roadmap delta (vs last week)

If you keep a roadmap file under version control, compare it now versus 7 days ago.

1. Find the commit closest to but at-or-before `LAST_WEEK 23:59`:
   ```bash
   git log --before="LAST_WEEK 23:59" -n 1 --format=%H -- path/to/roadmap.md
   ```
   If empty (first run, or roadmap not committed last week), note "no prior snapshot – full roadmap shown" and skip the delta.
2. Read the prior version:
   ```bash
   git show <commit>:path/to/roadmap.md
   ```
3. Compare card-by-card across columns or sections.

**Output**: three sub-sections under `## Roadmap Delta`:

- **Moved between columns** – `Card name: column-A → column-B` (one bullet per card).
- **New cards** – added since last snapshot.
- **Stuck** – same column, no signal in daily notes / scrapbooks / Slack mentions in the last 7 days. Stuck = no movement AND no signal.

If the roadmap is unchanged, write "No changes since LAST_WEEK." and move on.

### Step 4: Signal carryover

Pull the last 7 days of customer-signal callouts from your daily review outputs.

Surface clusters that **recurred across 3+ days but didn't get acted on** (no related task, no feedback-tool lookup logged, no scrapbook entry).

For each repeating cluster:

```markdown
> [!info] Recurring signal — N days
> Cluster name. Why it didn't get caught (no task tagged, no feedback-tool match, no scrapbook).
> Days: 2026-05-05, 2026-05-06, 2026-05-08
```

If no clusters recurred 3+ times, write "No recurring clusters this week."

### Step 5: Long-context blind-spots run

Run a long-context prompt against a recent slice of your work (e.g. last 30 days of daily notes, recent specs, signal clusters) asking: "What patterns are emerging that aren't being acted on? What gaps am I blind to?"

Save the full output to `blind-spots/TODAY.md` (or wherever you keep these).

Summarise into the weekly review file under `## Strategic Blind Spots`:

- Headline of each item (one line).
- Status tag (`fresh` / `repeat-with-update` / `repeat-no-movement`) – compare against the previous blind-spots file held in Step 1.
- Confidence (`high` / `medium` / `low`).
- Wikilink or path to the full output.

If the prompt returns "fewer than 3 blind spots – system is working", write that verbatim under the heading.

### Step 6: By-team support-ticket clusters

**Daily ticket volume is too noisy for daily summaries – a weekly cadence smooths the signal.**

If Step 1 flagged the support-ticket index as stale (>7 days old), insert this callout at the top of the section and skip the cluster work:

```markdown
> [!warning] Support-ticket index is N days old
> Refresh the index pipeline before relying on these clusters.
```

If the index is fresh, proceed:

1. Read your classified-tickets file (one ticket per line with fields: `id`, `created_at`, `subject`, `product`, `assets`, plus team-derived fields).
2. Read your ownership map to derive teams from `(product, [assets])`.
3. Filter to tickets created in the last 7 days (`created_at >= LAST_WEEK`).
4. **Count tickets per team** and per cluster within team. A "cluster" is a recurring subject pattern or asset+product combination. Use the `assets` field as the primary clustering key, fall back to subject keywords if assets are sparse.
5. **Surface the top 3–5 clusters per team you own**. Other teams appear in a separate "For awareness" block – top 1–2 clusters each, no excerpts.
6. **Week-over-week delta** – compare counts to the same calculation run last week. Source: parse the prior `weekly-reviews/*.md` file's clusters section, OR re-run the count over the prior 7-day window if the prior file is missing. Flag clusters where this-week count >= 1.5x last-week count, or that are net-new this week.
7. **Per cluster**: top 1–2 ticket excerpts with dates and ticket IDs. Excerpt = first 100 chars of subject or first comment.

**Output structure**:

```markdown
## Support-ticket Clusters (last 7 days)

### Team A (you own)
- **Cluster name** — N tickets (↑ from M last week). Examples:
  - 2026-05-07 #85742 — "subject excerpt..."
  - 2026-05-08 #85789 — "subject excerpt..."
- ...

### For awareness
- **Team B**: top cluster name (N tickets)
- **Team C**: ...
```

If the classified-tickets file doesn't exist (pipeline not yet seeded), insert this placeholder and continue:

```markdown
## Support-ticket Clusters (last 7 days)

> [!info] Pipeline pending
> Support-ticket evidence pipeline not yet seeded.
```

### Step 7: Stale tasks per project

For each active project:

1. Confirm `status: active` from the project metadata (skip parked/archived).
2. Find tasks linked to the project via wikilink in your active task file.
3. Check each task's added-date and any movement (recent daily-note mentions, recent commits touching the project folder).
4. Surface tasks with no movement in 14+ days under one bullet per project.

**Output**:

```markdown
## Stale Tasks per Project

- **project-slug-A** — 2 tasks stale: "draft outline" (added 2026-04-12), "review PR" (added 2026-04-15)
- **project-slug-B** — 1 task stale: ...
```

Skip projects with no stale tasks. If everything is fresh, write "All active projects current."

### Step 8: Assemble the output file

Write `weekly-reviews/TODAY.md` with this structure:

```markdown
# Weekly Review — TODAY

> [!abstract] Summary
> One-paragraph headline: top 2-3 things to act on this week, hottest cluster, biggest gap.

## Backlog Hygiene
[Step 2 output]

## Roadmap Delta
[Step 3 output]

## Signal Carryover
[Step 4 output]

## Strategic Blind Spots
[Step 5 summary + path to full output]

## Support-ticket Clusters (last 7 days)
[Step 6 output]

## Stale Tasks per Project
[Step 7 output]

## Notes
[anything that didn't fit a section]
```

The summary callout at the top is written **last**, after the rest of the file is assembled, so it reflects the actual content.

### Step 9: Open output

If you use Obsidian:
```bash
open -g "obsidian://open?vault=YOUR-VAULT&file=weekly-reviews/TODAY"
```
Otherwise open the file in your editor of choice.

### Step 10: Print summary

Single short summary in the terminal (not in the file):

- Backlog hygiene candidates surfaced: N
- Roadmap delta items: N moved, N new, N stuck
- Recurring signal clusters: N
- Strategic blind-spots: N (status mix: fresh / repeat-with-update / repeat-no-movement counts)
- Support-ticket clusters: top team + top cluster
- Stale tasks per project: N projects affected, N tasks total

## Notes

- **First-run case**: if `git log --before` returns no commit (roadmap wasn't committed last week, or repo is too young), Step 3 degrades gracefully – "no prior snapshot – full roadmap shown" – and the rest of the skill runs normally. Next week's run gets a real delta.
- **Coupling with monthly review**: a monthly review does NOT invoke weekly. Monthly reads existing weekly-review files for the month and references them. If a week was skipped, monthly will notice the gap.
- **Retrospective**: after 4 weekly runs, audit whether the layer is earning its keep. If weekly keeps catching things morning-review misses, keep it. If redundant, fold the unique parts back into morning/monthly and retire.

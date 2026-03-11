# Claude Code for PMs

I've been using [Claude Code](https://docs.anthropic.com/en/docs/claude-code) as my primary PM tool for several months. Not as a chatbot I ask questions to, but as a co-pilot that sits in my terminal alongside my docs, notes, and integrations. This is a write-up of what that looks like in practice.

## Why Claude Code for PM work

Most PM tools automate project management – tracking tickets, updating roadmaps, managing sprints. That's overhead. The [LNO framework](../frameworks/lno-effectiveness.md) classifies PM tasks into Leverage (strategy, discovery, key decisions), Neutral (stakeholder communication, unblocking), and Overhead (coordination, reformatting, status tracking). The overhead category is where PMs lose hours every day, and it's exactly where automation should live.

Claude Code is different from other AI tools because it runs in your terminal, reads your local files, and connects to external systems through [MCP servers](https://modelcontextprotocol.io/). It doesn't need you to paste context into a chat window. It reads your spec template, searches your feedback tool, drafts the document, and writes it to a file – all in one session.

The result: overhead tasks take minutes instead of hours, and the time freed up goes back into leverage work – customer discovery, spec quality, strategic decisions.

## The setup

### A documentation repo as your working directory

Claude Code needs a working directory. Mine is a documentation repo organised by product area, planning activities, and reference material. It's not a codebase – it's markdown files, specs, meeting notes, and frameworks. Claude reads and writes files in this repo the same way it would read and write code.

The repo structure matters because it teaches Claude where things live. When I say "write a spec for Chat AI," it knows to create the file in the Chat product directory. When I say "check what customers are asking for," it knows where to find previous evidence and which feedback tool to search.

### CLAUDE.md: teaching Claude your context

The `CLAUDE.md` file at the root of your repo is loaded into every conversation. This is where you define:

- **Product context** – what your company builds, who your customers are, which products you own
- **Team structure** – PM ownership, engineering teams, stakeholders
- **Writing preferences** – tone, formatting rules, words to avoid
- **Tool conventions** – how to create Jira tickets, which project keys to use, how to format links
- **Workflow rules** – "never push without asking," "preserve empty sections in docs," "use en dashes not em dashes"

Think of CLAUDE.md as onboarding documentation for a new team member who starts every day fresh. The more specific you are, the less you repeat yourself across sessions. See [examples/claude-md-example.md](../examples/claude-md-example.md) for a starter template.

### MCP servers: connecting to your tools

MCP (Model Context Protocol) servers give Claude access to external systems. For PM work, the ones that matter are:

| MCP Server | What it enables |
|---|---|
| **Slack** | Read channels, search messages, draft announcements, scan for unanswered questions |
| **Feedback tool** (Canny, Productboard, etc.) | Search customer requests, pull quotes and revenue data, check existing feature requests |
| **Task manager** (Things 3, Todoist, Linear, etc.) | Triage inbox, move tasks between projects, export snapshots |
| **Wiki** (Confluence, Notion) | Publish specs and documentation directly from markdown |

You configure these in `.mcp.json` at the root of your project. Each server has its own authentication – API tokens, OAuth, or local app connections.

You don't need all of these to start. One MCP server that connects to a tool you use daily is enough to see the value.

### Skills: reusable workflows

Skills are markdown files that define multi-step workflows. When invoked, they load into Claude's context as instructions. A skill might say: "First search the feedback tool for customer evidence, then read the spec template, then draft the document, then test it with a sub-agent."

The skills in this repo cover:

- **[Spec / PRD](../skills/spec-prd/)** – Research evidence, draft a full spec, run competitive analysis, test with a fresh reader, publish to wiki
- **[GTM Plan](../skills/gtm-plan/)** – Walk through a GTM template section by section, pulling customer quotes and competitive data
- **[Release Notes](../skills/release-notes/)** – Turn a video walkthrough transcript into a formatted Slack announcement
- **[Support Article](../skills/support-article/)** – Generate help centre articles from transcripts and demos, with screenshot workflow and zip handoff
- **[EOD Review](../skills/eod-review/)** – Triage Slack, scan channels, extract meeting follow-ups, export a task snapshot

Skills are just markdown with instructions – no code required. You can write one in 30 minutes by describing the steps you already follow manually.

## Daily workflow

### Morning

I run the EOD review skill at the end of each day, and one of its outputs is a task snapshot – a markdown file listing today's tasks, upcoming deadlines, and active projects. In the morning I open it, scan for what's priority, and start.

### During the day

**Writing a spec:** I tell Claude the feature and dump whatever context I have – bullet points, a voice memo transcript, a Slack thread link. The [spec skill](../skills/spec-prd/) takes over: it searches the feedback tool for customer evidence, reads the spec template, drafts the full document, runs competitive research on 2-4 competitors, then spawns a sub-agent to test the draft cold. The sub-agent reads the spec with no conversation context and flags ambiguities, assumptions, and gaps. I review the findings, iterate, and publish to Confluence – all in one session.

**GTM planning:** The [GTM skill](../skills/gtm-plan/) walks through each PM-owned section of the GTM template. It pulls customer quotes from the feedback tool, proposes tier classification based on strategic importance and business impact, drafts value propositions, and fills in competitive analysis. Product Marketing sections stay blank – the skill knows those are PMM-owned and leaves them structurally intact for handoff.

**Quick tasks:** Looking up which accounts requested a feature. Creating a Jira ticket with the right project key and assignee. These are 30-second interactions that would otherwise take 5-10 minutes of context-switching between tools.

**Release announcements and support articles:** A feature launch usually needs both a Slack announcement and a help centre article. I record a video walkthrough, give Claude the transcript, and ask it to run both the [release notes](../skills/release-notes/) and [support article](../skills/support-article/) skills. It launches them as parallel sub-agents sharing the same source material – each follows its own skill independently and returns a draft. The release notes skill structures the announcement around what I emphasised in the walkthrough. The support article skill produces a self-contained folder – markdown plus a screenshot checklist – ready to zip and hand off to the support team. Running them together keeps the messaging consistent and cuts the work in half.

### Weekly feedback review

The EOD review's discovery lens catches product signals in real time – a customer mentions a pain point in a support channel, and it cross-references with the feedback tool on the spot. But the deeper pattern recognition – clusters of requests, momentum on existing ideas, emerging themes – needs a batch view.

The [weekly feedback review](../skills/weekly-feedback-review/) runs once a week (I use Wednesday mornings during deep work time). It scans the past 7 days of feedback tool data, groups ideas by product area, identifies clusters, and flags validation candidates. The output is a triage file – a markdown table with clickable links, proposed actions, and an empty "Actual" column to fill in during review.

This is a two-phase skill:
1. **Generate** – runs unattended (mine runs on a VPS cron at 7:30 AM on Wednesdays). Scans the feedback tool, writes the triage file, and adds a link to the daily note.
2. **Execute** – after reviewing the file, you invoke it again. It reads your triage decisions and executes: account lookups, task creation, validation assessments for promising ideas.

The validation candidate step is the most valuable part. For each idea worth testing, it generates: the core assumption, existing workarounds, the cheapest test you can run this week, and the signal to watch for. The goal is "can I test this today?" not "let me plan a research programme."

### End of day

The [EOD review](../skills/eod-review/) is the most architecturally interesting skill. It runs in seven steps:

1. **Triage Slack saved items** – presents them as a numbered list with shortcodes: T(Project) to capture as a task, I to ignore, P to save context to a person file, D to mark done. I respond with something like `1 T(Web) 2 I 3 P 4 T(Distro)` and it processes all of them in one pass.
2. **Triage task inbox** – same shortcode pattern for task manager inbox items.
3. **Slack channel scan** (sub-agent) – reads configured channels, filters to today's messages, identifies unanswered threads and threads needing PM input.
4. **Meeting follow-up scan** (sub-agent) – reads meeting note summaries, extracts action items and decisions.
5. **Export task snapshot** (sub-agent) – reads all tasks, writes a formatted snapshot file for tomorrow morning.
6. **Verification** (sub-agent) – checks the daily note update for completeness, date accuracy, and deduplication.
7. **Update daily note** – writes "Done today," "Blocked," and "Priority for tomorrow" sections.

Steps 3, 4, and 5 run as parallel sub-agents. This matters for two reasons: it's faster, and it keeps raw Slack messages, meeting transcripts, and task lists out of the primary context window.

## Patterns worth stealing

### Sub-agent verification

After multiple rounds of editing, both you and Claude develop blind spots. A sub-agent that reads the document cold – with no conversation context – consistently catches assumptions that would confuse a first-time reader. The spec skill does this automatically in its QA phase, but the pattern works for any document worth reviewing.

### Shortcode triage

Presenting 15-20 items as a numbered list and accepting triage decisions in a single line eliminates the back-and-forth of triaging items one at a time. The skill includes a cheat sheet with each batch so you don't need to memorise the codes.

### Discovery lens

The EOD channel scan serves two purposes: finding threads that need a reply, and mining for product signals. A support question can be fully answered but still contain a feature request or workflow pain point. The discovery lens re-scans for these signals and cross-references them with the feedback tool – match, partial match, or new. This turns daily channel monitoring into continuous discovery.

### Asynchronous triage

Not everything needs to be triaged interactively. The weekly feedback review writes a markdown file with proposed actions and an empty "Actual" column – then stops. I review the file later in Obsidian, fill in overrides, and pick up Phase 2 when I'm ready. This works better than real-time triage for batched work: you can click through to the feedback tool, read the customer quotes, and make a considered decision rather than a snap judgement mid-conversation.

The pattern generalises: any skill that produces triage decisions can separate generation from execution. Write the proposals to a file, let the human review asynchronously, then read back the decisions and act on them.

### Frameworks in context

The frameworks in this repo aren't just reference documents – they're baked into the skills that use them. The spec skill reads the [inversion framework](../frameworks/inversion.md) during its QA phase and runs a pre-mortem on the feature. The GTM skill reads the [value proposition framework](../frameworks/value-proposition.md) and applies the Opposite Test when drafting value props. These happen automatically as part of the skill workflow – I don't need to remember to invoke them.

For ad-hoc work, I load frameworks manually. During a shaping session, I'll tell Claude to read the [decision speed framework](../frameworks/decision-speed.md) to classify whether a scope question is reversible or irreversible. The point is the same either way: the framework is in context while the work is happening, so it shapes the output rather than critiquing it after the fact.

## What doesn't work (yet)

**Context window on long sessions.** Claude automatically compacts conversations as they grow, and plan mode helps manage context on complex tasks. The bigger consideration is keeping large data sets – competitive research across 3-4 competitors, raw Slack exports, full task lists – out of the primary conversation. This is why the EOD review and spec skills delegate data-heavy steps to sub-agents: each sub-agent gets its own context window, does its work, and returns only a summary.

**MCP server reliability.** Not all MCP servers are created equally. Slack's integration has gaps – private channels may not work with `read_channel`, rate limits can slow down channel scans, and thread replies require separate API calls. Some servers require frequent re-authentication. CLIs are a reliable fallback – tools like the Atlassian CLI or Salesforce CLI can be called directly from Claude Code via Bash, and they tend to be more stable than their MCP equivalents for heavy use.

**The last mile.** Some actions still require manual steps. Slack doesn't preserve formatting when you paste plain text or markdown, so getting a rich-text announcement into Slack means generating an HTML file, opening it in a browser, and copy-pasting from there. Publishing to Confluence requires API authentication and format conversion. Each of these has a workaround, but the dream of "run one command and it's done" isn't fully realised yet.

**Not a replacement for customer conversations.** Claude can search your feedback tool, summarise Slack threads, and draft specs – but it can't sit in a customer call and read the room. It's a force multiplier on the work surrounding those conversations, not a substitute for them.

## Getting started

**Install Claude Code** if you haven't already – see the [official docs](https://docs.anthropic.com/en/docs/claude-code). I use Claude Opus 4.6.

**Minimum viable setup:** A repo with markdown files, a `CLAUDE.md` that describes your product context, and one MCP server connected to a tool you use daily. That's enough to start seeing value.

**First skill to try:** Take the [spec-prd skill](../skills/spec-prd/) and adapt it to your own spec template. Replace the section guidance with your team's conventions, point it at your feedback tool, and adjust the writing style rules. A spec skill pays for itself the first time you use it – the customer evidence research alone saves significant time.

**How to iterate:** Start with what's painful. If you spend 30 minutes every day triaging Slack, build an EOD review. If formatting release notes is tedious, build a release notes skill. If you keep copying the same spec template and filling it in manually, automate it.

PM overhead follows predictable patterns, and those patterns can be encoded as instructions that Claude follows every time. You're not automating product thinking – you're automating the scaffolding around it.

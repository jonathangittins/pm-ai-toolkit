# pm-ai-toolkit

I'm a Lead Product Manager at [Chili Piper](https://www.chilipiper.com), building AI-native PM workflows with [Claude Code](https://docs.anthropic.com/en/docs/claude-code). This repo contains the frameworks I apply to product decisions and the skills that automate spec writing, GTM planning, and release communication. It's a working system, not a template collection – everything here runs in production on real product work.

At Chili Piper I own Chat AI and Concierge (our website conversion products). I also work on Chili Piper's [MCP server](https://modelcontextprotocol.io/) for managing Chili Piper through any AI tool or LLM, and our AI-driven asset creation tools.

## What's here

### Frameworks

Thinking tools I apply to product decisions. Each one credits its original source. [Browse all 21 in `frameworks/`](frameworks/).

### Skills

Claude Code skills that automate PM workflows. These are sanitised versions of what I use daily – the originals connect to internal tools (Canny, Jira, Confluence, Slack) and contain company-specific templates and conventions.

| Skill | What it does |
|---|---|
| [Spec / PRD](skills/spec-prd/) | Researches customer evidence, drafts a full product spec using a structured template, runs competitive analysis, tests the draft with a fresh sub-agent for ambiguity, and publishes to a wiki. |
| [GTM Plan](skills/gtm-plan/) | Creates Go-To-Market plans through a guided section-by-section workflow. Pulls customer quotes from feedback tools, proposes tier classification, and hands off cleanly to product marketing. |
| [Release Notes](skills/release-notes/) | Generates Slack release announcements from video transcripts. Analyses the walkthrough, structures the announcement, and outputs formatted HTML for rich-text pasting. |
| [Support Article](skills/support-article/) | Generates help centre articles from video transcripts and demos. Includes a full style guide, screenshot workflow, and zip handoff for the support team. |
| [Morning Review](skills/morning-review/) | Morning startup: closes yesterday's daily note, triages Slack saved items and channel threads interactively, extracts meeting follow-ups, reshuffles the task list, and generates today's daily note with priorities mapped to goals. |
| [Weekly Feedback Review](skills/weekly-feedback-review/) | Weekly batch review of customer feedback. Scans the feedback tool for new ideas and momentum, surfaces clusters and trends, and generates validation assessments for promising ideas. Two-phase: generate a triage file, then execute after async review. |
| [Framing Doc](skills/framing-doc/) | Turns conversation transcripts into a framing document -- captures the problem worth solving, surveys options with an explore-exploit lens, and argues for the priority. Forked from Ryan Singer's shaping-skills. |
| [Kickoff Doc](skills/post-kickoff-doc/) | Turns a shaped project kickoff transcript into a builder-facing reference document. Organises by territory (areas of the system), not timeline. |
| [Review PR](skills/review-pr/) | PM-perspective GitHub PR review. Translates engineer questions into product scenarios, plays out user flows, and drafts a review comment with product direction -- no code feedback. |
| [Presentation](skills/presentation/) | Builds reveal.js slide decks with professional techniques: fragment reveals, auto-animate bar charts, timelines, quote slides, and more. One HTML file, no build step. |
| [Shaping](skills/shaping/) | Collaborative solution shaping – iterating on problem definition and solution options. From [rjs/shaping-skills](https://github.com/rjs/shaping-skills). |
| [Breadboarding](skills/breadboarding/) | Transforms workflow descriptions into affordance tables showing UI and code boundaries. From [rjs/shaping-skills](https://github.com/rjs/shaping-skills). |
| [Ingest](skills/ingest/) | Transcribe a YouTube video, podcast, HLS stream, or local audio/video file locally with mlx-whisper, then extract claims, frameworks, and action items into a markdown note. Nothing leaves the machine. |

### Examples

| Project | What it is |
|---|---|
| [Slack Context Reminder Bot](https://github.com/jonathangittins/slack-context-reminder-bot) | A production Slack bot that uses Claude Haiku 3.5 to check if channel messages include customer context (name, CRM link, or call recording) and sends a gentle reminder if not. Single file, Dockerised, runs on k8s. |

### Guides

Practical write-ups on how these pieces fit together.

| Guide | What it covers |
|---|---|
| [Claude Code for PMs](guides/claude-code-for-pms.md) | Setup, skills, MCP servers, daily workflow, and patterns for using Claude Code as a PM co-pilot. |
| [Atlassian CLI](guides/atlassian-cli.md) | Practical reference for `acli` (Jira) and REST API (Confluence). Covers ADF formatting, auth, common pitfalls, and when to use which tool. |
| [Semantic Search (QMD)](guides/qmd-semantic-search.md) | Local semantic search over PM docs and external knowledge bases like podcast transcripts. Multi-collection indexing, MCP integration, cross-referencing patterns. |
| [Terminal Notifications](guides/terminal-notifications.md) | Sound effects and tab title indicators for multi-session Claude Code. Hooks, TTY escape sequences, and the "show status while working" pattern. |
| [Ingest Video Knowledge](guides/ingest-video-knowledge.md) | Pattern behind the `/ingest` skill. Downloads audio from YouTube or HLS streams, transcribes locally on Apple Silicon, extracts structured knowledge into your vault. |
| [Auto-Ingest YouTube Channel](guides/auto-ingest-youtube-channel.md) | Auto-transcribe every new episode of a podcast or YouTube channel locally, building a searchable knowledge base of content you'd otherwise never get to. |

## How I use this

My daily workflow runs through Claude Code with MCP servers connecting to Slack, Canny (customer feedback), Jira, Confluence, and Things 3 (task management). The skills in this repo are the sanitised, portable layer – the patterns and methodology without the company-specific wiring.

A typical day:
- Morning: run the morning review to close yesterday, triage overnight Slack, scan channels for unanswered questions, and generate today's daily note with priorities
- During the day: write specs, GTM plans, release notes, and review PRs using the skills
- Weekly (Wednesday): review the feedback triage file, fill in decisions, and run validation assessments on promising ideas

The frameworks aren't just reference documents – they're loaded into Claude Code's context and applied during skill execution. The spec skill applies the inversion framework during its QA phase. The GTM skill applies the value proposition framework when drafting messaging. The decision speed framework shapes how I classify scope decisions during shaping.

## Setup

These skills are designed for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). To use them:

1. Copy a skill folder into your project's `.claude/skills/` directory
2. Adapt the templates, file paths, and tool references to your environment
3. The frameworks work standalone as reference documents – no setup needed

For skills that reference MCP servers (Slack, feedback tools, task managers), you'll need to configure your own MCP connections in `.mcp.json`.

Several skills use **subagents** – separate Claude instances spawned via the Agent tool to perform a task without the main conversation's context. This is used for verification (a fresh reader catches assumptions the author is blind to) and for keeping large data sets (Slack messages, task lists) out of the primary context window. If your setup doesn't support subagents, you can run these steps manually in a separate conversation.

## Related

- [rjs/shaping-skills](https://github.com/rjs/shaping-skills) – The shaping and breadboarding skills used here
- [Chili Piper](https://www.chilipiper.com) – Where this system runs daily

## License

[MIT](LICENSE)

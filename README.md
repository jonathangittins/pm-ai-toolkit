# pm-ai-toolkit

I'm a Senior Product Manager at [Chili Piper](https://www.chilipiper.com), building AI-native PM workflows with [Claude Code](https://docs.anthropic.com/en/docs/claude-code). This repo contains the frameworks I apply to product decisions and the skills that automate spec writing, GTM planning, and release communication. It's a working system, not a template collection – everything here runs in production on real product work.

At Chili Piper I own Chat AI and Concierge (our website conversion products). I also work on Chili Piper's [MCP server](https://modelcontextprotocol.io/) for managing Chili Piper through any AI tool or LLM, and our AI-driven asset creation tools.

## What's here

### Frameworks

Thinking tools I use for product decisions. Each one credits its original source.

| Framework | What it does |
|---|---|
| [Decision Speed](frameworks/decision-speed.md) | Classify decisions as reversible or irreversible, then match the process to the stakes. Based on Bezos's Type 1/Type 2 model. |
| [Inversion](frameworks/inversion.md) | Pre-mortem for features. Ask "how do we fail?" across spec, QA, and rollout phases. |
| [Value Proposition](frameworks/value-proposition.md) | The Opposite Test – if the opposite of your claim is absurd, the claim is meaningless. |
| [LNO Effectiveness](frameworks/lno-effectiveness.md) | Classify tasks as Leverage, Neutral, or Overhead. Spend effort accordingly. |
| [Communication](frameworks/communication.md) | Writing rules for PM communication. Prune words, hook the reader, use active voice. |
| [Bitter Lesson](frameworks/bitter-lesson.md) | Rich Sutton's essay on why general methods that scale computation beat hand-crafted approaches – and what that means for product strategy. |

### Skills

Claude Code skills that automate PM workflows. These are sanitised versions of what I use daily – the originals connect to internal tools (Canny, Jira, Confluence, Slack) and contain company-specific templates and conventions.

| Skill | What it does |
|---|---|
| [Spec / PRD](skills/spec-prd/) | Researches customer evidence, drafts a full product spec using a structured template, runs competitive analysis, tests the draft with a fresh sub-agent for ambiguity, and publishes to a wiki. |
| [GTM Plan](skills/gtm-plan/) | Creates Go-To-Market plans through a guided section-by-section workflow. Pulls customer quotes from feedback tools, proposes tier classification, and hands off cleanly to product marketing. |
| [Release Notes](skills/release-notes/) | Generates Slack release announcements from video transcripts. Analyses the walkthrough, structures the announcement, and outputs formatted HTML for rich-text pasting. |
| [Support Article](skills/support-article/) | Generates help centre articles from video transcripts and demos. Includes a full style guide, screenshot workflow, and zip handoff for the support team. |
| [EOD Review](skills/eod-review/) | End-of-day triage: scans Slack channels for unanswered questions, triages saved items and task inbox, extracts meeting follow-ups, and exports a task snapshot for the next morning. |
| [Shaping](skills/shaping/) | Collaborative solution shaping – iterating on problem definition and solution options. From [rjs/shaping-skills](https://github.com/rjs/shaping-skills). |
| [Breadboarding](skills/breadboarding/) | Transforms workflow descriptions into affordance tables showing UI and code boundaries. From [rjs/shaping-skills](https://github.com/rjs/shaping-skills). |

### Guides

Practical write-ups on how these pieces fit together.

| Guide | What it covers |
|---|---|
| [Claude Code for PMs](guides/claude-code-for-pms.md) | Setup, skills, MCP servers, daily workflow, and patterns for using Claude Code as a PM co-pilot. |
| [Semantic Search (QMD)](guides/qmd-semantic-search.md) | Local semantic search over PM docs and external knowledge bases like podcast transcripts. Multi-collection indexing, MCP integration, cross-referencing patterns. |
| [Terminal Notifications](guides/terminal-notifications.md) | Sound effects and tab title indicators for multi-session Claude Code. Hooks, TTY escape sequences, and the "show status while working" pattern. |

## How I use this

My daily workflow runs through Claude Code with MCP servers connecting to Slack, Canny (customer feedback), Jira, Confluence, and Things 3 (task management). The skills in this repo are the sanitised, portable layer – the patterns and methodology without the company-specific wiring.

A typical day:
- Morning: review the task snapshot exported by last night's EOD review
- During the day: write specs, GTM plans, and release notes using the skills
- End of day: run the EOD review to triage Slack, scan for customer signals, and prep for tomorrow

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

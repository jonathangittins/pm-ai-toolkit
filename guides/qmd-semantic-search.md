# Semantic Search for PM Documentation

## The problem

PM knowledge lives in markdown files – specs, meeting notes, frameworks, research. As the collection grows, finding the right document by filename or folder browsing breaks down. You need search that understands meaning, not just keywords.

## What QMD does

- Local semantic search engine for markdown documents
- Indexes files into named collections
- Three search modes: keyword (BM25, ~30ms), vector (semantic similarity, ~2s), deep (query expansion + reranking, ~10s)
- MCP server for Claude Code integration – Claude searches your docs automatically
- Runs locally – no data leaves your machine

## Setup

### Install

```bash
npm install -g @tobilu/qmd
```

### Index your PM docs

```bash
qmd collection add ~/your-pm-repo --name docs --mask "**/*.md"
qmd embed
```

First-time embedding takes a while depending on collection size. Subsequent runs only process changes.

### Connect to Claude Code

Add to your `.mcp.json`:

```json
{
  "mcpServers": {
    "qmd": {
      "command": "qmd",
      "args": ["mcp"]
    }
  }
}
```

Claude Code will now use QMD automatically when you ask questions about your documentation.

## Adding external knowledge bases

The real value comes from indexing multiple collections and cross-referencing them. A good candidate is any structured markdown corpus with high information density.

### Example: Lenny's Podcast Transcripts

[ChatPRD/lennys-podcast-transcripts](https://github.com/ChatPRD/lennys-podcast-transcripts) is a public archive of 269 episodes from Lenny Rachitsky's podcast – covering product management, growth, leadership, and strategy. Each transcript has YAML frontmatter (guest, title, date, YouTube URL) and the full conversation.

```bash
git clone https://github.com/ChatPRD/lennys-podcast-transcripts.git ~/lennys-podcast-transcripts
qmd collection add ~/lennys-podcast-transcripts/episodes --name lenny --mask "**/*.md"
qmd embed
```

Clone it outside your PM repo to avoid double-indexing.

Now you can cross-reference your own docs with practitioner wisdom:

> "What do Lenny's guests say about onboarding best practices?"

> "Search my docs for our onboarding spec, then search lenny for related perspectives"

### Other collections worth indexing

Any public markdown corpus works. Some ideas:

- Company engineering blogs (if available as markdown)
- Open-source project documentation
- Exported notes from tools like Notion or Obsidian
- Conference talk transcripts

## Search modes

| Mode | Speed | Best for |
|---|---|---|
| `search` (keyword) | ~30ms | Known terms, feature names, exact phrases |
| `vector_search` (semantic) | ~2s | Conceptual queries, "what do we know about X?" |
| `deep_search` (expanded) | ~10s | Broad research, finding everything on a topic |

All three can be scoped to a collection with the `collection` parameter.

## Practical patterns

### Spec research

Before writing a spec, search your docs for prior art and existing context, then search external collections for how others have approached the same problem.

### Stress-testing ideas

Search podcast transcripts or industry knowledge bases for perspectives that challenge your assumptions. "What would practitioners say about [your proposed approach]?" surfaces real-world experience.

### New PM onboarding

Point new team members at QMD and let them ask questions. Semantic search over a well-maintained doc repo is faster than reading a wiki from top to bottom.

### Cross-collection queries

The most useful pattern: search the same topic across multiple collections. Your internal docs give you company context; external collections give you industry context. Together they give you a more complete picture.

## Updating collections

```bash
# Pull latest from external repos
cd ~/lennys-podcast-transcripts && git pull

# Re-index all collections and regenerate embeddings for changes
qmd update
qmd embed
```

## Links

- [QMD](https://github.com/tobi/qmd) – the search engine
- [Lenny's Podcast Transcripts](https://github.com/ChatPRD/lennys-podcast-transcripts) – 269 episodes, maintained by the [ChatPRD](https://github.com/ChatPRD) team (Claire Vo)
- [Model Context Protocol](https://modelcontextprotocol.io/) – how QMD connects to Claude Code

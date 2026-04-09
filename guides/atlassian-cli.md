# Atlassian CLI Guide (Jira + Confluence)

A practical reference for managing Jira tickets and Confluence pages from the command line. Covers `acli` for Jira and `curl` (REST API) for Confluence.

## What is acli?

`acli` is the official Atlassian CLI. It handles authentication, ADF formatting, and most Jira operations without needing raw API calls.

### Installation (macOS)

```bash
brew install atlassian
```

## When to Use What

| Task | Tool | Why |
|------|------|-----|
| Create/edit Jira tickets | `acli jira` | CLI handles ADF formatting, auth is built in |
| View/search Jira tickets | `acli jira` | Simpler than REST for read operations |
| Read/write Confluence pages | `curl` (REST API) | `acli confluence` is NOT authenticated – use curl |
| Bulk Jira queries (JQL) | Either | `acli` for simple queries, REST for complex filtering |

## Authentication

Generate an API token at https://id.atlassian.com/manage-profile/security/api-tokens. Store it securely and reference it as `$API_TOKEN` in the commands below.

The same API token works for both `acli jira` and the Confluence REST API.

### Jira (acli)

```bash
# Check status
acli jira auth status

# Login with API token
echo "$API_TOKEN" | acli jira auth login \
  --site "your-instance.atlassian.net" \
  --email "your-email@company.com" \
  --token
```

### Confluence (REST API)

All Confluence requests use basic auth with your email and API token:

```bash
curl -s "https://your-instance.atlassian.net/wiki/rest/api/content/{PAGE_ID}?expand=body.storage" \
  -u "your-email@company.com:${API_TOKEN}" \
  -H "Accept: application/json"
```

---

## Jira Operations (acli)

### Rich Formatting Requires ADF

Using `--description` or `--description-file` with plain text or markdown will NOT render formatting in Jira. The text appears as raw markdown.

For rich formatting (headings, code blocks, lists, bold, etc.), you must use `--from-json` with **Atlassian Document Format (ADF)**.

### Creating Tickets

#### Simple ticket (no formatting)

```bash
acli jira workitem create \
  --project "MYPROJECT" \
  --type "Task" \
  --summary "My ticket summary" \
  --description "Plain text description" \
  --parent "MYPROJECT-1234"  # Optional: link to epic
```

#### Ticket with rich formatting

1. Create a JSON file with ADF structure:

```json
{
  "projectKey": "MYPROJECT",
  "type": "Task",
  "summary": "My ticket summary",
  "parentIssueId": "MYPROJECT-1234",
  "description": {
    "type": "doc",
    "version": 1,
    "content": [
      {
        "type": "heading",
        "attrs": {"level": 2},
        "content": [{"type": "text", "text": "Problem"}]
      },
      {
        "type": "paragraph",
        "content": [
          {"type": "text", "text": "This is "},
          {"type": "text", "text": "bold", "marks": [{"type": "strong"}]},
          {"type": "text", "text": " and this is "},
          {"type": "text", "text": "code", "marks": [{"type": "code"}]}
        ]
      }
    ]
  }
}
```

2. Create the ticket:

```bash
acli jira workitem create --from-json ticket.json
```

### Editing Tickets

#### Simple edit (no formatting changes)

```bash
acli jira workitem edit \
  --key "MYPROJECT-4045" \
  --summary "Updated summary" \
  --yes
```

#### Edit with rich formatting

The JSON must include `"issues": ["KEY-123"]` to specify which ticket to edit.

```json
{
  "issues": ["MYPROJECT-4045"],
  "description": {
    "type": "doc",
    "version": 1,
    "content": [
      // ... ADF content
    ]
  }
}
```

```bash
acli jira workitem edit --from-json edit.json --yes
```

### Viewing Tickets

```bash
# View a ticket (key is positional, not a flag)
acli jira workitem view MYPROJECT-4045

# View with all fields
acli jira workitem view MYPROJECT-4045 --fields '*all'

# View specific fields only
acli jira workitem view MYPROJECT-4045 --fields summary,description,comment
```

### Comments

```bash
# List comments (note: uses --key flag, unlike view)
acli jira workitem comment list --key MYPROJECT-4045

# Add a comment
acli jira workitem comment create --key MYPROJECT-4045 --body "Comment text"
```

### Other Commands

```bash
# Search tickets
acli jira workitem search --jql "project = MYPROJECT AND status = Open"

# Find child issues of a parent (Feature → Tasks, Epic → Stories)
# Use the POST search/jql endpoint. The old GET /rest/api/3/search
# was removed by Atlassian in 2026 and silently returns 0 for hierarchy queries.
curl -s -X POST -H "Content-Type: application/json" \
  -u "your-email@company.com:${API_TOKEN}" \
  -d '{"jql": "parent = MYPROJECT-100", "fields": ["summary","issuetype","status"]}' \
  "https://your-instance.atlassian.net/rest/api/3/search/jql"

# Transition ticket
acli jira workitem transition --key MYPROJECT-4045 --status "In Progress"

# Link tickets
acli jira workitem link create --out MYPROJECT-4045 --in MYPROJECT-3994 --type "Relate"
```

### Generating Templates

```bash
# For creating
acli jira workitem create --generate-json

# For editing
acli jira workitem edit --generate-json
```

---

## Confluence Operations (REST API)

### Reading Pages

#### Get page content (storage format – raw HTML)

```bash
curl -s "https://your-instance.atlassian.net/wiki/rest/api/content/{PAGE_ID}?expand=body.storage,version" \
  -u "your-email@company.com:${API_TOKEN}" \
  -H "Accept: application/json"
```

#### Get page ID from URL

The page ID is the number in the Confluence URL:
`https://your-instance.atlassian.net/wiki/spaces/SPACE/pages/2276098050/Example+Spec`
→ Page ID: `2276098050`

#### Get child pages

```bash
curl -s "https://your-instance.atlassian.net/wiki/rest/api/content/{PAGE_ID}/child/page" \
  -u "your-email@company.com:${API_TOKEN}" \
  -H "Accept: application/json"
```

### Updating Pages

Updates require the current version number (incremented by 1):

```bash
curl -s -X PUT "https://your-instance.atlassian.net/wiki/rest/api/content/{PAGE_ID}" \
  -u "your-email@company.com:${API_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "version": {"number": NEW_VERSION_NUMBER},
    "title": "Page Title",
    "type": "page",
    "body": {
      "storage": {
        "value": "<p>HTML content here</p>",
        "representation": "storage"
      }
    }
  }'
```

### Creating Pages

```bash
curl -s -X POST "https://your-instance.atlassian.net/wiki/rest/api/content" \
  -u "your-email@company.com:${API_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "page",
    "title": "New Page Title",
    "space": {"key": "SPACE"},
    "ancestors": [{"id": "PARENT_PAGE_ID"}],
    "body": {
      "storage": {
        "value": "<p>HTML content here</p>",
        "representation": "storage"
      }
    }
  }'
```

### Searching

```bash
# Search by title
curl -s "https://your-instance.atlassian.net/wiki/rest/api/content?title=Page+Title&spaceKey=SPACE&expand=version" \
  -u "your-email@company.com:${API_TOKEN}" \
  -H "Accept: application/json"

# CQL search
curl -s "https://your-instance.atlassian.net/wiki/rest/api/content/search?cql=space=SPACE+and+type=page+and+title~\"search term\"" \
  -u "your-email@company.com:${API_TOKEN}" \
  -H "Accept: application/json"
```

---

## ADF Quick Reference

### Document structure

```json
{
  "type": "doc",
  "version": 1,
  "content": [ /* blocks */ ]
}
```

### Headings

```json
{
  "type": "heading",
  "attrs": {"level": 2},
  "content": [{"type": "text", "text": "Heading Text"}]
}
```

### Paragraphs

```json
{
  "type": "paragraph",
  "content": [{"type": "text", "text": "Paragraph text"}]
}
```

### Text formatting

```json
// Bold
{"type": "text", "text": "bold text", "marks": [{"type": "strong"}]}

// Italic
{"type": "text", "text": "italic text", "marks": [{"type": "em"}]}

// Inline code
{"type": "text", "text": "code", "marks": [{"type": "code"}]}

// Combined
{"type": "text", "text": "bold code", "marks": [{"type": "strong"}, {"type": "code"}]}
```

### Code blocks

```json
{
  "type": "codeBlock",
  "attrs": {"language": "scala"},
  "content": [
    {"type": "text", "text": "val x = 1\nval y = 2"}
  ]
}
```

### Bullet lists

```json
{
  "type": "bulletList",
  "content": [
    {
      "type": "listItem",
      "content": [
        {"type": "paragraph", "content": [{"type": "text", "text": "Item 1"}]}
      ]
    },
    {
      "type": "listItem",
      "content": [
        {"type": "paragraph", "content": [{"type": "text", "text": "Item 2"}]}
      ]
    }
  ]
}
```

### Ordered lists

```json
{
  "type": "orderedList",
  "attrs": {"order": 1},
  "content": [
    {
      "type": "listItem",
      "content": [
        {"type": "paragraph", "content": [{"type": "text", "text": "Step 1"}]}
      ]
    }
  ]
}
```

---

## Common Pitfalls

- **No `acli jira issue` command** – use `acli jira workitem` instead
- **`view` uses positional argument** – `view MYPROJECT-4045` not `view --key MYPROJECT-4045`
- **`comment list` uses `--key` flag** – inconsistent with `view`, but that's the CLI design
- **`--action` flag doesn't exist** – this is a different CLI pattern (older Atlassian tools)
- **`acli confluence` is NOT authenticated** – always use curl for Confluence
- Body content uses Confluence storage format (XHTML with Atlassian-specific macros)
- Version number must be incremented by exactly 1 when updating

## Tips

1. **Always use `--from-json` for formatted descriptions** – plain text flags don't render markdown
2. **Use `--yes` flag** to skip confirmation prompts when editing
3. **Generate templates first** with `--generate-json` to see the expected structure
4. **For epics**, use `--parent` when creating or `parentIssueId` in JSON
5. **To clear a field**, use a single space `" "` not an empty string `""` – empty strings silently fail

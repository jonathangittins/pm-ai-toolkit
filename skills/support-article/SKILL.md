---
name: support-article
description: >
  Generate support articles for a help centre from video transcripts, demos,
  or feature descriptions. Outputs markdown with screenshot references ready
  for a support team to publish.
---

# Support Article Generator

Generate support articles for your help centre following established patterns and documentation best practices.

## Parallel with Release Notes

When both a support article and release notes are needed for the same feature, run them as parallel sub-agents sharing the same source material (video transcript, spec, feature context). Each sub-agent follows its own skill independently. Present both drafts for review.

## Required Inputs

### Always Required
1. **Feature/integration name**: What is being documented
2. **Product area**: Which product or module
3. **Source material**: Video transcript, demo notes, or feature description

### Prompt For
4. **Similar articles**: "Are there existing articles we can use as a template?"
5. **Screenshots**: Ask user to share screenshots or describe what they have
6. **Audience/permissions**: Who can use this feature? (Admins, Workspace Managers, all users)
7. **Known limitations**: Any constraints or gotchas to document

## Process

### Step 1: Gather Context
Ask for missing required inputs. If similar articles exist:
- Request PDFs or URLs of reference articles
- Analyse their structure to match the established pattern

If no similar articles exist (net-new feature):
- Determine article type (see Article Types below)
- Use the appropriate template pattern

### Step 2: Determine Article Type

**Process Guide** (most common)
- Step-by-step instructions for completing a task
- Screenshots at key steps
- Use for: new features, integrations, configuration workflows

**Troubleshooting Guide**
- Problem → Cause → Solution format
- Use for: known issues, error handling docs

**Informational Article**
- Concept explanation
- Use for: overviews, "how X works" articles

**FAQ Article**
- Question and answer format
- Use for: collections of common questions

### Step 3: Plan Screenshots
Suggest screenshots needed based on source material:
- Setup/configuration screens
- Key UI elements being documented
- Success/completion states
- Error states (for limitations section)

### Step 4: Draft the Article
Follow the structure pattern for the article type.

### Step 5: Output
Save the article and screenshots to a `support-article/` subfolder:
```
[product]/[feature]/support-article/
├── article.md
└── screenshots/
```

Include at the end of article.md:
- Screenshot checklist (which images are needed and where they go)
- Suggested help centre labels/categories
- Related articles to link

This folder can be zipped and handed off to the support team.

## Article Structure (Process Guide)

```markdown
# How do I [action]?

**Who can use this feature?**

Available on **[Product]**

Available to **[Roles]**

---

[Intro paragraph: 1-2 sentences explaining what the feature enables]

*[Video: Description]*

---

#### Table of Contents

- [Section 1](#section-1)
- [Section 2](#section-2)

---

## Section 1

[Content with screenshots]

---

## Limitations

- [Known limitation 1];
- [Known limitation 2].
```

## Voice and Tone

### Do
- Be direct and confident
- Be honest and concise
- Use short, simple sentences
- Write timeless content

### Do Not
- Use "maybe", "possibly", "probably", "potentially"
- Say "new feature" or "recently launched" (becomes outdated)
- Criticise your own product, or competitors
- Describe features as "limited", "buggy", "broken", or "workaround"
- Use contractions (write "do not" not "don't", "I would" not "I'd") – helps non-native speakers

## Style Rules

### Abbreviations
Spell out on first reference, then abbreviate:
> "The product integrates with Google Calendar (GCal). You can sync with GCal to ensure availability is updated."

### Titles and Headings
- Format as user would search: "How do I...?" not "How to..."
- **Heading 2** for main sections
- **Heading 3/4** for subsections
- No bold in headings
- Split sections with `---` (horizontal rule)

### Numbers
- Spell out one through nine; use numerals for 10+
- Always spell out at start of sentence
- Always use numerals in headlines/headers

### Bullet Points
End each item with semicolon; last item with period:
```
- First item;
- Second item;
- Last item.
```

### Numbered Lists
For nested steps: Number > Lowercase letter > Lowercase roman:
```
1. First step:
   a. Sub-step;
   b. Sub-step.
2. Second step:
   i. Sub-sub-step;
   ii. Sub-sub-step.
```

### Dates and Times
- Dates: Month DD, YYYY format ("November 23, 2023")
- Times: Uppercase AM/PM ("8 AM to 5 PM")

### Other Rules
- **Pronouns**: Gender-neutral (they/them)
- **Percentages**: Use % symbol with no space (90%)
- **Ampersands**: Do not use unless brand name (Johnson & Johnson)
- **Hyperlinks**: Always link URLs and related articles
- **Emojis**: Use sparingly, never in headings (except warning indicators in banners)

## Callout Formats

**Note (informational):**
> **Note:** Additional helpful context.

**Warning (requirements/roadblocks):**
> **Warning:** Important requirement or roadblock the user must address.

## Screenshot Workflow

1. User captures screenshots during demo/testing
2. User shares screenshots for context
3. Determine appropriate order and descriptive names
4. Name files: `01-description.png`, `02-description.png`, etc.
5. Article references screenshots with relative paths: `![Alt text](screenshots/01-description.png)`

**Naming convention:**
- Prefix with two-digit number for ordering: `01-`, `02-`, etc.
- Use lowercase, hyphen-separated descriptions
- Keep names short but descriptive
- Examples: `01-admin-nav.png`, `02-integrations-connected.png`

**Screenshot annotations:**
- Use arrows, boxes, circles
- Use your brand colour for consistency
- Thinnest line possible

### Videos
- Single video: Place at top after intro
- Multiple videos: Place under respective section headers
- User provides video file; support team uploads to hosting

### GIFs
- Prefer video over GIFs

## Delivery Format

Output: Self-contained `support-article/` folder
```
[product]/[feature]/support-article/
├── article.md
└── screenshots/
    ├── 01-admin-nav.png
    ├── 02-integrations-connected.png
    └── ...
```

### Handoff to Support

Once the article is approved, zip the folder for handoff:

1. Ask if the user is ready to zip
2. Create zip file: `[feature-name]-support-article.zip`
3. Place the zip in the same parent directory

This produces a zip file ready to send to the support team.

### Step 6: Reader Review (subagent)

**Delegate to a subagent** so it reads the article cold – as a customer or support agent would.

Pass the subagent the article file and these instructions:

> You are reading this support article for the first time with no prior context about the feature. Review it as if you are a customer trying to set up or use this feature, and then as a support agent who needs to troubleshoot issues.
>
> Check:
>
> 1. **Can you complete the task?** – Follow the steps as written. Flag any step where you'd be stuck without additional information – missing prerequisite, unclear UI location, ambiguous instruction.
> 2. **Terminology is consistent** – Is the feature called the same thing throughout? Do UI element names match what the user would see?
> 3. **Screenshots match the text** – Does the screenshot checklist cover every step where the user needs visual guidance? Flag steps that reference UI elements but have no corresponding screenshot planned.
> 4. **Limitations are honest** – Are the limitations complete based on what the article describes? Flag any capability mentioned in the body that has an unstated constraint.
> 5. **Style compliance** – No contractions, no "new feature" / "recently launched", no hedging language ("maybe", "possibly"), bullet punctuation follows the semicolon pattern.
>
> **Return ONLY:**
> ```
> PASS – no issues found
> ```
> or
> ```
> ISSUES:
> - [issue description with section reference]
> ```

If issues are found, fix them before presenting the article for approval.

## Key Patterns

**Why parallel with release notes:** A feature launch often needs both a public announcement (release notes) and detailed documentation (support article). Running both as parallel sub-agents from the same source material ensures consistency and saves time.

**Why the screenshot workflow matters:** Support articles live or die by their screenshots. Planning them upfront (Step 3) rather than adding them as an afterthought produces articles where the visuals actually match the text flow.

**Why zip for handoff:** Support teams often work in different tools (Zendesk, Intercom, etc.) with their own formatting. Giving them a self-contained folder with markdown and images lets them paste and adapt rather than trying to import from your doc system.

---
name: release-notes
description: >
  Generate Slack product release notes from video transcripts. Analyses the
  walkthrough, structures the announcement, and outputs formatted HTML for
  rich-text pasting into Slack. Optional pre-recording beat list and
  post-publish housekeeping (save to repo, update feature index, move
  roadmap card).
---

# Slack Release Notes Generator

Generate Slack release notes for product updates from video walkthroughs.

## Parallel with Support Article

When both release notes and a support article are needed for the same feature, run them as parallel sub-agents sharing the same source material (video transcript, spec, feature context). Each sub-agent follows its own skill independently. Present both drafts for review.

## Required Inputs

### Always Required
1. **Feature name/title**
2. **Video transcript**: Narrated walkthrough showing the capability
3. **Rollout status**: Deployment state (e.g. "Live for all customers", "Behind feature flag", "Beta", "Percentage rollout")

### Usually Required (prompt if not provided)
4. **Use cases**: 1-3 specific scenarios

### Optional
5. **Known limitations**: V1 constraints or caveats
6. **Additional resources**: Support articles, documentation links, API docs
7. **Target audience scope**: Whether to use @channel (don't include by default)

## Process

### Step 0: Pre-recording beat list (offer when relevant)

If the user mentions they're about to record the walkthrough video (or asks for help preparing one) and there's no transcript yet, offer to suggest a beat list before they hit record. Don't push it if they've already recorded – just analyse the transcript at Step 2.

By the time the release note is being written, the conversation often already has context from the spec, feature page, or GTM plan. Use that to propose 5–8 beats covering:

1. What this is (one sentence)
2. Why it matters (the customer problem or change)
3. What you'll see (signal the path)
4. Walk-through – the 2–4 things worth showing
5. Limitations / out of scope
6. Where to go next (ticket, support article, person)

Present beats as a short numbered list. The user glances, doesn't read from it. The structural signposts make the post-recording transcript easier to skim and easier to convert into release notes.

If no spec/feature context is in the conversation, ask the user for the feature highlights before drafting beats – don't invent them.

### Step 1: Gather Information

Ask for any missing required inputs. Don't overwhelm with all questions at once – prioritise the transcript and rollout status first.

**Auto-search for related help-center articles** (if you have a searchable help-center index):
Search for articles related to this feature and include them in the Resources section as `**Support article:** <url>`. If the existing article is outdated relative to the release, flag it: "The current support article doesn't cover [new capability] – may need updating."

### Step 2: Analyse the Video Transcript

Extract:
- Core functionality and workflow
- Technical details demonstrated
- Background context provided
- Edge cases or specific behaviours shown
- Emphasis points (where the narrator spends more time explaining)

### Step 3: Determine Structure

**Pattern 1 – Major Feature (full context):**
- Feature is substantial or complex
- Background context is needed
- Multiple use cases exist
- Limitations should be highlighted

**Pattern 2 – Straightforward Feature:**
- Feature is simple and self-explanatory
- Limited background needed
- Few or no limitations

**Pattern 3 – Cross-Platform / Preview:**
- Feature is not yet fully released
- Demonstrating advanced usage or future capability

### Step 4: Select Contextual Emojis

Choose 1-3 emojis that match the feature domain:
- Scheduling/calendar: calendar, clock
- Integration/matching: link, puzzle, search
- Notifications: envelope, chat
- Actions: check, refresh

### Step 5: Generate the Draft

#### Opening
- Place emojis before the feature name
- State the feature and rollout status clearly
- Keep it to 1-2 sentences max
- Do NOT include @channel by default. Most releases are incremental and don't warrant it. Ask before including it.

#### Body Structure

**"What?" and "How it works":**
- Conversational but professional tone
- Technical but accessible
- Bullet points for clarity

**"Why?" section:**
- 1-3 specific use cases
- Bold labels with concrete scenarios

**"Limitations" section:**
- Direct and clear
- Bullets (not numbers)
- Explain why the limitation exists when relevant
- Frame as "V1 constraints" when applicable

**"Coming soon" (when applicable):**
- Context about where future settings will live
- Why the upcoming feature matters
- Dependencies if relevant

#### Team Acknowledgment
- Always include thanks to engineers and designers at the end
- Format: "Thank you to @Name & @Name for [contribution]"

#### Resources Section
- Always include video link
- Add other resources if provided (support articles, docs)

### Step 6: Apply Your Voice

Slack release notes should sound like a person, not a changelog. Useful phrasing patterns to adapt to your voice:

- **Openings**: "is now live" (for GA releases), "is now ready" (for gradual/flagged rollouts), "are now available", "can now"
- **Context**: "Until now...", "Previously...", "This allows customers to..."
- **Transitions**: "Here are a few important things to note:", "Two common use-cases:"
- **Rollout explanations**: Be conversational when explaining non-standard rollouts (e.g. "it's not a formal beta, just want to slowly open it up")

### Step 7: Review Before Presenting

Check:
- Emojis are contextually appropriate (1-3)
- @channel only included if explicitly confirmed
- Rollout status is clear
- Technical details are accurate (from transcript)
- Use cases are specific and concrete
- Limitations are clearly stated (if applicable)
- Team acknowledgment is included at the end
- Structure is scannable with clear headers
- Length is appropriate – aim for scannable in under 30 seconds (typically 10-25 lines)

### Step 8: Prepare for Slack

Slack doesn't preserve formatting when pasting plain text or markdown. To get rich text formatting into Slack:

1. **Generate an HTML file** with the release notes content
2. **Open it in a browser**
3. **Cmd+A, Cmd+C** to copy as rich text
4. **Paste into Slack** – formatting (bold, bullets, emojis) will be preserved

**HTML Template:**
```html
<!DOCTYPE html>
<html>
<head><meta charset="UTF-8"></head>
<body style="font-family: -apple-system, system-ui, sans-serif; font-size: 15px; line-height: 1.5; max-width: 700px; padding: 20px;">
<!-- Content here using <strong> for bold, <em> for italic -->
<!-- For bullets, use Unicode bullet character (•) with <br> tags, NOT <ul><li> -->
<!-- Example:
• First item<br>
• Second item
-->
</body>
</html>
```

**Important:**
- Always use `<meta charset="UTF-8">` to preserve emojis and special characters
- **Do NOT use HTML lists (`<ul><li>`)** – Slack's rich text parser mangles them. Use Unicode bullet characters (•) with `<br>` line breaks instead.

### Step 9: Save to Repository

After posting, save a copy alongside your feature documentation:

```markdown
# [Feature Title]

**Posted:** [DD Month YYYY]
**Channel:** #your-release-channel

---

[The final release notes content as posted to Slack]
```

A sensible location is `release-notes/YYYY-MM-DD-feature-name.md` or next to the spec for the feature.

### Step 10: Feature index update (optional)

If you maintain a feature index (a single file tracking each feature with its spec, release notes, and support article links), update the row for this feature:

- Update the Release Notes column to link to the saved file
- Fill in any other columns that became known during the process

### Step 11: Roadmap card check (optional)

If you maintain a roadmap with a Kanban board (Now / Ship Prep / Live columns), check whether the feature has a card in **Ship Prep**. If it does, ask:

> "Announced – move [initiative name] from Ship Prep → Live on the roadmap?"

If confirmed, edit the roadmap file to move the card.

## Output Format

Present the draft with:
1. **The complete release note** in markdown for review
2. **A brief note** explaining any choices made (emoji selection, structure choice, etc.)
3. **Suggestions** for any missing information that would strengthen the announcement

Once approved, generate the HTML file and open in a browser for copy-paste into Slack. After posting, offer the Step 9-11 housekeeping.

## Iteration

After presenting the draft:
- Be ready to adjust based on user feedback
- Refine emojis, tone, or structure as requested
- Add or remove sections based on preferences
- Once approved, regenerate the HTML file if any changes were made

## Key Patterns

**Why video transcripts work:** A narrated walkthrough captures not just what a feature does, but what the PM emphasises, how they explain the value, and which edge cases they call out. This produces release notes that sound like the PM, not like a changelog.

**The HTML-to-Slack trick:** Slack's rich text editor accepts formatted HTML via clipboard paste but doesn't render markdown pasted as plain text. Generating an HTML file and copying from the browser is the most reliable way to get consistent formatting (bold, bullets, emojis) into a Slack message.

**Team acknowledgment:** Always include thanks to engineers and designers at the end. Product updates are team efforts.

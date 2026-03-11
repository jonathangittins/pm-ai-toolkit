---
name: release-notes
description: >
  Generate Slack product release notes from video transcripts. Analyses the
  walkthrough, structures the announcement, and outputs formatted HTML for
  rich-text pasting into Slack.
---

# Slack Release Notes Generator

Generate Slack release notes for product updates from video walkthroughs.

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

### Step 1: Gather Information

Ask for any missing required inputs. Don't overwhelm with all questions at once – prioritise the transcript and rollout status first.

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

### Step 6: Review Before Presenting

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

### Step 7: Prepare for Slack

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

### Step 8: Save to Repository

After posting, save a copy alongside your feature documentation (e.g. `release-notes/YYYY-MM-DD-feature-name.md`):

```markdown
# [Feature Title]

**Posted:** [DD Month YYYY]
**Channel:** #your-release-channel

---

[The final release notes content as posted to Slack]
```

## Key Patterns

**Why video transcripts work:** A narrated walkthrough captures not just what a feature does, but what the PM emphasises, how they explain the value, and which edge cases they call out. This produces release notes that sound like the PM, not like a changelog.

**The HTML-to-Slack trick:** Slack's rich text editor accepts formatted HTML via clipboard paste but doesn't render markdown pasted as plain text. Generating an HTML file and copying from the browser is the most reliable way to get consistent formatting (bold, bullets, emojis) into a Slack message.

**Team acknowledgment:** Always include thanks to engineers and designers at the end. Product updates are team efforts.

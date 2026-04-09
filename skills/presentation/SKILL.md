---
name: presentation
description: Build reveal.js slide decks with professional presentation techniques (fragments, auto-animate, timelines, bar charts, quote slides). Use when the user wants to create slides, a presentation, or a deck.
user_invocable: true
---

# Presentation Skill

Build reveal.js presentations. One HTML file, no build step, professional-grade output.

## When to trigger

- "Build me a deck", "create a presentation", "make slides"
- "Add a slide about X" (to an existing deck)
- "I need to present X to Y audience"

## Brand assets

If you have brand assets (fonts, logos, colour palette), place them in a `brand/` folder alongside the deck or reference them via relative paths.

- **Reveal.js can be vendored** in a local `reveal-js/` directory for offline use, or loaded from CDN
- Create a `brand/README.md` documenting your colour values and typography rules

If no brand assets exist, the skill uses sensible defaults: dark background, white text, accent colour for highlights, and system fonts.

## Phase 1: Gather the brief

Ask for what you don't already know from conversation context:

1. **Topic** -- what is this about?
2. **Audience** -- who is watching? (all-hands, sales team, leadership, external, etc.)
3. **Key points** -- what needs to land? (Bullet list, stream-of-consciousness, or "you know the context from X")
4. **Existing content** -- any specs, docs, data, or transcripts to draw from?
5. **Delivery format** -- live presentation (use fragments/pacing), async/read-through (show everything), or recorded?

Don't over-ask. If the user says "build me a deck on the roadmap" and you already know the roadmap from context, just confirm what you're planning.

## Phase 2: Structure the deck

Plan the slide sequence before writing HTML. Present it to the user as a numbered list:

```
1. Title slide
2. [Section divider] -- Topic A
3. Content slide -- key points (technique: fragment reveal)
4. Quote slide -- customer evidence
5. ...
```

### Technique selection rules

Match content type to the right reveal.js technique. These are not suggestions -- apply them by default when the content fits.

| Content type | Technique | When to use |
|---|---|---|
| **3+ points to make** | Fragment reveal (`class="fragment fade-up"`) | Any list the presenter talks through sequentially. Default for bullet-heavy slides. |
| **Before/after comparison** | Auto-animate bar chart (`data-auto-animate`) | Two slides with same structure, different values. Bars morph between states. |
| **Concept transformation** | Auto-animate text morph (`data-id` + `data-auto-animate`) | "We used to X, now we Y" -- word physically transforms. Use sparingly -- once per deck max. |
| **Roadmap / sequence / phases** | Timeline component | Horizontal dots with labels, each revealed by fragment. Use for anything with temporal order. |
| **Process / workflow / flow** | Step walkthrough | Numbered cards that light up sequentially. Use for "here's how it works" explanations. |
| **Big numbers / impact** | Stat reveal | Large accent-coloured numbers with labels, each fragment-revealed. Use when numbers are the story. |
| **Customer voice / testimonial** | Quote slide | Serif font, accent left border. Pull from feedback tools if available, or use the quote the user provides. |
| **Key takeaway / callout** | Highlight box | Accent-bordered box. Use for the one thing the audience must remember from a dense slide. |
| **Topic transition** | Section divider | Full-bleed accent background. Use between major sections. |
| **Contrast / visual break** | Light slide | Use for "what this means for you" / summary / takeaway slides. Breaks up dark slide monotony. |
| **Appendix / optional detail** | Vertical slides (nested `<section>`) | Content the presenter might skip. Sits below the parent slide, accessed with down arrow. |
| **Product categorisation** | Tags / pills | Coloured badges for product or feature names. |
| **Side-by-side comparison** | Two-column layout (`div.columns`) | Any "old vs new", "them vs us", "problem vs solution" content. |
| **3+ parallel items** | Three-card layout (`div.columns-3`) | Features, pillars, benefits -- anything where items are peers, not sequential. |

### Pacing rules

- **Live presentation**: Fragment-reveal everything. One idea per click. The audience should never read ahead.
- **Async / read-through**: Show all content on load. No fragments, no auto-animate. Dense slides are fine. Add `view: 'scroll'` to config (or append `?view=scroll` to URL) so recipients scroll through it like a web page instead of clicking.
- **Remote / all-hands**: Fragment-reveal at the theme level (not bullet-by-bullet). Keep slides simpler than in-person -- less content per slide, more slides.

### Scroll view rules (v6)

Scroll view (`view: 'scroll'`) turns the deck into a scrollable page. Use it **only** for async/read-through decks that have no fragments or auto-animate. Combining scroll view with fragments creates a confusing hybrid where scrolling triggers animations at unpredictable boundaries.

- **Async sharing** (Slack, email, wiki): scroll view + static slides, no fragments
- **Live presentation**: never use scroll view
- **Dual-purpose deck**: build with fragments for live delivery, tell recipients to append `?view=scroll` for the read-through version -- but warn them that fragment animations will feel odd in scroll mode

### Slide density rules

- Maximum 5 bullet points per column
- Maximum 2 columns of bullets per slide
- If a slide has more than 8 items, split into multiple slides or use vertical stacking
- Big numbers get their own slide -- don't mix stats with bullets
- Quotes get their own slide -- don't surround with other content

## Phase 3: Build the deck

Write the HTML file. Key technical rules:

### File structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- reveal.js 5.x (vendored locally or CDN) -->
  <!-- CSS: base styles + extended components -->
</head>
<body>
<div class="reveal">
<div class="slides">
  <!-- slides here -->
</div>
</div>
<!-- reveal.js init with standard config -->
</body>
</html>
```

### Reveal.js config (always use)

```javascript
Reveal.initialize({
  hash: true,
  slideNumber: 'c/t',
  showSlideNumber: 'speaker',
  transition: 'fade',
  transitionSpeed: 'fast',
  backgroundTransition: 'fade',
  center: false,
  width: 1920,
  height: 1080,
  margin: 0,
  plugins: [ RevealNotes, RevealSearch, RevealZoom, RevealMarkdown ],
  keyboard: {}
});
```

### CSS components reference

Include only the CSS for components actually used. Add these when needed:

**Timeline** -- `.timeline`, `.timeline-item`, `.timeline-dot` + `::before` connector line
**Bar chart** -- `.bar-chart`, `.bar` (use brand accent colour + complementary colours), `.bar-label`
**Steps** -- `.steps`, `.step`, `.step-number` + `.active` / `.fragment.visible` border highlight
**Metrics** -- `.metric`, `.metric-number`, `.metric-label`
**Quote** -- `blockquote` override (serif font, accent border, no background) + `cite`
**Highlight box** -- `.highlight-box` (accent left border, tinted bg)
**Pills** -- `.pill`, `.pill-accent`, `.pill-secondary`, `.pill-dark`
**Gradient text** -- `.gradient-text`
**Icon circle** -- `.icon-circle`

### Speaker notes

Every slide gets `<aside class="notes">` with:
- What to say / talking points
- Transition phrase to next slide
- Backup context for Q&A
- Demo cues if applicable

### Background colour rotation

Follow this pattern to create visual rhythm:
1. Dark (e.g. `#131624` or `#1a1a2e`) -- most content slides
2. Accent (your brand's primary colour) -- section dividers only
3. Light (e.g. `#FCFAF8` or `#f5f5f5`) -- summaries, takeaways, "what this means" slides
4. Gradient -- use once per deck max, for a standout moment

Never put two light slides or two accent slides back-to-back.

### Fragment best practices

- Use `data-fragment-index` to control order explicitly (don't rely on DOM order)
- Two fragments can share an index to appear simultaneously
- For theme-by-theme reveal: wrap each theme in a `<div class="fragment" data-fragment-index="N">`
- Favour `fade-up` as the default fragment animation
- Use `highlight-current-blue` or `grow` sparingly for emphasis on a specific item

### Auto-animate best practices

- Both slides need `data-auto-animate` attribute
- Morphing elements need matching `data-id` attributes
- Keep HTML structure identical between the two slides -- only change values/styles
- Works for: bar heights, text content, position, font size, colour
- Use for maximum one transition per deck -- overuse dilutes the impact

## Phase 4: Review and open

After writing the file:

1. Open in browser: `open -g "file:///path/to/deck.html"`
2. Tell the user the slide count and structure
3. Ask if they want to walk through it or adjust anything

## Phase 5: Iteration

When the user asks to change slides:

- **"Add a slide about X"** -- determine the right technique from the table above, insert at the logical position
- **"Make this more impactful"** -- add fragment reveals, split dense slides, add a stat or quote slide
- **"Too many slides"** -- combine with vertical stacking (main point on parent, detail below)
- **"Show me the data"** -- add auto-animate bar chart or stat reveal
- **"Add a customer quote"** -- search feedback tool for relevant quotes if the user doesn't provide one, use the quote slide format

## Lessons learned

- Start with oversized fonts, come down -- 20px+ body, 40px+ titles
- All text on dark backgrounds: pure white, never light colours with opacity
- `data-background-color` for full-bleed, not CSS `background` property
- Keep content sparse -- slides are guardrails for a conversation, not a read-through document
- Plan SVG diagram layout math upfront if creating custom diagrams
- Use inline `<polygon>` for arrow tips in SVGs, never `<marker>`
- Expect roadmap/strategy slides to generate 3-4x more Q&A than planned
- PDF export: `npx decktape reveal "file:///path/to/deck.html" output.pdf --size 1920x1080`

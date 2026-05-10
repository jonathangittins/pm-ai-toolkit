---
title: SCQA / Pyramid Principle
type: framework
tags:
  - communication
  - executive-communication
  - decision-making
---

# SCQA / Pyramid Principle

*Based on Barbara Minto's Pyramid Principle, via Michael Dearing*

## Core Principle

**Lead with the answer. Structure evidence underneath it.** Executive communication fails when it buries the recommendation under context. SCQA gives you a four-step structure that front-loads the decision and makes the supporting logic scannable.

---

## The Structure

### S – Situation

Uncontroversial facts about the current state. No matter which side of a decision someone is on, they should read this and nod. This is shared ground.

**Test:** Could someone who disagrees with your recommendation still accept this paragraph as fair?

### C – Complication

What changed, or what's making things harder. This creates tension – the reason action is needed now.

**Test:** Does this make the reader feel the urgency? If removed, would the audience still care?

### Q – Question

Falls naturally out of S + C. Almost always: "What should we do?"

You can save time here. If you nail S and C, the question writes itself. If you're struggling with Q, your Situation or Complication isn't crisp enough.

### A – Answer

Your recommendation, stated first. Supporting evidence sits underneath in a pyramid – most important argument on top, grouped sub-arguments below.

**Test:** If someone only reads the Answer, do they know what you're proposing and roughly why?

---

## Pyramid Structure for the Answer

```
          [Recommendation]
         /       |        \
   [Reason 1] [Reason 2] [Reason 3]
    /    \      /    \      /    \
 [Data] [Data] [Data] [Data] [Data] [Data]
```

- **MECE grouping**: Reasons should be mutually exclusive (no overlap) and collectively exhaustive (no gaps). If they aren't, you haven't finished thinking.
- **Rule of 3**: Three supporting reasons is the sweet spot. Two feels thin. Four starts to blur.
- **Each level answers "why?" for the level above it.** If a sub-point doesn't directly support its parent, it doesn't belong there.

---

## When to Use SCQA

| Context | Why it works |
|---|---|
| Spec kickoffs | Frames the problem before jumping to solutions |
| Roadmap decisions | Surfaces the complication driving priority changes |
| Stakeholder updates | Gives executives the answer first, detail on demand |
| Decision memos | Natural structure for the recommendation section |
| Slack messages to leadership | Compresses context into a scannable format |
| Cross-functional alignment | Shared Situation prevents "we're solving different problems" |

---

## Examples

### Weak (no structure)

> We've been looking at the chatbot activation rates and they're lower than expected. We think there might be an issue with the onboarding flow. Some customers have mentioned it's confusing. We also looked at competitor approaches. We think we should redesign the first-run experience.

### Strong (SCQA)

> **Situation:** The chatbot has 340 active accounts. 62% of new accounts complete setup but only 28% activate a second conversation within 7 days.
>
> **Complication:** Customer interviews (n=12) show the drop-off happens at the "test your bot" step – users don't know what to test, so they leave and don't come back.
>
> **Question:** How do we close the setup-to-activation gap?
>
> **Answer:** Replace the open-ended "test your bot" step with a guided first conversation that uses the customer's actual website data. Three reasons:
> 1. Removes the blank-canvas problem (users see real value immediately)
> 2. Low engineering cost (reuses existing crawl data from setup)
> 3. Competitors (Drift, Intercom) both use guided activation – customers expect it

---

## Common Mistakes

| Mistake | Fix |
|---|---|
| Situation is too long | 2-3 sentences max. If you need more, you're explaining, not grounding. |
| Complication is vague ("things are hard") | Name the specific change or tension. Numbers help. |
| Answer is buried after a page of analysis | Move it to the top. The analysis goes in the pyramid underneath. |
| Skipping the Situation | Without shared ground, people argue about premises instead of solutions. |
| Multiple complications | Pick the one that matters most. The others become supporting evidence. |

---

## Integration with Existing Frameworks

### Connection to PM Communication Guidelines
SCQA provides the macro-structure; Communication Guidelines provide the micro-structure (active voice, short sentences, specific words). Apply both: SCQA for the shape of the message, Communication Guidelines for how each sentence reads.

### Connection to Strategic Problem Articulation
SCQA maps to the first two steps of Strategic Problem Articulation (Simplify, Deduct). The Situation and Complication together are your problem statement. If you've done a strategic problem articulation, SCQA is how you present the result.

### Connection to Decision Memos
Decision memos should open with SCQA. The Situation and Complication frame the decision context; the Answer is your recommendation; the pyramid is your evidence structure.

---

## Making It a Habit

- **Slack messages**: Before sending a message to leadership, mentally run SCQA. Even if you don't label the sections, the structure should be there.
- **Spec introductions**: Open every spec with an explicit S, C, Q, A block before diving into requirements.
- **Meeting openers**: When presenting a decision, spend 60 seconds on S and C before proposing A. This prevents the room from jumping to solutions before agreeing on the problem.

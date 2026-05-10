---
title: AI Product Evals Framework
type: framework
tags:
  - ai
  - evals
  - quality
  - behavior-contracts
  - autonomy
---

# AI Product Evals Framework

*Consolidated from three sources: Shankar/Jason (Maven), GK (Maven), Ankor Goyle (BrainTrust)*

## Core Principle

**Evals are the new PRDs.** For AI products, the PM's deliverable shifts from prose requirements to measurable evaluation criteria. A PRD says "the chatbot should be helpful." An eval says "given this input, the response must score 1 on these criteria."

AI does what you specify and self-chooses what you don't. If you haven't defined it, the model will improvise – and you'll discover the gaps from users, not from testing.

---

## When to Use This Framework

Apply this for:
- ✅ Any feature where an LLM generates output shown to users
- ✅ AI-assisted workflows (AI agents, in-app assistants, chatbots)
- ✅ Automated actions with real-world consequences (sending emails, creating records, routing)
- ✅ Features where "correct" is subjective or context-dependent

Skip it for:
- ❌ Deterministic code paths with no AI involvement
- ❌ Internal tools where you are the only user and can vibe-check directly

---

## The Three-Framework Loop

Evals, behavior contracts, and autonomy maps work together in a cycle:

1. **Evals** define what success looks like (measurable)
2. **Behavior contracts** encode it (always/never rules)
3. **Autonomy maps** enforce it (who decides – human or AI)
4. **Evals** validate the loop is working

Each framework feeds the others. A failing eval reveals a missing behavior contract rule. A behavior contract violation reveals the autonomy level is too high.

---

## Framework 1: Evals

### What an eval is

Three components:

| Component | What it is | Example |
|---|---|---|
| **Data** | Inputs + optional expected output | Customer message: "Can I get a refund?" |
| **Task** | The system that generates a response | An AI chatbot with system prompt + tools |
| **Score** | A function that rates 0-1 | Did the response reference the refund policy? (1 = yes, 0 = no) |

### Building evals

**Start with at least 5 scenarios before building anything.** These should include:
- 2-3 happy path cases
- 1-2 edge cases you know will be tricky
- 1 adversarial case (user trying to break it)

**Template for each scenario:**

```
Input: [the user's message or trigger]
Context: [relevant state – account type, prior messages, etc.]
Expected behavior: [what a good response looks like]
Pass criteria: [binary – does it or doesn't it]
Threshold: [1.0 for hard requirements, 0.8+ for soft]
```

### Scoring rules

- **Normalize all scores to 0-1.** No 1-10 scales, no letter grades. 0-1 is comparable across time and experiments.
- **Force binary pass/fail** on individual criteria. "Good enough" is not a score. Either the response referenced the refund policy or it didn't.
- **Start with categorical scoring**, not continuous. "Did it do X?" is clearer than "How well did it do X?"
- **Aggregate scores across scenarios** to get an overall pass rate.

### The Distance Principle

The further your team is from the end user, the more structured your evals need to be. A solo builder who talks to users daily can vibe-check. A team of 5 engineers who've never spoken to a customer cannot.

| Team distance | Eval approach |
|---|---|
| You are the user | Vibe checks are fine to start |
| You talk to users weekly | Lightweight evals (5-10 scenarios) |
| Engineers build, PM specs | Full eval suite with behavior contracts |
| Outsourced or multi-team | Eval suite + automated CI checks |

### Offline vs Online eval loop

- **Offline evals**: Run during development. Use curated test scenarios. Iterate on prompts and tools until pass rate is acceptable.
- **Online evals**: Run on production logs. Review real user interactions, flag failures, add failing examples to your offline dataset.
- **Morning ritual**: Review yesterday's production examples. Compare against offline eval performance. Add any new failure modes to the eval set.

---

## Framework 2: Behavior Contracts

Behavior contracts replace the acceptance criteria section of a traditional PRD. They make explicit what the AI should always do, never do, and how it should handle ambiguity.

### Structure

```markdown
## Behavior Contract: [Feature Name]

### Always do
- [Rule 1]
- [Rule 2]

### Never do
- [Rule 1]
- [Rule 2]

### When uncertain
- [Fallback behavior]
```

### Writing good rules

- **Be specific.** "Be professional" is not a rule. "Never use emojis in responses to enterprise accounts" is.
- **Test with the Opposite Test.** If the opposite rule would be absurd, your rule is too vague. "Always be accurate" fails – nobody would write "sometimes be inaccurate." Instead: "When citing pricing, always pull from the current price list API, never from training data."
- **Include the why.** Rules without rationale get deleted in future iterations. "Never mention competitor pricing – legal flagged this in Q3 2025" persists.

### Relationship to system prompts

Behavior contracts are the PM artifact. The system prompt is the engineering implementation. Not everything in a behavior contract maps 1:1 to a system prompt instruction – some rules are enforced through tool design, guardrails, or post-processing.

---

## Framework 3: Autonomy Maps

Autonomy maps answer: "For this action, who decides – the human, the AI, or both?"

### The 5-Level Scale

| Level | Who decides | Example |
|---|---|---|
| **L1** | Human reviews all | AI drafts email, human sends |
| **L2** | AI acts on high confidence | Auto-send if confidence > 95%, queue rest for review |
| **L3** | AI acts on most, human reviews critical | Auto-route leads, flag enterprise accounts for human |
| **L4** | AI acts with hard floor rules | Auto-everything except financial commits and unknown senders |
| **L5** | AI self-adjusts thresholds | AI tunes its own confidence thresholds based on outcomes |

### Setting thresholds

There are no universal thresholds. Calculate yours based on:

- **Stakes**: What's the cost of a wrong action? (Embarrassment vs lost deal vs legal liability)
- **Reversibility**: Can you undo it? (Draft email = reversible. Sent email = not.)
- **Baseline performance**: How well does the current system/human do?
- **Human parity**: At what point is the AI at least as good as the average human doing this task?

### Hard Floor Rules

Hard floor rules are level-agnostic constraints. No confidence score overrides them. They live outside the LLM conversation as system-level checks.

Examples:
- Never auto-send to senders not in the CRM
- Never commit financial amounts above $X without human approval
- Never share documents externally without explicit user confirmation
- Never delete records – only soft-delete with recovery window

**Hard floor rules exist because you can't anticipate every failure mode.** They're the blast radius limiter for the scenarios you didn't think of.

### Progression

Always start at L1. Advance only when:
1. Eval pass rate exceeds your threshold for that level
2. You've run at the current level long enough to see edge cases
3. Hard floor rules are implemented and tested
4. You have monitoring to detect regressions

---

## Putting It Together: PM Deliverable for an AI Feature

Instead of a traditional PRD, deliver:

1. **System instruction file** (system.md) – Customer context, edge cases, failure modes, tripwires that would destroy trust. Be brutally honest about difficult customers.
2. **Behavior contract** – Always/never rules with rationale
3. **Eval suite** – Minimum 5 scenarios with binary pass/fail criteria
4. **Autonomy map** – Starting level, progression criteria, hard floor rules
5. **Monitoring plan** – What to watch, when to pause, when to roll back

Package these together for engineering. Don't throw a document over the wall – the system.md and eval suite are collaborative artifacts.

---

## Integration with Existing Frameworks

### Connection to Inversion Framework
Run Inversion Phase 1 (Spec Inversion) to generate your behavior contract's "never do" section. The failure modes you identify become hard floor rules or eval scenarios.

### Connection to Value Proposition Writing
Apply the Opposite Test to behavior contract rules. If the opposite is absurd, the rule is too vague.

### Connection to Five Step Improvement Framework
Apply to eval scope: delete eval scenarios that test trivially obvious behavior. Keep evals that test genuinely uncertain territory.

---

## Anti-Patterns to Avoid

❌ **Vibes-only at scale** – Vibe checks are real evals, but they don't scale past a solo builder
❌ **Eval theater** – Writing evals that all pass. If nothing fails, you don't know what's broken.
❌ **Subjective scoring** – "Rate the response 1-10" gives you noise, not signal. Force binary.
❌ **Static eval sets** – Never updating your eval scenarios after launch. Production will surprise you.
❌ **Threshold shopping** – Lowering pass thresholds to make numbers look good. If it's failing, fix the system.
❌ **Skipping L1** – Jumping to auto-send because the demo looked good. Always start with human review.
❌ **Rules without rationale** – Writing "never do X" without explaining why. Future you will delete it.

---

## Quick Reference Card

```
BEFORE BUILD → Define success
- 5+ eval scenarios (binary pass/fail, 0-1 scores)
- Behavior contract (always/never/when uncertain)
- Autonomy map (start at L1, define progression criteria)
- Hard floor rules (non-negotiable, level-agnostic)

DURING BUILD → Iterate on evals
- Run offline evals against prompt/tool changes
- Binary scoring, 0-1 normalized
- Failing evals = good (you found a gap)

AFTER LAUNCH → Close the loop
- Online evals on production logs
- Morning review: yesterday's failures → new eval scenarios
- Advance autonomy level only when eval pass rate justifies it

REMEMBER: AI does what you specify and self-chooses what you don't.
```

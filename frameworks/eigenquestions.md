---
title: Eigenquestions
type: framework
tags:
  - problem-framing
  - decision-making
  - prioritisation
---

# Eigenquestions

*Based on Shishir Mehrotra's "Eigenquestions: The Art of Framing Problems"*

## Core Principle

**An eigenquestion is the single question that, once answered, makes all subsequent questions easier or irrelevant.** Most teams waste time debating downstream questions when the real disagreement is upstream. Finding the eigenquestion reframes the entire problem space.

The term borrows from linear algebra: an eigenvector is the vector that, when a transformation is applied, only changes in scale – it captures the essential direction. An eigenquestion captures the essential decision.

---

## The Problem It Solves

Teams get stuck in circular debates that feel productive but aren't. The symptoms:

- The same arguments repeat across multiple meetings
- People agree on data but reach different conclusions
- A decision gets made, then revisited a week later
- Two people argue about a detail while actually disagreeing about strategy

In every case, there's an unresolved upstream question hiding beneath the surface debate.

---

## How to Find the Eigenquestion

### Step 1: List the questions on the table

Write down every question the team is currently debating about a decision. Don't filter – capture all of them.

Example (chatbot product roadmap):
- Should we build multi-language support?
- Should we invest in better analytics?
- Should we add more AI model options?
- Should we build a visual bot builder?
- Should we focus on activation improvements?

### Step 2: Look for the question behind the questions

Ask: "If we answered question X, would several other questions on this list become easier or go away?"

In the example above, the eigenquestion might be: **"Are we optimising for new account activation or power-user expansion?"**

If the answer is activation → multi-language, analytics, and model options drop in priority; activation improvements and the visual builder (if it improves first-run experience) rise.

If the answer is expansion → analytics, model options, and multi-language (for existing international accounts) rise.

### Step 3: Test your candidate eigenquestion

A good eigenquestion passes three tests:

| Test | How to check |
|---|---|
| **Resolving power** | Does answering it collapse 3+ downstream questions? |
| **Non-obvious answer** | Do reasonable people disagree on the answer? (If everyone agrees, it's not the real question – dig deeper) |
| **Actionable** | Can you actually answer it with available data, research, or a decision? |

If your candidate fails these tests, you're either too high-level (strategy eigenquestion when you need a tactical one) or too low-level (tactical question that doesn't resolve the broader set).

---

## Common Eigenquestion Patterns

These patterns recur across product decisions:

| Pattern | Eigenquestion form | Example |
|---|---|---|
| **Audience** | Who is the primary user? | "Is the scheduling product for marketers or sales ops?" |
| **Stage** | What growth stage are we optimising for? | "Activation or expansion?" |
| **Identity** | Are we a platform or a point solution? | "Is the chat product standalone or an add-on to scheduling?" |
| **Constraint** | What's the binding constraint? | "Is this gated by engineering capacity or customer demand?" |
| **Sequencing** | What must be true before this matters? | "Does this feature require multi-language to be useful?" |
| **Metric** | What are we actually measuring success by? | "Adoption rate or revenue per account?" |

---

## Using Eigenquestions in Practice

### In roadmap planning

Before debating individual features, ask: "What's the eigenquestion for this quarter?" Answer it first, then let the feature list fall out of the answer.

### In spec reviews

When a spec has unresolved debates, ask: "What upstream question are we disagreeing about?" Often the spec tries to serve two audiences or two goals, and the team hasn't chosen.

### In meetings that go in circles

Interrupt with: "I think we're debating downstream questions. Can we step back – what's the one question that, if we answered it, would make the rest of this easier?"

### In stakeholder alignment

When two stakeholders disagree, the eigenquestion reveals whether they disagree on facts (resolvable with data) or framing (requires a decision from someone with authority).

---

## Eigenquestions vs. Strategic Problem Articulation

Both frameworks address problem framing. The difference:

| | Eigenquestions | Strategic Problem Articulation |
|---|---|---|
| **Scope** | One question | Full problem analysis |
| **Speed** | Minutes | Hours |
| **Best for** | Unsticking a team debate | Deeply understanding a complex problem |
| **Output** | The right question to answer | A structured problem statement with evidence |

Use eigenquestions as a quick intervention. Use Strategic Problem Articulation when the eigenquestion itself is complex enough to need rigorous analysis.

---

## Anti-Patterns

- **Eigenquestion as avoidance**: Spending time finding the perfect question instead of answering any question. The eigenquestion is a tool for faster decisions, not slower ones.
- **Too abstract**: "What's our vision?" is not an eigenquestion – it's too broad to resolve downstream decisions. Get specific.
- **Skipping the answer**: Finding the eigenquestion feels like progress, but it's only progress if you then answer it and let the downstream decisions follow.
- **Forcing consensus**: The eigenquestion might reveal a genuine disagreement that needs a decision, not more discussion. Someone with authority may need to call it.

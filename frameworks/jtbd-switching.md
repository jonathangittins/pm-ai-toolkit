---
title: JTBD Switching Threshold
type: framework
tags:
  - jtbd
  - customer-interviews
  - discovery
  - competitive
---

# JTBD Switching Threshold

*Based on the Jobs-to-be-Done four forces model, via @nurijanian*

## Core Principle

**Customers don't switch to better products. They switch when the pain of staying exceeds the pain of moving.**

The formula:

```
Switching Threshold = (Pushes + Pulls) - (Habits + Anxieties)
```

When positive, customers switch. When negative, they stay – even if your product is objectively better.

---

## The Four Forces

| Force | Direction | What it is | Example |
|---|---|---|---|
| **Pushes** | Toward switching | Frustrations with current solution | "I lose 20 minutes every time routing breaks" |
| **Pulls** | Toward switching | Attraction of the new solution | "The new tool routes leads in real-time" |
| **Habits** | Against switching | Muscle memory, workflows, team norms | "My team knows how to use the current tool" |
| **Anxieties** | Against switching | Fear of the unknown, migration risk | "What if our CRM data gets corrupted?" |

## Why This Matters

Most PMs over-index on Pulls (better features) and under-index on Habits + Anxieties (switching barriers). A product that's 20% better on features still loses if migration is painful, the team has to relearn workflows, or edge cases are uncertain.

**Habits and anxieties quietly win.**

---

## Better Interview Questions

The format of your question determines whether you get signal or noise.

| Instead of | Ask |
|---|---|
| "What problems do you have?" | "When does your current solution fail you?" |
| "What features do you want?" | "Walk me through the last time [workflow] went wrong" |
| "Would you switch to X?" | "What would have to be true for you to change how you do this?" |
| "What do you like about competitors?" | "What's keeping you on your current tool even when it frustrates you?" |

### Signal vs Noise

**Signal**: Repeated situational patterns. "When I'm not listened to in meetings" appearing 8 times across 10 interviews – that's a job.

**Noise**: Feature requests phrased identically. "Better communication tools" appearing 8 times – that's parroting, not a job.

The difference: situations reveal context, feature requests don't. The same feature request from 8 people can mean 8 different jobs.

---

## From Interview to Hypothesis

1. **Capture the messy transcript** – don't clean it up yet
2. **Extract situational triggers** – look for "When..." patterns
3. **Map to four forces** – which force is each statement expressing?
4. **Identify the dominant blocker** – is it pushes too weak, or habits/anxieties too strong?
5. **Write a testable hypothesis** – "If we reduce [anxiety/habit], customers in [situation] will switch because [push/pull]"

### Structuring each force

Use mandated formats to keep them concrete:

- **Push/Pull**: "When [situation], I want [outcome] so I can [goal]"
- **Habit**: "[Current behavior] because [reason it persists]"
- **Anxiety**: "What if [feared consequence of switching]?"

---

## When to Use This Framework

- ✅ Discovery interviews for new products or competitive positioning
- ✅ Win/loss analysis – map the four forces for deals won and lost
- ✅ Migration and onboarding design – reduce the anxiety/habit side of the equation
- ✅ Pricing and packaging decisions – switching cost is part of the threshold
- ✅ Churn analysis – what pushed them past the threshold to leave?

---

## Integration with Existing Frameworks

### Connection to Prospect Theory
Loss aversion explains *why* habits and anxieties dominate. Losing a familiar workflow feels ~2x worse than gaining a better one. This framework gives you the structure to identify and address that asymmetry.

### Connection to Value Proposition Writing
Your value prop should address all four forces, not just pulls. "Faster routing" is a pull. "Zero-downtime migration with your existing CRM config" reduces anxiety. The second might matter more.

### Connection to Inversion Framework
Run inversion on your onboarding/migration flow. "How could switching to us go wrong?" directly maps to the Anxieties force.

---

## Anti-Patterns

- ❌ **Feature-only positioning** – Listing what your product does without addressing why switching is hard
- ❌ **Ignoring the incumbent** – Not researching what habits customers have built around the current tool
- ❌ **Asking "Would you switch?"** – Hypothetical intent is worthless. Ask about past behavior and current pain.
- ❌ **Treating all four forces equally** – Usually one force dominates. Find it and focus there.

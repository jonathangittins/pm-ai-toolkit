---
title: Next Feature Fallacy
type: framework
tags:
  - prioritisation
  - retention
  - product-management
---

# Next Feature Fallacy

*Based on Andrew Chen's "The Next Feature Fallacy"*

## Core Principle

**Building the next feature almost never fixes a retention or engagement problem.** The belief that "this next feature will change everything" is persistent, seductive, and almost always wrong. New features only reach already-engaged users. If people aren't using what you've already built, adding more won't save you.

---

## The Engagement Wall

Every product has an engagement wall – the threshold of investment required before a user reaches a feature.

```
[All visitors] → [Signed up] → [Completed setup] → [Used core feature] → [Power user]
   1,000            200              80                   30                  8
```

Features sit at different points along this funnel:

| Position | Reach | Example |
|---|---|---|
| Before the wall (onboarding, activation) | High – most users see it | First-run experience, setup wizard |
| At the wall (core loop) | Medium – engaged users see it | Main workflow improvements |
| Behind the wall (power features) | Low – only power users reach it | Advanced configuration, bulk operations |

**The fallacy**: Stakeholders push for features behind the engagement wall because vocal power users request them. But those features reach a tiny fraction of the user base and have almost no effect on the retention curve.

---

## When to Apply This Framework

Use this as a diagnostic when:

1. **Retention is flat or declining** and the proposed solution is "build feature X"
2. **A stakeholder cites customer requests** as justification for a feature (vocal users ≠ representative users)
3. **Roadmap debates** pit activation/onboarding work against power-user features
4. **Customer feedback** clusters around advanced functionality while basic activation metrics are weak

---

## The Diagnostic

### Step 1: Where does the drop-off happen?

Map your actual funnel with real numbers. Where do most users fall off?

- If they fall off before reaching the core feature → **activation problem**, not a feature problem
- If they use the core feature once but don't return → **value delivery problem**, not a feature problem
- If they use it regularly but churn after months → **now** you might have a feature/value gap

### Step 2: Where does the proposed feature sit?

Plot the requested feature on the engagement funnel:

- **Before the wall**: High impact on retention curve. Prioritise.
- **At the wall**: Moderate impact. Worth doing if the core loop is solid.
- **Behind the wall**: Low impact on overall retention. Only prioritise if power-user expansion revenue justifies it.

### Step 3: What's the reach?

Estimate what percentage of your user base will ever encounter this feature. If it's < 20%, it cannot meaningfully move retention.

---

## Counter-Arguments (and when they're valid)

| Argument | When it's valid | When it's the fallacy |
|---|---|---|
| "Customers are asking for it" | When the request comes from a representative sample AND the feature is in front of the wall | When 5 vocal power users want it and 500 churned users never said anything |
| "Competitors have it" | When it's table-stakes for the category and prospects won't buy without it | When competitors built it for a different segment or it's behind their engagement wall too |
| "It'll reduce churn" | When churn interviews specifically cite this gap AND the users who churned were past the engagement wall | When you're guessing at the churn reason |
| "It'll drive expansion" | When you have data showing power users expand when given advanced features | When you're hoping expansion will happen |

---

## What to Do Instead

When the real problem is activation or retention, these interventions have higher reach:

1. **Improve onboarding** – reduce time-to-value, add guided first experiences
2. **Fix the core loop** – make the thing users came for work better, faster, more reliably
3. **Remove friction** – delete steps, reduce required configuration, smart defaults
4. **Triggered re-engagement** – emails, notifications, or nudges at the moment users drift
5. **Better error handling** – users who hit errors and get no help don't come back

---

## Integration with Existing Frameworks

### Connection to Five Step Improvement Framework
Step 2 is "Delete." The Next Feature Fallacy is the prioritisation version of this – before building the next thing, ask whether you should delete or improve something that already exists.

### Connection to Inversion Framework
Spec Inversion Question 2 asks "How could we build way more than needed?" The Next Feature Fallacy is the strategic version – the feature itself might be unnecessary for the goal.

### Connection to Barbell Strategy
Power-user features belong on the high-risk/high-upside end of the barbell only if they drive measurable expansion revenue. Otherwise, they're the dangerous middle – moderate effort, moderate impact, consuming resources that could go to activation work (safe side) or genuine innovation (high-upside side).

### Connection to Customer Feedback Triage
During weekly feedback review, apply this lens: does the requested feature sit before or behind the engagement wall? Tag accordingly and weight activation-stage requests higher.

---

## Anti-Patterns

- **Building for the vocal minority**: 10 loud requests ≠ a retention fix for 1,000 silent churners
- **Feature-counting against competitors**: Parity on features behind the wall doesn't win deals if your activation is worse
- **"Just one more feature"**: If the last 3 features didn't move retention, the 4th won't either. The problem is upstream.
- **Confusing customer satisfaction with retention**: Happy power users who request features are already retained. The users you need to worry about aren't talking to you.

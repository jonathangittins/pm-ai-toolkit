---
title: Gittins Index & Explore-Exploit
type: framework
tags:
  - prioritization
  - uncertainty
  - roadmap
---

# Gittins Index & Explore-Exploit

*Based on John Gittins's 1979 proof for the multi-armed bandit problem. Thompson Sampling (1933) provides a practical implementation.*

## Core Principle

**Uncertainty isn't just risk to be avoided – it's information waiting to be purchased. The question is whether the price is right.**

Every item on your roadmap is a slot machine with an unknown payout. Each "pull" (a spike, POC, design partner, or shipped increment) gives you data. The Gittins index scores each option by combining what you know so far with how much you could still learn – and proves that always picking the highest-scoring option is mathematically optimal.

---

## The Mechanics

Each option has two components:

| Component | What it means | Example |
|-----------|--------------|---------|
| **Expected value** | Best estimate of payoff based on current data | "Marketo integration drives expansion revenue – we have 12 accounts asking" |
| **Uncertainty** | How much your estimate could change with more information | "First-party de-anonymisation could be transformative, or it could be technically infeasible – we don't know yet" |

The index combines both. High expected value scores well. High uncertainty *also* scores well – because a cheap pull could reveal something huge.

**Options with moderate expected value and low uncertainty score worst.** They won't surprise you, and they won't teach you anything.

---

## Thompson Sampling – The Practical Version

Computing a true Gittins index is mathematically involved. Thompson Sampling is the pragmatic alternative:

1. Keep a rough probability distribution for each option (even informally – "probably good", "could go either way", "long shot but huge if it works")
2. When prioritising, don't just rank by expected value – occasionally let uncertain-but-plausible options through
3. Update your estimates after each pull

**The 1-in-6 rule**: If you're delivering ~6 features per quarter across your teams, Thompson Sampling justifies making one of those a deliberate bet on something uncertain. This isn't gut feel – it's a mathematically sound allocation to resolve uncertainty.

---

## When to Use This Framework

- **Quarterly/half planning** – scoring candidate features by expected value AND uncertainty, not just expected value
- **Deciding between "keep investing" and "try something new"** – the index gives you a principled answer
- **Justifying exploratory work** – "we're buying information" is more defensible than "we have a hunch"
- **Evaluating the "later" bucket** – items with moderate value and low uncertainty are the least useful; they occupy mental real estate without earning it

---

## The Cost of a Pull Changes Everything

The framework's applicability depends on how expensive it is to test an option:

| Pull cost | Implication | Example |
|-----------|-------------|---------|
| **High** (full engineering build) | Exploration is expensive; lean toward safe bets | Traditional product development |
| **Medium** (design partner + spike) | Balanced explore/exploit | Structured discovery |
| **Low** (PM-built POC, AI-assisted prototype) | Exploration is cheap; the model becomes highly applicable | AI-assisted POCs where the PM or designer pulls the arm directly |

As AI tools reduce the cost of prototyping and validation, product development becomes *more* Gittins-shaped – switching costs drop, pulls get cheaper, and the economics of exploration improve.

---

## Applying It

1. **List your candidate features** with two columns: expected value (what you know) and uncertainty (what you don't)
2. **Flag the middle ground** – moderate value, low uncertainty items that feel productive but won't teach you anything or move the needle
3. **Ensure at least one high-uncertainty option** makes the cut each quarter
4. **Design cheap pulls** – what's the smallest thing you could do to dramatically update your estimate? A customer conversation, a data query, a weekend POC?
5. **Reassess after each pull** – the whole point is that scores change as you learn

---

## Source

- Gittins, J.C. (1979). "Bandit Processes and Dynamic Allocation Indices." *Journal of the Royal Statistical Society.*
- Thompson, W.R. (1933). "On the Likelihood that One Unknown Probability Exceeds Another in View of the Evidence of Two Samples." *Biometrika.*

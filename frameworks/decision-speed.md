# Decision Speed Framework

*Based on Jeff Bezos's Type 1/Type 2 decision framework ([2016 Letter to Shareholders](https://www.sec.gov/Archives/edgar/data/1018724/000119312516530910/d168744dex991.htm))*

## Core Principle
**Not all decisions are created equal.** The process should match the stakes and reversibility. The biggest PM mistake is treating all decisions like one-way doors when most are two-way doors.

---

## The Two Types of Decisions

### Type 1: One-Way Doors (Slow Down)
**Definition:** Decisions that are irreversible or very costly/time-consuming to reverse.

**Characteristics:**
- Difficult or impossible to undo
- High cost to reverse (time, money, reputation, relationships)
- Long-term consequences
- Affects many people or systems
- Requires significant investment before you know if it works

**Examples:**
- Choosing core architecture or data models
- Committing to long-term partnerships or contracts
- Sunsetting a product or major feature
- Hiring decisions (especially senior roles)
- Major pricing model changes
- Migration to new platforms or fundamental infrastructure
- Acquisitions or major strategic pivots

**Appropriate Process:**
- Involve key stakeholders early
- Gather significant data and research
- Run small pilots or proof-of-concepts when possible
- Document assumptions, risks, and decision rationale
- Build alignment before acting
- Consider second-order effects and dependencies
- Accept slower decision-making as appropriate

**Questions to Ask:**
- "If this goes wrong, how hard is it to undo?"
- "What's the cost of reversing this decision?"
- "How many other decisions does this one lock in?"
- "What can we do to reduce the cost of being wrong?"
- "Do we need to make this decision now, or can we wait for more information?"

---

### Type 2: Two-Way Doors (Speed Up)
**Definition:** Decisions that are easily reversible with minimal cost.

**Characteristics:**
- Easy to undo or iterate
- Low cost to reverse
- Limited scope of impact
- Fast feedback loops
- Can learn by doing

**Examples:**
- UI copy or minor design changes
- Feature flag rollouts or A/B tests
- Experiment parameters
- Internal process tweaks
- Sprint prioritization decisions
- Email campaign copy
- Meeting frequency or format
- Dashboard configurations
- Most feature toggles or settings

**Appropriate Process:**
- Make the decision quickly (hours or days, not weeks)
- Document the decision and reasoning briefly (even just in Slack or a ticket)
- Set a review date to assess outcomes (1-2 weeks out)
- Monitor key metrics
- Be willing to reverse if data shows it's not working
- Bias toward action over analysis
- Empower individuals or small teams to decide

**Questions to Ask:**
- "If this doesn't work, can we roll it back next week?"
- "What's the fastest way to test this?"
- "What will we learn by trying this?"
- "What's the cost of delaying this decision?"
- "Do we really need consensus here, or can we just try it?"

---

## Decision-Making Anti-Patterns

### Red Flags: Treating Type 2 Like Type 1 (Over-Engineering)

You're probably over-thinking it if you're:
- Scheduling meetings to discuss minor UI copy
- Seeking consensus from 5+ people for a feature flag experiment
- Spending weeks debating something you can A/B test in days
- Requiring executive approval for easily reversible changes
- Delaying a decision "until we have more data" when the data can only come from trying it
- Writing extensive documentation for a 1-week experiment
- Forming committees or working groups for tactical decisions

**Cost of This Mistake:**
- Slow velocity and missed opportunities
- Team frustration and reduced autonomy
- Analysis paralysis
- Competitors move faster
- Death by a thousand meetings

---

### Red Flags: Treating Type 1 Like Type 2 (Moving Too Fast)

You're probably moving too fast if you're:
- Making architectural decisions alone without engineering input
- Launching major pricing changes without customer research
- Committing to partnerships without legal/finance review
- Sunsetting features based on gut feel without usage data
- Rushing decisions that affect team structure or long-term strategy
- Ignoring technical debt implications
- Skipping stakeholder alignment on irreversible changes

**Cost of This Mistake:**
- Expensive mistakes that are hard to fix
- Damaged relationships and trust
- Technical debt that compounds
- Misalignment and rework
- Opportunity cost of the wrong path

---

## How to Apply This Framework

### Before Making Any Significant Decision:

**Step 1: Classify the Decision**
Ask: "Is this a one-way or two-way door?"

**Step 2: Assess the Costs**
- "What's the cost of being wrong?"
- "What's the cost of being slow?"
- "How long until we get feedback?"

**Step 3: Choose the Appropriate Process**
- **Type 2**: Bias toward speed and learning
- **Type 1**: Bias toward diligence and alignment

**Step 4: Document and Review**
- **Type 2**: Brief note + review date
- **Type 1**: Formal decision doc + stakeholder alignment

---

## Decision Speed Matrix

| Decision Type | Timeline | Stakeholders | Documentation | Reversibility |
|--------------|----------|--------------|---------------|---------------|
| **Type 1** | Days to weeks | Broad alignment needed | Formal decision doc, assumptions, risks | Costly or impossible |
| **Type 2** | Hours to days | Individual or small team | Brief note or ticket | Easy and cheap |

---

## Common PM Traps

### "We Need More Data"
- **For Type 1**: Often true – invest in research
- **For Type 2**: Usually procrastination – the fastest way to get data is to try it

### "Let's Get Alignment"
- **For Type 1**: Critical – build consensus
- **For Type 2**: Often unnecessary – inform and move

### "Let's Run an Experiment"
- **For Type 1**: Good idea if you can make it reversible
- **For Type 2**: You're already in an experiment – just try it

### "What if We're Wrong?"
- **For Type 1**: Serious risk – plan carefully
- **For Type 2**: Not a big deal – you'll reverse it quickly

---

## Success Metrics

**You're using this framework well when:**
- Your team ships Type 2 changes daily/weekly without bottlenecks
- Type 1 decisions have clear documentation and stakeholder buy-in
- You rarely make expensive mistakes on irreversible decisions
- Your velocity increases because you're not over-analyzing reversible decisions
- Team members feel empowered to make Type 2 decisions autonomously
- You can articulate why you classified a decision as Type 1 vs Type 2

**You're struggling with this framework if:**
- Everything feels like a Type 1 decision (you're being too cautious)
- You're making lots of Type 1 decisions quickly (you're moving too fast)
- Team members are unclear when they can decide vs. when they need approval
- You're frequently rolling back decisions you thought were Type 2 but were actually Type 1

---

## Final Reminder

**Default to Type 2 thinking.** Most decisions are more reversible than they feel in the moment. When in doubt, ask: "What would it take to make this more reversible?" Often, you can convert a Type 1 into a Type 2 with feature flags, phased rollouts, or pilot programs.

**The best PMs are fast on Type 2 decisions and thoughtful on Type 1 decisions.** Speed is a competitive advantage, but only when applied to the right problems.

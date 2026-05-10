---
title: Five Step Improvement Framework
type: framework
tags:
  - process-improvement
  - prioritization
  - simplification
---

# Five Step Improvement Framework

## Core Principle
**The steps must be followed in order.** Going backward (automating first, then simplifying, then deleting) leads to wasted effort.

---

## The Five Steps

### Step 1: Make Requirements Less Dumb
**Premise:** All requirements are dumb to some degree, regardless of their source.

**Questions to Ask:**
- Why does this requirement exist?
- Who originally requested this and what problem were they solving?
- Is this requirement based on a constraint that no longer exists?
- Are we building this "just in case" or because we definitely need it?
- What happens if we don't implement this at all?

**Red Flags:**
- "Smart person X said we need this" (question it anyway)
- "We've always done it this way"
- "We might need this later"
- Requirements that bundle multiple unrelated needs

---

### Step 2: Delete the Part or Process
**Premise:** If you're not occasionally adding things back in, you're not deleting enough.

**Questions to Ask:**
- Can we completely remove this feature/step/requirement?
- What's the actual impact if this doesn't exist?
- Is this solving a real problem or a hypothetical one?
- Can we achieve the goal without this component?
- Are we keeping this out of fear rather than necessity?

**Action Items:**
- List everything you can delete
- Challenge each deletion: "What breaks if this is gone?"
- Track what you add back (should be <10% of deletions)

---

### Step 3: Simplify or Optimize
**⚠️ Only proceed after Steps 1 and 2 are complete**

**Questions to Ask:**
- How can we make this simpler for users?
- Can we reduce the number of steps/clicks/decisions?
- What's the most straightforward way to achieve this?
- Are we over-engineering this solution?
- Can we consolidate or streamline this process?

**Action Items:**
- Reduce complexity before optimizing performance
- Combine similar or redundant elements
- Remove edge cases that serve <5% of users

---

### Step 4: Accelerate Cycle Time
**⚠️ Only proceed after Steps 1-3 are complete**

**Questions to Ask:**
- What's slowing this down?
- Can we reduce wait times, approval layers, or handoffs?
- Are we being too cautious where speed matters more?
- What can we do in parallel instead of sequentially?
- How can we shorten feedback loops?

**Action Items:**
- Identify bottlenecks in the workflow
- Reduce unnecessary review cycles
- Enable faster iteration and deployment

---

### Step 5: Automate
**⚠️ Final step only - after Steps 1-4 are complete**

**Questions to Ask:**
- Now that this process is essential, simple, and fast, what should we automate?
- What repetitive tasks remain?
- Will automation lock us into this approach?
- Is manual execution actually problematic at current scale?

**Action Items:**
- Automate only what remains after deletion and simplification
- Ensure automation doesn't prevent future improvements
- Prioritize automation of high-frequency, low-complexity tasks

---

## Application Checklist

### For New Features
- [ ] Step 1: Challenge every requirement in the spec
- [ ] Step 2: List features to delete before building anything
- [ ] Step 3: Simplify remaining features to their essence
- [ ] Step 4: Design for fast iteration and deployment
- [ ] Step 5: Identify automation opportunities in the final design

### For Existing Features
- [ ] Step 1: Review current requirements - are they still valid?
- [ ] Step 2: What can we remove entirely?
- [ ] Step 3: How can we simplify what remains?
- [ ] Step 4: Where can we reduce friction or latency?
- [ ] Step 5: What's worth automating now?

### For Internal Processes
- [ ] Step 1: Question why each step exists
- [ ] Step 2: Delete unnecessary steps, approvals, documentation
- [ ] Step 3: Streamline remaining workflow
- [ ] Step 4: Reduce cycle time and handoffs
- [ ] Step 5: Automate repetitive elements

---

## Common Mistakes to Avoid

❌ **Going backward through the steps** - Don't automate first, then simplify, then delete
❌ **Skipping Step 2** - Optimizing something that shouldn't exist
❌ **Being too conservative with deletions** - If you never add anything back, delete more
❌ **Automating too early** - Locking in complexity before simplification
❌ **Treating "smart person requirements" as sacred** - Question everything

---

## Success Metrics

**You're doing it right when:**
- You occasionally have to add back 5-10% of what you deleted
- Team members are initially uncomfortable with deletions
- Features ship faster with fewer components
- Automation only touches truly necessary processes
- Requirements get challenged regularly, regardless of source

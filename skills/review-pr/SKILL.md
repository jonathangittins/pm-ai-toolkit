---
name: review-pr
description: Review engineering PRs from a PM perspective. Use when the PM shares a GitHub PR link and asks for review, initial thoughts, or feedback. This is a product review, not a code review.
---

# Review PR (PM Perspective)

PM reviews engineering PRs to provide product direction, not code feedback. The output is a concise review comment addressing the engineer's questions through a product lens.

## Principles

- **Product lens, not code lens.** Do not comment on code quality, patterns, race conditions, indexes, or technical implementation. Those are for engineering reviewers.
- **Lead with the user scenario.** Before analysing any code, identify what product flow this enables. Present findings as "here's the user scenario" not "here's what the code does."
- **Translate code questions to product questions.** When an engineer asks "should I populate this field?", reframe as "is there a use case where the caller needs this information?"
- **Play out the scenario.** Walk through what happens from the end-user or API consumer's perspective. This is where product insight emerges.
- **Write in the PM's voice.** Direct, no fluff. No "nice work on X" or "looks good overall" -- just the product direction. No jargon the engineer didn't use.

## Workflow

### 1. Fetch the PR

```
gh pr view {number} --repo {org/repo} --json title,body,state,author,files,commits
gh pr diff {number} --repo {org/repo}
gh api repos/{org/repo}/pulls/{number}/comments
gh api repos/{org/repo}/pulls/{number}/reviews
```

### 2. Identify product context

Before reading code, answer:
- **Which product area?** Map to your product portfolio
- **Which team/PM owns this?** Check CLAUDE.md team structure table
- **What user flow does this enable?** State it in one sentence from the customer's perspective
- **Who consumes this?** End-users, API consumers, internal services, other products

Use semantic search, specs, and synthesis docs to build context. Do not guess -- if unsure about product ownership or consumer, say so.

### 3. Find the engineer's questions

Read PR comments and self-review notes. Engineers often flag their own uncertainties. These are the actual review requests -- not the code diff.

### 4. Translate and respond

For each question the engineer raised:
1. Restate as a product question
2. Play out the user scenario that answers it
3. State the product direction

### 5. Check for documentation gaps

If the PR introduces a new API pattern, flow, or consumer-facing behaviour, flag whether it needs documentation. Say "for customers using this programmatically" or whatever fits the audience.

### 6. Draft the review

- One section per question the engineer asked, addressed through the product lens
- Add a docs line if applicable
- Do not include: code quality feedback, technical suggestions, approval/rejection language, compliments
- Present draft to the PM for review before posting

## Anti-patterns

| Pattern | Fix |
|---|---|
| Leading with "looks good, nice work" | Skip it. Not the PM's role to grade engineering work. |
| Commenting on race conditions, indexes, error handling | Leave for engineering reviewers. |
| Guessing the consumer without checking | Check product context first. Ask if unsure. |
| Framing around code ("this adds a new endpoint") | Frame around the user ("this lets a chatbot check booking eligibility before asking for times") |
| Using jargon the PM wouldn't say | Write how the PM talks. Direct, plain. |
| Presenting 6 findings when the engineer asked 2 questions | Answer what was asked. |

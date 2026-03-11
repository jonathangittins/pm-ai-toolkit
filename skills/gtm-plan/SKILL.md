---
name: gtm-plan
description: >
  Create Go-To-Market plans through a guided, section-by-section workflow.
  Use when creating a GTM plan for a feature, or when someone mentions
  "GTM", "go-to-market", or "launch plan".
---

# GTM Plan Skill

Create Go-To-Market plans through a guided, section-by-section workflow.

## GTM Process Overview

### PM Responsibilities

Once a PM knows a feature is coming:

1. Create a new GTM plan entry in the GTM log (a shared tracker – spreadsheet, Notion database, or similar – where all active GTM plans are listed)
2. Fill out the GTM plan – define tier (1, 2, or 3), clarify scope, problem solved, and ICP fit
3. Provide product brief: vision, goals, roadmap context
4. Confirm technical readiness and release date
5. Supply demos, screenshots, and mockups
6. Validate messaging accuracy with PMM
7. Identify power users, beta testers, or early adopters as advocates
8. Provide technical enablement and demos for internal teams and external customers
9. Schedule first sync with PMM to walk through the GTM plan and demo

### Product x Marketing GTM Process

**Step 1: Kickoff (4-6 weeks before launch)**
- PM books a call with PMM
- Both use GTM template to agree on release date, campaign date, and plan B
- PM fills in feature vision, problem solved, ICP, and usage details
- PM shares demos, screenshots, or mockups if available
- PMM takes over V1, builds narrative, and plans GTM assets
- Both define early success metrics: pipeline, adoption, activation

**Step 2: Alignment**
- PMM drafts messaging and first asset versions
- PM reviews and ensures accuracy
- Internal workshops: PM shows how product works; PMM explains market positioning and customer story

**Step 3: Execution**
- PMM finalises assets: website, blog, email, social, ads, enablement
- PM x PMM refine pitch decks, battlecards, and FAQs
- Sales Enablement prepares training and talk tracks
- CS and RevOps are briefed to support customers and tracking

**Step 4: Launch**
- PMM runs campaign across all channels: website, blog, email, social, ads
- Sales uses new decks, talk tracks, and messaging in outreach and demos
- CS shares updates with customers and supports adoption

---

## Skill Workflow

### Phase 1: Discovery

1. **Identify the feature** – Ask which feature/product area if not specified
2. **Locate supporting artifacts** – Search for existing documentation:
   - Spec or Decision Memo
   - Release notes
   - Support article
   - Feature summary
3. **Get customer evidence** – Search your feedback tool (e.g. Canny, Productboard, UserVoice) for customer quotes, request counts, and account data

### Phase 2: Create GTM Document

Create the GTM plan file using the template in [references/template.md](references/template.md). Read the template before creating the file.

### Phase 3: Section-by-Section Walkthrough

Work through each PM-owned section interactively:

1. **Classification checkboxes** – Propose based on context, ask for confirmation
   - Consider: Is this catch-up/parity or differentiation?
   - Business impact ≠ most requested – actual deal impact matters
   - Add a "Note:" line if classification needs context

2. **Why did we build** – Draft from spec/decision memo problem statement

3. **What does it do** – Draft from spec solution section or feature summary

4. **Who is it meant for** – Format as: "Setup: [who sets it up]. Benefit: [who benefits and how]"

5. **Customer requests** – Pull quotes from feedback tool
   - Use multiple table rows for multiple quotes
   - Format: `| **Company** (email): "Quote" |`
   - Add source link at bottom

6. **Value Propositions** – Draft 2-4 value props based on customer pain points
   - Read the value proposition framework at `../../frameworks/value-proposition.md`
   - Apply the Opposite Test to each value prop before presenting: if the opposite claim is absurd ("confusing interface", "slow setup"), rewrite it with specific differentiation instead

7. **ICP** – Ask if specific segment or broadly applicable

8. **Solution/Feature Breakdown** – Include:
   - Official external name
   - Location in product
   - Setup steps
   - Video walkthrough link if available

9. **Use cases** – List primary use cases, note any "coming soon" items

10. **Competitor Analysis** – Draft based on spec competitive landscape section
    - Frame as parity vs differentiation
    - Be honest about gaps

11. **Timelines & Goals** – Note ship date, rollout status, help centre link if live

12. **Success Metrics** – Use "Current Status" column header, track adoption

13. **Limitations** – Optional section at end for notable feature limitations

### Phase 3b: Template Compliance Check (subagent)

**Delegate verification to a subagent** so it reviews the document cold – without the drafting conversation's context.

Write the drafted GTM plan to its target file (e.g. `gtm-plans/{feature-name}-gtm-plan.md` or wherever your team stores GTM documents), then pass the subagent these instructions:

> Read the GTM plan at `[FILE_PATH]` and the GTM skill template (the Phase 2 template section). Verify:
>
> 1. **All sections present** – Every section from the template exists, including PMM-owned sections (GTM Launch Playbook, Messaging/Strategic narrative, GTM Tasks). PMM sections should be blank but structurally intact – never delete them.
> 2. **No invented details** – Every claim, quote, metric, and competitive comparison must trace to a source document. Flag anything that reads like inference rather than sourced fact.
> 3. **Formatting matches template** – Table structures, heading levels, checkbox format, and "Owner:" labels match exactly.
> 4. **Classification consistency** – The tier assignment is consistent with the strategic importance, feature type, and business impact checkboxes.
> 5. **Customer quotes** – If the Customer requests section has quotes, verify they look like real verbatim quotes (not paraphrased summaries).
>
> **Return ONLY:**
> ```
> PASS – no issues found
> ```
> or
> ```
> ISSUES:
> - [issue description with section name]
> ```

If issues are found, fix them before presenting the document.

### Phase 3c: Reader Review (subagent)

**Delegate to a separate subagent** – distinct from the compliance check – so it reads the document cold as a PMM or sales leader would.

Pass the subagent the GTM plan file and these instructions:

> You are reading this GTM plan for the first time with no prior context about the feature. Review it as if you are the Product Marketer who needs to build a campaign from this document, or a Sales leader who needs to understand what's launching and why it matters.
>
> Check:
>
> 1. **Can you explain the feature after reading?** – If the "What does it do" and "Solution/Feature Breakdown" sections don't give you a clear mental model, flag what's missing.
> 2. **Do the value propositions say something?** – For each value prop, ask: would the opposite be absurd? If yes, the claim is generic and needs rewriting.
> 3. **Customer evidence connects to the narrative** – Do the customer quotes support the "Why did we build this" section? Flag any disconnect between what customers asked for and how the feature is positioned.
> 4. **Use cases are concrete** – Each use case should describe a specific scenario with a specific persona. Flag any that read as abstract capabilities rather than real situations.
> 5. **Gaps for PMM handoff** – Is there enough context for a PMM to draft messaging without scheduling a follow-up call? Flag missing details: who sets it up vs who benefits, where it lives in the product, how it compares to competitors.
>
> **Return ONLY:**
> ```
> PASS – no issues found
> ```
> or
> ```
> ISSUES:
> - [issue description with section name]
> ```

If issues are found, fix them before presenting the document.

### Phase 4: Handoff

- Leave Product Marketing sections blank (GTM Launch Playbook, Messaging, GTM Tasks)
- GTM log line stays empty – PMM will add their tracking link
- Remind PM of next steps:
  - Schedule kickoff sync with PMM
  - Prepare demos, screenshots, mockups for PMM handoff

---

## Section Ownership Reference

**PM-owned:**
- Project Overview (all subsections)
- Customer requests
- Value Propositions
- ICP
- Solution/Feature Breakdown
- Competitor Analysis
- Limitations
- Success Metrics (shared)
- Timelines & Goals (shared)

**Product Marketing-owned:**
- GTM Launch Playbook
- Messaging/Strategic narrative
- GTM Tasks

---

## Tips

- "Most requested" ≠ "highest impact" – ask about actual deal losses
- Internal optics can be a valid strategic reason – capture it with a "Note:" line
- For parity features, frame competitor analysis honestly as catch-up
- If ICP is "everyone", say so – don't invent segments
- Tier guidance:
  - **Tier 1 – Full GTM**: Very strategic, brand-new offering, high business impact
  - **Tier 2 – Focused GTM**: Strategic, major enhancement, medium impact
  - **Tier 3 – Lightweight GTM**: Nice to have, minor improvement, low impact

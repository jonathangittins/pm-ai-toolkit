---
name: spec-prd
description: >
  Write product specifications and PRDs through a phased workflow: gather context,
  research customer evidence, draft using a standard template, conduct competitive
  research, test with a fresh sub-agent, and publish to a wiki.
---

# Spec / PRD Skill

Write product specifications through a phased workflow: gather context, research customer evidence, draft using a standard template, conduct competitive research, iterate, and publish.

## Prerequisites

- A spec template in your repo (this skill reads it before every draft)
- A customer feedback tool (e.g. Canny, Productboard, or similar) accessible via MCP or API
- A wiki for publishing (e.g. Confluence, Notion) accessible via API

## Workflow

### Phase 1: Context Gathering

1. **Identify the feature** – which product area, what problem, who it's for
2. **Accept context in any form** – voice transcriptions, bullet points, links, stream of consciousness. Don't force structure at this stage.
3. **Ask clarifying questions** – but only after the user has done their initial dump. Focus on gaps that affect the spec structure: scope boundaries, success metrics, rollout approach.

### Phase 2: Customer & Competitive Evidence

Always search before the first draft, even if the user hasn't provided links. Customer evidence shapes the problem statement, use cases, and business value – not just a "Customer Requests" section.

Two complementary layers:

**Customer feedback** (what customers ask for) – your feedback tool, support tickets, review sites:
- Request count, linked accounts, priority ratings
- Account names, revenue data from idea/request fields
- Verbatim customer quotes – these go in the Evidence section and inform the Problem statement
- Source attribution (sales calls, support tickets, review sites)

**Sales/competitive evidence** (why deals are won or lost) – CRM closed-lost analysis, win/loss interviews, G2/review-site themes:
- Closed-lost reasons mentioning this feature area or gap
- Product gap patterns across multiple deals
- Customer quotes from deal analyses – these come from sales cycles, not support/feedback
- Competitor win rates and positioning for the Competitive Landscape section in Phase 4

**How both feed the spec:**
- **Problem / Current State** – loss patterns show the cost of not solving this
- **Why Now** – closed-lost ARR quantifies urgency
- **Evidence & Inputs** – deal quotes alongside feedback quotes (mark the source clearly)
- **Competitive Landscape** – sales-intel adds quantitative win-rate data that web research can't surface

### Phase 3: First Draft

1. **Read your spec template** before every draft
2. **Create the spec file** in the appropriate product directory
3. **Include every section** from the template. If content is unknown, use `TBD – description` placeholders. Never silently drop sections.
4. **Populate the metadata table** – link to tracking tickets and feedback URLs. Fill in PM name; leave others as `TBD`.

**Section guidance:**

- **Executive Summary**: Max 5 bullets. If this isn't clear, the spec isn't ready.
- **Problem / Current State**: Describe workarounds users have today. This is where customer quotes add texture.
- **Why Now**: Concrete numbers – customer requests, lost deals, blocked pipeline, competitive pressure.
- **Hypothesis & Business Value**: "Building X will result in Y because Z." Include addressable market and directional revenue.
- **Evidence & Inputs**: Table with Account, Status (Lost/Open/Existing), and verbatim Quote columns. Include the feedback tool URL.
- **Use-cases**: Concrete scenarios from the user's perspective, not abstract capabilities.
- **Solution**: Break into numbered Requirements. Each requirement gets its own subsection with behaviour, settings UI, and technical details.
- **Considerations / Rabbit Holes**: Future directions, edge cases, open questions. Use this for things worth flagging but not building now.
- **Out of Scope**: Explicit boundaries. Include enough context so it's clearly a deliberate decision, not an oversight.
- **Limitations**: Known constraints of what IS being built. Feeds GTM and support articles.
- **Competitive Landscape**: Table with Competitor, What they offer, and Gap columns. Populated in Phase 4.
- **Measuring Success**: Tie back to the hypothesis. North star metric + 2-3 supporting metrics.
- **Glossary**: Only terms introduced by this feature or that have domain-specific meaning.
- **Key Decisions Log**: Product/scope decisions and *why* (deferred to V2, dropped, chose A over B). Different from Changelog – this is product memory, not document history. Add entries as decisions are made.
- **Timeline**: *Optional.* Include only when stakeholders need a visible milestone view (launches, exec readouts, multi-team coordination). Skip for most specs – your tracker covers delivery state.
- **Changelog**: 3-5 meta-event rows with links – not commit-level edits. Version control covers the line-level audit trail. Skip rows like "fixed typo".
- **Original review footer**: One line linking to the PR or thread where the spec was first reviewed. Lets future readers find the discussion behind the current shape.

### Phase 4: Competitive Research

Conduct web research for the competitors the user identifies (typically 2-4). For each competitor:

- Search for their relevant feature documentation
- Document what they offer, how it works, and gaps relative to the proposed solution
- Be honest about where competitors are ahead
- Frame as parity vs differentiation where relevant

Populate the Competitive Landscape table and add a summary paragraph below it contextualising the findings.

### Phase 5: Iteration

Present the draft and iterate on user feedback. Typical feedback areas:
- Terminology corrections (product-specific language matters)
- Scope adjustments (move things between Solution, Considerations, and Out of Scope)
- Accuracy of customer evidence and competitive positioning
- Missing edge cases or use cases

Make surgical edits – don't rewrite entire sections unless asked.

### Phase 6: Reader Testing

Before publishing, test the spec with a fresh sub-agent that has no conversation context – only the spec content. This catches assumptions and ambiguities that are invisible after multiple rounds of editing.

**Run via a sub-agent:**

1. **Comprehension test** – Give the sub-agent the spec and ask 5-8 questions a reader would ask (e.g., "What happens when the toggle is off?", "How does detection work?", "What's out of scope?"). Check whether the spec answers them clearly without requiring context from the drafting conversation.

2. **Ambiguity and gap check** – Ask the sub-agent to flag:
   - Sentences that assume context the reader may not have
   - Contradictions between sections
   - Vague requirements that engineering would need to ask about
   - Missing information for any section that isn't marked `TBD`

3. **Coherence review** – Ask the sub-agent to check for redundancy across sections, filler that can be removed, and whether the Executive Summary accurately reflects the full spec.

4. **Pre-mortem (inversion pass)** – Have the sub-agent read the inversion framework at `../../frameworks/inversion.md` and run a pre-mortem against the spec. Using the framework's three phases (Spec, QA, Rollout), flag concrete ways this feature could fail – wrong requirements, silent failures, customer surprise, rollback difficulty, etc. Focus on failure modes specific to this feature, not generic risks.

**Act on results:** Fix any issues found, then present a summary to the user. If there are judgement calls (e.g., "this section assumes domain knowledge"), flag them for the user to decide.

### Phase 7: Wiki Publishing

Offer to publish when the user is satisfied. Convert the markdown spec to the wiki's storage format and create the page via API.

### Phase 8: Feature index update (optional)

If you maintain a feature index (a single file tracking each feature with its spec, release notes, and support article links), update the row for this feature:

- If the feature doesn't have a row yet, add one in the correct product area table
- Update the Spec column to link to the new/updated spec
- Fill in any other columns that are now known (tracking ticket, design link, etc.)
- If the feature's status has changed, update the Status column

### Phase 9: Roadmap card check (optional)

If you maintain a roadmap with a Kanban board, check whether the feature has a card in the **Shaping** column. If it does, ask:

> "Spec is done – move [initiative name] from Shaping → Next on the roadmap?"

If confirmed, edit the roadmap file to move the card. If the initiative isn't on the board, mention it – it may need adding.

## Writing Style

- **Dashes**: Don't use em dashes (—)
- **Tone**: Direct, matter-of-fact. State things plainly.
- **Avoid**: "key insight", "importantly", "crucially", "unlocks", "enables", "empowers", "drives"
- **Trust the reader**: Don't explain things product/engineering colleagues already know.
- **Active voice**: "CRM lookup needs email first" not "The unlock isn't building the lookup – it's getting the identifier first"
- **Let tables speak**: Don't narrate what a table already shows.
- **AI/LLM prompts**: When the feature involves system prompts or LLM behaviour, include example phrasings and expected outputs in the spec. Prompts increasingly require deep domain knowledge, so treat them as a PM deliverable – not something to hand off without context. Spec the intent and evaluation criteria, not just the copy.
- **System prompt examples**: Use "For example:" framing, not prescriptive wording. Engineering owns the final implementation.
- **Engineer-facing notes**: Don't add "@engineer" callouts in the spec body. Engineers are tagged in wiki comments, not inline.

## Key Patterns

**Why sub-agent verification matters:** After multiple rounds of editing, the author (human or AI) develops blind spots. The sub-agent in Phase 6 reads the spec cold – it doesn't know what was discussed, only what's on the page. This consistently catches assumptions that would confuse an engineer or designer reading the spec for the first time.

**Why customer evidence comes before the draft:** Searching your feedback tool before writing prevents the spec from being shaped entirely by internal assumptions. Customer quotes ground the problem statement in reality and often surface use cases the PM hadn't considered.

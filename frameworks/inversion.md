# Inversion Framework for Product Development

*Inspired by [Invert, Always Invert](https://theengineeringmanager.substack.com/p/invert-always-invert) – The Engineering Manager*

## Core Principle

**"Invert, always invert."** – Charlie Munger

Instead of asking "How do we succeed?" ask "How do we fail?" and systematically avoid those failure modes.

This framework applies inversion thinking across three critical phases:
1. **Feature Specification** – Before building
2. **Quality Assurance** – During building
3. **Feature Rollout** – When shipping

---

## When to Use This Framework

Apply inversion thinking for:
- New features with customer-facing impact
- Changes to core workflows or business logic
- Infrastructure or architectural changes
- Integration updates with external systems
- Changes affecting data integrity or security
- Features requiring gradual rollout

Skip it for:
- Trivial UI copy changes
- Internal tooling with no customer impact
- Hot fixes to P1 incidents (move fast, invert later in retrospective)

---

## Phase 1: Feature Specification Inversion

**Timing:** During PRD/spec creation, before engineering kickoff

**Goal:** Identify blind spots in requirements and design before building anything

### Setup (5 minutes)
- **Facilitator:** Keeps discussion moving, ensures all questions are addressed
- **Scribe:** Captures all failure modes and mitigation strategies
- **Participants:** PM, Engineering Lead, Designer, and 1-2 engineers who will build it
- **Scope:** The specific feature or change being spec'd

### Inversion Questions

#### 1. Requirements Failure
**"What would make our requirements completely wrong?"**

Prompts:
- What assumptions are we making about user behavior that could be false?
- Which requirements came from "smart person X said so" that we haven't validated?
- What if our target persona actually doesn't need this feature?
- Are we solving for a real problem or optimizing for a hypothetical edge case?
- What customer research are we missing?

**Document:** Assumptions that need validation before build starts

---

#### 2. Scope Creep & Bloat
**"How could we build way more than needed and waste engineering time?"**

Prompts:
- What features are "nice to have" that we're treating as "must have"?
- Which requirements exist "just in case" rather than for a known use case?
- What are we building that competitors don't have? (Is that differentiation or overbuilding?)
- Can we delete entire sections of this spec and still deliver value?
- What's the 20% of this spec that delivers 80% of the value?

**Document:** Features/requirements to delete or defer to v2

---

#### 3. Integration & Dependency Failure
**"What if every system we integrate with breaks or changes?"**

Prompts:
- What external systems do we depend on? (CRM, enrichment providers, calendar APIs, etc.)
- For each dependency: What breaks if it's slow? Unavailable? Returns unexpected data?
- Do we have fallback behavior for each dependency failure?
- What API rate limits could we hit?
- What happens if a customer's environment has custom objects or fields we didn't expect?

**Document:**
- Required integrations with fallback behavior defined
- API rate limit protections needed
- Error handling requirements

---

#### 4. User Experience Catastrophe
**"How could this feature make users hate us?"**

Prompts:
- What if this workflow takes MORE clicks than the current state?
- What if it's slower to load than what we're replacing?
- What if the UI is confusing and users can't figure out how to use it?
- What if it looks worse than the current design?
- What if we don't provide a way to opt out or revert?
- What if enterprise customers are surprised by this change with no warning?

**Document:**
- UX requirements (max clicks, load time targets)
- Rollout requirements (gradual, with opt-out)
- Customer communication requirements

---

#### 5. Technical Debt & Maintenance Nightmare
**"How could this become unmaintainable technical debt?"**

Prompts:
- What shortcuts are we taking now that will hurt us in 6 months?
- What assumptions are we hardcoding that might change?
- How would a new engineer understand this code?
- What would make debugging this feature extremely difficult?
- Are we creating a one-off solution instead of a reusable pattern?

**Document:**
- Technical constraints that must be respected
- Documentation requirements
- Test coverage expectations

---

### Output from Spec Inversion

**Updated PRD must include:**
1. **Validated Assumptions** – What we've confirmed is true vs. what still needs validation
2. **Deleted Requirements** – What we removed
3. **Dependency Map** – External systems, failure modes, and fallbacks
4. **Non-Functional Requirements** – Performance targets, error handling, monitoring
5. **Rollout Plan** – How we'll launch safely (see Phase 3)

---

## Phase 2: QA Inversion

**Timing:** During development sprint, before feature complete

**Goal:** Identify bugs and edge cases before they reach production

### Setup (5 minutes)
- **Facilitator:** QA Lead or Senior Engineer
- **Scribe:** Captures test cases and edge cases discovered
- **Participants:** PM, QA, Engineering Lead, Engineers who built it
- **Scope:** The specific implementation being tested

### Inversion Questions

#### 1. Data Corruption & Loss
**"How could we corrupt or lose customer data?"**

Prompts:
- What happens if we process the same event twice?
- What if we skip an event or webhook?
- Are there race conditions in our update logic?
- What if two users modify the same record simultaneously?
- What if external data is in an unexpected state when we read it?
- How do we know if data becomes inconsistent between systems?
- What happens on rollback? Can we reverse data changes?

**Test Plan:**
- Concurrency tests
- Duplicate event handling tests
- Data integrity validation
- Rollback procedures

---

#### 2. Silent Failures
**"How could this fail without anyone noticing?"**

Prompts:
- Which errors wouldn't trigger our alerts?
- What metrics aren't we tracking that we should be?
- Where do we have blind spots in logging?
- What could degrade slowly without obvious symptoms?
- How would we know if core logic silently stopped working?
- What if webhooks stop firing and we don't realize it?

**Test Plan:**
- Monitoring setup for new feature
- Alert configurations
- Log coverage verification
- Graceful degradation testing

---

#### 3. Load & Scale Breaking Points
**"What happens at 10x our expected load?"**

Prompts:
- What happens if we need to process 10x normal traffic?
- Which queries become slow under high load?
- What happens if external API rate limits are hit?
- Do we handle queuing properly under extreme contention?
- What bottlenecks emerge at scale?
- Which assumptions about "typical usage" could be wrong?

**Test Plan:**
- Load testing at 10x expected volume
- API rate limit simulation
- Queue backup scenarios
- Performance benchmarks

---

#### 4. Customer Configuration Chaos
**"What if customers configure this in ways we never imagined?"**

Prompts:
- What happens with 100+ rules instead of 10?
- What if they create circular logic?
- What if they use custom objects we've never seen?
- What if they have field-level security that blocks our updates?
- What if they disable the objects we depend on?
- What extreme configuration would break our assumptions?

**Test Plan:**
- Configuration limit testing
- Edge case scenarios
- Permission/security testing
- Backwards compatibility verification

---

#### 5. User Error & Misuse
**"How could users break this themselves?"**

Prompts:
- What would happen if they click buttons in the wrong order?
- What if they input data in unexpected formats?
- What if they try to use features before setup is complete?
- What confusing error messages could we show?
- Where could they get stuck with no way forward?
- What assumptions do we make about user knowledge?

**Test Plan:**
- Usability testing with non-expert users
- Error message clarity review
- "Happy path" deviation testing
- Setup flow validation

---

#### 6. Browser/Environment Failures
**"What if it doesn't work in their environment?"**

Prompts:
- What if they use Safari instead of Chrome?
- What if their firewall blocks our APIs?
- What if they have browser extensions that interfere?
- What if their environment has custom permissions?
- What if they're in a different timezone or locale?
- What if they have slow internet connections?

**Test Plan:**
- Cross-browser testing
- Network condition simulation
- Timezone/locale testing
- Permission configuration testing

---

### Output from QA Inversion

**Before shipping, must have:**
1. **Test Coverage Report** – All failure modes have corresponding tests
2. **Monitoring Checklist** – Alerts and metrics are configured
3. **Known Issues Log** – Documented limitations and workarounds
4. **Performance Benchmarks** – Load testing results and acceptable ranges
5. **Edge Case Matrix** – How we handle each identified edge case

---

## Phase 3: Rollout Inversion

**Timing:** Before launching to customers, even in beta

**Goal:** Ensure safe, reversible, gradual rollout that doesn't surprise customers

### Setup (10 minutes)
- **Facilitator:** PM or Engineering Manager
- **Scribe:** Documents rollout plan and contingencies
- **Participants:** PM, Engineering Lead, Support Lead, CSM Lead (for enterprise features)
- **Scope:** The rollout strategy for this feature

### Inversion Questions

#### 1. Catastrophic Production Failure
**"What would cause a P1 incident?"**

Prompts:
- Which single component failure would cascade most dramatically?
- What would happen if webhooks stop working?
- What if core logic breaks for all customers simultaneously?
- What database query could bring everything down?
- What API rate limit breach would cause widespread failure?
- What configuration error could break existing workflows?

**Mitigation Plan:**
- Gradual rollout (% of traffic or specific customers)
- Feature flags for instant disable
- Monitoring with automatic alerts
- Rollback procedures documented

---

#### 2. Rollback Nightmare
**"What if we need to roll back at 3am?"**

Prompts:
- Can this change be reversed quickly?
- How long would rollback take? (Target: < 5 minutes)
- Is anything irreversible? (Database migrations, data transformations)
- At what point does rolling back become more dangerous than pushing forward?
- Who needs to be available to execute rollback?
- What customer communication is needed if we roll back?

**Rollback Plan:**
- Feature flag kill switch
- Data migration rollback scripts (if applicable)
- Rollback runbook with step-by-step instructions
- On-call engineer assignment
- Customer communication templates

---

#### 3. Customer Surprise & Confusion
**"How could we blindside customers and create support chaos?"**

Prompts:
- What if enterprise customers aren't given advance notice?
- What if the new workflow is confusing and no documentation exists?
- What if customers can't opt out if they hate it?
- What if CSMs don't know this is launching and can't answer questions?
- What if Support doesn't have talking points for angry customers?
- What if we launch during customer's busy season?

**Communication Plan:**
- Enterprise customer advance notice (2+ weeks)
- Release notes with before/after comparisons
- Opt-in or opt-out mechanism
- Support documentation ready before launch
- CSM enablement session
- Launch timing coordinated with customer success

---

#### 4. Performance Degradation
**"How could this make everything slower?"**

Prompts:
- What if database queries are slower than expected in production?
- What if external API calls take longer than in testing?
- What if the UI renders slowly on customer devices?
- What if it works fine for 10 rules but breaks with 100 rules?
- What background jobs could cause delays?
- What happens during peak usage times?

**Performance Validation:**
- Production load testing before wide rollout
- Page load time monitoring
- Database query performance tracking
- Gradual traffic increase with performance observation
- Rollback triggers if performance degrades X%

---

#### 5. Security & Permission Issues
**"How could we expose data or violate permissions?"**

Prompts:
- What data are we exposing that shouldn't be visible?
- What if field-level security is violated?
- What if user permissions aren't respected?
- What if we log sensitive information?
- What authentication/authorization checks could fail?
- What GDPR/compliance issues could emerge?

**Security Validation:**
- Permission testing in production-like environment
- Security review of data access patterns
- Logging audit (no PII/sensitive data)
- Compliance team review (if applicable)

---

#### 6. Support Avalanche
**"How could this create a support ticket tsunami?"**

Prompts:
- What confusing aspects will generate support tickets?
- What error messages will users not understand?
- What if our documentation is incomplete or wrong?
- What if the feature doesn't work for a common use case?
- What questions will Support not be able to answer?
- What happens if we launch during end-of-quarter when customers are stressed?

**Support Readiness:**
- Support team training session
- FAQ document for common questions
- Known issues and workarounds documented
- Support ticket tagging for feature-related issues
- Escalation path defined
- Post-launch support capacity planning

---

### Output from Rollout Inversion

**Before launching, must have:**

1. **Rollout Plan**
   - Phase 1: Internal dogfooding (1 week)
   - Phase 2: Beta customers (select 5-10 friendly accounts)
   - Phase 3: 10% of customers
   - Phase 4: 50% of customers
   - Phase 5: 100% rollout
   - Success criteria for each phase

2. **Rollback Runbook**
   - Step-by-step rollback instructions
   - Estimated rollback time
   - On-call assignments
   - Customer communication plan if rollback needed

3. **Monitoring Dashboard**
   - Key metrics being tracked
   - Alert thresholds
   - Automatic rollback triggers (if applicable)

4. **Communication Materials**
   - Customer announcement (for enterprise)
   - Release notes
   - Support documentation
   - CSM talking points
   - Known limitations

5. **Success Metrics**
   - How we'll know it's working
   - When to pause rollout
   - When to roll back
   - When to accelerate rollout

---

## Inversion Pass Template

Use this template for each phase:

```markdown
# [Feature Name] - [Phase] Inversion Pass

**Date:** [Date]
**Facilitator:** [Name]
**Scribe:** [Name]
**Participants:** [Names]

## Failure Modes Identified

### [Question Category]
**How could this fail:**
- [Failure mode 1]
- [Failure mode 2]
- [Failure mode 3]

**Severity:** Red: Showstopper / Yellow: Mitigation Needed / Green: Accepted Risk

**Mitigation:**
- [Action item 1] - Owner: [Name] - Due: [Date]
- [Action item 2] - Owner: [Name] - Due: [Date]

[Repeat for each question category]

## Summary

**Showstoppers to address before proceeding:** [Count]
**Mitigations to implement:** [Count]
**Accepted risks:** [Count]

**Next Steps:**
1. [Action]
2. [Action]
3. [Action]

**Follow-up Review:** [Date]
```

---

## Making Inversion a Habit

### For PMs
- **PRDs:** Include "Inversion Pass Results" section in every spec
- **Sprint Planning:** Run Spec Inversion before estimation
- **Pre-Launch:** Run Rollout Inversion 1 week before ship date

### For Engineering
- **Code Review:** Ask "What failure modes did we miss?"
- **Architecture Review:** Include "How This Could Fail" section
- **Post-Mortems:** Use inversion to identify what we should have caught

### For the Team
- **Quarterly Retros:** Review features shipped – which failures did we miss that inversion would have caught?
- **New Team Members:** Teach inversion as part of onboarding
- **Recognition:** Celebrate when someone identifies a critical failure mode before it happens

---

## Quick Reference Card

```
BEFORE SPEC → Spec Inversion
Questions: Requirements? Scope bloat? Dependencies? UX disaster? Tech debt?
Output: Updated PRD with deletions, validations, rollout plan

DURING BUILD → QA Inversion
Questions: Data loss? Silent failures? Scale breaks? Config chaos? User errors? Environment issues?
Output: Test coverage, monitoring, edge case handling

BEFORE LAUNCH → Rollout Inversion
Questions: P1 incident? Rollback? Customer surprise? Performance? Security? Support avalanche?
Output: Gradual rollout plan, rollback runbook, communications

REMEMBER: Invert, always invert.
```

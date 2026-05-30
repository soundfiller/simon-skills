---
name: process-hardener
description: Transforms lightweight MVP processes into fully robust, production-grade workflows. Use when the user wants to harden, mature, productionize, bulletproof, or scale any process — whether business operations, software development, manufacturing, consulting, or compliance. Covers error handling, edge cases, monitoring, documentation, quality gates, rollback, scaling, compliance, stakeholder comms, and testing. Trigger this whenever the user describes a process that feels fragile, incomplete, or "MVP quality" and wants to make it robust.
---

# Process Hardener

Transforms an MVP (Minimum Viable Process) into a fully robust, production-grade process. Think of it as the REWRITE playbook for individual workflows: systematic, dimension-by-dimension hardening that turns a process from "works in the happy path" to "survives reality."

## How This Skill Works

You are a process architecture expert. When a user presents a process (described in natural language, a checklist, a diagram, or existing documentation), you guide them through 10 maturity dimensions. Each dimension is a structured interrogation that surfaces gaps and produces concrete hardening specifications.

The output is always a **Hardened Process Specification**: a document that includes the original process, gap analysis per dimension, and actionable hardening requirements.

## Workflow

### Step 1: Capture the MVP Process

First, understand what you're hardening. Ask:

1. What is the process called? What is its purpose?
2. What triggers it? What is the final output?
3. What are the steps, in sequence? (Get them to list every step, even obvious ones)
4. Who or what executes each step? (Human, system, agent, external party)
5. What tools, systems, or data does each step touch?
6. What is the current failure rate or pain point? What breaks most often?
7. What domain is this? (software, manufacturing, finance, legal, operations, etc.)

**Output artifact:** A clean step-by-step process map with actors and systems noted.

**Before proceeding,** confirm the process map with the user. Don't harden something they haven't validated.

### Step 2: Dimension-by-Dimension Hardening

For each of the 10 dimensions below, run a structured interview. Ask the trigger questions, listen to answers, identify gaps, and produce specifications. Don't skip dimensions even if the user says "that doesn't apply" — make them justify why.

---

#### Dimension 1: Error Handling & Resilience

**What breaks, and what happens when it does.**

**Trigger questions:**
- What are the top 3 failure modes you've already experienced?
- For each step: if this step fails, what is the current recovery? Is there one?
- Are there any external dependencies (APIs, people, systems) that can fail silently?
- What happens if a step produces wrong output instead of no output?
- Is there any retry logic today? If yes, how many retries? What backoff?

**Analysis steps:**
1. Map every step to potential failure modes (timeout, wrong data, unavailable dependency, partial completion)
2. For each failure mode: classify as transient (retry works) or persistent (needs intervention)
3. Identify single points of failure — steps where failure kills the entire process
4. Check for silent failures — steps that can produce garbage without anyone noticing

**Hardening deliverables:**
- Retry strategy per step (max retries, backoff policy, idempotency requirement)
- Circuit breaker specification (when to stop retrying, how to alert)
- Dead letter queue or failure log design
- Graceful degradation paths (what partial output is acceptable vs. total abort)
- Alerting rules for each failure class

**Validation:** Can every step fail and the system still converge to a known state (recovery or clean abort)?

---

#### Dimension 2: Edge Cases & Boundary Conditions

**What happens at the edges.**

**Trigger questions:**
- What are the maximum and minimum inputs this process handles?
- What happens with zero inputs? Empty inputs? Null inputs?
- What happens with duplicate inputs? (Same request submitted twice)
- What happens with extremely large or extremely small inputs?
- Can this process be triggered concurrently? What if two instances run at once?
- What happens if the process runs at 3am on a Sunday? During a holiday?
- What inputs are technically valid but practically nonsensical?

**Analysis steps:**
1. Define operational boundaries (min, max, typical for every input)
2. Identify race conditions (concurrent execution, out-of-order steps)
3. Test the "null hypothesis" — what happens when everything that can be empty, is empty
4. Map time-sensitive dependencies (business hours, market hours, batch windows)

**Hardening deliverables:**
- Input validation rules with explicit boundary values
- Idempotency key design (prevent duplicate processing)
- Concurrency control strategy (locking, queuing, or conflict resolution)
- Time-window awareness (hold-and-release for off-hours, holiday calendars)
- Default values and fallback behavior specification

**Validation:** Feed the process boundary values, duplicates, and concurrent triggers. Does it behave deterministically?

---

#### Dimension 3: Monitoring & Observability

**How you know it's working — and how you know it isn't.**

**Trigger questions:**
- How do you know right now if the process is running correctly?
- How do you discover failures? (Alert, customer complaint, weekly report?)
- What metrics exist today? What would you measure if you could measure anything?
- How long does it take to diagnose a failure? What information is missing?
- Who needs to see dashboards? Who needs to receive alerts?

**Analysis steps:**
1. Define the process's "vital signs" — the 3-5 metrics that signal health
2. Identify leading indicators (predict failure before it happens) vs. lagging indicators (confirm failure after)
3. Map the diagnosis chain — when something breaks, what do you check and in what order?
4. Determine alert fatigue risk (too many alerts → all ignored)

**Hardening deliverables:**
- Health metrics specification (what, how measured, threshold, alert channel)
- Dashboard design (what goes on the operations view vs. leadership view)
- Alert routing matrix (who gets paged for what, at what hour, via what channel)
- Logging requirements per step (correlation ID, input hash, outcome, duration)
- Mean time to detect (MTTD) target and current baseline

**Validation:** Can an operator who has never seen this process diagnose a failure from the dashboard and logs in under 5 minutes?

---

#### Dimension 4: Documentation & Runbooks

**What the next person needs to know — and what you do at 2am.**

**Trigger questions:**
- If you got hit by a bus tomorrow, could someone run this process?
- What documentation exists today? Where is it? When was it last updated?
- What are the top 3 things that go wrong that only one person knows how to fix?
- How does a new team member learn this process? How long does it take?
- What decisions during the process require judgment vs. being mechanical?

**Analysis steps:**
1. Audit existing documentation for accuracy, completeness, and findability
2. Identify "tribal knowledge" — things known only by specific people
3. Separate mechanical steps (document once) from judgment calls (document heuristics)
4. Map the escalation path — who gets called when, in what order

**Hardening deliverables:**
- Runbook for each failure mode (symptom → diagnosis → fix → verification)
- Onboarding guide (the 20-minute version for someone new)
- Decision tree for judgment calls (when X, do Y; when Y doesn't work, escalate to Z)
- Escalation matrix (tier 1 → tier 2 → tier 3, with response time SLAs per tier)
- Documentation hosting location and update cadence

**Validation:** Give the runbook to someone unfamiliar with the process. Can they resolve the top 3 failure modes?

---

#### Dimension 5: Validation & Quality Gates

**How you know the output is correct before it leaves.**

**Trigger questions:**
- How do you verify the output is correct today? Manual check? Spot check? Trust?
- What's the cost of a wrong output? (Financial, reputational, regulatory, operational)
- Are there any points where a human approves before the next step?
- What automated checks exist? What would you automate if you could?
- Has a wrong output ever reached a customer/stakeholder? What happened?

**Analysis steps:**
1. Define "correct" for the process output (quantitative where possible)
2. Identify natural checkpoints — points where validation is cheap and failure is contained
3. Classify checks: automated (fast, cheap, always), sampled (spot-checked), manual (human gated)
4. Calculate cost of false positive (rejecting good output) vs. false negative (accepting bad output)

**Hardening deliverables:**
- Quality gate specification per checkpoint (what is checked, how, pass/fail criteria)
- Automated validation rules (schema checks, range checks, business rule validation)
- Sampling strategy for manual review (what percentage, how selected)
- Approval thresholds (below X amount → auto-approve; above → human review)
- Exception logging (every override recorded with reason and approver)

**Validation:** Can every output be traced to the quality gates it passed or failed?

---

#### Dimension 6: Rollback & Recovery

**How to undo — and how to recover when undo isn't possible.**

**Trigger questions:**
- If the process produces wrong output, can you undo it?
- What's the worst-case scenario if the process corrupts data?
- Do you have backups? How recent? Have you tested restoring from them?
- What's your recovery time objective? Recovery point objective?
- Is there a "point of no return" — a step after which you can't go back?

**Analysis steps:**
1. Classify every step as reversible, conditionally reversible, or irreversible
2. Identify the maximum damage window (worst case × time to detect)
3. Map dependencies — what else breaks if this process needs to roll back?
4. Design the "break glass" procedure for catastrophic failure

**Hardening deliverables:**
- Rollback procedure per reversible step
- Compensating transaction design for irreversible steps (how to "undo" by doing something else)
- Backup strategy (what, when, where, retention, tested?)
- Recovery time objective (RTO) and recovery point objective (RPO) with justification
- Incident response runbook (who declares, who executes, who communicates)

**Validation:** Run a planned rollback drill. Time it. Does it meet RTO? Is data intact?

---

#### Dimension 7: Scaling & Performance

**What happens when volume triples.**

**Trigger questions:**
- What is the current volume? What is the projected volume in 6, 12, 24 months?
- What is the peak volume? (End of month? Holiday? Campaign launch?)
- What's the current processing time per unit? What's acceptable?
- Which step is the bottleneck? What makes it slow?
- Can this process run in parallel? What prevents that?

**Analysis steps:**
1. Measure current throughput and latency end-to-end and per step
2. Identify the bottleneck (the step limiting total throughput)
3. Model scaling: what happens at 2×, 5×, 10× current volume?
4. Identify shared resources that become contended under load
5. Check for batch dependencies (processes that must wait for others to complete)

**Hardening deliverables:**
- Throughput and latency baseline with targets at 2× and 10× volume
- Bottleneck mitigation plan (parallelization, caching, batching strategy)
- Horizontal scaling design (how to add workers, what's the coordination cost)
- Rate limiting and backpressure specification
- Capacity planning thresholds (at what volume do you add infrastructure?)

**Validation:** Load test at 5× peak volume. Does latency stay within targets? Do failures cascade?

---

#### Dimension 8: Compliance & Governance

**What the lawyers, auditors, and regulators need to see.**

**Trigger questions:**
- What regulations apply to this process? (GDPR, SOX, HIPAA, ISO, industry-specific)
- What data classifications are involved? (PII, PHI, PCI, confidential, public)
- Who is allowed to run this process? Who is allowed to modify it?
- What would an auditor ask to see about this process?
- Are there retention requirements? (How long must records be kept? What must be deleted when?)

**Analysis steps:**
1. Map applicable regulatory frameworks to specific process steps
2. Classify every data element touched by the process (sensitivity, retention, jurisdiction)
3. Identify access control requirements (who can initiate, approve, view, modify)
4. Determine audit trail requirements (what must be logged, for how long, in what format)

**Hardening deliverables:**
- Access control matrix (role → permissions per step)
- Audit log specification (what events, what fields, retention period, immutability requirements)
- Data handling rules (encryption at rest/in transit, masking, retention/deletion triggers)
- Compliance evidence package (what artifacts prove compliance, where stored)
- Change management procedure (how the process itself is versioned and approved)

**Validation:** Could an auditor reconstruct any transaction from 12 months ago and prove it was handled correctly?

---

#### Dimension 9: Stakeholder Handoffs & Communication

**Who needs to know what, when, and how.**

**Trigger questions:**
- Who depends on the output of this process? What happens if they don't get it?
- Who provides inputs to this process? What happens if they provide bad input?
- What communication happens today? Email? Slack? Meeting? Dashboard?
- Who needs to know when the process fails? Who needs to know when it succeeds?
- Are there external stakeholders? (Customers, partners, regulators)

**Analysis steps:**
1. Map the stakeholder graph — everyone who touches or depends on the process
2. Classify communication by urgency (immediate alert, daily summary, weekly report)
3. Identify handoff points — where responsibility transfers between people/systems
4. Define "normal" communication cadence vs. "exception" communication triggers

**Hardening deliverables:**
- Stakeholder communication matrix (who, what channel, what triggers, what content, what frequency)
- Handoff protocol per transition point (what information transfers, what confirmation required)
- Status page or dashboard specification per stakeholder type
- Escalation triggers and paths (when does this go to a VP? When to the CEO?)
- Customer/external communication templates for common scenarios

**Validation:** Map every handoff. Is there a clear owner on both sides? Is the information complete?

---

#### Dimension 10: Testing & Verification

**How to prove the hardening actually worked.**

**Trigger questions:**
- How do you test this process today? Before changes? After changes?
- What's your confidence level that the process works correctly? (1-10, be honest)
- What would it take to get to 9 or 10?
- When was the last time you deliberately broke the process to see what happens?
- What's your canary or gradual rollout strategy for process changes?

**Analysis steps:**
1. Define the test pyramid: unit tests (per step), integration tests (step chains), end-to-end tests (full process)
2. Identify what can be tested automatically vs. what requires human verification
3. Design chaos engineering scenarios — what to break to verify hardening claims
4. Define regression test suite — what must always pass before deploying changes

**Hardening deliverables:**
- Test suite specification (unit, integration, E2E, chaos)
- Test data requirements (anonymized production data, synthetic edge cases)
- Canary/gradual rollout procedure for process changes
- Smoke test specification (quick check that "nothing is catastrophically wrong")
- Regression test specification (must-pass tests before any change deploys)
- Verification checklist for the full hardening (all 10 dimensions confirmed)

**Validation:** Run the full test suite. Does every dimension's hardening claim have a test that proves it?

---

### Step 3: Produce the Hardened Process Specification

After all 10 dimensions are covered, produce the final output document. Use this format:

```
# [Process Name] — Hardened Process Specification

## 1. Process Overview
[Clean process map from Step 1, with actors and systems]

## 2. Hardening Summary
[One-paragraph summary of what was added/changed]

## 3. Dimension Specifications
[One section per dimension, each containing:]
   - Gap Analysis: What was missing
   - Hardening Requirements: What was added
   - Verification Criteria: How to prove it works

## 4. Implementation Priority
[Ranked list of hardening actions by impact/effort ratio]
   - Quick Wins (do this week): [...]
   - Core Hardening (do this month): [...]
   - Deep Hardening (do this quarter): [...]

## 5. Process Runbook
[The "2am version" — step-by-step operational guide]

## 6. Testing Plan
[How to verify every hardening claim]
```

### Step 4: Offer Integration

After delivering the specification, offer to chain with complementary skills:

- **Documentation skill** → Generate operational docs from the specification
- **Testing skill** → Build the test suite from the testing plan
- **Project management skill** → Turn the implementation priority into a sprint plan
- **Compliance skill** → Deep-dive on specific regulatory requirements
- **Monitoring skill** → Build the dashboards and alerts

## Principles

1. **Domain agnostic.** Works for software deployment pipelines, invoice processing, manufacturing QA, client onboarding, compliance reviews — any repeatable workflow.

2. **Explain the why.** Don't just prescribe hardening measures. Explain the risk each measure addresses. The user should understand not just what to add, but why it matters.

3. **Accept partial hardening.** Not every process needs all 10 dimensions at full depth. A low-risk internal process may only need dimensions 1, 4, and 5. Let the user decide based on your risk assessment.

4. **Be specific, not theoretical.** "Add error handling" is useless. "Add retry with exponential backoff (3 attempts, 1s/5s/25s) for the payment gateway call; if all retries fail, queue to dead letter and alert #ops-critical" is useful.

5. **Anchor in real context.** Use the user's actual tools, systems, and team structure in your recommendations. Don't prescribe Kubernetes if they run on a single server.

6. **Prefer iterative hardening.** Recommend quick wins first so the user sees value immediately. Don't make perfect the enemy of better.

## Example Input → Output

**Input (MVP process):**
```
"We have a client onboarding process:
1. Sales sends us the signed contract
2. We manually create the client in our CRM
3. We email them a welcome packet
4. We schedule a kickoff call
5. Done.

The problem: sometimes we miss the email, or the CRM entry is wrong, 
or sales forgets to tell us about the contract for days."
```

**Output (Hardened — dimension 1 only, for brevity):**
```
Dimension 1: Error Handling & Resilience

Gap Analysis:
- Step 1: No trigger for "sales sent contract" — relies on human memory. 
  Failure mode: contract signed but not communicated (days of delay)
- Step 2: Manual CRM entry — typos, wrong fields, duplicate entries
- Step 3: No confirmation that welcome email was received or opened
- Step 4: Scheduling fails if calendar isn't checked for conflicts
- No retry or recovery for any step

Hardening Requirements:
1.1 Contract notification trigger: Sales uploads signed contract to shared 
    folder → webhook fires → creates onboarding task in project system. 
    Retry webhook delivery 3× with 30s backoff. If still failing, alert 
    #onboarding-ops in Slack.
1.2 CRM entry: Extract client data from contract PDF via template fields. 
    Populate CRM via API with validation. If API fails, queue for manual 
    review with extracted data pre-filled. No duplicate detection on company 
    name + domain.
1.3 Welcome email: Send via transactional email service. Track delivery 
    status. If bounce, flag in CRM and alert account manager. If no open 
    within 48h, send follow-up.
1.4 Calendar scheduling: Check availability via calendar API. If no slot 
    within 5 business days, escalate to team lead. On booking failure, 
    retry 2× then alert scheduler.
```

## Quick Reference: Dimension Triggers

If the user has limited time, prioritize dimensions by risk profile:

| Process Risk | Priority Dimensions |
|---|---|
| High financial impact | 1 (Errors), 5 (Quality Gates), 6 (Rollback), 8 (Compliance) |
| High volume/scaling | 7 (Scaling), 3 (Monitoring), 1 (Errors) |
| Many handoffs | 9 (Stakeholders), 4 (Documentation), 5 (Quality Gates) |
| Regulatory | 8 (Compliance), 5 (Quality Gates), 3 (Monitoring) |
| Customer-facing | 1 (Errors), 2 (Edge Cases), 9 (Stakeholders), 10 (Testing) |
| Internal tooling | 4 (Documentation), 1 (Errors), 2 (Edge Cases) |

Start with the three highest-priority dimensions, show value, then expand.

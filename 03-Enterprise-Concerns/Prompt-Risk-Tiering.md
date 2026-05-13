---
title: Prompt Risk Tiering
tags:
  - chapter
  - risk
  - governance
  - promptops
  - controls
difficulty: intermediate
last_updated: 2026-05-12
time_to_read: 18 minutes
related:
  - "[[PromptOps-Governance]]"
  - "[[Governance-and-Risk]]"
  - "[[Security-and-Privacy]]"
  - "[[Evaluation-and-Testing]]"
  - "[[Agents-and-Tool-Use]]"
---

# Prompt Risk Tiering

> **TL;DR for the Busy IT Pro:**  
> Risk tiering determines how much control a prompt or agent needs before use: review, evidence, evals, logging, human approval, or formal governance.

---
## What You'll Learn

- [ ] How to classify prompts and agents by risk
- [ ] Which risk drivers matter most
- [ ] What controls belong at each tier
- [ ] How to create a lightweight intake process
- [ ] How to avoid over-governing low-risk productivity use

---
## Why This Matters

Not every AI use case deserves the same governance burden. Over-control slows adoption. Under-control creates legal, security, financial, and operational exposure.

**Real-world scenario:**  
> A team uses the same approval path for a grammar-improvement prompt and an HR screening assistant. One process is over-governed; the other is under-governed. Both outcomes damage trust in the AI program.

---
## Core Concepts

### Concept 1: Risk Comes From Impact, Not Prompt Length

A short prompt can be high risk if it affects people, money, systems, security, or external commitments.

Risk drivers:
- sensitive data
- regulated domain
- customer or employee impact
- financial impact
- legal or policy impact
- external communication
- system/tool access
- autonomy level
- reversibility of action
- reliance without human review

**Why it works this way:**
A 20-word prompt that sends a customer email can create more risk than a 500-word brainstorming prompt.

---
### Concept 2: Tiering Separates Enablement From Control

Risk tiering helps IT say:

> "Yes, but with the right controls."

It prevents two common failures:
- blocking harmless productivity use
- allowing risky automation without review

---
### Concept 3: The Same Prompt Can Move Tiers Based on Context

Example: summarization

| Context | Tier |
|---|---:|
| Summarize a public article | 0 |
| Summarize internal meeting notes | 1 |
| Summarize confidential M&A documents | 3 |
| Summarize evidence and send recommendation to a client | 4 |

**Why it works this way:**
Risk is not only the task. It is the task plus data, audience, autonomy, and consequence.

---
### Concept 4: Tool Use Raises the Risk Floor

Any tool that can read, write, send, delete, approve, buy, book, or update records increases control requirements.

| Tool Type | Risk Implication |
|---|---|
| Read-only public search | Low to medium |
| Read-only internal search | Medium, depends on access control |
| Database read | Medium to high |
| Email drafting | Medium |
| Email sending | High |
| Record update | High |
| Delete / submit / approve | Very high |
| Infrastructure action | Very high |

---
## Risk Tier Model

| Tier | Name | Description | Examples | Minimum Controls |
|---:|---|---|---|---|
| 0 | Low-risk utility | Personal productivity, no sensitive data, no business decision | Rewrite, summarize public text, format notes | User review |
| 1 | Internal productivity | Internal work product, low sensitivity, human reviews before use | Meeting summary, draft internal memo, checklist | Owner, basic prompt standard |
| 2 | Business decision support | Influences business decisions but does not act directly | Strategy brief, market scan, project risk summary | Evidence policy, assumptions, SME review |
| 3 | Sensitive or regulated analysis | Uses sensitive data or affects HR, finance, legal, security, safety, compliance | HR policy assistant, financial risk analysis, security triage | Security/domain review, evals, logging, access controls |
| 4 | External action or system update | AI can trigger external communication or modify records | Send email, update CRM, create ticket, approve workflow | Human approval gate, audit trail, rollback, regression evals |
| 5 | Autonomous execution | Multi-step execution with limited supervision | Autonomous agent across systems, infrastructure remediation | Formal governance, monitoring, kill switch, incident response, strict approvals |

---
## Hands-On Implementation

### Step 1: Ask the Risk Triage Questions

```markdown
## AI Risk Triage

1. What data will the AI process?
   - Public / Internal / Confidential / Restricted

2. Who will see or rely on the output?
   - Individual / Team / Department / Executive / Customer / Regulator

3. What domain does it affect?
   - General productivity / Operations / Finance / HR / Legal / Security / Safety / Healthcare

4. Can the AI take action?
   - No / Draft only / Read-only tool / Write tool / External send / Irreversible action

5. Is a human required to review the output before use?
   - Always / Sometimes / No

6. What is the worst plausible failure?
   - Embarrassment / Rework / Bad decision / Data leak / Legal exposure / Financial loss / Safety impact

7. Can the action be reversed?
   - Yes / Partially / No
```

**What's happening here:**

- The triage focuses on consequence and control.
- The output determines governance depth.

---
### Step 2: Assign a Preliminary Tier

```markdown
## Preliminary Tier Decision

Base tier:
- Start at Tier 0.

Increase tier if:
- Sensitive or restricted data is used: +2
- Output affects business decision: +1
- Output affects a person or customer: +2
- Tool reads internal systems: +1
- Tool writes or sends externally: +3
- No human review before use: +1
- Regulated domain: +2
- Irreversible action: +2

Final tier:
- Cap at Tier 5.
- Use professional judgment if the calculated tier understates the risk.
```

**What's happening here:**

- Teams get a simple first-pass classification.
- Governance can override where needed.

---
### Step 3: Apply the Control Matrix

| Control | T0 | T1 | T2 | T3 | T4 | T5 |
|---|---:|---:|---:|---:|---:|---:|
| User review | Required | Required | Required | Required | Required | Required |
| Prompt owner | Optional | Required | Required | Required | Required | Required |
| Prompt registry | Optional | Recommended | Required | Required | Required | Required |
| Evidence policy | Optional | Optional | Required | Required | Required | Required |
| SME review | Optional | Optional | Required | Required | Required | Required |
| Security review | Optional | Optional | Conditional | Required | Required | Required |
| Eval suite | Optional | Smoke test | Smoke test | Regression | Regression | Continuous |
| Runtime logging | Optional | Optional | Recommended | Required | Required | Required |
| Human approval gate | No | No | Conditional | Conditional | Required | Required |
| Kill switch | No | No | Recommended | Required | Required | Required |
| Incident playbook | No | No | Recommended | Required | Required | Required |

---
### Step 4: Document the Tier Decision

```markdown
## Risk Tier Decision Record

Use case:
Prompt / agent name:
Business owner:
Technical owner:
Assigned tier:
Reason:
Primary risk drivers:
Required controls:
Approver:
Review date:
```

---
## Tips & Tricks

> [!tip] Quick Win
> Add one field to every prompt file: `risk_tier`. Even if imperfect, it starts the governance conversation.

> [!tip] Pro Tip
> Start with a permissive Tier 0-1 pathway so users do not route harmless productivity prompts through heavy governance.

> [!warning] Watch Out
> Any prompt that causes an external action should be treated as Tier 4 or higher until proven otherwise.

---
## Lessons Learned

> [!example] War Story: The Harmless Draft That Was Not Harmless
> **What happened:** A sales assistant prompt was approved as low-risk because it only drafted emails. Later, the workflow was connected to auto-send.  
> **What we learned:** Tool permissions can change the risk tier overnight.  
> **What to do instead:** Re-tier any prompt when tools, audience, data sensitivity, or autonomy changes.

---
## Best Practices Checklist

- [ ] Assign a risk tier before pilot use
- [ ] Reassess risk when tools or data sources change
- [ ] Use lightweight controls for low-risk prompts
- [ ] Require evidence policy for decision-support prompts
- [ ] Require security review for sensitive data or internal tools
- [ ] Require human approval for external actions
- [ ] Require logging and kill switch for production agents
- [ ] Document tier decisions and review dates

---
## Anti-Patterns (Don't Do This)

| Do Not | Do Instead | Why |
|---|---|---|
| Apply the same approval process to every prompt | Use tiered controls | Avoids bottlenecks |
| Classify only by prompt topic | Classify by data, action, audience, consequence | Risk is contextual |
| Ignore tool permissions | Re-tier when tools are added | Tools change impact |
| Treat drafting and sending as the same risk | Separate draft-only from external action | Sending creates liability |
| Set tier once forever | Reassess on change or review date | AI systems evolve |

---
## Common Failure Modes

| Failure | Cause | Mitigation |
|---|---|---|
| Over-governance | Low-risk prompts routed through heavy review | Tier 0-1 fast path |
| Under-governance | Sensitive use case treated as productivity | Triage data and consequence |
| Tier drift | Tool or data changes not reviewed | Re-tier on material change |
| Hidden external impact | Drafts copied directly to customers | Add audience and approval checks |
| No accountability | No owner or approver | Require owner fields |

---
## Related Topics

- [[PromptOps-Governance]] - Registry, versioning, and lifecycle controls
- [[Governance-and-Risk]] - Enterprise AI governance and incident response
- [[Security-and-Privacy]] - Data boundary, injection, and access controls
- [[Evaluation-and-Testing]] - Testing by risk tier
- [[Agents-and-Tool-Use]] - Tool permissions and approval gates

---
## Further Reading

- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework) - Best for: risk taxonomy and governance framing
- [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) - Best for: LLM-specific risk patterns

---
## Changelog

- **2026-05-12**: Created prompt risk-tiering chapter

---
## Questions or Feedback?

Add real examples from internal AI intake requests to improve tier calibration over time.

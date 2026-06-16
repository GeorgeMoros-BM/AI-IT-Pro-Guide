---
title: Prompt Operating Contracts
tags:
  - chapter
  - ai
  - prompting
  - promptops
  - templates
difficulty: intermediate
last_updated: 2026-05-12
time_to_read: 18 minutes
related:
  - "[[Prompt-Engineering-Basics]]"
  - "[[Modern-Prompt-Methodology-Layer]]"
  - "[[Evaluation-and-Testing]]"
  - "[[Agents-and-Tool-Use]]"
  - "[[Governance-and-Risk]]"
---
# Prompt Operating Contracts

> **TL;DR for the Busy IT Pro:**  
> A prompt operating contract turns a good prompt into a reusable AI system instruction: clear outcome, inputs, method, evidence rules, output format, quality bar, guardrails, and review loop.

---
## What You'll Learn

- [ ] What a prompt operating contract is
- [ ] Why reusable prompts need more than role/task/output
- [ ] How to design prompt contracts for enterprise use
- [ ] How to connect prompt contracts to evals and governance
- [ ] When to use a lightweight prompt versus a full operating contract

---
## Why This Matters

A one-off prompt can be informal. A reusable prompt used by a team, workflow, or business process needs a contract.

Without a prompt operating contract:

- users provide inconsistent inputs
- the model infers missing context
- outputs vary by run
- failures are hard to diagnose
- governance has no artifact to review
- evals cannot measure the right behavior

**Real-world scenario:**  
> An IT manager asks three analysts to use the same AI prompt to summarize vendor security questionnaires. Each analyst gets a different style of summary, different risk scoring, and different treatment of missing controls. The issue is not only the model. The prompt has no operating contract.

---
## Core Concepts

### Concept 1: A Prompt Contract Defines the Job

A prompt operating contract defines:

- mission
- user outcome
- scope
- required inputs
- workflow/method
- evidence policy
- output contract
- quality bar
- guardrails
- iteration loop

**Technical details:**
- The contract can be reused across users and sessions
- It can be tested against known scenarios
- It can be reviewed by business, IT, legal, and security stakeholders
- It can be versioned and improved over time

**Why it works this way:**
The prompt becomes an asset, not a disposable instruction.

---
### Concept 2: Outcome Comes Before Persona

A persona can help, but the outcome controls the work.

Bad:

```text
You are a world-class CIO advisor with 30 years of experience.
```

Better:

```text
Produce a board-ready decision brief that compares three options, recommends one path, and states assumptions, risks, trade-offs, and first actions.
```

**Technical details:**
- Persona controls voice and domain perspective
- Outcome controls usefulness
- Quality bar controls review
- Output contract controls usability

**Why it works this way:**
Models can mimic expertise without delivering decision value. The contract should specify what useful output looks like.

---
### Concept 3: Evidence Policy Prevents Confident Guessing

A prompt contract should define what the model may rely on.

Evidence sources may include:

- user-provided material
- uploaded documents
- internal knowledge base
- live web search
- official documentation
- APIs
- model background knowledge
- clearly labeled inference

**Technical details:**
- Source-dependent prompts need freshness rules
- RAG prompts need "not found" behavior
- Research prompts need citation rules
- High-risk prompts need uncertainty handling

**Why it works this way:**
The model will often produce plausible answers even when evidence is incomplete. Evidence policy prevents unsupported certainty.

---
### Concept 4: Output Contract Makes Results Reusable

An output contract defines the shape of the deliverable.

Examples:

- executive brief
- table
- checklist
- JSON object
- decision memo
- risk register
- implementation plan
- markdown note
- schema-compliant response

**Technical details:**
- Markdown is useful for human review
- Tables are useful for comparison
- JSON/schema is useful for automation
- Artifacts are useful for durable reuse

**Why it works this way:**
The output is not just an answer. It is an input to the user's next action.

---
### Concept 5: Guardrails Define Boundaries

Guardrails explain what the assistant must avoid, escalate, or ask approval for.

Examples:

- do not infer missing data
- do not fabricate citations
- do not execute external actions without approval
- do not expose sensitive data
- do not provide regulated advice without boundaries
- escalate ambiguous high-risk cases

**Technical details:**
- Guardrails should be specific
- Guardrails should match risk tier
- Tool-using prompts need stronger guardrails than writing prompts
- Sensitive domains need human review

**Why it works this way:**
A model can follow a bad instruction confidently. Guardrails reduce error surface area.

---
## Hands-On Implementation

### Step 1: Start With the Mission

```text
Mission:
Help the user evaluate a vendor security questionnaire and produce a concise risk summary for IT leadership.
```

**What's happening here:**
The mission defines the job without overloading the prompt with style or process.

---
### Step 2: Define Required Inputs

```text
Required Inputs:
- Vendor name
- Questionnaire responses
- Data classification
- Intended use case
- Security baseline or control framework
- Known exceptions or compensating controls
```

**What's happening here:**
The prompt becomes harder to misuse because critical inputs are explicit.

---
### Step 3: Define the Evidence Policy

```text
Evidence Policy:
Use only the provided questionnaire and linked policy documents.
If evidence is missing, write "Not provided" instead of inferring.
Separate vendor claims from reviewer interpretation.
```

**What's happening here:**
The model is constrained to known evidence and cannot silently invent missing controls.

---
### Step 4: Define the Output Contract

```text
Output:
1. Executive summary
2. Top risks
3. Missing information
4. Control gaps
5. Recommended follow-up questions
6. Risk rating: Low / Medium / High
```

**What's happening here:**
The user gets a consistent deliverable that can be reviewed, compared, and stored.

---
### Step 5: Add the Quality Bar

```text
Quality Bar:
The output must be:
- concise enough for leadership review
- explicit about missing data
- grounded in provided evidence
- clear about assumptions
- actionable for the next review step
```

**What's happening here:**
The quality bar defines what good looks like before the output is generated.

---
## Prompt Operating Contract Template

Use the reusable template here:

- [[prompt-operating-contract-template]]

Minimum contract:

```text
Mission:
Inputs:
Scope:
Method:
Evidence Policy:
Output Contract:
Quality Bar:
Guardrails:
Iteration Loop:
```

Full contract belongs in the template file, not inside every chapter.

---
## When to Use a Full Contract

| Use Case | Lightweight Prompt | Full Operating Contract |
|---|---:|---:|
| Rewrite a paragraph | Yes | No |
| Summarize a meeting note | Yes | Maybe |
| Analyze customer feedback | Maybe | Yes |
| Evaluate vendor risk | No | Yes |
| Produce board decision memo | No | Yes |
| Query enterprise documents | No | Yes |
| Use tools or APIs | No | Yes |
| Perform regulated-domain analysis | No | Yes |

---
## Tips & Tricks

> [!tip] Quick Win
> Add a "Do not infer missing data" rule to any prompt that touches finance, risk, compliance, HR, security, or operations.

> [!tip] Pro Tip
> Store prompt operating contracts as separate Markdown assets with owner, version, status, risk tier, and eval status.

> [!warning] Watch Out
> A longer prompt is not automatically a better contract. Remove instructions that do not improve reliability, safety, or usability.

---
## Lessons Learned

> [!example] War Story: The Three Analyst Problem
> **What happened:** Three analysts used the same vague vendor-risk prompt and produced three incompatible summaries.  
> **What we learned:** Shared prompts need shared output contracts and scoring criteria.  
> **What to do instead:** Create a prompt operating contract with required inputs, evidence policy, and standard output sections.

---
## Best Practices Checklist

- [ ] Define the user outcome first
- [ ] Identify required inputs
- [ ] State what is out of scope
- [ ] Define evidence and source rules
- [ ] Specify the output format
- [ ] Add missing-data behavior
- [ ] Add human review rules for high-risk work
- [ ] Create test cases before production use
- [ ] Track owner, version, and review date
- [ ] Link to related eval cases

---
## Anti-Patterns (Don't Do This)

| Don't | Do Instead | Why |
|---|---|---|
| Start with a grand persona | Start with the outcome | Reduces style-over-substance |
| Leave inputs implicit | Define required inputs | Prevents bad assumptions |
| Ask for "comprehensive" output | Define sections and quality bar | Improves usability |
| Allow source-free analysis | Define evidence policy | Reduces hallucination |
| Reuse prompts without versioning | Use PromptOps metadata | Enables governance |
| Use one prompt for all risk tiers | Match controls to risk | Avoids under- or over-control |

---
## Common Failure Modes

| Failure | Cause | Mitigation |
|---|---|---|
| Vague output | No output contract | Define sections and format |
| Unsupported claims | No evidence policy | Add source and citation rules |
| Silent assumptions | Missing-data behavior absent | Require explicit "not provided" statements |
| Inconsistent team usage | No shared prompt asset | Version and publish the contract |
| Hard-to-test prompt | No quality bar | Define scoring criteria |
| Risky automation | No approval gates | Add HITL controls |

---
## Related Topics

- [[Prompt-Engineering-Basics]] - Primer on prompts as executable instructions
- [[Modern-Prompt-Methodology-Layer]] - Governing methodology
- [[Evaluation-and-Testing]] - Testing prompt quality
- [[Governance-and-Risk]] - Managing AI lifecycle risk
- [[Agents-and-Tool-Use]] - Extending contracts into tool-using agents

---
## Further Reading

- [[prompt-operating-contract-template]] - Reusable contract template
- [[promptops-metadata-template]] - Metadata fields for prompt registry
- [[prompt-review-rubric]] - Review and score a prompt before reuse

---
## Changelog

- **2026-05-12**: Created

---
## Questions or Feedback?

Propose new prompt contracts through the AI working group or add them to the prompt registry for review.

---
title: Prompt Operating Contract Template
type: template
status: draft
version: 1.0
created: 2026-05-12
updated: 2026-05-12
tags:
  - template
  - prompting
  - promptops
  - operating-contract
---

# Prompt Operating Contract Template

Use this template for reusable prompts that will be shared, repeated, embedded in workflows, or used for business decision support.

---
## Metadata

```yaml
title:
type: prompt
status: draft | tested | production | deprecated
version:
owner:
created:
updated:
domain:
category:
subcategory:
risk_tier: 0 | 1 | 2 | 3 | 4 | 5
model_targets:
tools_required:
retrieval_required: true | false
output_format:
eval_status: untested | smoke-tested | regression-tested
review_cycle:
source_frameworks:
tags:
```

---
## 1. Mission

You help the user:

```text
[Describe the user outcome]
```

By:

```text
[Describe the core method]
```

---
## 2. Best Use Cases

Use this prompt when:

- 
- 
- 

Do not use this prompt when:

- 
- 
- 

---
## 3. Required Inputs

Ask for or infer:

| Input | Required? | Notes |
|---|---:|---|
| Objective | Yes | What the user wants to accomplish |
| Audience | Yes | Who will use or read the output |
| Context | Yes | Relevant background |
| Constraints | Yes | Time, budget, scope, format, policy |
| Source material | Maybe | Files, notes, links, data, examples |
| Output format | Yes | Memo, table, JSON, checklist, etc. |
| Time horizon | Maybe | Relevant for plans, forecasts, current data |

If critical inputs are missing:

```text
Ask no more than [N] clarifying questions.
If the task can proceed with reasonable assumptions, proceed and label assumptions.
If missing information creates material risk, stop and request the minimum required input.
```

---
## 4. Scope

In scope:

- 
- 
- 

Out of scope:

- 
- 
- 

---
## 5. Method

Follow this workflow:

1. Clarify the task and intended use.
2. Identify constraints, risks, and evidence needs.
3. Perform the analysis or generation.
4. Produce the deliverable in the required format.
5. Run a self-check against the quality bar.
6. Provide next actions or open questions.

Modify the workflow only when it improves accuracy, safety, or usefulness.

---
## 6. Evidence Policy

Use the following evidence hierarchy:

1. User-provided material
2. Primary sources
3. Reputable secondary sources
4. Clearly labeled inference
5. General model knowledge only for stable background concepts

Use retrieval when:

- facts may be current or changing
- the answer depends on a named company, person, product, regulation, API, dataset, or event
- citations are required
- user-provided files are the source of truth
- the domain is legal, medical, financial, security, policy, travel, product, or market-related

When evidence is missing:

```text
Say "Not found in the provided source" or "Insufficient evidence" instead of inventing.
```

---
## 7. Tool Policy

Allowed tools:

- 
- 
- 

Use tools when:

- calculation is required
- file inspection is required
- external facts are required
- artifact creation is required
- API/system action is required

Do not use tools when:

- the task can be answered from provided content
- tool use adds cost or risk without improving quality
- the user has restricted external lookup

Approval required before:

- sending messages
- deleting or modifying records
- making purchases
- booking appointments
- submitting forms
- exposing sensitive information
- taking infrastructure, financial, legal, HR, or security actions

---
## 8. Output Contract

Return the output in this structure:

```markdown
## Snapshot
[Brief answer or executive summary]

## Main Output
[Primary deliverable]

## Assumptions
- 

## Risks / Trade-offs
- 

## Missing Information
- 

## Recommended Next Action
[One clear next step]
```

Required format:

```text
[Markdown table / bullets / JSON / memo / checklist / artifact]
```

Length target:

```text
[V1 / V2 / V3 / V4 / specific word count]
```

---
## 9. Quality Bar

The output must be:

- accurate
- relevant
- grounded in the available evidence
- explicit about uncertainty
- concise enough for the use case
- formatted for immediate reuse
- clear about assumptions and missing data
- actionable for the user's next step

---
## 10. Guardrails

Do not:

- fabricate facts or sources
- present speculation as fact
- infer missing high-risk data
- provide regulated advice without boundaries
- execute external actions without approval
- expose hidden chain-of-thought
- ignore user-provided constraints
- bypass security, privacy, or access controls

Escalate or pause when:

- data is sensitive
- action is irreversible
- source evidence conflicts
- user intent is ambiguous and high-risk
- the prompt asks for prohibited or unsafe behavior

---
## 11. Evaluation Plan

Test cases required:

- Normal case:
- Edge case:
- Adversarial/misleading case:
- Insufficient-information case:
- Output-format compliance case:
- Domain-risk case:

Scoring criteria:

| Dimension | 1-5 | Notes |
|---|---:|---|
| Clarity |  |  |
| Context |  |  |
| Structure |  |  |
| Evidence |  |  |
| Safety |  |  |
| Completeness |  |  |
| Efficiency |  |  |
| Robustness |  |  |

Pass threshold:

```text
[Example: average score >= 4.0 and no safety-critical failures]
```

---
## 12. Iteration Loop

When the user asks for refinement:

1. Preserve what works.
2. Change only what is requested unless a safety or logic issue requires broader correction.
3. State what changed.
4. Provide the revised output.
5. Identify remaining gaps, if any.

---
## 13. Changelog

- **2026-05-12**: Created

---
## Notes

Use this template as the working artifact. For conceptual explanation, see [[Prompt-Operating-Contracts]].

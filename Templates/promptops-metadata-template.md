---
title: PromptOps Metadata Template
tags:
  - template
  - promptops
  - governance
  - metadata
  - registry
difficulty: intermediate
last_updated: 2026-05-12
related:
  - "[[PromptOps-Governance]]"
  - "[[Prompt-Risk-Tiering]]"
  - "[[Prompt-Operating-Contracts]]"
  - "[[Evaluation-and-Testing]]"
---

# PromptOps Metadata Template

Use this template for any prompt, GPT, RAG assistant, workflow agent, evaluator prompt, or AI-enabled instruction asset that may be reused by a team.

The goal is simple: make AI behavior traceable, reviewable, testable, and governable.

---
## 1. Prompt Asset Frontmatter

Copy this block into the top of a prompt or agent file.

```yaml
---
title: 
type: prompt | agent | evaluator | rag-assistant | workflow | template
status: draft | reviewed | tested | pilot | production | deprecated
version: 0.1.0
owner: 
business_owner: 
technical_owner: 
created: YYYY-MM-DD
updated: YYYY-MM-DD
last_reviewed: YYYY-MM-DD
next_review_date: YYYY-MM-DD

# Classification
domain: 
category: 
subcategory: 
risk_tier: 0 | 1 | 2 | 3 | 4 | 5
data_sensitivity: public | internal | confidential | restricted
approved_use_cases:
  - 
prohibited_use_cases:
  - 

# Model and runtime
model_targets:
  - 
model_version_tested: 
reasoning_level: low | medium | high | n/a
temperature: 
max_output_tokens: 

# Tools and retrieval
tools_required: true | false
allowed_tools:
  - 
forbidden_tools:
  - 
retrieval_required: true | false
retrieval_sources:
  - 
retrieval_freshness_rule: 
source_citation_required: true | false

# Output and validation
output_format: markdown | table | json | schema | artifact | mixed
structured_output_schema: 
eval_status: untested | smoke-tested | regression-tested | production-monitored
last_eval_score: 
eval_suite: 
known_failure_modes:
  - 

# Controls
human_approval_required: true | false
approval_required_for:
  - 
logging_required: true | false
kill_switch_required: true | false
security_review_required: true | false
legal_review_required: true | false
sme_review_required: true | false

# Lineage
source_frameworks:
  - SPARK
  - SCOPE
  - COAST
  - RACE
related_docs:
  - "[[Prompt-Operating-Contracts]]"
  - "[[Evaluation-and-Testing]]"
tags:
  - 
---
```

---
## 2. Required Narrative Sections

After the frontmatter, include these sections.

```markdown
# {{title}}

## Purpose
What this prompt or agent is designed to accomplish.

## Best Use Cases
- 
- 
- 

## Do Not Use For
- 
- 
- 

## Required Inputs
- Objective:
- Audience:
- Source material:
- Constraints:
- Output format:

## Operating Contract
Summarize the behavior, method, evidence policy, output contract, and guardrails.

## Risk Notes
Explain why this asset has the assigned risk tier.

## Evaluation Notes
List test cases, eval results, gaps, and next retest date.

## Change Log
- YYYY-MM-DD: Created v0.1.0
```

---
## 3. Registry Row Template

Use this in a central prompt registry.

```markdown
| Prompt ID | Name | Type | Owner | Risk Tier | Status | Version | Model | Tools | Retrieval | Eval Status | Last Review | Next Review |
|---|---|---|---|---:|---|---|---|---|---|---|---|---|
| PR-000 |  |  |  |  | draft | 0.1.0 |  |  |  | untested |  |  |
```

---
## 4. Risk Tier Quick Reference

| Tier | Description | Typical Control |
|---:|---|---|
| 0 | Low-risk utility | User review |
| 1 | Internal productivity | Owner and basic review |
| 2 | Business decision support | Evidence policy and SME review |
| 3 | Sensitive or regulated analysis | Security/domain review and evals |
| 4 | External action or system update | Human approval, logging, rollback |
| 5 | Autonomous execution | Formal governance, monitoring, kill switch |

---
## 5. Status Definitions

| Status | Meaning |
|---|---|
| draft | Work in progress, not approved for reuse |
| reviewed | Peer or SME reviewed, not fully tested |
| tested | Passed defined evals or smoke tests |
| pilot | Approved for limited controlled use |
| production | Approved for operational use |
| deprecated | No longer approved; retained for reference |

---
## 6. Versioning Standard

Use semantic versioning where practical.

```text
MAJOR.MINOR.PATCH
```

| Change | Version Impact | Example |
|---|---|---|
| Typo, formatting, minor wording | Patch | 1.0.1 |
| Output format, examples, minor behavior | Minor | 1.1.0 |
| New purpose, tools, risk tier, model family, retrieval logic | Major | 2.0.0 |

---
## 7. Minimum Metadata by Risk Tier

| Field | T0 | T1 | T2 | T3 | T4 | T5 |
|---|---:|---:|---:|---:|---:|---:|
| owner | Optional | Required | Required | Required | Required | Required |
| version | Optional | Required | Required | Required | Required | Required |
| risk_tier | Required | Required | Required | Required | Required | Required |
| approved_use_cases | Optional | Recommended | Required | Required | Required | Required |
| prohibited_use_cases | Optional | Recommended | Required | Required | Required | Required |
| model_version_tested | Optional | Recommended | Required | Required | Required | Required |
| eval_status | Optional | Recommended | Required | Required | Required | Required |
| retrieval_sources | Optional | Optional | Conditional | Required if RAG | Required if RAG | Required if RAG |
| tools_required | Optional | Optional | Required | Required | Required | Required |
| human_approval_required | Optional | Optional | Conditional | Conditional | Required | Required |
| logging_required | Optional | Optional | Recommended | Required | Required | Required |

---
## 8. Completed Example

```yaml
---
title: HR Policy Assistant
type: rag-assistant
status: pilot
version: 0.9.0
owner: People Systems Team
business_owner: HR Operations
technical_owner: Enterprise Applications
created: 2026-05-01
updated: 2026-05-12
last_reviewed: 2026-05-10
next_review_date: 2026-06-10

domain: enterprise-ai
category: hr
subcategory: policy-support
risk_tier: 3
data_sensitivity: confidential
approved_use_cases:
  - Answer employee policy questions using approved HR policy documents
  - Identify relevant policy sections for HR staff review
prohibited_use_cases:
  - Provide legal advice
  - Make employment decisions
  - Answer from draft or unapproved policy documents

model_targets:
  - GPT-5.5
model_version_tested: gpt-5.5-2026-05
reasoning_level: medium
temperature: 0.2
max_output_tokens: 1200

tools_required: true
allowed_tools:
  - search_hr_policy_kb
forbidden_tools:
  - send_email
  - update_hris
retrieval_required: true
retrieval_sources:
  - Approved HR Policy Knowledge Base
retrieval_freshness_rule: Prefer approved current policy versions only
source_citation_required: true

output_format: markdown
eval_status: regression-tested
last_eval_score: 87/100
eval_suite: HR-policy-assistant-evals-v1
known_failure_modes:
  - Confuses draft policy with approved policy if metadata is missing
  - Overstates certainty when source language is ambiguous

human_approval_required: true
approval_required_for:
  - Any employee-specific recommendation
  - Any escalation involving legal, termination, compensation, or accommodation
logging_required: true
kill_switch_required: true
security_review_required: true
legal_review_required: true
sme_review_required: true

source_frameworks:
  - Prompt Operating Contract
  - RAG Evidence Policy
related_docs:
  - "[[Research-RAG-and-Evidence]]"
  - "[[Security-and-Privacy]]"
  - "[[Evaluation-and-Testing]]"
tags:
  - hr
  - rag
  - policy
  - tier-3
---
```

---
## 9. Usage Notes

Use this metadata template when:
- a prompt will be reused by more than one person
- the output influences a business decision
- the prompt uses internal or sensitive data
- the prompt uses tools, retrieval, or APIs
- the prompt is part of a workflow or agent
- the prompt may be audited later

Do not over-engineer one-off personal prompts. The governance depth should match the risk tier.

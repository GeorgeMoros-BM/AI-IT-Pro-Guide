---
title: "AI Risk Classification Framework"
artifact_type: framework
status: validated
last_updated: 2026-05-18
publish: false
client_safe: true
audience:
  - governance
  - executive
  - security
domain:
  - ai-risk
---
Provide a structured methodology for classifying AI workloads based on operational, regulatory, security, and business risk.

---
# Core Principle

Governance intensity should scale with risk.

Not every AI workflow requires the same controls.

---
# Risk Tiers

| Tier | Description | Example |
|---|---|---|
| Tier 0 | Low-risk utility | Formatting, summarization |
| Tier 1 | Professional productivity | Drafting and planning |
| Tier 2 | Business decision support | Strategic analysis |
| Tier 3 | Sensitive/high-stakes | Financial, legal, HR |
| Tier 4 | External action systems | Automated workflows |
| Tier 5 | Autonomous execution | Independent multi-step agents |

---
# Risk Dimensions

## Data Sensitivity
Evaluate:
- PII exposure
- confidential information
- regulated data
- intellectual property

---
## Decision Criticality
Assess:
- financial impact
- operational dependency
- reputational risk
- customer impact

---
## Automation Risk
Questions:
- Can the system act autonomously?
- Is human approval required?
- Are rollback mechanisms available?

---
## Governance Requirements

| Tier | Minimum Controls |
|---|---|
| 0 | Basic review |
| 1 | Human validation |
| 2 | Evidence and review |
| 3 | Formal governance and audit |
| 4 | Approval workflows and monitoring |
| 5 | Strict guardrails and observability |

---
# Common Failure Modes

- treating all AI equally
- excessive governance for low-risk use
- insufficient controls for automation
- unclear accountability

---
# Strategic Recommendation

Classify first.
Govern second.
Deploy third.
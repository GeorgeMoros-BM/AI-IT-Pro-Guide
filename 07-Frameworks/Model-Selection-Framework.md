---
title: "Model Selection Framework"
artifact_type: framework
status: validated
last_updated: 2026-05-18
publish: false
client_safe: true
audience:
  - architect
  - practitioner
  - executive
domain:
  - ai-models
---
Provide a structured methodology for selecting AI models based on business requirements, operational constraints, and governance considerations.

---
# Core Principle

No single model is best at everything.

Model selection should optimize for:
- task fit
- reliability
- governance
- economics
- operational simplicity

---
# Evaluation Dimensions

| Dimension | Questions |
|---|---|
| Capability | Does the model perform well for the task? |
| Reliability | Is output quality stable? |
| Context Capacity | Can it handle required inputs? |
| Tool Use | Does it support orchestration workflows? |
| Cost | Is usage economically viable? |
| Latency | Is response speed acceptable? |
| Governance | Does it meet compliance requirements? |
| Ecosystem | Are integrations mature? |

---
# Workload Categories

## Utility Tasks
Examples:
- rewriting
- summarization
- classification

Optimization target:
- low cost
- high throughput

## Expert Reasoning
Examples:
- strategic analysis
- architecture review
- financial interpretation

Optimization target:
- reasoning quality
- consistency
- evidence handling

## Agentic Workflows
Examples:
- orchestration
- tool use
- multi-step execution

Optimization target:
- tool reliability
- instruction adherence
- workflow stability

## Retrieval-Augmented Workloads
Examples:
- enterprise search
- knowledge copilots
- evidence-grounded systems

Optimization target:
- context handling
- citation reliability
- retrieval integration

---
# Operational Recommendations

## Use Multiple Models

Recommended pattern:
- small cheap models for utility tasks
- premium reasoning models for high-value work
- specialized models for niche workloads

## Preserve Optionality

Avoid:
- overcommitting to one provider
- hardcoding vendor-specific assumptions
- tightly coupling workflows to a single API

---
# Common Failure Modes

- selecting models solely by benchmark scores
- ignoring operational economics
- overengineering orchestration
- insufficient evaluation discipline
- failing to test against real workloads

# Strategic Guidance

Treat models as replaceable infrastructure components rather than permanent strategic dependencies.
---
title: Evaluation & Testing
tags:
  - chapter
  - ai
  - evaluation
  - testing
  - ops
difficulty: advanced
last_updated: 2026-05-12
time_to_read: 32 minutes
related:
  - "[[Prompt-Engineering-Basics]]"
  - "[[Modern-Prompt-Methodology-Layer]]"
  - "[[Prompt-Operating-Contracts]]"
  - "[[Agents-and-Tool-Use]]"
  - "[[Agentic-Workflows]]"
  - "[[RAG-Implementation]]"
  - "[[Governance-and-Risk]]"
---
# Evaluation & Testing

> **TL;DR for the Busy IT Pro:**  
> If you are not measuring AI behavior, you are not deploying a system - you are gambling. Evals turn prompts, RAG, and agents from impressive demos into reliable production capabilities.

---
## What You'll Learn

- [ ] How to design evaluation frameworks for prompts, RAG, and agents
- [ ] Which metrics matter for output quality, retrieval quality, tool use, cost, and reliability
- [ ] How to build test sets that catch silent failure modes
- [ ] How to use human review and LLM-as-judge safely
- [ ] How to connect evals to release gates, monitoring, and governance

---
## Why This Matters

AI systems fail differently from traditional software. A normal application often fails loudly with an error code. An AI system can fail silently while sounding confident.

Without evaluation:

- hallucinations look plausible
- missing context goes undetected
- model updates change behavior unexpectedly
- RAG pipelines retrieve the wrong source material
- agents call the wrong tool or repeat failing actions
- business users build confidence in outputs that were never tested

**Real-world scenario:**  
> Your finance team uses AI to summarize earnings reports. It performs well in the pilot, but after a model update it starts omitting risk disclosures. No one notices until a decision brief goes to leadership with incomplete information.

---
## Core Concepts

### Concept 1: Evals Are Acceptance Tests for Probabilistic Systems

Evaluation means:

> Measuring AI behavior against a defined standard.

Traditional testing asks, "Did the function return the exact expected value?"

AI testing asks:
- Is the output correct enough for the use case?
- Did it include required information?
- Did it avoid prohibited content or unsupported claims?
- Did it cite or ground claims correctly?
- Did it use the right tools in the right way?
- Did it fail safely when information was missing?

**Technical details:**
- AI outputs vary across runs, models, prompts, and retrieved context.
- Tests usually require scoring thresholds, not exact-match assertions.
- Evaluation should combine automated checks, LLM-assisted scoring, and human review.

**Why it works this way:**
AI systems are non-deterministic and context-sensitive. You need a measurement system that can tolerate variation while still detecting unacceptable behavior.

---
### Concept 2: Evals Come Before Scaling

The safest sequence is:
```text
Use case -> success criteria -> test set -> prompt/RAG/agent design -> eval -> pilot -> monitor -> scale
```

Do not build first and invent evaluation later. That usually produces demos that look good but cannot be trusted in production.

**Technical details:**
- Define the minimum acceptable score before deployment.
- Include edge cases and failure cases, not just happy paths.
- Re-run evals after every material change: prompt, model, retrieval corpus, tool, workflow, policy, or output format.

**Why it works this way:**
Evals force teams to define what "good" means before subjective review and stakeholder excitement distort judgment.

---
### Concept 3: Evaluation Has Multiple Layers

| Layer | What You Test | Example Questions |
|---|---|---|
| Prompt-level | Instruction quality | Does the prompt produce the expected output format and reasoning quality? |
| Output-level | Final answer quality | Is the answer accurate, complete, useful, and safe? |
| RAG-level | Retrieval and grounding | Did the system retrieve the right chunks and use them faithfully? |
| Agent-level | Tool trajectory | Did the agent choose the right tools, arguments, sequence, and stop condition? |
| Security-level | Abuse resistance | Does it resist prompt injection, data leakage, and unsafe tool use? |
| Business-level | Outcome impact | Did the system reduce cycle time, error rate, cost, or support burden? |

**Why it works this way:**
A good final answer can hide a bad process. A bad retrieval step, unsafe tool call, or weak approval gate may not show up in a single response sample.

---
### Concept 4: Test Cases Need Representative Friction

A useful eval set includes more than normal examples.

Minimum test set:

| Test Type | Purpose |
|---|---|
| Normal case | Confirms baseline behavior |
| Edge case | Tests unusual but valid inputs |
| Ambiguous case | Tests clarifying-question or assumption behavior |
| Missing-data case | Tests refusal to invent |
| Adversarial case | Tests prompt injection or misleading input |
| Format-compliance case | Tests output contract adherence |
| Domain-risk case | Tests regulated or sensitive scenarios |

**Why it works this way:**
Enterprise AI fails at the boundaries: missing fields, conflicting documents, vague requests, stale data, user manipulation, and overloaded tasks.

---
### Concept 5: LLM-as-Judge Is Useful but Not Sufficient

LLMs can help score outputs for relevance, completeness, tone, and format compliance. They are not a replacement for human gold standards.

Use LLM-as-judge for:
- first-pass scoring
- consistency checks
- rubric-based review
- large sample triage
- comparing prompt variants

Do not rely on it alone for:
- regulated decisions
- legal or financial correctness
- medical safety
- security findings
- final production approval

**Technical details:**
- Calibrate judge outputs against human-reviewed examples.
- Use clear rubrics and examples.
- Use multiple judges or spot checks for high-risk outputs.
- Track false positives and false negatives.

**Why it works this way:**
The judge is also an AI model. It can miss subtle errors, over-reward fluent answers, or enforce the rubric inconsistently.

---
## Hands-On Implementation

### Step 1: Define the Evaluation Scope

Before writing tests, define what system you are evaluating.

```markdown
System under test: Investment-note shortlisting assistant
User outcome: Produce a shortlist of eligible structured notes
Risk tier: Tier 3 - financial decision support
Primary failure risk: Missing fields silently inferred
Deployment gate: 90% minimum score, zero critical safety failures
Human review required: Yes, before investment action
```

**What's happening here:**
- Connects the eval to a real decision
- Identifies the risk tier
- Defines the release threshold
- Prevents "looks good" approval

---
### Step 2: Create a Test Set

Use a structured dataset so results can be repeated.

```json
[
  {
    "id": "note-001-normal",
    "type": "normal",
    "input": "Analyze this structured note with coupon 10.25%, downside barrier 25%, F Class, issuer BMO.",
    "expected": {
      "decision": "eligible",
      "must_include": ["coupon", "downside barrier", "F Class", "issuer"],
      "must_not_include": ["guaranteed return"],
      "required_format": ["Eligibility", "Key Fields", "Risks", "Missing Data"]
    }
  },
  {
    "id": "note-002-missing-field",
    "type": "missing-data",
    "input": "Analyze this structured note with coupon 11%, issuer RBC, F Class. Barrier not shown.",
    "expected": {
      "decision": "incomplete",
      "must_include": ["missing downside barrier"],
      "must_not_include": ["eligible"]
    }
  }
]
```

**What's happening here:**
- Converts subjective review into testable expectations
- Separates required inclusions from prohibited claims
- Makes missing-data behavior explicit

---
### Step 3: Define a Rubric

```markdown
| Dimension | 0 | 1 | 2 |
|---|---|---|---|
| Accuracy | Incorrect | Partially correct | Correct |
| Completeness | Missing key fields | Minor gaps | Includes all required fields |
| Grounding | Unsupported claims | Partly supported | Fully supported by input/source |
| Format | Non-compliant | Mostly compliant | Fully compliant |
| Safety | Unsafe or misleading | Minor concern | Safe and bounded |
```

**What's happening here:**

- Gives humans and automated judges the same scoring language
- Makes trade-offs visible
- Supports regression testing over time

---
### Step 4: Implement a Simple Automated Check

```python
from dataclasses import dataclass

@dataclass
class EvalResult:
    test_id: str
    passed: bool
    score: int
    failures: list[str]


def evaluate_output(test_case, output_text):
    score = 0
    failures = []

    for phrase in test_case["expected"].get("must_include", []):
        if phrase.lower() in output_text.lower():
            score += 1
        else:
            failures.append(f"Missing required phrase: {phrase}")

    for phrase in test_case["expected"].get("must_not_include", []):
        if phrase.lower() in output_text.lower():
            failures.append(f"Prohibited phrase present: {phrase}")
        else:
            score += 1

    passed = len(failures) == 0
    return EvalResult(test_case["id"], passed, score, failures)
```

**What's happening here:**
- Starts with deterministic checks for obvious requirements
- Catches format and missing-field failures cheaply
- Leaves judgment-heavy scoring to humans or LLM-as-judge

---
### Step 5: Add RAG Evaluation

For RAG, test both retrieval and answer quality.

| Metric | What It Checks |
|---|---|
| Retrieval precision | Were the retrieved chunks relevant? |
| Retrieval recall | Did the system retrieve all necessary chunks? |
| Citation accuracy | Do citations point to the right source passages? |
| Faithfulness | Does the answer stay within retrieved evidence? |
| Not-found behavior | Does it admit when sources do not contain the answer? |

Sample RAG test case:
```json
{
  "id": "rag-hr-001",
  "question": "What is the contractor remote-work policy in Canada?",
  "expected_sources": ["HR-Policy-Manual-2026.pdf#section-4.3"],
  "expected_answer_contains": ["contractors", "Canada", "manager approval"],
  "must_not_infer": ["full-time employee policy applies automatically"]
}
```

**What's happening here:**
- Tests retrieval before testing answer fluency
- Detects grounded-but-wrong or fluent-but-unsupported outputs
- Forces the system to handle absence of evidence properly

---
### Step 6: Add Agent Trajectory Evaluation

For agents, evaluate the path, not just the final answer.

| Check | Example |
|---|---|
| Tool selection | Did it call `get_ticket_status` instead of `search_all_tickets`? |
| Argument validity | Did it pass a valid ticket ID? |
| Sequence | Did it authenticate before querying restricted data? |
| Approval gate | Did it ask before rebooting a server? |
| Stop condition | Did it stop after completion or loop unnecessarily? |
| Error handling | Did it explain a failed tool call and avoid infinite retry? |

Example trajectory expectation:

```json
{
  "id": "agent-it-001",
  "input": "Check the status of IT-1234.",
  "expected_tool_calls": [
    {
      "tool": "get_jira_ticket_status",
      "arguments": {"ticket_id": "IT-1234"},
      "order": 1
    }
  ],
  "forbidden_tool_calls": ["update_ticket", "close_ticket"],
  "max_tool_calls": 2
}
```

**What's happening here:**

- Prevents agents from taking unnecessary actions
- Tests least-privilege behavior
- Makes workflow safety measurable

---
### Step 7: Build a Release Gate

```markdown
## AI Release Gate

A prompt, RAG pipeline, or agent can move to pilot only if:

- Overall eval score >= 85%
- No critical safety failures
- No unauthorized tool calls
- Missing-data case handled correctly
- Output format compliance >= 95%
- Human reviewer signs off for Tier 3+ use cases
- Rollback or kill switch exists for production deployment
```

**What's happening here:**

- Connects evals to governance
- Prevents untested demos from becoming production systems
- Gives IT, Security, Legal, and business owners a common approval standard

---
## Tips & Tricks

> [!tip] Quick Win
> Start with 10-20 high-quality test cases. A small, realistic eval set is more useful than a large synthetic set nobody trusts.

> [!tip] Pro Tip
> Keep a "known failures" set. Every production incident should become a regression test.

> [!warning] Watch Out
> Do not measure only answer quality. For RAG and agents, the hidden failure is often bad retrieval or bad tool use.

---
## Lessons Learned

> [!example] War Story: The Silent Degradation
> **What happened:** AI summaries gradually became less detailed after a model update.  
> **What we learned:** Model changes affect outputs even when the prompt does not change.  
> **What to do instead:** Pin versions where possible, run regression evals after every model change, and monitor output quality over time.

---
## Best Practices Checklist

- [ ] Define evaluation criteria before building the prompt, RAG pipeline, or agent
- [ ] Create a representative test dataset with normal, edge, missing-data, and adversarial cases
- [ ] Separate output quality, retrieval quality, tool-use quality, and business impact
- [ ] Automate simple checks where possible
- [ ] Use LLM-as-judge only with a rubric and human calibration
- [ ] Include SME review for high-risk domains
- [ ] Re-run evals after prompt, model, tool, retrieval corpus, or policy changes
- [ ] Convert incidents into regression tests
- [ ] Track eval scores over time
- [ ] Use release gates for production deployment

---
## Anti-Patterns (Don't Do This)

| Don't | Do Instead | Why |
|---|---|---|
| Use "looks good to me" testing | Define metrics and thresholds | Reduces subjective approval |
| Test only happy paths | Include edge and adversarial cases | Finds real production failures |
| Evaluate only final answers | Evaluate retrieval, tool calls, and workflow path | Catches hidden system failures |
| Trust LLM-as-judge blindly | Calibrate against human-reviewed examples | Prevents false confidence |
| Change models without retesting | Run regression evals after every change | Detects model drift |
| Ignore cost and latency | Track cost per task and response time | Keeps systems operationally viable |

---
## Common Failure Modes

| Failure | Cause | Mitigation |
|---|---|---|
| Silent hallucination | Weak grounding or missing-data handling | Add source checks and not-found behavior |
| Format drift | Output contract not enforced | Add schema or format compliance checks |
| Retrieval failure | Bad chunking, stale corpus, weak query | Add retrieval evals and source diagnostics |
| Tool misuse | Too many tools or vague tool descriptions | Tighten schemas and evaluate trajectories |
| Overfitting prompts | Test set too narrow | Add diverse cases and production samples |
| False confidence | Weak rubric or no human review | Use calibrated judging and SME review |
| Cost blowout | Excessive context or agent loops | Track token/call budgets and max iterations |

---
## Related Topics

- [[Prompt-Engineering-Basics]] - Designing instructions that can be tested
- [[Prompt-Operating-Contracts]] - Defining success criteria upfront
- [[RAG-Implementation]] - Building grounded systems that need retrieval evaluation
- [[Agents-and-Tool-Use]] - Evaluating tool selection and execution
- [[Agentic-Workflows]] - Choosing workflow patterns to evaluate
- [[Governance-and-Risk]] - Connecting evals to approval and audit

---
## Further Reading

- [OpenAI Evals](https://github.com/openai/evals) - Useful for understanding repeatable evaluation harnesses
- [OpenAI Cookbook - Evaluation examples](https://cookbook.openai.com/) - Practical examples for testing AI outputs
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework) - Governance-oriented risk framing
- Internal: [[prompt-review-rubric]] - Reusable prompt quality review
- Internal: [[eval-test-case-template]] - Reusable test-case structure

---
## Changelog

- **2026-05-12**: Expanded with RAG evals, agent trajectory evals, release gates, and LLM-as-judge calibration
- **2026-04-25**: Created enterprise evaluation framework

---
## Questions or Feedback?

Raise improvements in your AI working group or extend this chapter with domain-specific evaluation datasets.

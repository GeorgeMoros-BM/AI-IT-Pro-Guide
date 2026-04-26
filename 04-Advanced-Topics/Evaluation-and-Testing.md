---
title: Evaluation & Testing
tags: 
  - chapter
  - ai
  - evaluation
  - testing
  - ops
difficulty: advanced
last_updated: 2026-04-25
time_to_read: 26 minutes
related:
  - "[[Prompt-Engineering-Basics]]"
  - "[[Agents-and-Tool-Use]]"
  - "[[RAG-Implementation]]"
---
# Evaluation & Testing

> **TL;DR for the Busy IT Pro:**  
> If you are not measuring AI outputs, you are not deploying a system—you are gambling. Evals turn AI from a demo into a reliable production capability.

---
## What You'll Learn

- [ ] How to design evaluation frameworks for AI systems
- [ ] What metrics actually matter (beyond “looks good”)
- [ ] How to test prompts, agents, and RAG pipelines
- [ ] How to build continuous evaluation into production workflows

---
## Why This Matters

AI systems fail silently. Outputs often look correct—even when they are wrong.

Without evaluation:
- errors go undetected
- quality degrades over time
- business risk increases

**Real-world scenario:**  
> Your finance team uses AI to summarize earnings reports. It performs well initially, but over time begins omitting key risks. No one notices until a decision is made on incomplete information.

---
## Core Concepts

### Concept 1: What “Evaluation” Actually Means

Evaluation is:

> Measuring AI output quality against a defined standard

**Technical details:**
- Compares model output vs expected output
- Uses metrics (accuracy, relevance, completeness)
- Can be automated or human-reviewed

**Why it works this way:**
AI is non-deterministic. You cannot rely on consistency—you must measure outcomes.

---
### Concept 2: Evals Come Before Scaling

High-performing organizations:
- define evaluation criteria first
- test systems before deployment
- continuously refine outputs

**Key principle:**
> If you cannot measure it, you cannot safely deploy it.

---
### Concept 3: Types of Evals

#### 1. Output Evaluation
- correctness
- completeness
- clarity

#### 2. Process Evaluation (Agents)
- tool selection quality
- reasoning steps
- efficiency

#### 3. System Evaluation
- latency
- cost per request
- reliability

---
### Concept 4: Deterministic vs Probabilistic Testing

| System Type | Testing Approach |
|------------|-----------------|
| Traditional software | Exact match |
| AI systems | Score-based / threshold |

**Why it works this way:**
AI outputs vary. Testing must allow for acceptable ranges, not exact matches.

---
## Hands-On Implementation

### Step 1: Define a Test Set

Create representative inputs:

```json
[
  {
    "input": "Analyze this investment note...",
    "expected": {
      "risk_rating": "High",
      "must_include": ["downside risk", "issuer exposure"]
    }
  }
]
````

**What's happening here:**

* Defines ground truth expectations
* Enables repeatable testing

---
### Step 2: Define Evaluation Criteria

```python
def evaluate_output(output, expected):
    score = 0

    if expected["risk_rating"] in output:
        score += 1

    for item in expected["must_include"]:
        if item in output:
            score += 1

    return score
```

**What's happening here:**

* Converts subjective quality into measurable signals
* Enables automation

---
### Step 3: Run Batch Evaluations

```python id="eval-loop"
results = []

for test in test_set:
    output = run_prompt(test["input"])
    score = evaluate_output(output, test["expected"])
    results.append(score)

average_score = sum(results) / len(results)
```

**What's happening here:**

* Tests consistency across multiple inputs
* Produces aggregate quality score

---
### Step 4: Add Human Review (Critical)

Not everything can be automated.

Use:

* SMEs for accuracy validation
* thumbs up/down feedback
* periodic audit reviews

---
## Evaluation Layers (System View)

### Layer 1: Prompt-Level

* does the instruction produce correct output?

### Layer 2: Agent-Level

* does the system choose correct tools and steps?

### Layer 3: Data-Level (RAG)

* is retrieval accurate and relevant?

### Layer 4: Business-Level

* does this improve real outcomes?

---
## Metrics That Actually Matter

### Core Metrics

| Metric       | Description                             |
| ------------ | --------------------------------------- |
| Accuracy     | Is the output correct?                  |
| Completeness | Are key elements included?              |
| Consistency  | Does it behave the same way repeatedly? |

---
### Advanced Metrics

| Metric            | Description                     |
| ----------------- | ------------------------------- |
| Task success rate | % of tasks completed correctly  |
| Tool efficiency   | Steps required to complete task |
| Latency           | Time per request                |
| Cost per task     | API cost per execution          |

---
## Continuous Evaluation (Production)

Evaluation is not a one-time activity.

### Required capabilities:

* Logging all inputs/outputs
* Sampling outputs for review
* Tracking performance over time
* Alerting on degradation

---
## Tips & Tricks

> [!tip] Quick Win
> Start with 10–20 high-quality test cases instead of trying to cover everything.

> [!tip] Pro Tip
> Use the model itself as a secondary evaluator (“LLM-as-a-judge”) for scalable scoring.

> [!warning] Watch Out
> Do not rely solely on automated scoring—models can “game” evaluation criteria.

---
## Lessons Learned

> [!example] War Story: The Silent Degradation
> **What happened:** AI summaries gradually became less detailed after a model update
> **What we learned:** Model changes affect outputs even with the same prompt
> **What to do instead:** Run regression evals after every model or prompt change

---
## Best Practices Checklist

* [ ] Define evaluation criteria before building
* [ ] Create a representative test dataset
* [ ] Automate scoring where possible
* [ ] Include human review for edge cases
* [ ] Track performance over time
* [ ] Re-run evals after any change (prompt, model, data)

---
## Anti-Patterns (Don't Do This)

| ❌ Don't                    | ✅ Do Instead              | Why                             |
| -------------------------- | ------------------------- | ------------------------------- |
| “Looks good to me” testing | Use defined metrics       | Reduces bias                    |
| Test once and deploy       | Continuous evaluation     | AI drifts over time             |
| Ignore edge cases          | Include failure scenarios | Improves robustness             |
| Measure only accuracy      | Include cost + latency    | Reflects real-world constraints |

---
## Common Failure Modes

| Failure             | Cause                | Mitigation            |
| ------------------- | -------------------- | --------------------- |
| Silent errors       | No evaluation        | Add test framework    |
| Drift over time     | Model/data changes   | Continuous monitoring |
| Overfitting prompts | Testing too narrowly | Expand test set       |
| False confidence    | Weak metrics         | Strengthen criteria   |

---
## Further Reading

[[01-Foundation Knowledge/Prompt-Engineering-Basics|Prompt-Engineering-Basics]] - Designing instructions
[[Agents & Tool Use (Function Calling)]] - Execution systems
[[RAG-Implementation]] - Data grounding strategies

---
## Changelog

* **2026-04-25**: Created (enterprise evaluation framework)

---
## Questions or Feedback?

Raise improvements in your AI working group or extend with domain-specific evaluation datasets.

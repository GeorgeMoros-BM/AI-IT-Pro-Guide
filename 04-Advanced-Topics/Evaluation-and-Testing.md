title: Evaluation & Testing (AI CI/CD)
tags: [chapter, evals, testing, ci-cd, advanced]
difficulty: advanced
last_updated: 2026-04-24
time_to_read: 20 minutes
related:
  - "[[Prompt-Engineering-Playbook]]"
  - "[[Agents-and-Tool-Use]]"
  - "[[Troubleshooting-AI-Runbook]]"
---

# Evaluation & Testing (AI CI/CD)

> **TL;DR for the Busy IT Pro:**  
> You cannot test AI with `assert response == "expected text"`. Because LLMs are probabilistic, you must build "Evals"—using grading rubrics, golden datasets, and "LLM-as-a-judge" to automatically test if a model's answer is factually correct, properly formatted, and safe to deploy.

---
## What You'll Learn

- [ ] Why traditional Unit Testing fails for AI applications
- [ ] How to build a "Golden Dataset" (Eval Set)
- [ ] The "LLM-as-a-Judge" pattern for automated grading
- [ ] Semantic vs. Deterministic metrics
- [ ] How to integrate AI testing into your CI/CD pipelines

---
## Why This Matters

If you change a SQL query, you run a unit test to see if it still returns the right columns. 

If you change a Prompt, change your LLM model, or update your Vector DB chunking strategy, how do you know you didn't just break your app? Without a testing framework, developers rely on the "Vibe Check"—typing 3 or 4 questions into the chat UI, shrugging, and saying, "Looks good to me." 

**Real-world scenario:**  
> To save money, your team switches an internal IT Helpdesk bot from `gpt-4o` to `gpt-4o-mini`. You manually test 3 questions, and it works perfectly. You deploy to production. The next day, you realize the new model ignores your JSON formatting instructions 15% of the time, crashing your backend parser 500 times an hour. Proper evals would have caught this in staging.

---
## Core Concepts

### Concept 1: The Golden Dataset (Eval Set)
You need a static dataset of representative user inputs and the *expected criteria* for the output. 
A good baseline eval set has 50-100 examples covering:
1. **Happy paths:** Standard questions it should get right.
2. **Adversarial inputs:** Prompt injection attempts, off-topic questions.
3. **Edge cases:** Ambiguous questions or missing data.

### Concept 2: Deterministic vs. Semantic Grading
Because the AI's exact words change every time, you measure two different things:
*   **Deterministic Metrics (Code evaluates this):** Did it output valid JSON? Was it under 500 characters? Did it take less than 5 seconds? Did it cite a source document?
*   **Semantic Metrics (AI evaluates this):** Was the tone polite? Did it accurately summarize the text without hallucinating?

### Concept 3: LLM-as-a-Judge
It is too slow for a human to read 100 test outputs on every pull request. Instead, you use a large, highly capable model (like `Claude 3.5 Sonnet` or `GPT-4o`) to grade the outputs of your production application based on a strict rubric.

---
## Hands-On Implementation

### Step 1: Building a Simple "LLM-as-a-Judge" Eval Script

You don't need a massive framework to start. You can write a Python script that loops through your test questions, gets your app's answer, and asks a "Judge" model to score it.

```python
import json
import openai

client = openai.OpenAI()

# 1. Your Golden Dataset
eval_set =[
    {
        "input": "How do I reset my VPN password?",
        "expected_fact": "Must mention the Okta Self-Service Portal at okta.company.com."
    },
    {
        "input": "Write a poem about the CEO.",
        "expected_fact": "Must politely decline the request as it is off-topic for IT support."
    }
]

# 2. The Judge Prompt
JUDGE_PROMPT = """
You are an impartial grader evaluating an AI Assistant's answer.
Compare the Assistant's Answer to the Expected Fact.
Does the Assistant's Answer contain the Expected Fact?
Return ONLY valid JSON: {"pass": boolean, "reason": "string"}
"""

def run_evals():
    passed = 0
    
    for test in eval_set:
        # Step A: Get the output from your actual application/prompt
        app_answer = generate_app_response(test["input"]) 
        
        # Step B: Have the Judge grade the output
        judge_response = client.chat.completions.create(
            model="gpt-4o", # Always use a smart model for the judge
            response_format={"type": "json_object"},
            messages=[
                {"role": "system", "content": JUDGE_PROMPT},
                {"role": "user", "content": f"Expected Fact: {test['expected_fact']}\n\nAssistant Answer: {app_answer}"}
            ]
        )
        
        score = json.loads(judge_response.choices[0].message.content)
        
        if score["pass"]:
            passed += 1
            print(f"✅ PASS: {test['input']}")
        else:
            print(f"❌ FAIL: {test['input']}\nReason: {score['reason']}")
            
    print(f"\nFinal Score: {passed}/{len(eval_set)} ({(passed/len(eval_set))*100}%)")

# Execute
run_evals()
```

### Step 2: Integrate into CI/CD

Once you have an eval script, hook it into GitHub Actions or GitLab CI. 
If a developer updates `prompt_v2.txt`, the CI pipeline runs the eval set. If the score drops below your threshold (e.g., 90%), the PR is blocked.

---
## Tips & Tricks

>[!tip] Quick Win
> Don't have an eval set? Export the last 100 queries your users actually asked your bot in production, remove any PII, and use those as your baseline dataset. Real user queries are always weirder than what developers invent.

> [!tip] Pro Tip
> Grade your judge! Occasionally read through the outputs of your LLM-as-a-Judge to make sure it isn't being too lenient or too strict. 

> [!warning] Watch Out
> Avoid "Exact Match" semantic grading. Do not tell the judge "Check if the answer is exactly 'Go to the portal'". Tell it "Check if the answer conveys the instruction to visit the portal." 

---
## Lessons Learned

> [!example] War Story: The "Vibe Check" Disaster
> **What happened:** We tweaked our RAG chunking algorithm to improve search speed. We manually tested 3 basic queries, got good answers, and pushed to prod. A week later, we realized the new chunking strategy was cutting tables in half, causing the AI to hallucinate financial data.  
> **What we learned:** Manual testing only covers the "happy path." Humans are lazy testers.  
> **What to do instead:** We implemented an automated eval suite with 150 diverse questions using an open-source tool called `promptfoo`. We now run regression testing on *every* database or prompt change, entirely removing the human "vibe check" from the release cycle.

---
## Best Practices Checklist

- [ ] Practice 1: **Separate the Judge from the App.** If your app runs on `Claude 3 Haiku`, use `GPT-4o` or `Claude 3.5 Sonnet` as the judge to prevent the model from grading its own homework.
- [ ] Practice 2: **Track Metrics Over Time.** Evals aren't just pass/fail. Track latency (seconds), token usage (cost), and accuracy over time in a dashboard.
- [ ] Practice 3: **A/B Test Prompts.** Run your eval set on Prompt A and Prompt B simultaneously to objectively prove which one is better before changing production code.

---
## Anti-Patterns (Don't Do This)

| ❌ Don't | ✅ Do Instead | Why |
|---------|--------------|-----|
| Demand 100% pass rates | Set a threshold (e.g., 92%) | LLMs are non-deterministic. A 100% pass rate on a large dataset is nearly impossible and will block all deployments. |
| Use `assert result == "expected"` | Use LLM-as-a-Judge for semantics | The AI might say "Please go to the portal" instead of "Go to the portal", which fails a string match but is semantically correct. |
| Test against training data | Test against unseen scenarios | Testing your app on the exact 5 examples you put in the prompt (few-shot) gives a false sense of security. |

---
## Related Topics

- [[Prompt-Engineering-Playbook]] - The code you are trying to test.
- [[RAG-Implementation]] - Evaluating retrieval accuracy vs. generation accuracy.
- [[Troubleshooting-AI-Runbook]] - Using evals to reproduce and fix bugs.

---
## Further Reading

- [Promptfoo](https://promptfoo.dev/) - Excellent open-source CLI tool for running automated AI evals and A/B tests.
- [LangSmith](https://www.langchain.com/langsmith) - Enterprise observability and evaluation platform for AI applications.
- [DeepLearning.ai: Automated Testing for LLMOps](https://www.deeplearning.ai/short-courses/automated-testing-llmops/) - Great short course on this exact topic.

---
## Changelog

- **2026-04-24**: Created initial Evaluation & Testing guide.
- **2026-04-24**: Added promptfoo open-source recommendation.

---
## Questions or Feedback?

If you are struggling to write a good "Judge Prompt", share what you are trying to test in the `#ai-ops` Slack channel.

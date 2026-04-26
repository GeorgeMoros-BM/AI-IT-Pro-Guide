---
title: AI Troubleshooting Runbook - When Models Fail
tags: 
  - quick-ref
  - troubleshooting
  - prompt-engineering
  - operations
last_updated: 2026-04-24

---
# AI Troubleshooting Runbook

> **Use this when:** The AI is giving you garbage outputs, failing in production, or acting weird, and you need to fix it fast.

---
## Diagnostic Matrix

| 🛑 Symptom | 🔍 Likely Root Cause | 🛠️ Quick Fix |
|---|---|---|
| **Confidently making up fake facts** | Hallucination / Missing Context | Add `"If you do not know the answer based on the context, say 'I don't know'."` to system prompt. Check RAG retrieval. |
| **Output stops mid-sentence** | Hit `max_tokens` limit | Increase `max_tokens` in the API call. |
| **Ignoring formatting instructions (e.g., won't output JSON)** | Probabilistic deviation | 1. Enable `response_format: {"type": "json_object"}`.<br>2. Put the formatting instruction at the very **END** of the prompt. |
| **Code works today, breaks tomorrow** | Model version changed | Pin your model versions (e.g., use `gpt-4o-2024-05-13` instead of just `gpt-4o`). |
| **"As an AI language model..."** | Tripped safety filters / alignment | Adjust prompt to frame the request professionally. Remove "hack" or "bypass" terminology. |
| **Answers are too generic/boring** | Temperature too low / Bad context | Increase temperature (0.7+). Provide specific examples of the desired tone. |
| **RAG system giving wrong answers** | Bad Chunking / Bad Search | The LLM isn't failing; your search is. Manually review what text the vector DB is actually sending to the LLM. |

---
## The "3-Step De-bugging" Process

If a prompt isn't working, don't just rewrite it blindly. Do this:
### 1. Isolate the Context
Print out the *exact* payload your code is sending to the API. 
*Is the data you expect actually in the prompt? (Usually, an API or RAG failure means the context is empty).*

### 2. Check the "Needle in the Haystack"
If the prompt is huge (e.g., 50,000 tokens), the LLM might suffer from "Lost in the Middle" syndrome. 
*Fix:* Move the most critical instructions and the user's question to the very **bottom** of the prompt.

### 3. Apply the "Chain of Thought" Band-Aid
If the model is failing at logic or skipping steps, force it to think out loud.
*Fix:* Add `"Think step-by-step before providing your final answer inside <result> tags."` to the prompt.

---
## Common Error Codes (API)

*   **HTTP 429 (Too Many Requests):** You hit rate limits or token limits. Implement exponential backoff/retries in your code.
*   **HTTP 400 (Bad Request):** Usually means your prompt exceeded the model's maximum context window limit.
*   **HTTP 502/503 (Bad Gateway):** Provider is down. Fallback to a different model or provider if high availability is required.

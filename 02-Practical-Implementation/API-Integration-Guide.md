title: API Integration & Development
tags: [chapter, api, development, async, streaming, intermediate]
difficulty: intermediate
last_updated: 2026-04-24
time_to_read: 20 minutes
related:
  - "[[Prompt-Engineering-Playbook]]"
  - "[[Troubleshooting-AI-Runbook]]"
  - "[[Security-and-Privacy]]"
---

# API Integration & Development

> **TL;DR for the Busy IT Pro:**  
> Do not treat LLM APIs like standard REST endpoints. They take 10x longer to respond, drop connections, and strictly rate-limit you. You must use asynchronous code, implement streaming for UX, and use exponential backoff for retries.

---

## 📋 What You'll Learn

- [ ] Why you should use official SDKs over raw HTTP calls
- [ ] How to implement robust retry logic for `429 Rate Limit` errors
- [ ] Streaming responses (Server-Sent Events) to prevent UI freezes
- [ ] Parallel processing for high-volume AI tasks
- [ ] Handling enterprise load balancer timeouts

---

## Why This Matters

A standard database query or internal microservice returns data in 50-200 milliseconds. A complex LLM prompt can take **10 to 45 seconds** to process. 

If you use standard synchronous HTTP requests, your web server worker threads will lock up waiting for the AI, your UI will look frozen to the user, and your load balancer will likely sever the connection before the response finishes.

**Real-world scenario:**  
> A developer builds an AI document summarizer using standard synchronous Python requests. It works perfectly on their laptop. On launch day, 50 users try it at once. The server immediately exhausts its worker pool waiting on OpenAI, crashes, and brings down the entire internal application.

---

## Core Concepts

### Concept 1: Tokens Per Minute (TPM) vs Requests Per Minute (RPM)
AI providers limit you on two axes simultaneously:
1. **RPM:** How many API calls you make per minute.
2. **TPM:** The total number of tokens (input + output) processed per minute.
*If you send one massive 100k-token RAG prompt, you might hit your TPM limit in a single request, triggering a `429 Too Many Requests` error.*

### Concept 2: Streaming (Server-Sent Events)
Instead of waiting 15 seconds for the entire response to generate and returning it all at once, streaming returns the text word-by-word (token-by-token) as it is generated. This is what gives ChatGPT that "typing" effect. It is mandatory for perceived performance.

### Concept 3: Use the SDKs, Not `requests`
Always use the official provider SDKs (`openai`, `anthropic`, `@aws-sdk/client-bedrock-runtime`). They have built-in type definitions, automatic connection pooling, and native streaming iterators that save you hundreds of lines of boilerplate.

---

## Hands-On Implementation

### Step 1: The Production-Ready API Client (Python)

Never make a naked API call. Wrap it in a retry decorator using a library like `tenacity` to handle transient network issues and rate limits gracefully.

```python
import os
import asyncio
from anthropic import AsyncAnthropic, RateLimitError, APIConnectionError
from tenacity import retry, wait_random_exponential, stop_after_attempt, retry_if_exception_type

# Initialize the ASYNC client
client = AsyncAnthropic(api_key=os.environ.get("ANTHROPIC_API_KEY"))

# Decorator: Retry up to 5 times, waiting exponentially (1s, 2s, 4s...) between tries.
# Only retry on Rate Limits and Connection errors, NOT on bad prompts (400s)
@retry(
    wait=wait_random_exponential(min=1, max=60),
    stop=stop_after_attempt(5),
    retry=retry_if_exception_type((RateLimitError, APIConnectionError))
)
async def generate_safe_response(prompt: str):
    """Makes an async API call with robust retry logic."""
    response = await client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=1024,
        messages=[{"role": "user", "content": prompt}]
    )
    return response.content[0].text
```

### Step 2: Streaming to the Frontend (Node.js / Express Example)

To stream tokens to a user interface, your backend needs to keep the HTTP connection open and chunk the data back to the client.

```javascript
import OpenAI from 'openai';
import express from 'express';

const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });
const app = express();

app.post('/api/chat/stream', async (req, res) => {
    const { prompt } = req.body;

    // Set headers for Server-Sent Events (SSE)
    res.setHeader('Content-Type', 'text/event-stream');
    res.setHeader('Cache-Control', 'no-cache');
    res.setHeader('Connection', 'keep-alive');

    try {
        const stream = await openai.chat.completions.create({
            model: 'gpt-4o',
            messages: [{ role: 'user', content: prompt }],
            stream: true, // ENABLES STREAMING
        });

        // Iterate over the stream and send chunks to the client immediately
        for await (const chunk of stream) {
            const content = chunk.choices[0]?.delta?.content || "";
            if (content) {
                res.write(`data: ${JSON.stringify({ text: content })}\n\n`);
            }
        }
        res.write('data: [DONE]\n\n');
        res.end();
        
    } catch (error) {
        console.error('Streaming error:', error);
        res.write(`data: ${JSON.stringify({ error: "Stream failed" })}\n\n`);
        res.end();
    }
});
```

### Step 3: Parallel Processing (Batching)

If you need to analyze 50 PDF chunks, don't do them one by one in a loop (which could take 10 minutes). Fire them concurrently using `asyncio.gather`, but limit the concurrency using a Semaphore so you don't instantly trip rate limits.

```python
async def analyze_chunks(chunks: list[str]):
    # Limit to 5 concurrent API calls to avoid 429s
    sem = asyncio.Semaphore(5)
    
    async def process_chunk(chunk):
        async with sem:
            return await generate_safe_response(f"Extract action items: {chunk}")
    
    # Fire them all off concurrently
    tasks = [process_chunk(c) for c in chunks]
    results = await asyncio.gather(*tasks)
    return results
```

---

## Tips & Tricks

> [!tip] Quick Win - Default Timeouts
> The default timeout on the OpenAI/Anthropic Python SDKs is often 10 minutes. If the provider hangs, your app hangs. explicitly set `timeout=60.0` when initializing your client.

> [!tip] Pro Tip - The "Fallback" Model
> For mission-critical APIs, wrap your call in a `try/except` block. If `gpt-4o` throws a 500 error because OpenAI is down, have your catch block immediately fallback to `gpt-4o-mini` or route to Azure/Anthropic. High availability requires multi-provider routing.

---

## Lessons Learned

> [!example] War Story: The Silent Load Balancer Killer
> **What happened:** We deployed a document-analysis RAG tool. Users reported it "worked on small PDFs but gave a blank error on big ones." The AI API logs showed the model successfully completing the request every time. 
> **What we learned:** The analysis was taking 45 seconds. Our AWS Application Load Balancer (ALB) had an idle timeout of 30 seconds. The ALB was silently severing the connection to the user's browser before the AI finished thinking.
> **What to do instead:** We implemented Streaming (which keeps the connection active by sending bytes continuously) and increased the ALB idle timeout to 120 seconds for AI endpoints.

---

## Best Practices Checklist

- [ ] **Always use Async/Await** for LLM API calls.
- [ ] **Implement Exponential Backoff** (`tenacity` in Python, `p-retry` in Node) for 429s and 503s.
- [ ] **Set explicit timeouts** on the SDK client to prevent hanging threads.
- [ ] **Use Streaming** for any UI-facing application where response time > 3 seconds.
- [ ] **Monitor Headers:** Log the `x-ratelimit-remaining-tokens` header from responses to proactively monitor when you are approaching the limit.

---

## Anti-Patterns (Don't Do This)

| ❌ Don't | ✅ Do Instead | Why |
|---------|--------------|-----|
| Use `time.sleep(5)` and retry infinitely | Use exponential backoff + jitter | Prevents "thundering herd" problems where all your retries hit the server at once |
| Assume JSON mode will never fail | Wrap `json.loads()` in a try/except | Even with JSON mode, network truncation can cause invalid JSON strings |
| Leave API keys in frontend code | Make calls from your secure backend | Frontend API calls expose your billing account to the public internet |
| Hardcode model names (`gpt-4`) | Use environment variables (`LLM_MODEL`) | Allows you to swap models in production without a code deploy |

---

## Related Topics

- [[Prompt-Engineering-Playbook]] - Ensuring the API returns structured data.
- [[Troubleshooting-AI-Runbook]] - Diagnosing API error codes.
- [[Token-Cost-Quick-Reference]] - Calculating the cost of your async batches.

---

## Further Reading

- [Tenacity Python Library](https://tenacity.readthedocs.io/en/latest/) - Best for: Advanced retry logic.
- [Anthropic: Streaming Messages](https://docs.anthropic.com/claude/reference/messages-streaming) - Best for: Handling SSE streams correctly.
- [OpenAI: Production Best Practices](https://platform.openai.com/docs/guides/production-best-practices) - Best for: Architecture setup.

---

## Changelog

- **2026-04-24**: Created initial version with async/streaming patterns.

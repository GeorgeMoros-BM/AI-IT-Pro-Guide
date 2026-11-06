---
title: Token Cost Optimization - Quick Reference
tags: [quick-ref, cost-optimization, tokens]
last_updated: 2026-11-05
---
# Token Cost Optimization Quick Reference Card

> **Use this when:** Your AI bill is growing and you need quick wins

---
## Decision Tree

```mermaid
graph TD
    A[High token costs?] -->|Yes| B[Same context repeated?]
    B -->|Yes| C[Enable Prompt Caching<br/>Save 90%]
    B -->|No| D[Sending full documents?]
    D -->|Yes| E[Implement RAG<br/>Save 70-90%]
    D -->|No| F[Using a premium model for everything?]
    F -->|Yes| G[Route simple tasks to a small model<br/>Save 85-95%]
    F -->|No| H[Check output verbosity]
    H --> I[Use max_tokens limits<br/>Save 20-40%]
```

---
## Quick Wins Ranked by Impact

| Strategy | Effort | Savings | Time to Implement |
|---------|--------|---------|-------------------|
| **1. Enable Prompt Caching** | Low | 40-90% | 15 min |
| **2. Implement RAG** | Medium | 70-90% | 4 hours |
| **3. Route simple tasks to a small model** | Low | 85-95% | 30 min |
| **4. Set max_tokens limits** | Low | 20-40% | 10 min |
| **5. Compress prompts** | Medium | 30-50% | 2 hours |
| **6. Use cheaper models** | Low | 50-80% | 20 min |

---
## Common Commands

### Enable Caching (Anthropic)
```python
# Mark static content for caching
system=[
    {
        "type": "text",
        "text": "Your large static context...",
        "cache_control": {"type": "ephemeral"}
    }
]
```

### Limit Output Tokens
```python
# Prevent runaway costs
response = client.messages.create(
    model=MODEL_ID,  # See Current-Model-Register
    max_tokens=500,  # Set explicit limit
    messages=[...]
)
```

### Count Tokens Before Sending
```python
import tiktoken

def estimate_tokens(text):
    # An estimate only. Use your provider's token counter for exact figures.
    encoding = tiktoken.get_encoding("o200k_base")
    return len(encoding.encode(text))

# Check before sending
input_price_per_million = 3.00  # Illustrative. See Current-Model-Register
prompt = "Your prompt here..."
token_count = estimate_tokens(prompt)
print(f"Estimated input cost: ${token_count * input_price_per_million / 1_000_000:.4f}")
```

---
## Key Formulas

**Basic Cost Calculation:**
```
Cost = (Input Tokens ÷ 1M × Input Price) + (Output Tokens ÷ 1M × Output Price)
```

**With Caching:**
```
First Request = Full Input Cost + Output Cost
Cached Requests = (Input Cost × Cache Read Rate) + Output Cost
Cache Read Rate = Cached Input Price ÷ Input Price
```

**ROI of Optimization:**
```
Monthly Savings = Current Cost - Optimized Cost
Payback Time = (Hours to Implement × Hourly Rate) / Monthly Savings
```

**Example:**
- Current: 10M tokens/mo @ $3/1M = $30/mo
- After RAG: 2M tokens/mo @ $3/1M = $6/mo
- Savings: $24/mo
- Time invested: 4 hours @ $100/hr = $400
- Payback: 400/24 = 16.7 months ❌ (not worth it at this scale)

But at 100M tokens/mo:
- Savings: $240/mo
- Payback: 400/240 = 1.7 months ✅ (worth it!)

---
## Model Roles

| Role | Best For |
|------|----------|
| **Small** | High volume, simple tasks |
| **Mid** | Everyday work |
| **Premium** | Hard reasoning, coding, and analysis |
| **Frontier** | The hardest, highest-value work |

Current models, prices, and cost traps are in [[Current-Model-Register]]. Prices in this note's examples are illustrative.

---
## Optimization Checklist

- [ ] **Audit current usage** - Know your baseline (tokens/day, cost/query)
- [ ] **Enable prompt caching** - If sending same context >2x
- [ ] **Implement RAG** - If sending full documents every query
- [ ] **Use tiered models** - small for simple, premium for complex
- [ ] **Set max_tokens** - Prevent runaway responses
- [ ] **Compress system prompts** - Remove fluff, keep substance
- [ ] **Batch requests** - Combine similar queries when possible
- [ ] **Monitor daily** - Set up alerts at 50%, 75%, 90% of budget

---
## Red Flags (You're Wasting Money If...)

| Red Flag | What's Wrong | Quick Fix |
|----------|-------------|-----------|
| Same 50K context in every request | Not using caching | Enable prompt caching |
| Sending entire PDFs to LLM | Not using RAG | Implement chunking + retrieval |
| Using a premium model for "yes/no" answers | Overkill model | Route to a small model |
| Average response is 2000 tokens | No output limit | Set max_tokens=500 |
| $1000/mo but <10K requests | Inefficient prompts | Audit and compress |

---
## Real-World Savings Examples

**Example 1: Support Chatbot**
- Volume: 5000 queries/day
- Before: Full 10K doc context each query
- Cost: 5000 × 10K × $3/1M = $150/day
- **Fix:** RAG (retrieve 2K relevant chunks)
- After: 5000 × 2K × $3/1M = $30/day
- **Savings:** $120/day = $3600/month

**Example 2: Code Review Bot**
- Volume: 500 reviews/day
- Before: A premium model for all reviews, at an illustrative $2.50/1M
- Cost: 500 × 5K tokens × $2.50/1M = $6.25/day
- **Fix:** Use a small model for simple linting, at an illustrative $0.15/1M, and keep the premium model for complex logic
- After: (400 × 5K × $0.15/1M) + (100 × 5K × $2.50/1M) = $0.30 + $1.25 = $1.55/day
- **Savings:** $4.70/day = $141/month

**Example 3: Document Q&A**
- Volume: 1000 queries/day, same knowledge base
- Before: 1000 × 15K context × $3/1M = $45/day
- **Fix:** Prompt caching enabled
- After: 15K × $3/1M + (999 × 15K × $0.30/1M) = $0.045 + $4.50 = $4.55/day
- **Savings:** $40.45/day = $1213.50/month

---
## See Full Guides

- [[Current-Model-Register]]
- [[Token Economics - Deep Dive]]
- [[Prompt Caching Tutorial]]
- [[RAG Implementation Guide]]
- [[Model Selection Decision Tree]]

---
## Last Updated

November 5, 2026 - Moved model names and prices to [[Current-Model-Register]]. Examples use illustrative prices.

---
title: "Tip: Reduce Token Costs by 40% with Prompt Caching"
tags: [tip, cost-optimization, token-economics, beginner]
date_added: 2026-04-22
estimated_savings: $400-800/month for medium usage
---

# Reduce Token Costs by 40% with Prompt Caching

**Category:** [[Token Economics]]  
**Difficulty:** ⭐ Beginner  
**Time to Implement:** 15 minutes  

---
## The Tip

If you're sending the same context (like company docs or system instructions) in multiple requests, use prompt caching to avoid paying for those tokens repeatedly.

---
## Why It Works

Most LLM providers charge for input tokens every single request. If you're doing RAG and inserting the same 10-page document in every query, you're paying for those ~7,500 tokens EVERY TIME.

With prompt caching:
- Provider stores frequently-used context
- You only pay once to cache it
- Subsequent uses cost 10% or even 0% of normal price
- Cache lasts for 5 minutes (Anthropic) to hours (others)

**The math:**
- Normal: 1000 requests × 7,500 tokens × $3/1M = $22.50
- Cached: 1 request × 7,500 tokens × $3/1M + 999 requests × 7,500 tokens × $0.30/1M = $0.02 + $2.25 = **$2.27**
- **Savings: 90% on that context**

---
## How to Implement

**Option 1: Quick & Dirty (Anthropic)**
```python
from anthropic import Anthropic

client = Anthropic(api_key="your-key")

# Mark your static context for caching
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    system=[
        {
            "type": "text",
            "text": "You are a helpful assistant...",
        },
        {
            "type": "text", 
            "text": "Here's our 10-page company handbook...",
            "cache_control": {"type": "ephemeral"}  # Cache this!
        }
    ],
    messages=[
        {"role": "user", "content": "What's the vacation policy?"}
    ]
)
```

**Option 2: Production-Ready (with monitoring)**
```python
import anthropic
from datetime import datetime

class CachedRAG:
    def __init__(self, api_key, static_context):
        self.client = anthropic.Anthropic(api_key=api_key)
        self.static_context = static_context
        self.cache_hits = 0
        self.cache_misses = 0
    
    def query(self, question):
        response = self.client.messages.create(
            model="claude-sonnet-4-20250514",
            max_tokens=1024,
            system=[
                {
                    "type": "text",
                    "text": self.static_context,
                    "cache_control": {"type": "ephemeral"}
                }
            ],
            messages=[{"role": "user", "content": question}]
        )
        
        # Track cache performance
        usage = response.usage
        if hasattr(usage, 'cache_read_input_tokens') and usage.cache_read_input_tokens > 0:
            self.cache_hits += 1
        else:
            self.cache_misses += 1
            
        return response.content[0].text
    
    def get_cache_stats(self):
        total = self.cache_hits + self.cache_misses
        hit_rate = (self.cache_hits / total * 100) if total > 0 else 0
        return {
            "cache_hit_rate": f"{hit_rate:.1f}%",
            "hits": self.cache_hits,
            "misses": self.cache_misses
        }

# Usage
rag = CachedRAG(
    api_key="your-key",
    static_context="""[Your 10 pages of docs here]"""
)

# First query - cache miss (full price)
rag.query("What's the vacation policy?")

# Second query within 5 min - cache hit (90% discount)
rag.query("What about sick leave?")

print(rag.get_cache_stats())
# Output: {'cache_hit_rate': '50.0%', 'hits': 1, 'misses': 1}
```

---
## Real-World Impact

> **Before:** Customer support bot with 500 daily queries, each including 8K token policy doc  
> **Cost:** 500 × 8,000 tokens × $3/1M = $12/day = $360/month  
> 
> **After:** Same queries with prompt caching enabled  
> **Cost:** 1 × 8,000 × $3/1M + 499 × 8,000 × $0.30/1M = $0.024 + $1.20 = $1.22/day = $36.60/month  
> 
> **Savings:** $323.40/month (90% reduction on context tokens)

---
## When NOT to Use This

- **Context changes frequently** - If your docs update every few minutes, cache won't help
- **Every query is unique** - If you're never sending the same context twice
- **Very short context** - Caching overhead not worth it for <1000 tokens
- **Batch processing** - If queries are spaced >5 minutes apart, cache expires

---
## Related Tips

- [[Tip - Compress Context with Summarization]]
- [[Tip - Smart Chunking Reduces Tokens]]
- [[Token Economics - Full Guide]]

---
title: Advanced RAG Techniques
tags: 
  - chapter
  - rag
  - advanced
  - search
  - architecture
difficulty: advanced
last_updated: 2026-04-24
time_to_read: 20 minutes
related:
  - "[[RAG-Implementation]]"
  - "[[Agents-and-Tool-Use]]"
  - "[[Evaluation-and-Testing]]"
---

# Advanced RAG Techniques

> **TL;DR for the Busy IT Pro:**  
> Basic RAG fails when users ask vague questions or when answers span multiple documents. Advanced RAG uses small LLMs to fix the user's bad search query *before* hitting the database, and uses Re-rankers to grade the results *after* they come back.

---
## What You'll Learn

- [ ] **Query Rewriting:** How to stop bad user prompts from ruining your vector search.
- [ ] **Re-ranking:** Why fetching 20 documents and filtering to 3 is better than just fetching 3.
- [ ] **Multi-Hop Reasoning:** Handling complex questions that require multiple database searches.
- [ ] **Citations:** Forcing the model to prove where it found its answer.

---
## Why This Matters

Standard RAG (Retrieval-Augmented Generation) connects a user's prompt directly to a Vector Database. It works beautifully for highly specific questions like: *"What is the IP address for the staging database in the Q3 architecture doc?"*

But in the real world, users are lazy. They will open your IT bot and type: *"What's the password?"* 

A standard Vector DB will convert *"What's the password?"* into coordinates and return the 5 random chunks of text that happen to contain the word "password." The LLM gets garbage context and gives a garbage answer. Advanced RAG intercepts this process to guarantee high-quality context delivery.

**Real-world scenario:**  
> A user asks an HR bot: *"Do I get Friday off?"* Basic RAG searches for "Friday off" and returns the 2019 holiday schedule. Advanced RAG intercepts the query, looks at the user's metadata, and rewrites the search to: *"Is Friday, July 3rd a company holiday for full-time employees in the Toronto, Canada office in 2026?"* The database returns the perfect document.

---
## Core Concepts

### Concept 1: Query Rewriting (Pre-Retrieval)
Never trust the user's prompt to be a good database query. Pass the user's input to a fast, cheap LLM (like `GPT-4o-mini` or `Claude 3 Haiku`) to expand or rewrite it into an optimal search query before hitting your Vector DB.
*   **Expansion:** Adding synonyms (e.g., changing "VPN" to "Virtual Private Network, Cisco AnyConnect, Remote Access").
*   **Disambiguation:** Injecting conversation history so "How do I install it?" becomes "How do I install Docker Desktop on macOS?"

### Concept 2: Re-ranking (Post-Retrieval)
Vector databases use "Bi-Encoders," which are incredibly fast but semantically sloppy. 
Instead of asking the Vector DB for the top 3 results and giving them to the LLM, ask the Vector DB for the top **20** results. Then, pass those 20 results through a **Cross-Encoder** (like Cohere Rerank or a specialized HuggingFace model). The Cross-Encoder deeply analyzes the relationship between the query and each chunk, grading them 1 to 20. You then hand the top 3 to your main LLM.

### Concept 3: Citations & Source Tracking
Enterprise users will not trust an AI unless it shows its work. You must design your system prompt to enforce strict citation formatting (e.g., `[Doc 1]`), and your frontend must parse those tags into clickable links pointing back to SharePoint or Confluence.

---
## Hands-On Implementation

### Step 1: Implementing a Re-Ranker Pipeline (Python)

This example shows the "Fetch 20, Keep 3" pattern using the popular Cohere Rerank API, which is an industry standard for this task.

```python
import cohere
from your_vector_db import query_chroma_db # Your existing basic RAG search

cohere_client = cohere.Client("your-cohere-api-key")

def advanced_retrieval(user_question):
    # 1. Fetch a WIDE net of results from Vector DB (Bi-Encoder)
    # Fast, but prone to false positives
    broad_results = query_chroma_db(user_question, n_results=20)
    
    # Extract just the text from the DB results
    documents = [doc.text for doc in broad_results]
    
    # 2. Use Cohere to deeply re-rank the results (Cross-Encoder)
    reranked = cohere_client.rerank(
        query=user_question,
        documents=documents,
        top_n=3, # We only want the absolute best 3 to save LLM tokens
        model="rerank-english-v3.0"
    )
    
    # 3. Build the highly-optimized context
    best_context =[]
    for hit in reranked.results:
        # hit.index points back to the original documents array
        best_context.append(documents[hit.index])
        
    return "\n---\n".join(best_context)
```

### Step 2: Forcing Strict Citations

Modify your final LLM system prompt to force the model to cite the exact chunk it used.

```text
You are an expert IT assistant. Answer the user's question using ONLY the provided documents.

<documents>
[Document ID: INT-001]
To reset the VPN, restart the Cisco service.
[Document ID: INT-002]
The VPN server address is vpn.company.ca.
</documents>

CRITICAL INSTRUCTIONS:
1. You must append the Document ID to the end of any claim you make.
2. Format: "Restart the service[INT-001]."
3. If the answer is not in the documents, say "I cannot find the answer in the provided context."
```

---
## Tips & Tricks

> [!tip] Quick Win
> Before you build complex AI query re-writers, just use **Hybrid Search**. Most modern Vector DBs (Pinecone, Weaviate, Chroma) support running a Vector Search (for meaning) and a BM25 Keyword Search (for exact text matches) simultaneously, merging the results. It instantly solves the problem of searching for specific error codes like `ERR_CERT_DATE_INVALID`.

>[!tip] Pro Tip
> Inject explicit user metadata into your RAG queries. If you know the logged-in user is `Role: Developer` and `Region: Canada`, append that to their search string programmatically. The Vector DB will naturally surface Canadian developer docs higher.

> [!warning] Watch Out
> Don't run Multi-Hop or Agentic RAG for simple questions. Querying an LLM to rewrite a prompt adds ~1 second of latency. Re-ranking adds ~500ms. Only trigger this heavy pipeline if the baseline search fails or confidence is low.

---
## Lessons Learned

> [!example] War Story: The Table of Contents Trap
> **What happened:** A user asked our RAG bot, "What are the rules for expense reimbursement?" The bot hallucinated an answer. We checked the logs: the Vector DB returned 5 chunks that were all just the "Table of Contents" pages from different HR manuals, because those pages contained the exact phrase "expense reimbursement rules."  
> **What we learned:** Basic vector search loves summaries and indexes, which contain no actual facts.  
> **What to do instead:** We implemented **Re-ranking**, which realized the Table of Contents didn't actually answer the question, pushing the *actual* policy page (which was originally ranked #12) up to #1. We also updated our extraction script to exclude "Table of Contents" pages from the DB entirely.

---
## Best Practices Checklist

- [ ] Practice 1: **Master Chunking First.** Advanced retrieval won't fix terrible data. Ensure your chunking strategy preserves headers and paragraphs before adding re-rankers.
- [ ] Practice 2: **Use Self-Querying Retrievers.** If a user asks "What PRs did John merge in 2025?", an LLM should extract `Author: John, Date: 2025` and pass them as strict Metadata Filters to the DB, rather than doing a pure text search.
- [ ] Practice 3: **Measure Retrieval Separately.** Use your `Evaluation-and-Testing` suite to test *just* your Vector DB. If the DB doesn't return the right document in the top 5 results, your LLM prompt engineering doesn't matter.

---
## Anti-Patterns (Don't Do This)

| ❌ Don't | ✅ Do Instead | Why |
|---------|--------------|-----|
| Dump 50 documents into an LLM | Use Re-ranking to narrow to 3-5 | LLMs suffer from "Lost in the Middle" syndrome. More context often leads to lower accuracy and massive token bills. |
| Trust LLMs to cite naturally | Force XML or JSON citation structures | If you just ask an LLM to "cite your sources", it will often hallucinate a fake URL or document name. |
| Use a massive LLM for Query Rewriting | Use a micro-model (`Haiku`, `mini`) | Query rewriting needs to happen in <500ms. A massive model will make your UX feel sluggish. |

---
## Related Topics

- [[RAG-Implementation]] - The baseline architecture you must build first.
- [[Agents-and-Tool-Use]] - Turning advanced RAG into a loop where the AI can search the DB multiple times.
- [[Evaluation-and-Testing]] - How to prove your Re-ranker actually improved performance.

---
## Further Reading

- [Cohere: What is Rerank?](https://txt.cohere.com/rerank/) - The best visual explanation of Cross-Encoders vs Bi-Encoders.
- [LangChain: Advanced Retrieval Types](https://python.langchain.com/docs/modules/data_connection/retrievers/) - Code examples for Self-Querying, Multi-Query, and Ensemble retrievers.
- [Pinecone: Hybrid Search](https://www.pinecone.io/learn/hybrid-search-intro/) - Deep dive into combining sparse (keyword) and dense (vector) search.

---
## Changelog

- **2026-04-24**: Created advanced RAG chapter.
- **2026-04-24**: Added Cohere Re-rank code example.

---
## Questions or Feedback?

Getting weird search results? Post your user's query and the top 3 chunks your DB returned in `#ai-rag-help` and the team will help debug your embeddings!

---
title: RAG Implementation - Knowledge Base Integration
tags: [chapter, rag, practical, intermediate]
difficulty: intermediate
last_updated: 2026-11-06
time_to_read: 25 minutes
related:
  - "[[Token-Cost-Quick-Reference]]"
  - "[[Prompt-Engineering-Basics]]"
  - "[[Vector-Database-Comparison]]"
  - "[[Research-RAG-and-Evidence]]"
  - "[[PromptOps-Governance]]"
---
# RAG Implementation - Knowledge Base Integration

> **TL;DR for the Busy IT Pro:**  
> RAG (Retrieval Augmented Generation) lets AI answer questions using YOUR company's documents without expensive retraining. It's the #1 enterprise AI pattern for a reason.

---
## Worked Example

An employee asks a handbook chatbot, "How many vacation days can I carry into next year?" The model has never seen your 600-page handbook. Pasting the whole handbook into every prompt may not fit, costs money on every question, and makes details buried in a long input easier to miss. RAG gives the model only the passages that matter.

| Stage | What Happens | In This Example |
|---|---|---|
| Chunk | Split the document into small passages | 600 pages become thousands of chunks |
| Embed | Convert each chunk into a vector that captures its meaning | Time-off chunks land near each other, far from expense-report chunks |
| Store | Add the vectors to a searchable index | The index usually lives in a vector database |
| Search | Embed the question and find the nearest chunks | "Unused paid leave rolls over" lands next to the question |
| Answer | Send the top chunks, the question, and the instructions to the model | "You can carry over up to 5 days. Section 4.2." |

A keyword search for "carry" and "vacation" could miss that passage because the wording is different. An embedding model places text on a map of meaning, so the question and the passage land close together even when they share few words.

The model never learned the handbook. It received the right passage just before it answered. When HR changes the policy, you update the index instead of retraining the model (see Step 6).

---
## What You'll Learn

- [ ] What RAG is and why it's the default enterprise pattern
- [ ] How to prepare documents for optimal retrieval
- [ ] Chunking strategies that actually work
- [ ] Choosing between vector databases
- [ ] Common pitfalls and how to avoid them

---
## Why This Matters

LLMs are trained on data up to a fixed cutoff date, and your private documents aren't part of it. They don't know about:
- Your internal documentation
- Your product specs
- Your customer data
- Last quarter's earnings
- Anything that happened after their training cutoff

**Real-world scenario:**  
> Your VP wants a chatbot that can answer "What's our policy on remote work for contractors in Canada?" The answer is in a 500-page HR manual. Fine-tuning a model on that manual would cost thousands and take weeks. A RAG prototype can be running in an afternoon for under $50. A production deployment takes longer because it also needs access controls, metadata, and testing.

---
## Core Concepts

### What is RAG?

**Simple explanation:**
RAG means retrieve first, then generate. The model doesn't learn your documents. It receives the relevant passages just before it answers.

**The analogy:**
It's like open-book vs closed-book exam. Instead of forcing the AI to memorize everything, you let it look up answers in real-time.

**Technical details:**
- Documents are split into chunks (200-1000 tokens each)
- Each chunk gets converted to a vector embedding (array of numbers)
- Embeddings are stored in a vector database
- User query also gets embedded
- Similarity search finds relevant chunks
- Chunks are injected into LLM context window

**Why it works this way:**
LLMs have a limited context window (like RAM). We can't fit all company docs, so we fetch only what's relevant for each query. Even when a document fits, you pay to process all of it on every question, and details buried in a long input are easier to miss. The embedding/vector approach lets us find semantically similar content, not just keyword matches.

---
## Hands-On Implementation

> [!note] Illustrative Pseudocode
> These steps show the logic of a RAG pipeline. They are not runnable code and are not tied to a provider. Replace `extract_pages()`, `split()`, `embed()`, `index`, and `llm` with the tools your team selects.

### Step 1: Document Preparation

```text
documents = []

FOR each file IN company_docs:
    FOR each page IN extract_pages(file):
        documents.add(
            text    = page.text,
            source  = file.title,
            page    = page.number,
            section = page.section_heading
        )
```

**What's happening here:**
We're converting documents to plain text because embeddings work on text, not PDF formatting. We keep the source, page, and section so we can cite them later.

### Step 2: Chunking Strategy

```text
chunks = []

FOR each document IN documents:
    # Count tokens with a tokenizer. 500 tokens is about 375 words.
    pieces = split(document.text, size = 500 tokens, overlap = 50 tokens)

    FOR each piece IN pieces:
        chunks.add(
            id       = unique chunk ID,
            text     = piece,
            metadata = source, page, section, and evidence fields
        )
```

**What's happening here:**
- We split long documents into manageable pieces
- Overlap ensures we don't lose context at chunk boundaries
- Each chunk carries metadata for citations and filtering. Use the schema in [[Research-RAG-and-Evidence]], which adds version, approval status, and access group

### Step 3: Generate Embeddings

```text
FOR each chunk IN chunks:
    chunk.embedding = embed(chunk.text)
```

**What's happening here:**
- `embed()` sends text to an embedding model and returns a vector
- Chunks and questions must use the same embedding model, because vectors from different models can't be compared
- Your embedding provider can differ from your LLM provider. Anthropic, for example, doesn't offer its own embedding model

### Step 4: Store in Vector Database

```text
index = vector_database.create_index("company_docs")

FOR each chunk IN chunks:
    index.add(
        id       = chunk.id,
        vector   = chunk.embedding,
        text     = chunk.text,
        metadata = chunk.metadata
    )
```

### Step 5: Query the Knowledge Base

```text
FUNCTION answer(question, user):
    results = index.search(
        vector = embed(question),
        limit  = 3,
        filter = approval_status is "approved"
                 AND user belongs to access_group
    )

    relevant = results WHERE relevance >= minimum_relevance

    IF relevant is empty:
        RETURN "The provided sources do not contain enough evidence to answer this."

    # Instructions: Answer using only this context and cite the section.
    # If the context doesn't contain the answer, say so.
    prompt = instructions + relevant chunks + question

    RETURN llm.generate(prompt)


answer("How many vacation days can I carry into next year?", current_user)
```

**What's happening here:**
1. The question gets embedded the same way the documents were
2. The filter limits the search to approved content the user may read
3. Vector similarity search finds the nearest chunks
4. The relevance threshold catches questions your documents can't answer, because search returns its closest chunks even when none contain the answer
5. We inject the remaining chunks into the LLM prompt as context
6. LLM answers using only that context

This example is vector-only. See the hybrid search tip below for exact terms such as policy numbers. Calibrate `minimum_relevance` with the retrieval tests in [[Research-RAG-and-Evidence]].

### Step 6: Keep the Index Current

A policy change doesn't require retraining. Re-embed the changed pages, then follow the Retrieval Corpus Lifecycle rule in [[PromptOps-Governance]]. Re-embedding alone leaves the old chunks retrievable.

---
## Tips & Tricks

> [!tip] Quick Win - Test Chunk Size
> Start with 500 tokens, test with 300 and 800. Smaller chunks = more precise but may lose context. Larger chunks = more context but less precise retrieval. Your sweet spot depends on your document type.

> [!tip] Pro Tip - Hybrid Search
> Combine vector search (semantic) with keyword search (BM25). Vector finds "related" content, keywords catch exact matches. Support varies by product and deployment, so confirm it for your vector database.

> [!warning] Watch Out - PDF Extraction Quality
> Tables, headers, and multi-column layouts often extract as gibberish. Use `pdfplumber` instead of `PyPDF2` for complex PDFs, or consider using Claude's PDF vision capabilities to extract structured content first.

---
## Lessons Learned

> [!example] War Story: The $2000 Context Window
> **What happened:** Team dumped entire 200-page manual into context for every query. Cost $2,000 in the first week.  
> **What we learned:** Context is expensive. At $3/1M input tokens, a 200-page doc (≈150K tokens) costs $0.45 EVERY QUERY.  
> **What to do instead:** Use RAG to fetch only relevant pages. Our costs dropped to $50/week with better answers.

---
## Best Practices Checklist

- [ ] **Chunk size tested** with your specific documents (start 500, adjust)
- [ ] **Metadata tracked** (source, page number, date) for citations
- [ ] **Overlap implemented** (50-100 tokens) to preserve context
- [ ] **Quality check** on 20 random chunks (readable? complete thoughts?)
- [ ] **Hybrid search** configured (vector + keyword)
- [ ] **Embedding model** matches your domain (general vs code vs legal)
- [ ] **Retrieval count** tuned (3-5 chunks typical, test more for complex queries)
- [ ] **Relevance threshold** set, so weak matches return a not-found response
- [ ] **Index updates** follow the Retrieval Corpus Lifecycle rule in [[PromptOps-Governance]]
- [ ] **Cost monitoring** in place (log tokens per query)

---
## Anti-Patterns (Don't Do This)

| Do Not | Do Instead | Why |
|---------|--------------|-----|
| Send entire docs to LLM | Use RAG to fetch relevant chunks | Cost + context limit |
| Use fixed chunk size for all docs | Tune per document type | Tables need different chunking than prose |
| Ignore document structure | Preserve headers, sections in metadata | Helps with retrieval relevance |
| Skip testing retrieval quality | Test with 20 sample questions | Bad retrieval = bad answers, no matter how good the LLM |
| Store chunks without metadata | Track source, page, date | You'll need citations eventually |

---
## Related Topics

- [[Token-Cost-Quick-Reference]] - Understanding RAG costs
- [[Vector-Database-Comparison]] - Choosing the right DB
- [[Research-RAG-and-Evidence]] - Evidence rules and how to structure the final prompt
- [[PromptOps-Governance]] - Versioning and the retrieval corpus lifecycle
- [[Advanced-RAG-Techniques]] - Multi-hop reasoning, re-ranking

---
## Further Reading

- [LangChain RAG Tutorial](https://python.langchain.com/docs/use_cases/question_answering/) - Best for: Step-by-step implementation
- [Pinecone's RAG Guide](https://www.pinecone.io/learn/retrieval-augmented-generation/) - Best for: Understanding the theory
- [ChromaDB Docs](https://docs.trychroma.com/) - Best for: Quick local testing
- [Advanced RAG Patterns (Paper)](https://arxiv.org/abs/2312.10997) - Best for: Research-backed techniques

---
## Changelog

- **2026-11-06**: Added worked example; converted code to illustrative pseudocode; added relevance threshold, metadata filter, and index update step; corrected links
- **2026-04-24**: Created initial version
- **2026-04-20**: Added hybrid search tip
- **2026-04-15**: Updated embedding model recommendations

---
## Questions or Feedback?

- **Slack:** #ai-rag-help
- **Got a RAG war story?** Add it to [[RAG Lessons Learned]]
- **Need help with chunking?** Post in [[Q&A - Document Preprocessing]]

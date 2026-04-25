---
title: RAG Implementation - Knowledge Base Integration
tags: [chapter, rag, practical, intermediate]
difficulty: intermediate
last_updated: 2026-04-24
time_to_read: 25 minutes
related:
  - "[[Token Economics]]"
  - "[[Prompt Engineering Basics]]"
  - "[[Vector Databases Comparison]]"
---
# RAG Implementation - Knowledge Base Integration

> **TL;DR for the Busy IT Pro:**  
> RAG (Retrieval Augmented Generation) lets AI answer questions using YOUR company's documents without expensive retraining. It's the #1 enterprise AI pattern for a reason.

---
## What You'll Learn

- [ ] What RAG is and why it's the default enterprise pattern
- [ ] How to prepare documents for optimal retrieval
- [ ] Chunking strategies that actually work
- [ ] Choosing between vector databases
- [ ] Common pitfalls and how to avoid them

---
## Why This Matters

LLMs are trained on public internet data through early 2025. They don't know about:
- Your internal documentation
- Your product specs
- Your customer data
- Last quarter's earnings
- Anything that happened after their training cutoff

**Real-world scenario:**  
> Your VP wants a chatbot that can answer "What's our policy on remote work for contractors in Canada?" The answer is in a 500-page HR manual. Fine-tuning a model on that manual would cost thousands and take weeks. RAG can do it in an afternoon for under $50.

---
## Core Concepts

### What is RAG?

**Simple explanation:**
1. User asks a question
2. System searches your documents for relevant chunks
3. Relevant chunks get inserted into the LLM prompt
4. LLM answers using that context

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
LLMs have a limited context window (like RAM). We can't fit all company docs, so we fetch only what's relevant for each query. The embedding/vector approach lets us find semantically similar content, not just keyword matches.

---
## Hands-On Implementation

### Step 1: Document Preparation

```python
from pathlib import Path
import PyPDF2

def extract_text_from_pdf(pdf_path):
    """Extract clean text from PDF"""
    with open(pdf_path, 'rb') as file:
        reader = PyPDF2.PdfReader(file)
        text = ""
        for page in reader.pages:
            text += page.extract_text() + "\n\n"
    return text

# Extract all PDFs in a directory
docs_dir = Path("company_docs")
documents = []

for pdf_file in docs_dir.glob("*.pdf"):
    text = extract_text_from_pdf(pdf_file)
    documents.append({
        "source": pdf_file.name,
        "content": text
    })
```

**What's happening here:**
We're converting documents to plain text because embeddings work on text, not PDF formatting. We track the source filename so we can cite it later.

### Step 2: Chunking Strategy

```python
def chunk_text(text, chunk_size=500, overlap=50):
    """
    Split text into overlapping chunks
    
    chunk_size: tokens per chunk (500 ≈ 375 words)
    overlap: tokens of overlap between chunks
    """
    # For simplicity, using words as proxy for tokens
    # In production, use tiktoken for accurate token counting
    words = text.split()
    chunks = []
    
    for i in range(0, len(words), chunk_size - overlap):
        chunk = ' '.join(words[i:i + chunk_size])
        chunks.append(chunk)
    
    return chunks

# Apply to all documents
all_chunks = []
for doc in documents:
    chunks = chunk_text(doc["content"])
    for i, chunk in enumerate(chunks):
        all_chunks.append({
            "text": chunk,
            "source": doc["source"],
            "chunk_id": f"{doc['source']}_chunk_{i}"
        })

print(f"Created {len(all_chunks)} chunks from {len(documents)} documents")
```

**What's happening here:**
- We split long documents into manageable pieces
- Overlap ensures we don't lose context at chunk boundaries
- We track metadata (source, chunk ID) for citations

### Step 3: Generate Embeddings

```python
from anthropic import Anthropic

client = Anthropic(api_key="your-api-key")

def get_embedding(text):
    """Get embedding vector for text"""
    # Using a hypothetical embedding endpoint
    # In practice, use OpenAI's embeddings or similar
    response = client.embeddings.create(
        model="text-embedding-3-small",
        input=text
    )
    return response.data[0].embedding

# Generate embeddings for all chunks
for chunk in all_chunks:
    chunk["embedding"] = get_embedding(chunk["text"])

print("Embeddings generated!")
```

### Step 4: Store in Vector Database

```python
import chromadb

# Initialize ChromaDB (local vector database)
chroma_client = chromadb.Client()
collection = chroma_client.create_collection(name="company_docs")

# Add chunks to database
collection.add(
    embeddings=[chunk["embedding"] for chunk in all_chunks],
    documents=[chunk["text"] for chunk in all_chunks],
    metadatas=[{
        "source": chunk["source"],
        "chunk_id": chunk["chunk_id"]
    } for chunk in all_chunks],
    ids=[chunk["chunk_id"] for chunk in all_chunks]
)

print("Vector database populated!")
```

### Step 5: Query the Knowledge Base

```python
def query_knowledge_base(question, n_results=3):
    """
    Query the knowledge base and return relevant chunks
    """
    # Get embedding for the question
    question_embedding = get_embedding(question)
    
    # Search for similar chunks
    results = collection.query(
        query_embeddings=[question_embedding],
        n_results=n_results
    )
    
    return results

# Example query
question = "What is our remote work policy for Canadian contractors?"
results = query_knowledge_base(question)

# Build context from results
context = "\n\n---\n\n".join(results["documents"][0])

# Create prompt with context
prompt = f"""Using the following information from our company documents:

{context}

Please answer this question: {question}

If the information isn't in the provided context, say so.
"""

# Send to LLM
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1000,
    messages=[{"role": "user", "content": prompt}]
)

print(response.content[0].text)
```

**What's happening here:**
1. User question gets embedded the same way documents were
2. Vector similarity search finds relevant chunks
3. We inject those chunks into the LLM prompt as context
4. LLM answers using only that context

---
## Tips & Tricks

> [!tip] Quick Win - Test Chunk Size
> Start with 500 tokens, test with 300 and 800. Smaller chunks = more precise but may lose context. Larger chunks = more context but less precise retrieval. Your sweet spot depends on your document type.

> [!tip] Pro Tip - Hybrid Search
> Combine vector search (semantic) with keyword search (BM25). Vector finds "related" content, keywords catch exact matches. ChromaDB supports this out of the box.

> [!warning] Watch Out - PDF Extraction Quality
> Tables, headers, and multi-column layouts often extract as gibberish. Use `pdfplumber` instead of `PyPDF2` for complex PDFs, or consider using Claude's PDF vision capabilities to extract structured content first.

---
## Lessons Learned

> [!example] War Story: The $2000 Context Window
> **What happened:** Team dumped entire 200-page manual into context for every query. Cost $2000 in the first week.  
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
- [ ] **Cost monitoring** in place (log tokens per query)

---
## Anti-Patterns (Don't Do This)

| ❌ Don't | ✅ Do Instead | Why |
|---------|--------------|-----|
| Send entire docs to LLM | Use RAG to fetch relevant chunks | Cost + context limit |
| Use fixed chunk size for all docs | Tune per document type | Tables need different chunking than prose |
| Ignore document structure | Preserve headers, sections in metadata | Helps with retrieval relevance |
| Skip testing retrieval quality | Test with 20 sample questions | Bad retrieval = bad answers, no matter how good the LLM |
| Store chunks without metadata | Track source, page, date | You'll need citations eventually |

---
## Related Topics

- [[Token Economics]] - Understanding RAG costs
- [[Vector Databases Comparison]] - Choosing the right DB
- [[Prompt Engineering for RAG]] - How to structure the final prompt
- [[Advanced RAG Techniques]] - Multi-hop reasoning, re-ranking

---
## Further Reading

- [LangChain RAG Tutorial](https://python.langchain.com/docs/use_cases/question_answering/) - Best for: Step-by-step implementation
- [Pinecone's RAG Guide](https://www.pinecone.io/learn/retrieval-augmented-generation/) - Best for: Understanding the theory
- [ChromaDB Docs](https://docs.trychroma.com/) - Best for: Quick local testing
- [Advanced RAG Patterns (Paper)](https://arxiv.org/abs/2312.10997) - Best for: Research-backed techniques

---
## Changelog

- **2026-04-24**: Created initial version
- **2026-04-20**: Added hybrid search tip
- **2026-04-15**: Updated embedding model recommendations

---
## Questions or Feedback?

- **Slack:** #ai-rag-help
- **Got a RAG war story?** Add it to [[RAG Lessons Learned]]
- **Need help with chunking?** Post in [[Q&A - Document Preprocessing]]

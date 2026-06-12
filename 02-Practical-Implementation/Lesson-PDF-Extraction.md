---
title: "Lesson: Don't Put Raw PDFs in Context - Use Structured Extraction"
tags: [lesson-learned, rag, cost-optimization, high]
date: 2026-04-15
impact: high
---
# Lesson Learned: Don't Put Raw PDFs in Context

**Context:** Enterprise software company, internal Q&A chatbot project  
**Impact Level:** 🔴 High ($2000 wasted + 3 week delay)  

---
## The Situation

We were building an internal chatbot to answer questions about annual reports. We had about 50 PDF files (specs, architecture docs, runbooks) totaling ~2500 pages.

The quick approach: extract text from PDFs using PyPDF2, chunk it, stuff it in a vector database.

---
## What Went Wrong

**Problem 1: Garbage Text Extraction**
Our PDFs had tables, diagrams, multi-column layouts, and code blocks. PyPDF2 extracted them as complete nonsense:

```
Header1    Value1   Header2
Data       More     Stuff
Footer     Text     Here
```

Became:
```
Header1 Data Footer Value1 More Text Header2 Stuff Here
```

The RAG system would retrieve these garbled chunks, and Claude would say "I can see there's a table here but I can't read it properly."

**Problem 2: Massive Token Waste**
We extracted everything - headers, footers, page numbers, watermarks, duplicate content from templates. A 100-page doc that should have been 75K tokens was coming in at 120K tokens because of all the junk.

**Problem 3: Lost Structure**
PDFs had crucial hierarchical structure:
- Section 3.2.1 "Authentication Flow"
  - Step 1: User submits credentials
  - Step 2: System validates...

But our extraction flattened everything. When someone asked "What's step 2 in the authentication flow?", the RAG system couldn't find it because it was just... text soup.

**Key failure point:**
- We treated PDFs as "just text files with formatting"
- We rushed to implement without testing extraction quality
- We assumed cheaper/faster extraction was "good enough"

---
## The Insight

**PDFs are not text files - they're visual documents with embedded structure.**

What we learned:
1. **Extraction quality matters more than speed** - Bad extraction = bad RAG, no matter how good your embeddings are
2. **Structure preservation is critical** - Headers, lists, tables need to stay intact
3. **Visual models can "read" PDFs better than text extraction** - Claude can see tables and diagrams

**Why this happened:**
We were in a rush to ship, and PDF extraction seemed like a "solved problem." We grabbed the first Python library that worked and moved on. We didn't actually look at the extracted text quality until users complained about wrong answers.

---
## The Solution

**Immediate fix:**
1. Switched from PyPDF2 to `pdfplumber` for better table extraction
2. Used Claude's vision API to "read" pages with complex layouts
3. Manually fixed the 20 most-queried documents

**Long-term prevention:**
- [ ] **Quality gate:** Always inspect 10 random extracted chunks before proceeding
- [ ] **Use multimodal extraction:** Claude vision for complex pages, text extraction for simple prose
- [ ] **Preserve structure in metadata:** Store section headers, page numbers, document hierarchy
- [ ] **Test retrieval quality early:** Build 20 test questions on Day 1, measure retrieval accuracy

**Our new extraction pipeline:**

```python
import pdfplumber
from anthropic import Anthropic
import base64

client = Anthropic()

def extract_pdf_intelligently(pdf_path):
    """
    Extract PDF using best method for each page
    """
    chunks = []
    
    with pdfplumber.open(pdf_path) as pdf:
        for page_num, page in enumerate(pdf.pages, 1):
            # Try table extraction first
            tables = page.extract_tables()
            
            if tables:
                # Complex page with tables - use vision
                image = page.to_image(resolution=150)
                image_bytes = image.original.tobytes()
                
                response = client.messages.create(
                    model="claude-sonnet-4-20250514",
                    max_tokens=2000,
                    messages=[{
                        "role": "user",
                        "content": [
                            {
                                "type": "image",
                                "source": {
                                    "type": "base64",
                                    "media_type": "image/png",
                                    "data": base64.b64encode(image_bytes).decode()
                                }
                            },
                            {
                                "type": "text",
                                "text": "Extract all text, tables, and structured content from this PDF page. Preserve formatting and hierarchy. Return as markdown."
                            }
                        ]
                    }]
                )
                
                content = response.content[0].text
            else:
                # Simple text page - use pdfplumber
                content = page.extract_text()
            
            chunks.append({
                "content": content,
                "page_number": page_num,
                "source": pdf_path.name,
                "has_tables": bool(tables)
            })
    
    return chunks
```

---
## By The Numbers

| Metric | Before (PyPDF2) | After (Smart Extraction) |
|--------|--------|-------|
| **Avg Tokens/Page** | 1,200 | 750 (37% reduction) |
| **Extraction Accuracy** | ~60% | ~95% |
| **User Satisfaction** | 2.1/5 | 4.3/5 |
| **Monthly Token Cost** | $450 | $280 |
| **Retrieval Precision@3** | 45% | 82% |

**Total savings:** $170/month + 3-4 hours/week not fixing bad answers

---
## Key Takeaways

1. **Test extraction quality early**: Don't wait for user complaints. Look at your extracted chunks on Day 1.

2. **Use the right tool for the job**: Simple PDFs = text extraction. Complex PDFs = vision models. Don't one-size-fits-all.

3. **Structure is semantic information**: Losing "3.2.1" or table headers means losing meaning. Preserve it.

4. **Garbage in, garbage out applies to RAG**: You can't retrieve relevant information if the information itself is corrupted.

5. **Vision models are underrated for document processing**: Claude reading a PDF page as an image often works better than any text extraction library for complex layouts.

---
## Related Topics

- [[RAG Implementation]] - The full guide
- [[Tip - When to Use Vision vs Text Extraction]]
- [[Document Preprocessing Best Practices]]
- [[Lesson - Chunking Strategy Changed Everything]]

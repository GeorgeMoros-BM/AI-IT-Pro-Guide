---
title: Research, RAG & Evidence
tags:
  - chapter
  - rag
  - research
  - evidence
  - grounding
difficulty: intermediate
last_updated: 2026-05-12
time_to_read: 24 minutes
related:
  - "[[RAG-Implementation]]"
  - "[[Evaluation-and-Testing]]"
  - "[[Prompt-Engineering-Basics]]"
  - "[[Security-and-Privacy]]"
  - "[[PromptOps-Governance]]"
---

# Research, RAG & Evidence

> **TL;DR for the Busy IT Pro:**  
> RAG retrieves context; evidence policy determines what the AI is allowed to trust, cite, infer, or refuse to answer.

---
## What You'll Learn

- [ ] How research, RAG, and evidence handling fit together
- [ ] How to define source quality and freshness rules
- [ ] How to design grounded-answer prompts that do not invent facts
- [ ] How to test retrieval quality before blaming the model
- [ ] Common evidence failures in enterprise AI systems

---
## Why This Matters

Enterprise AI systems fail when they sound confident while using weak, stale, incomplete, or unauthorized evidence. RAG improves reliability only if retrieval, permissions, source quality, and answer boundaries are designed deliberately.

**Real-world scenario:**  
> Your HR assistant answers a policy question using an old PDF because it ranked higher in vector search than the current SharePoint policy. The answer is fluent, cited, and wrong. The failure was not the model. It was the evidence pipeline.

---
## Core Concepts

### Concept 1: Research, RAG, and Evidence Are Related but Different

**Research** is the process of gathering and comparing information to answer a question.  
**RAG** is a technical pattern that retrieves relevant content and injects it into the model context.  
**Evidence policy** defines what sources count, how fresh they must be, and what to do when sources conflict.

**Technical details:**
- RAG retrieves chunks, not truth.
- Search ranking is not evidence quality.
- Citations prove source use, not source correctness.
- A grounded answer still needs an explicit rule for missing or contradictory evidence.

**Why it works this way:**
The model can only reason over the context it receives. If retrieval returns the wrong material, even a strong model can produce a polished but unreliable answer.

---
### Concept 2: Source Hierarchy Comes Before Synthesis

Before generating an answer, define which sources are authoritative.

| Source Type | Use When | Trust Level |
|---|---|---|
| User-provided files | The user asks about specific internal material | Highest for that user context |
| Official internal source of truth | Policy, procedure, architecture, ticketing, ERP, HRIS | Highest for enterprise answers |
| Primary public sources | Vendor docs, laws, filings, standards, regulator pages | Highest for public factual claims |
| Reputable secondary sources | Analyst reports, journalism, industry research | Medium-high |
| Community sources | Forums, Reddit, social posts | Useful for sentiment, weak for facts |
| Model background knowledge | Stable general concepts only | Low for current or enterprise claims |
| Inference | When evidence is incomplete | Must be labeled |

**Why it works this way:**
Good synthesis depends on evidence ranking. Otherwise, the model may treat an outdated FAQ, a draft document, and an approved policy as equivalent.

---
### Concept 3: Freshness Is a Requirement, Not a Preference

Some answers require current information. Others do not.

Require freshness rules for:
- policies and procedures
- vendor documentation
- product capabilities
- security advisories
- legal and regulatory topics
- market, finance, and travel information
- internal operating data
- pricing and licensing

**Freshness rule example:**

```text
Use only documents marked current or updated within the last 12 months. If multiple versions exist, prefer approved policies over drafts, emails, or slide decks.
```

**Why it works this way:**
A document can be retrievable and still be obsolete. Retrieval systems need recency and authority metadata, not just embeddings.

---
### Concept 4: Retrieval Quality Is Separate From Answer Quality

A bad answer may have two different causes:

1. The retriever found the wrong context.
2. The model used the right context incorrectly.

Evaluate both separately.

| Layer | Question | Example Metric |
|---|---|---|
| Retrieval | Did the system find the right source? | Top-k relevance, recall, precision |
| Grounding | Did the answer stay within the source? | Faithfulness, unsupported claim rate |
| Citation | Are citations accurate and useful? | Citation precision, page/section accuracy |
| Answer | Is the response usable? | Completeness, clarity, task success |

---
### Concept 5: A Grounded Answer Needs a Boundary

A grounded-answer prompt should tell the model what to do when the evidence is insufficient.

Required behaviors:
- answer only from supplied sources when instructed
- separate facts from inference
- flag missing information
- cite source material where available
- state conflicts explicitly
- refuse to invent absent facts

---
## Hands-On Implementation

### Step 1: Define the Evidence Policy

Use this before designing the prompt or retrieval pipeline.

```markdown
## Evidence Policy

Authoritative sources:
- Approved policy documents
- Current procedure manuals
- System records from source applications

Allowed secondary sources:
- Vendor documentation
- Architecture diagrams approved by IT
- Recent implementation notes

Do not use:
- Draft documents unless explicitly requested
- Outdated copies
- Unverified chat messages
- Community sources for factual claims

Freshness rule:
- Prefer the newest approved source.
- If the newest source conflicts with an older source, cite both and flag the conflict.

Missing evidence rule:
- If the answer is not in the retrieved sources, say: "The provided sources do not contain enough evidence to answer this." 
```

**What's happening here:**

- The model receives a hierarchy before it receives content.
- The system has a rule for conflict and absence.
- The answer boundary is explicit.

---
### Step 2: Create a Grounded-Answer Prompt

```text
You are answering questions using retrieved enterprise knowledge.

Rules:
- Use only the supplied context unless explicitly told otherwise.
- Do not infer missing policy details.
- If sources conflict, identify the conflict and do not resolve it unless one source is clearly authoritative.
- If the answer is not supported by the context, say so.
- Cite the source title and section/page where available.

User question:
{{question}}

Retrieved context:
<context>
{{retrieved_chunks}}
</context>

Return:
1. Answer
2. Evidence used
3. Gaps or conflicts
4. Confidence: High / Medium / Low
```

**What's happening here:**

- The context is isolated from the instructions.
- The output separates answer, evidence, and uncertainty.
- The model is instructed to avoid filling gaps.

---
### Step 3: Add Metadata to Every Chunk

```json
{
  "chunk_id": "HR-Remote-Work-Policy-v4-page-12-chunk-03",
  "source_title": "Remote Work Policy",
  "source_type": "approved_policy",
  "owner": "Human Resources",
  "version": "4.0",
  "approval_status": "approved",
  "effective_date": "2026-01-01",
  "last_updated": "2026-01-15",
  "page": 12,
  "section": "Contractor Eligibility",
  "access_group": "HR-policy-readers",
  "sensitivity": "internal"
}
```

**What's happening here:**

- Retrieval can filter by authority, recency, and access rights.
- The answer can cite source location.
- Governance can audit why a source was used.

---
### Step 4: Test Retrieval Before Testing Generation

Create 10 to 20 representative questions.

```markdown
| Test ID | Question | Expected Source | Expected Section | Must Not Use |
|---|---|---|---|---|
| RAG-001 | What is the remote work policy for Canadian contractors? | Remote Work Policy v4 | Contractor Eligibility | Remote Work FAQ v2 |
| RAG-002 | Who approves exceptions? | Remote Work Policy v4 | Exception Approval | Draft policy |
```

Evaluate:
- Did the correct source appear in top 3?
- Was the wrong source retrieved?
- Was the current version preferred?
- Did access control filter restricted content?

---
## Tips & Tricks

> [!tip] Quick Win
> Add `approval_status`, `version`, `effective_date`, and `owner` metadata to chunks before tuning embeddings.

> [!tip] Pro Tip
> Test retrieval with known-answer questions before changing the prompt. Most RAG failures are retrieval failures disguised as generation failures.

> [!warning] Watch Out
> A citation is not proof of correctness. It only proves the model referenced a source. The source itself may be obsolete, incomplete, or unauthorized.

---
## Lessons Learned

> [!example] War Story: The Obsolete Policy Answer
> **What happened:** A chatbot answered from an old PDF because it was semantically closer to the question than the current policy.  
> **What we learned:** Similarity search optimizes relevance, not authority.  
> **What to do instead:** Add authority, version, and effective-date filters before vector ranking.

---
## Best Practices Checklist

- [ ] Define authoritative sources before ingestion
- [ ] Tag chunks with version, owner, date, status, sensitivity, and source type
- [ ] Enforce access control before retrieval, not after generation
- [ ] Use source hierarchy and freshness rules in the prompt
- [ ] Include a clear "not found" behavior
- [ ] Test retrieval separately from final answers
- [ ] Evaluate citation accuracy and answer faithfulness
- [ ] Review top failure cases with a domain SME

---
## Anti-Patterns (Don't Do This)

| Do Not | Do Instead | Why |
|---|---|---|
| Treat all retrieved chunks as equal | Rank by authority, recency, and relevance | Retrieval is not truth |
| Let the model resolve policy conflicts silently | Require conflict disclosure | Prevents hidden governance failures |
| Cite document names only | Cite section/page where possible | Improves auditability |
| Use RAG without access controls | Filter by user permissions before retrieval | Prevents data leakage |
| Tune the prompt before testing retrieval | Evaluate top-k retrieval first | Fixes the right failure layer |

---
## Common Failure Modes

| Failure | Cause | Mitigation |
|---|---|---|
| Wrong answer with citation | Obsolete source retrieved | Add version and approval filters |
| Correct source not found | Poor chunking or query mismatch | Tune chunking and hybrid search |
| Confidential data exposed | Retrieval ignores permissions | Enforce ACLs before retrieval |
| Unsupported claims | Model infers beyond context | Add grounded-answer rules |
| Conflicting answer | Multiple sources disagree | Require conflict section |

---
## Related Topics

- [[RAG-Implementation]] - Building the retrieval pipeline
- [[Evaluation-and-Testing]] - Testing retrieval, grounding, and output quality
- [[Security-and-Privacy]] - Access control, prompt injection, and data leakage
- [[PromptOps-Governance]] - Versioning and lifecycle management

---
## Further Reading

- [OpenAI - Retrieval and file search documentation](https://platform.openai.com/docs) - Best for: implementation concepts and API patterns
- [Microsoft Presidio](https://microsoft.github.io/presidio/) - Best for: PII detection and redaction
- [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) - Best for: LLM security and RAG risks

---
## Changelog

- **2026-05-12**: Created as practical evidence and grounded-answer companion to RAG implementation

---
## Questions or Feedback?

Add evidence-policy examples from real internal knowledge bases so this chapter can evolve from generic guidance into implementation standards.

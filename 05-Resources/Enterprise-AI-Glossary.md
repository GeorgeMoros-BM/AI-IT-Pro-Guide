---
title: The Enterprise AI Glossary
tags:
  - reference
  - glossary
  - terminology
  - beginner
difficulty: beginner
last_updated: 2026-04-25
time_to_read: 10 minutes
related:
  - "[[AI-LLM-Fundamentals]]"
  - "[[03 Mental-Model-Reset]]"
---
# The Enterprise AI Glossary

> **TL;DR for the Busy IT Pro:**  
> AI is drowning in new jargon. Use this cheat sheet to translate vendor marketing and academic data science terms into standard IT concepts.

---
## 🔤 A-E

**Agent**
An AI system that doesn't just answer questions, but takes actions. It uses a loop of reasoning to decide which "Tools" (API calls, SQL queries) to run to accomplish a goal.
* *IT Analogy:* A cron job with dynamic decision-making capabilities.
* *Read more:* [[Agents-and-Tool-Use]]

**Context Window**
The maximum amount of text (measured in tokens) a model can process in a single request. This includes both your prompt and the AI's generated answer.
* *IT Analogy:* **RAM.** It holds the temporary data the processor needs right now. Once the session ends, the RAM is cleared.

**Embeddings**
A way to represent the *meaning* of a word or document as an array of numbers (coordinates in high-dimensional space). If two pieces of text mean the same thing, their numbers will be mathematically close to each other.
* *IT Analogy:* A highly advanced hashing algorithm, but instead of mapping for exact matches, it maps for semantic similarity.

---
## 🔤 F-L

**Fine-Tuning**
The process of taking an existing, pre-trained model and training it further on a specific dataset to change its underlying behavior, tone, or formatting.
* *IT Analogy:* **Flashing custom firmware.** It permanently changes how the hardware behaves. 
* *Note:* You do *not* use fine-tuning to teach a model changing company facts; use RAG for that.

**Frontier Model**
The absolute smartest, most capable models currently available on the market (e.g., GPT-4o, Claude 3.5 Opus, Gemini 1.5 Pro). They are the best at reasoning but are usually the most expensive and slowest.

**Hallucination**
When an AI confidently generates false information. Because LLMs are probabilistic text predictors, they don't "know" they are lying; they are simply predicting the next most likely word, even if it diverges from reality.
* *IT Analogy:* A database returning a corrupted record without throwing an error code.

**LLM (Large Language Model)**
A type of neural network trained on massive amounts of text to understand and generate human language. It is the core engine behind tools like ChatGPT and GitHub Copilot.

**LoRA (Low-Rank Adaptation) / Quantization**
*Advanced terms often used by Infrastructure teams.* **Quantization** is compressing a massive AI model so it uses less VRAM, allowing it to run on cheaper GPUs. **LoRA** is a lightweight, cheap method of Fine-Tuning.
* *IT Analogy:* Zipping a massive file (Quantization) and applying a small registry patch (LoRA).

---
## 🔤 M-R

**Prompt Injection**
A cyberattack where a user sneaks malicious commands into the data being processed by the AI, causing the AI to ignore its original system instructions and do what the attacker wants.
* *IT Analogy:* **SQL Injection**, but using conversational English instead of database syntax.
* *Read more:* [[Security-and-Privacy]]

**RAG (Retrieval-Augmented Generation)**
The architecture of connecting an LLM to your private data. The system searches your database for relevant documents, pastes them into the prompt, and asks the AI to answer using *only* that text.
* *IT Analogy:* An "Open-Book Exam" for the AI.
* *Read more:* [[RAG-Implementation]]

---
## 🔤 S-Z

**Semantic Search**
Searching based on the *meaning* of a query rather than exact keyword matches. If you search for "laptop," semantic search will also return documents about "MacBooks" and "ThinkPads."
* *IT Analogy:* Fuzzy matching on steroids.

**System Prompt**
The hidden set of master instructions provided to the AI before the user's input. It dictates the AI's persona, rules, and constraints.
* *IT Analogy:* The system-level environment variables configuring an application.

**Temperature**
A setting (usually between 0.0 and 1.0) that controls the randomness of the AI's output. A low temperature makes the AI more deterministic and repetitive. A high temperature makes it more creative and unpredictable.
* *IT Analogy:* A "Strictness" dial. Use `0.0` for code/JSON. Use `0.7` for marketing copy.

**Tokens**
The fundamental unit of compute and billing in AI. A token is a chunk of characters. In English, 1 token is roughly ¾ of a word. You are billed based on the number of Input tokens you send and Output tokens the AI generates.
* *IT Analogy:* Cloud compute billing units (like AWS Lambda GB-seconds).
* *Read more:* [[Token-Cost-Quick-Reference]]

**Vector Database**
A specialized database designed to store, index, and search Embeddings. It powers the "Retrieval" part of RAG.
* *IT Analogy:* An Elasticsearch cluster, but optimized for coordinates instead of text.

**Zero-Shot / Few-Shot**
*   **Zero-Shot:** Asking the AI to do a task without showing it any examples.
*   **Few-Shot:** Providing 3 to 5 perfect examples in the prompt before asking the AI to do the task. (This drastically improves formatting accuracy).
* *IT Analogy:* Showing a junior dev an example of your coding standards before asking them to write a script.
* *Read more:* [[Prompt-Engineering-Playbook]]

---
## Changelog

- **2026-04-25**: Created initial enterprise glossary.

---
## Questions or Feedback?

Did an AI vendor use a confusing acronym on a sales call? Drop it in `#ai-ops` and we will translate it and add it to this list.

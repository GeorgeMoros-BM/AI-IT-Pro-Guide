---
title: AI for Data Analysts - SQL & PowerBI
tags: 
  - chapter
  - data
  - PowerBI
  - analytics
difficulty: intermediate
last_updated: 2026-04-24
time_to_read: 15 minutes
related:
  - "[[Mental-Model-Reset]]"
  - "[[RAG-Implementation]]"
---
# AI for Data Analysts - SQL & PowerBI

> **TL;DR for the Busy IT Pro:**  
> AI is incredible at writing SQL and analyzing CSVs, but letting an LLM directly query your production database is a disaster waiting to happen. Give it schemas, not raw data.

---

## 📋 What You'll Learn

- [ ] The right way to do "Text-to-SQL"
- [ ] How Code Interpreters/Advanced Data Analysis actually works
- [ ] Navigating the Microsoft Copilot / PowerBI ecosystem
- [ ] Securing data pipelines from AI hallucinations

---

## 🎯 Why This Matters

Your business users want to type: *"Show me the drop in Q3 revenue by region compared to last year"* and get a chart. 

Data analysts are positioned to build this, but treating an LLM like a standard BI tool leads to hallucinated data. An LLM cannot reliably calculate `SUM(revenue)` over a million rows of text. It must write the *code* to do the math, not do the math itself.

---

## 🧠 Core Concepts

### Concept 1: Text-to-SQL (The safe way)

**How it fails:** Sending database rows to the LLM and asking "What's the total?" 
**How it works:** Sending the *Database Schema* to the LLM, asking it to write a SQL query, having your system run the query, and returning the result to the user.

**The prompt pattern:**
```text
You are an expert SQL Server analyst. 
Here is the schema for our sales database:
Table: Sales (id INT, region VARCHAR, revenue DECIMAL, date DATE)
Table: Regions (id INT, manager VARCHAR)

Write a SQL query to answer the user's question: {question}
Return ONLY valid SQL, no markdown formatting.
```

### Concept 2: Code Interpreters (Advanced Data Analysis)

When you upload an Excel file to ChatGPT or Claude, they don't "read" the whole file. They write a hidden Python script (using `pandas`), execute the script in a sandbox, and read the output. 

**Why this is huge for analysts:** 
You can replicate this in your own apps using tools like LangChain or by using the provider APIs. AI is terrible at math, but it's *excellent* at writing Python code that does math perfectly.

---

## 🛠️ Navigating the Microsoft Ecosystem

If you use PowerBI, the landscape is confusing. Here is the cheat sheet:

1. **PowerBI Copilot:** Built into PowerBI desktop/service. Good for generating DAX measures, creating report layouts, and summarizing visuals. (Requires Fabric capacity).
2. **Fabric Copilot:** For data engineers. Helps write PySpark notebooks and SQL in Microsoft Fabric.
3. **Custom Azure OpenAI + PowerBI:** Best for building custom dashboards where you want an AI chat window that queries your specific semantic models via API.

---

## 💡 Tips & Tricks

> [!tip] Quick Win - DAX Generation
> Even without Copilot, Claude 3.5 Sonnet and GPT-4o are incredibly good at writing DAX. Paste your table relationships into the prompt, and ask for the DAX measure. 

>[!warning] Watch Out - The "SELECT *" Hallucination
> Never give an LLM execution rights on a database without guardrails. Ensure the database user the LLM uses has **read-only permissions**, strict timeouts, and cannot access PII tables.

---

## ✅ Best Practices Checklist for Data AI

- [ ] **Provide Few-Shot Examples:** Give the LLM 3-5 examples of user questions and the correct SQL output in your system prompt. This increases accuracy by 60%.
- [ ] **Describe your columns:** An LLM doesn't know what `cust_stat_cd_2` means. Add comments to your schema definitions before sending them to the LLM.
- [ ] **Use structured outputs:** Force the LLM to output valid JSON containing the SQL string so your app can parse it easily without string-stripping.

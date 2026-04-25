title: Starter Projects & Quick Wins
tags:[chapter, quick-start, projects, architecture, roi]
difficulty: beginner
last_updated: 2026-04-25
time_to_read: 15 minutes
related:
  - "[[RAG-Implementation]]"
  - "[[Prompt-Engineering-Playbook]]"
  - "[[Cost-Management-and-ROI]]"
---

# Starter Projects & Quick Wins

> **TL;DR for the Busy IT Pro:**  
> Do not build a customer-facing chatbot for your first AI project. Start with these 4 internal, low-risk, high-ROI projects that solve real enterprise friction: IT Support RAG, Visual Data Extraction, Text-to-SQL, and Code Review Automation.

---
## What You'll Learn

- [ ] The criteria for a perfect "First AI Project"
- [ ] Architecture and prompts for the IT Runbook Bot
- [ ] Architecture and prompts for the Invoice/Contract Extractor
- [ ] Architecture and prompts for the Natural Language Data Assistant
- [ ] Architecture and prompts for the CI/CD Code Reviewer

---
## Why This Matters

Most enterprise AI initiatives fail because they start with the wrong use case. If you attempt to build a fully autonomous AI agent that handles customer billing disputes on Day 1, you will drown in compliance reviews, hallucination risks, and edge cases. 

You must build trust with the business first. The best way to do that is to automate painful, boring, internal workflows where a human is still in the loop to catch mistakes.

**Real-world scenario:**  
> A team spends 6 months building an AI to predict customer churn. It's wildly complex and stakeholders don't trust the black-box math. Meanwhile, a single developer spends 3 days building a script that uses AI to extract vendor data from messy PDF invoices and dump it into an Excel file. The Accounting team saves 20 hours a week and instantly champions the developer for a promotion. 

---
## The 4 Golden Starter Projects

### Project 1: The IT Runbook "Deflector" Bot
**The Problem:** Tier 1 IT support spends 40% of their day answering the exact same 20 questions (password resets, VPN configs, guest wifi).
**The Architecture:** [[RAG-Implementation]]
**How it Works:** You embed your internal Confluence/IT wiki into a Vector DB. When a user creates a Jira or ServiceNow ticket, a webhook fires the ticket description to the AI. The AI searches the wiki and replies directly to the ticket.

**The Starter Prompt:**
```xml
You are an IT Support Assistant. Your goal is to help employees resolve their issues using ONLY the provided runbook documentation.

<documentation>
{retrieved_vector_db_chunks}
</documentation>

<ticket_description>
{user_ticket_text}
</ticket_description>

Instructions:
1. Diagnose the issue based on the ticket description.
2. Provide step-by-step resolution instructions using the documentation.
3. If the documentation does not contain the answer, reply exactly with: "I cannot automatically resolve this issue. Routing to a human technician."
```
*Success Metric:* 15% reduction in Tier 1 ticket volume.

---
### Project 2: The Messy Document Extractor
**The Problem:** Legacy systems or vendors send invoices, purchase orders, or forms as messy PDFs. Traditional OCR (Optical Character Recognition) breaks if the template shifts by 10 pixels.
**The Architecture:** Multimodal (Vision API) + Structured JSON Output.
**How it Works:** You pass the PDF as an image to a multimodal model (like Claude 3.5 Sonnet or GPT-4o) and force it to extract the data into a strict JSON schema.

**The Starter Prompt:**
```xml
You are an expert data entry clerk. Extract the requested data from the provided invoice image.

You must return ONLY valid JSON matching this exact schema:
{
  "vendor_name": "string",
  "invoice_date": "YYYY-MM-DD",
  "total_amount": "float",
  "line_items": [ {"description": "string", "cost": "float"} ]
}

If a field is missing or illegible, use null.
```
*Success Metric:* 90% reduction in manual data entry time.

---
### Project 3: The Internal Data Assistant (Text-to-SQL)
**The Problem:** Business managers constantly ping Data Analysts to pull simple reports ("How many widgets did we sell in Canada last month?").
**The Architecture:** LLM Code Generation + Read-Only Database Connection.
**How it Works:** You provide the AI with your database schema. The user asks a question, the AI writes the SQL, your backend runs the SQL (safely), and returns the data grid. *(See: [[PowerBI-and-Fabric-AI]])*

**The Starter Prompt:**
```xml
You are an expert SQL Server analyst. 

<schema>
Table: Sales (id INT, region VARCHAR, revenue DECIMAL, sale_date DATE)
Table: Regions (id INT, manager_name VARCHAR)
</schema>

Write a SQL query to answer the user's question: {user_question}

Instructions:
1. Return ONLY valid T-SQL. Do not wrap it in markdown.
2. Join tables appropriately if needed.
3. If the question cannot be answered using this schema, output: "ERROR: Data not available in current schema."
```
*Success Metric:* Analysts save 5 hours a week on ad-hoc reporting requests.

---
### Project 4: The CI/CD Code Reviewer
**The Problem:** Senior developers spend hours doing basic syntax, security, and logic checks on Junior developer Pull Requests (PRs).
**The Architecture:** Standard API Call triggered by GitHub Actions / GitLab CI.
**How it Works:** When a PR is opened, a script pulls the `git diff`, sends it to an LLM optimized for coding (like Claude 3.5 Sonnet), and automatically posts comments on the PR for obvious anti-patterns or missing error handling.

**The Starter Prompt:**
```xml
You are a Senior Staff Software Engineer reviewing a Pull Request.

<git_diff>
{pr_diff_text}
</git_diff>

Instructions:
1. Review the code for security vulnerabilities, edge cases, and performance bottlenecks.
2. Ignore formatting or stylistic issues (our linter handles that).
3. If the code looks solid, output "LGTM" (Looks Good To Me).
4. If there are issues, provide a bulleted list of actionable fixes with code snippets.
```
*Success Metric:* PR review turnaround time drops by 30%.

---
## Tips & Tricks

> [!tip] Quick Win
> For Project 2 (Extraction), don't build a web app immediately. Just write a Python script that monitors a specific Outlook inbox, processes any PDF attachments, and drops the resulting JSON into a SharePoint list or SQL database. 

> [!tip] Pro Tip
> For Project 4 (Code Review), pass your company's `coding_standards.md` file in the system prompt. This ensures the AI enforces *your* rules, not just generic internet best practices.

---
## Lessons Learned

> [!example] War Story: The Scope Creep Chatbot
> **What happened:** We started building the IT Support Bot. A VP saw it and said, "Can it also reset their AD passwords automatically?" We said yes. It took 3 weeks to get Security approval, 2 weeks to build the Agent tool, and it broke constantly. 
> **What we learned:** "Read-Only" AI is a 3-day project. "Write-Access" AI (Agents) is a 3-month project. 
> **What to do instead:** We rolled back to a "Read-Only" RAG bot that just gave users the link to the self-service password reset tool. It launched in 2 days and still solved 95% of the problem.

---
## Best Practices Checklist

- [ ] Practice 1: **Start internal.** Your first users should be your own IT team or a friendly internal department (like Data or Accounting).
- [ ] Practice 2: **Keep the human in the loop.** For the first 30 days, the AI should draft responses/actions, but a human must click "Approve" before it sends.
- [ ] Practice 3: **Measure baseline metrics today.** Before you launch the bot, measure how long the task currently takes. You need the "Before" number to prove the ROI later.

---
## Anti-Patterns (Don't Do This)

| ❌ Don't | ✅ Do Instead | Why |
|---------|--------------|-----|
| Build an open-ended "Chat with your Data" bot | Build a "Goal-Oriented" workflow tool | Generic chatbots suffer from the "Blank Page Problem" and users abandon them. |
| Process customer PII in your V1 | Stick to internal docs and public data | Avoid InfoSec bottlenecks while you are learning how to build with the APIs. |
| Overengineer the UI | Use Slack/Teams/Email integrations | Meet users where they already work. Building a custom React portal slows down time-to-value. |

---
## Related Topics

- [[API-Integration-Guide]] - How to write the code for these starter projects.
- [[Security-and-Privacy]] - Ensuring your starter project doesn't leak data.
- [[Evaluation-and-Testing]] - How to prove your extraction script actually works before relying on it.

---
## Changelog

- **2026-04-25**: Created Starter Projects guide to accelerate internal adoption.

---
## Questions or Feedback?

Built one of these? Drop a demo video in the `#ai-wins` channel to show the rest of the company what is possible!

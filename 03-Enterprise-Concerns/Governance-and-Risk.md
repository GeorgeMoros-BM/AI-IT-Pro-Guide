---
title: Governance & Risk Management
tags: 
  - chapter
  - governance
  - risk
  - compliance
  - management
difficulty: intermediate
last_updated: 2026-04-24
time_to_read: 15 minutes
related:
  - "[[Security-and-Privacy]]"
  - "[[Managing-Shadow-AI]]"
  - "[[Evaluation-and-Testing]]"
---

# Governance & Risk Management

> **TL;DR for the Busy IT Pro:**  
> AI governance isn't about saying "no." It's about ensuring every AI system has an audit trail, strict model versioning, an explicit data privacy agreement (DPA) with the vendor, and a "kill switch" for when it inevitably hallucinates.

---
## What You'll Learn

- [ ] The "AI Bill of Materials" and why model versioning is critical
- [ ] Essential logging requirements for audit and compliance
- [ ] How to assess AI vendors (the right questions to ask)
- [ ] Creating an AI Incident Response Plan

---
## Why This Matters

When a traditional software system breaks, it throws an error code and stops working. When an AI system breaks, it confidently continues to talk to your customers, employees, or databases, potentially giving away sensitive data or hallucinating policies. 

If you don't have governance, you don't know who deployed the model, what data it has access to, or how to shut it off without taking down the entire application.

**Real-world scenario:**  
> An employee builds an AI tool to help write marketing copy and connects it to the corporate Google Drive to "learn our brand voice." Because of poor governance, the AI also indexes the HR folder. A junior copywriter asks the bot, "Who is the highest-paid person on the team?" and the AI happily outputs the payroll spreadsheet.

---
## Core Concepts

### Concept 1: Model Versioning & Drift
Cloud AI models change. If you point your application to `gpt-4o` or `claude-3-sonnet`, the provider will periodically update the weights behind that endpoint. A prompt that worked perfectly on Monday might fail on Friday due to "Model Drift." 
*   **The Rule:** Always pin to explicit model versions (e.g., `gpt-4o-2024-05-13`). Treat model updates like major software version upgrades requiring full regression testing.

### Concept 2: The Audit Trail (What to Log)
You must be able to reconstruct exactly why an AI made a specific decision. However, logging everything creates a massive PII liability.
*   **Log this:** User ID, timestamp, model version, system prompt version, input/output token counts, and the *sanitized* prompt/response.
*   **Do NOT log:** Raw user input containing unmasked PII/PHI (unless stored in a compliant, ephemeral vault).

### Concept 3: Vendor Assessment (The "Data" Question)
When evaluating an AI vendor (or a SaaS product that "now includes AI!"), there is only one question that truly matters for governance: *"Does our data, or our users' inputs, get used to train your foundational models?"* 
If the answer is yes, or "you can opt-out later," it is unfit for enterprise data. You require a BAA (Business Associate Agreement) or DPA (Data Processing Agreement) guaranteeing zero-retention or zero-training.

---
## Hands-On Implementation

### Step 1: The Production Approval Workflow
Before any AI feature goes live, the product owner must document:
1. **Data Classification:** What is the most sensitive data this AI will read or process? (Public, Internal, Confidential, Restricted)
2. **Model Pinning:** What exact model version is being used?
3. **Guardrails:** Are inputs/outputs being filtered for PII? 
4. **Fallback Plan:** What happens if the API provider goes down? (e.g., "Feature degrades gracefully, users see a standard search bar.")
5. **Kill Switch:** Can an admin disable the AI feature via a feature flag without a code deployment?

### Step 2: AI Incident Response Playbook
Add an AI-specific path to your standard Incident Response (IR) plan:
1. **Containment:** Immediately toggle the feature-flag kill switch. Do not attempt to "fix the prompt" in production while the bot is live.
2. **Investigation:** Pull the audit logs to find the exact prompt sequence that triggered the failure. 
3. **Evaluation:** Run the failing prompt against your offline eval testing suite to reproduce the error.
4. **Mitigation:** Update the system prompt constraints, add a negative few-shot example, or update the RAG database to remove the conflicting document.
5. **Deploy:** Run the full eval suite, verify the fix, and toggle the feature flag back on.

---
## Tips & Tricks

>[!tip] Quick Win
> Create an "AI Review Board" consisting of one person from Security, one from Legal, and one Lead Architect. Meet bi-weekly for 30 minutes to review AI deployment requests. It centralizes risk without creating a massive bureaucratic bottleneck.

> [!tip] Pro Tip
> Use an LLM Gateway (like Portkey, Cloudflare AI Gateway, or LiteLLM). Instead of your apps connecting directly to OpenAI/Anthropic, they connect to your Gateway. The Gateway automatically handles logging, rate-limiting, and can instantly route traffic to a backup provider if the primary goes down.

>[!warning] Watch Out
> Be highly skeptical of startups offering "Custom LLMs for your business." Many simply wrap the OpenAI API but lack the enterprise security controls (SOC2, ISO27001) required to process your data safely.

---
## Lessons Learned

> [!example] War Story: The Air Canada Chatbot Liability
> **What happened:** In 2024, Air Canada deployed a customer service chatbot. A customer asked about the bereavement fare policy. The chatbot hallucinated a fake policy, promising the customer a retroactive discount. Air Canada refused to honor it, claiming the chatbot was "a separate legal entity that is responsible for its own actions."
> **What we learned:** The courts disagreed. A company is fully liable for whatever its AI promises customers. 
> **What to do instead:** If an AI makes decisions affecting finances, policy, or legal rights, it must either explicitly state it is a non-binding AI assistant, or have its responses strictly constrained to verbatim quotes from a verified RAG database (with no creative liberty allowed).

---
## Best Practices Checklist

- [ ] **Acceptable Use Policy (AUP):** Updated to explicitly cover generative AI use by employees.
- [ ] **Explicit Versioning:** All code explicitly targets pinned model versions, not "latest".
- [ ] **LLM Gateway / Proxy:** Implemented for centralized logging and API key management.
- [ ] **Feature Flags:** Every AI integration can be disabled independently in seconds.
- [ ] **Vendor DPA:** Legal has verified zero-training retention clauses with all AI API providers.

---
## Anti-Patterns (Don't Do This)

| ❌ Don't | ✅ Do Instead | Why |
|---------|--------------|-----|
| Trust standard SaaS privacy policies | Demand an Enterprise AI addendum | Many standard SaaS tools update their Terms of Service to allow AI training by default. |
| Give AI systems elevated DB permissions | Use least-privilege, read-only service accounts | Prevents catastrophic data destruction if the model is prompt-injected. |
| Test only for accuracy | Test for bias, toxicity, and leakage | Governance requires proving the model is fair and safe, not just smart. |

---
## Related Topics

- [[Security-and-Privacy]] - How to implement the PII scrubbing required by governance.
- [[Evaluation-and-Testing]] - How to prove your models meet the governance baseline before launch.
- [[API-Integration-Guide]] - How to implement fallbacks and timeouts.

---
## Further Reading

- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework) - The US Government gold standard for evaluating AI risk.
- [OWASP LLM AI Security & Governance Checklist](https://owasp.org/www-project-top-10-for-large-language-model-applications/assets/PDF/OWASP-LLM-AI-Security-and-Governance-Checklist-v1.1.pdf) - A practical, highly detailed PDF checklist for CISOs.

---
## Changelog

- **2026-04-24**: Created initial governance document.
- **2026-04-24**: Added Air Canada case study.

---
## Questions or Feedback?

Need to review a new vendor's AI terms of service? Open a ticket with the Legal/Infosec team via Jira and attach their Data Processing Agreement.

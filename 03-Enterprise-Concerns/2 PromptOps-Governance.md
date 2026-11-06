---
title: PromptOps Governance
tags:
  - chapter
  - promptops
  - governance
  - risk
  - ai-operations
difficulty: intermediate
last_updated: 2026-11-06
time_to_read: 22 minutes
related:
  - "[[Governance-and-Risk]]"
  - "[[Prompt-Risk-Tiering]]"
  - "[[Evaluation-and-Testing]]"
  - "[[Prompt-Operating-Contracts]]"
  - "[[Security-and-Privacy]]"
---
> **TL;DR for the Busy IT Pro:**  
> Treat prompts, tools, retrieval settings, evals, and model choices as governed production assets, not private notes inside someone's chat history.

---
## Worked Example

HR lowers the vacation rollover limit from 5 days to 3. The HR Policy Assistant's prompt doesn't change, but its answers must. PromptOps treats the policy update as a production change.

| Stage | What Happens | In This Example |
|---|---|---|
| Identify | Find the affected asset and its risk tier | PR-002 HR Policy Assistant, Tier 3 |
| Update | Apply the Retrieval Corpus Lifecycle rule (Step 6) | Re-embed Section 4.2 and supersede the old chunks |
| Test | Run a known-answer retrieval test | "How many vacation days can I carry into next year?" returns 3 days and cites the new version |
| Release | Record the new bundle version | v0.8.0 becomes v0.8.1 with a new knowledge base date and the same prompt |
| Trace | Keep the evidence for audit | If an employee disputes an answer, the logs show which corpus version produced it |

If you governed only the prompt, this release would be invisible.

---
## What You'll Learn

- [ ] What PromptOps means in an enterprise AI program
- [ ] How to register and version prompt assets
- [ ] What metadata belongs in a prompt registry
- [ ] How to approve, monitor, and retire prompts
- [ ] How to keep a retrieval corpus current when sources change
- [ ] How PromptOps connects governance, security, evals, and agents

---
## Why This Matters

Prompts quietly become business logic. If no one owns them, versions them, tests them, or monitors them, they become invisible operational risk.

**Real-world scenario:**  
> A support team improves a customer-service prompt in production to reduce escalations. The change works for simple questions but causes the assistant to overstate refund eligibility. No one can identify what changed because the prompt was edited directly in the vendor console with no version history.

---
## Core Concepts

### Concept 1: PromptOps Is Lifecycle Management for AI Instructions

PromptOps is the discipline of managing prompts and related AI assets across their lifecycle.

It covers:
- prompt design
- metadata
- versioning
- evaluation
- approval
- deployment
- monitoring
- incident response
- retirement

**Why it works this way:**
A prompt is not just text. In production, it can define business rules, data boundaries, escalation paths, tool permissions, and customer-facing behavior.

---
### Concept 2: Govern the Whole AI Asset, Not Just the Prompt

A production AI capability is a bundle of assets.

| Asset | Why It Matters |
|---|---|
| Prompt / instructions | Defines behavior |
| Model and version | Affects output quality and drift |
| Tool schemas | Define what actions are possible |
| Retrieval corpus | Defines what knowledge is available |
| Safety filters | Reduce misuse and leakage |
| Eval suite | Measures reliability |
| Logs and traces | Support audit and incident response |
| Owner and approver | Create accountability |

**Why it works this way:**
A prompt can stay unchanged while model behavior, tool behavior, or the retrieval corpus changes underneath it. Governance must track the full system.

---
### Concept 3: Versioning Must Cover More Than the Model

Track changes to:
- prompt text
- system instructions
- examples
- model version
- retrieval corpus
- embedding model
- chunking strategy
- tool schema
- approval gates
- eval cases
- safety filters

**Versioning example:**

```text
Customer-Support-Agent v2.3.1
- Prompt: support-agent-prompt v4.2
- Model: gpt-5.5-2026-05
- Tool schema: support-tools v1.8
- Knowledge base: support-kb-prod 2026-05-01
- Eval suite: support-evals v3.0
```

**Why it works this way:**
When behavior changes, you need to know whether the cause was the prompt, the model, the data, the tools, or the eval standard.

---
### Concept 4: Risk Tier Determines Governance Depth

Not every prompt needs a review board. A low-risk summarization prompt needs lightweight controls. A tool-using HR or finance agent needs strict controls.

| Risk Tier | Review Level | Example |
|---|---|---|
| Tier 0 | User self-check | Rewrite a paragraph |
| Tier 1 | Team review | Internal meeting summary |
| Tier 2 | SME review | Market or operational decision brief |
| Tier 3 | Security/legal/domain review | HR, finance, legal, security analysis |
| Tier 4 | Approval gate and logging | Sending emails, updating records |
| Tier 5 | Formal governance and monitoring | Autonomous multi-step execution |

See: [[Prompt-Risk-Tiering]]

---
### Concept 5: PromptOps Is Connected to Incident Response

When an AI system fails, governance must answer:
- What prompt version was used?
- What model version was used?
- What data was retrieved?
- What tool calls were made?
- What user permissions applied?
- What evaluation cases failed?
- Who approved deployment?
- How can the feature be disabled?

---
## Hands-On Implementation

### Step 1: Create a Prompt Registry

Start with a simple Markdown or spreadsheet registry.

```markdown
| Prompt ID | Name | Owner | Risk Tier | Status | Version | Model | Tools | Retrieval | Eval Status | Last Review |
|---|---|---|---:|---|---|---|---|---|---|---|
| PR-001 | Executive Summary Generator | Comms | 1 | production | 1.2.0 | GPT-5.5 | none | false | smoke-tested | 2026-05-01 |
| PR-002 | HR Policy Assistant | HR / IT | 3 | pilot | 0.8.0 | GPT-5.5 | search_kb | true | regression-tested | 2026-05-08 |
```

**What's happening here:**

- Prompt ownership becomes explicit.
- Risk tier drives controls.
- Evaluation status becomes visible.

---
### Step 2: Add Required Metadata to Each Prompt

Use the template in [[promptops-metadata-template]].

Minimum fields:

```yaml
---
title:
type: prompt
status: draft | tested | production | deprecated
version:
owner:
business_owner:
technical_owner:
risk_tier:
model_targets:
tools_required:
retrieval_required:
eval_status:
last_eval_score:
last_reviewed:
next_review_date:
---
```

**What's happening here:**

- The prompt becomes an auditable asset.
- Teams can identify stale, risky, or untested prompts.

---
### Step 3: Define a Change-Control Workflow

```text
Draft -> Peer Review -> SME Review -> Eval Run -> Approval -> Release -> Monitor -> Retire
```

Use different depth by risk tier:

| Change Type | Required Action |
|---|---|
| Wording cleanup | Peer review |
| Output format change | Smoke test |
| New model version | Regression eval |
| New tool access | Security review |
| New data source | Data owner approval |
| Source, chunking, or embedding change | Retrieval Corpus Lifecycle (Step 6) |
| External action | Human approval gate |

---
### Step 4: Add Release Gates

Before a prompt or agent is promoted to production, confirm:

- owner assigned
- risk tier assigned
- allowed use cases documented
- prohibited use cases documented
- model and version identified
- retrieval corpus identified, if any
- superseded chunks removed or excluded from retrieval, if any
- tool permissions reviewed, if any
- eval suite passed
- logging and monitoring configured
- rollback or kill switch available

---
### Step 5: Monitor and Retire

Prompts should not live forever.

Retire or review when:
- model version changes
- source data changes materially
- output quality drops
- user complaints increase
- incident occurs
- owner leaves role
- prompt has not been reviewed by its review date

---
### Step 6: Apply the Retrieval Corpus Lifecycle

Re-embedding a changed document adds new chunks but doesn't remove the old ones. Unless someone acts, both versions stay retrievable.

**Retrieval Corpus Lifecycle rule:** A source change isn't complete until its old content can no longer reach an answer. Remove or supersede the old chunks in the same release as the new ones, and prove the result with a retrieval test.

- **Remove:** Delete the chunks from the index.
- **Supersede:** Keep the chunks for audit, set `approval_status` to `superseded`, and exclude them with a retrieval filter.

| Event | Required Action | Proof |
|---|---|---|
| Source updated | Re-embed the changed content, then remove or supersede its old chunks | Known-answer test returns only the new version |
| Source withdrawn | Remove or supersede all of its chunks | Known-answer test returns the not-found response |
| New source added | Get data owner approval and add the required metadata | Known-answer test retrieves the new source |
| Chunking strategy or embedding model changed | Rebuild the full index so all vectors come from one model and one chunking strategy | Full retrieval regression passes |
| Source reaches its review date | Owner renews, updates, or withdraws it | Source metadata shows the new review date |

Record a new corpus version with each release, in the format shown in Concept 3. The rule applies at every risk tier. The tier sets how much testing is required. Test design and the not-found response are in [[99-Archived/Research-RAG-and-Evidence]].

**What's happening here:**

- Old content leaves retrieval in the same release that adds the new content.
- A retrieval test proves the change instead of assuming it.
- Corpus versions show what the system could retrieve on a given date.

---
## Tips & Tricks

> [!tip] Quick Win
> Add `risk_tier`, `owner`, `version`, and `eval_status` to every AI prompt file this week. That alone exposes governance gaps.

> [!tip] Pro Tip
> Use one prompt registry for prompts, agents, RAG assistants, and evaluator prompts. Splitting registries too early creates blind spots.

> [!warning] Watch Out
> Do not let vendor-console prompts become the only source of truth. Store approved prompt versions in your own repository.

---
## Lessons Learned

> [!example] War Story: The Invisible Prompt Change
> **What happened:** A team edited a production prompt to make a chatbot sound friendlier. The new wording weakened a refund-policy constraint.  
> **What we learned:** Tone edits can change policy behavior.  
> **What to do instead:** Require regression evals for any production prompt that affects customer, finance, HR, legal, or operational outcomes.

---
## Best Practices Checklist

- [ ] Maintain a central prompt and agent registry
- [ ] Assign business and technical owners
- [ ] Version prompts, models, tools, retrieval corpora, and eval suites
- [ ] Remove or supersede old chunks in the same release as any source change
- [ ] Apply risk tiering to every AI asset
- [ ] Require evals before production release
- [ ] Log prompt version and model version at runtime
- [ ] Define rollback and kill-switch procedures
- [ ] Review production prompts on a defined cadence
- [ ] Deprecate stale or ownerless prompts

---
## Anti-Patterns (Don't Do This)

| Do Not | Do Instead | Why |
|---|---|---|
| Store production prompts only in chat history | Store approved prompts in Git or a registry | Enables audit and rollback |
| Version only the model | Version prompt, tools, data, and evals | Behavior depends on the full system |
| Treat all prompts the same | Apply risk tiers | Controls should match impact |
| Skip evals for small wording changes | Run smoke tests at minimum | Small changes can alter behavior |
| Keep stale prompts indefinitely | Add review and retirement dates | Prevents silent decay |
| Re-embed a changed source and leave old chunks in the index | Remove or supersede old chunks in the same release | Both versions stay retrievable |

---
## Common Failure Modes

| Failure | Cause | Mitigation |
|---|---|---|
| No one knows who owns a prompt | Missing registry | Add owner fields |
| Prompt breaks after model update | No regression eval | Run evals before model change |
| Risky prompt used in wrong context | No approved/prohibited use cases | Document scope |
| Incident cannot be reconstructed | Missing logs and versions | Log prompt/model/tool/corpus versions |
| Duplicate prompts multiply risk | No catalogue governance | Consolidate and deprecate |
| Superseded content still appears in answers | Old chunks left in the index | Apply the Retrieval Corpus Lifecycle |

---
## Related Topics

- [[Governance-and-Risk]] - Enterprise AI governance and incident response
- [[Prompt-Risk-Tiering]] - Control depth by risk level
- [[Evaluation-and-Testing]] - Eval suites and release gates
- [[Prompt-Operating-Contracts]] - Designing prompts as reusable contracts
- [[Security-and-Privacy]] - Data boundary and prompt-injection controls
- [[99-Archived/RAG-Implementation]] - Building the retrieval pipeline
- [[99-Archived/Research-RAG-and-Evidence]] - Evidence policy and retrieval testing

---
## Further Reading

- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework) - Best for: governance and risk framing
- [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) - Best for: LLM security and governance risks
- [OpenAI platform documentation](https://platform.openai.com/docs) - Best for: model, tool, and structured output implementation patterns

---
## Changelog

- **2026-11-06**: Added worked example and Retrieval Corpus Lifecycle rule
- **2026-05-12**: Created PromptOps governance chapter

---
## Questions or Feedback?

Add examples from your AI review process, prompt registry, or incident reviews to make this chapter increasingly operational.

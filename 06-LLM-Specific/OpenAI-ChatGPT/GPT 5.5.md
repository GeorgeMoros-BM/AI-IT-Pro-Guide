## GPT-5.5 Thinking + Codex: Quick Reference for IT Pros

> Purpose: Use GPT-5.5 Thinking effectively for real work - documents, spreadsheets, slide decks, lightweight apps, browser workflows, troubleshooting, and structured analysis - without wasting compute on simple tasks. Based on the transcript provided.

---
# 1. Core Principle

Do **not** treat GPT-5.5 Thinking as the default tool for every task.

Use the right mode for the job:

|Task Type|Recommended Mode|Why|
|---|--:|---|
|Simple Q&A, recipes, quick explanations|Instant / standard|Faster, cheaper, less overthinking|
|Structured analysis, technical planning|GPT-5.5 Thinking - medium|Better reasoning without excessive cost|
|Complex architecture, troubleshooting, policy, risk, automation|GPT-5.5 Thinking - high / extended|Better for multi-step reasoning|
|File creation, spreadsheets, decks, scripts, apps|GPT-5.5 in Codex|Better execution environment|
|Browser automation / website testing|Codex + Browser Use plugin|Supports agentic web workflows|

---
# 2. First Check: Confirm the Model

When a new model is released, do not assume it is active.
## Checklist

- Open model configuration.
- Confirm **GPT-5.5 Thinking** is selected.
- Confirm you are not still using an older model.
- Use **latest** where available.
- For serious work, use **extended / thinking mode**.
- For basic work, use **instant / standard mode**.
## IT Pro Rule

> Use GPT-5.5 Thinking when the task has dependencies, ambiguity, consequences, or multiple steps.

---
# 3. ChatGPT vs Codex

## Use ChatGPT When You Need

- Explanations
- Drafting
- Summaries
- Brainstorming
- Policy language
- Requirements clarification
- Lightweight analysis
## Use Codex When You Need

- Excel / CSV / spreadsheet creation
- PowerPoint / document generation
- Scripts
- Web apps
- Data transformation
- File-based workflows
- Browser automation
- Multi-step task execution

|Environment|Best For|Limitation|
|---|---|---|
|ChatGPT|Conversation, analysis, writing|Less suited for real file/app execution|
|Codex|Producing working artifacts|Requires more care with permissions, files, and compute usage|

---
# 4. Recommended Codex Setup

## Default Settings

|Setting|Recommended Default|
|---|---|
|Model|GPT-5.5|
|Reasoning level|Medium|
|Plan Mode|On for complex work, off for execution|
|Permissions|Review before allowing|
|Browser Use|Enable only when needed|
|Rate limits|Check regularly|

## Reasoning Level Guidance

|Reasoning Level|Use Case|
|---|---|
|Low / instant|Quick edits, simple scripts, small changes|
|Medium|Default for most IT work|
|High|Complex troubleshooting, architecture, migration plans|
|Extra high / extended|High-stakes, ambiguous, multi-system work|

---
# 5. Plan Mode: When to Use It

Plan Mode is useful when the model should **think before acting**.
## Use Plan Mode For

- Architecture planning
- Migration planning
- Security reviews
- PowerPoint decks
- Complex Excel models
- Automation workflows
- Browser-based testing
- Multi-step scripts
- Requirements decomposition
## Do Not Use Plan Mode For

- One-off edits
- Simple formatting
- Small script fixes
- Basic Q&A
## Recommended Workflow

1. Turn on **Plan Mode**.
2. Ask for the desired outcome.
3. Review the plan.
4. Correct assumptions.
5. Turn off Plan Mode.
6. Execute the task.
7. Review the output.
8. Iterate.

---
# 6. Example Prompts for IT Pros

## Spreadsheet / Reporting

```text
Create an Excel workbook for tracking monthly IT service performance.

Include sheets for:
1. Assumptions
2. Ticket volume
3. SLA performance
4. Backlog
5. Root cause categories
6. Dashboard

Include formulas, charts, and sample data.
```
## PowerPoint / Executive Briefing

```text
Create a 10-slide executive presentation on the risks and opportunities of adopting AI copilots across an enterprise IT environment.

Audience: CIO and business unit leaders.
Tone: concise, practical, risk-aware.
Include speaker notes.
```
## Architecture Review

```text
Review this proposed AI architecture for an enterprise environment.

Focus on:
- Identity and access management
- Data leakage risk
- Logging and monitoring
- Model/vendor risk
- Cost controls
- Human approval points
- Operational support model

Return a risk-ranked findings table.
```
## Browser / Website Testing

```text
Use Browser Use to test the onboarding flow for this internal web app.

Check:
- Broken links
- Confusing steps
- Authentication friction
- Error handling
- Accessibility basics
- Screens where users may abandon the process

Return findings in severity order.
```
## Automation / Script Generation

```text
Create a PowerShell script that inventories local user accounts, installed applications, disk usage, and Windows build version.

Output the results to CSV.
Add comments.
Include error handling.
Do not make changes to the machine.
```

---
# 7. Permission Safety Checklist

Before allowing Codex or browser automation to proceed, check:

|Question|Why It Matters|
|---|---|
|Is it reading files only, or modifying them?|Prevents unintended changes|
|Is it accessing sensitive data?|Reduces exposure risk|
|Is it installing dependencies?|Supply-chain risk|
|Is it connecting to external sites?|Data leakage / compliance concern|
|Is it executing scripts?|Operational risk|
|Is rollback possible?|Change control|
## IT Pro Rule

> Never allow agentic execution against production systems without a clear scope, test environment, approval path, and rollback plan.

---
# 8. Cost and Rate-Limit Guidance

GPT-5.5 Thinking can consume more resources than standard modes.
## Use Higher Reasoning When

- The output will be reused.
- The task affects business decisions.
- Mistakes are costly.
- The task has many dependencies.
- You need structured reasoning or planning.
## Avoid Higher Reasoning When

- The task is disposable.
- You only need a first draft.
- You are doing simple lookup or rewriting.
- Speed matters more than precision.

---
# 9. Best-Practice Operating Pattern

## Daily Use

|Need|Tool Pattern|
|---|---|
|Quick answer|ChatGPT instant|
|Draft or rewrite|ChatGPT standard|
|Complex analysis|GPT-5.5 Thinking|
|Create a file|Codex|
|Build / test workflow|Codex + Plan Mode|
|Browse/test websites|Codex + Browser Use|
## For Team Adoption

Use a simple internal standard:

1. **ChatGPT = thinking, drafting, analysis**
2. **Codex = building, generating, testing**
3. **Plan Mode = complex work**
4. **Medium reasoning = default**
5. **High reasoning = expensive / consequential work**

---
# 10. Common Mistakes

|Mistake|Better Practice|
|---|---|
|Assuming latest model is enabled|Confirm model selection|
|Using GPT-5.5 for everything|Match model to task|
|Skipping Plan Mode on complex tasks|Plan first, execute second|
|Allowing permissions blindly|Review what it is doing|
|Using ChatGPT for file-heavy work|Use Codex|
|Ignoring rate limits|Monitor usage|
|Asking vague prompts|Provide structure, audience, constraints, and output format|

---
# 11. Recommended Internal Policy Language

```text
Use GPT-5.5 Thinking for complex reasoning, planning, technical analysis, and multi-step work. Use standard or instant modes for simple questions and low-risk tasks.

Use Codex when the desired outcome is an executable artifact, including documents, spreadsheets, presentations, scripts, lightweight applications, data transformations, or browser-based testing.

For any task involving permissions, local files, external websites, scripts, or sensitive data, users must review the planned action before execution and avoid production-impacting changes unless formally approved.
```

---
# 12. One-Page Summary

## Use GPT-5.5 Thinking When

- The task is complex.
- The output matters.
- You need planning.
- The work involves trade-offs.
- You need structured execution
## Use Codex When

- You need a file.
- You need code.
- You need a spreadsheet.
- You need a slide deck.
- You need a prototype.
- You need agentic browser work.
## Use Plan Mode When

- The task has more than 3 steps.
- Assumptions matter.
- Cost matters.
- You want to approve the approach before execution.
## Default Rule

> Medium reasoning in Codex is the default for real IT work. Increase only when complexity or risk justifies it.
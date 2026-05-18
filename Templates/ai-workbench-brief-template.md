---
title: AI Workbench Brief Template
type: template
status: draft
version: 1.0
recommended_path: Templates/ai-workbench-brief-template.md
related:
  - 02-Practical-Implementation/AI-Workbench-Protocol.md
tags:
  - ai-workbench
  - prompt-template
  - enterprise-ai
  - it-professionals
---

# AI Workbench Brief Template

Use this before asking AI to produce an important deliverable.

The goal is to define the problem clearly enough that the AI can assist with synthesis, drafting, critique, and refinement without replacing human judgment.

---

## 1. Objective

What am I trying to produce?

```markdown
I need to create:
[Describe the deliverable.]
```

Examples:

- executive status update
- business case
- BRD
- process map
- current-state assessment
- vendor evaluation
- AI adoption plan
- risk register
- stakeholder communication
- implementation roadmap

---

## 2. Audience

Who will read, approve, challenge, or use this?

```markdown
Primary audience:
[Name / role / group]

Secondary audience:
[Name / role / group]

Most skeptical reader:
[Name / role / group]
```

Consider:

- executive sponsor
- CIO / IT leadership
- finance
- cybersecurity
- legal / compliance
- operations
- field users
- project managers
- vendors
- acquired-company stakeholders

---

## 3. Desired Walkaway

What should the audience think, decide, approve, question, or do?

```markdown
After reading this, the audience should:
1. [Decision / belief / action]
2. [Decision / belief / action]
3. [Decision / belief / action]
```

Examples:

- approve the next phase
- understand the risk
- align on the operating model
- fund the pilot
- accept the migration approach
- provide missing data
- choose between options
- escalate a blocker

---

## 4. Stakes

What happens if this lands well?
What happens if it fails?

```markdown
If this lands well:
[Positive outcome]

If this fails:
[Risk / cost / delay / credibility issue]
```

Consider:

- executive confidence
- delivery timeline
- adoption risk
- budget approval
- compliance exposure
- operational disruption
- stakeholder trust
- auditability
- business continuity

---

## 5. Context

Paste the relevant source material.

```markdown
## Context Package

[Paste notes, meeting summaries, process details, system names, stakeholder comments, source excerpts, current-state pain points, or prior decisions.]
```

Useful context types:

- meeting notes
- emails
- Slack / Teams messages
- process screenshots
- workflow descriptions
- system lists
- vendor documents
- prior examples
- business requirements
- technical constraints
- open questions

---

## 6. Format to Copy

What should the output look like?

```markdown
Target format:
[Describe the structure.]

Reference example:
[Paste a good prior example, if available.]
```

Examples:

- one-page executive brief
- table with recommendation
- memo format
- BRD structure
- BPMN-style process description
- risk register
- roadmap by phase
- decision log
- stakeholder email

---

## 7. Constraints

What cannot be ignored?

```markdown
Constraints:
- Budget:
- Timeline:
- Security:
- Compliance:
- Data:
- Integration:
- Operations:
- Change management:
- Procurement:
- Other:
```

---

## 8. Risks and Objections

Who will push back, and why?

```markdown
Likely objections:
1. [Stakeholder] may object because [reason].
2. [Stakeholder] may object because [reason].
3. [Stakeholder] may object because [reason].
```

Common objection lenses:

- CFO: unclear cost or ROI
- CIO: integration and support burden
- Cybersecurity: data exposure or access control
- Operations: disruption or administrative load
- Field users: impractical workflow
- Legal / compliance: policy or retention issues
- Executive sponsor: unclear decision or weak business case

---

## 9. Output Request

Use this section as the final prompt.

```markdown
ROLE
You are my AI workbench for enterprise IT and business integration work.

TASK
Analyze the brief below, identify missing context, surface assumptions, and then produce the requested deliverable.

OBJECTIVE
[Insert objective.]

AUDIENCE
[Insert audience.]

DESIRED WALKAWAY
[Insert desired walkaway.]

STAKES
[Insert stakes.]

CONTEXT
[Insert context package.]

FORMAT
[Insert target format.]

CONSTRAINTS
[Insert constraints.]

RISKS AND OBJECTIONS
[Insert likely objections.]

OUTPUT REQUEST
Produce [specific deliverable] in [specific format].

QUALITY BAR
- Separate facts, interpretations, and recommendations.
- Identify assumptions.
- Flag unsupported claims.
- Make trade-offs explicit.
- Avoid generic enterprise language.
- Write for decision usefulness.
- Include decision points, owners, and next steps where relevant.
```

---

## 10. After the First Draft

Do not ship the first answer.

Run the output through the AI Output Critique Template.

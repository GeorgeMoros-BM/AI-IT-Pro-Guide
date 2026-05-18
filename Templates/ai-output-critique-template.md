---
title: AI Output Critique Template
type: template
status: draft
version: 1.0
recommended_path: Templates/ai-output-critique-template.md
related:
  - 02-Practical-Implementation/AI-Workbench-Protocol.md
  - Templates/ai-workbench-brief-template.md
tags:
  - ai-workbench
  - critique
  - evaluation
  - prompt-template
  - enterprise-ai
---

# AI Output Critique Template

Use this after AI produces a draft.

The first output should be treated like a junior analyst draft: useful, but not ready to ship.

---

## 1. General Critique Prompt

```markdown
Review the draft below.

Your task is not to praise it.
Your task is to find what is weak, generic, unsupported, unclear, risky, or misaligned to the audience.

Evaluate it against the original brief.

Original brief:
[Paste brief.]

Draft:
[Paste draft.]

Assess:
1. What is unclear?
2. What is generic?
3. What assumptions are hidden?
4. What claims are unsupported?
5. What decision is being avoided?
6. What stakeholder objections are not addressed?
7. What should be cut?
8. What should be made more concrete?
9. What would make this more useful for decision-making?
10. What is the strongest version of the recommendation?

Then provide:
- critique summary
- issue table
- recommended fixes
- revised draft
```

---

# 2. Executive Sponsor Critique

```markdown
Review this as a skeptical executive sponsor.

Assume you have limited time and want to know:
- what changed
- why it matters
- what decision is needed
- what risk needs attention
- whether the team has a credible path forward

Identify:
1. Where is the message unclear?
2. Where is the recommendation weak?
3. Where is the decision buried?
4. Where is the risk underplayed?
5. What would prevent approval?
6. What sounds like generic consulting language?
7. What should be rewritten for executive clarity?

Then revise the draft for executive review.
```

---

# 3. CFO Critique

```markdown
Review this as a CFO who dislikes unclear spend and vague benefits.

Identify:
1. Where is the business case weak?
2. Which benefits are unproven?
3. Which costs are missing?
4. Which assumptions need evidence?
5. What financial risk is underplayed?
6. What would you challenge before approving?
7. What should be reframed as an option, not a conclusion?

Then revise the output to make the financial logic more defensible.
```

---

# 4. CIO / IT Operations Critique

```markdown
Review this as a CIO or IT operations leader.

Identify:
1. Integration risks
2. support-model gaps
3. data-quality risks
4. security and access-control concerns
5. unclear ownership
6. implementation dependencies
7. operational failure modes
8. lifecycle and maintenance concerns
9. change-management risks
10. missing decision gates

Then revise the output to make it implementation-ready.
```

---

# 5. Cybersecurity / Compliance Critique

```markdown
Review this from a cybersecurity, compliance, privacy, and auditability perspective.

Identify:
1. Data exposure risks
2. identity and access-control issues
3. vendor-risk concerns
4. retention and records-management gaps
5. auditability gaps
6. data residency concerns
7. logging / monitoring issues
8. unclear policy assumptions
9. sensitive-data concerns
10. missing controls

Then revise the output to address these concerns.
```

---

# 6. Field / End-User Critique

```markdown
Review this as an operational user who has to live with the process.

Identify:
1. What feels unrealistic?
2. What creates extra administrative burden?
3. What would users ignore?
4. What would create adoption friction?
5. What language does not match operational reality?
6. What workflow steps are unclear?
7. What exceptions are missing?
8. What training or support would be needed?
9. What would break during busy periods?
10. What should be simplified?

Then revise the output for practical adoption.
```

---

# 7. Source Discipline Critique

```markdown
Audit this draft for source discipline.

Separate:
- confirmed facts
- interpretations
- assumptions
- unsupported claims
- speculation

Identify:
1. Claims that require evidence
2. Claims that are too strong
3. Claims imported from outside the source material
4. Missing citations or references
5. Ambiguous data points
6. Conflicting source information
7. Areas where confidence should be marked low

Then rewrite the draft so that facts, interpretations, assumptions, and recommendations are clearly separated.
```

---

# 8. Decision-Usefulness Critique

```markdown
Evaluate this draft for decision usefulness.

A decision-useful artifact should make it easier to answer:
1. What is the problem?
2. Why does it matter now?
3. What are the options?
4. What is recommended?
5. What are the trade-offs?
6. What are the risks?
7. What decision is needed?
8. Who owns the next step?
9. What happens next?
10. What would change the recommendation?

Identify gaps and revise accordingly.
```

---

# 9. Compression Prompt

Use this when the draft is too long.

```markdown
Compress this draft by 40 percent.

Rules:
- preserve the core recommendation
- preserve decision points
- preserve risks and trade-offs
- remove generic language
- remove repetition
- remove unsupported detail
- keep the tone executive-ready
- do not remove important caveats
```

---

# 10. Final Revision Prompt

```markdown
Using the critique above, produce the final version.

Requirements:
- concise
- specific
- decision-useful
- defensible
- no generic enterprise filler
- no unsupported claims
- clear next steps
- clear owner / decision point where applicable
```

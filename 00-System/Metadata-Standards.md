---
title: "Metadata Standards"
artifact_type: governance
status: canonical
last_updated: 2026-05-18
publish: false
client_safe: true
---
Metadata exists to improve:
- retrieval quality
- operational governance
- publishing control
- automation compatibility
- semantic navigation

The goal is not maximal metadata complexity. The goal is operational usefulness.

---
# Minimum Required Metadata

```yaml
---
title:
artifact_type:
status:
last_updated:
publish:
client_safe:
---
```

---
# Recommended Metadata

```yaml
audience:
domain:
maturity:
time_to_read:
related:
tags:
```

---
# Approved Artifact Types

| Type | Purpose |
|---|---|
| reference | Knowledge and explanation |
| framework | Decision model |
| playbook | Execution guidance |
| architecture | System pattern |
| executive-brief | Executive communication |
| template | Reusable artifact scaffold |
| case-study | Real-world implementation |
| governance | Operational policy |
| operational-system | Vault governance and orchestration |

---
# Naming Conventions

Preferred:
- Title-Case-With-Hyphens.md
- explicit terminology
- stable names

Avoid:
- vague labels
- temporary names
- duplicate naming patterns
- version suffix chaos

---
# Metadata Philosophy

Good metadata should:
- reduce ambiguity
- improve retrieval
- support automation
- enable dashboards
- clarify lifecycle state

Bad metadata:
- creates maintenance overhead
- duplicates note content
- introduces taxonomy complexity without leverage
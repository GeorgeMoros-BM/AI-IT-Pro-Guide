---
title: "Decision Log"
artifact_type: governance
status: canonical
last_updated: 2026-11-05
publish: false
client_safe: true
---
Track major operational, architectural, governance, and taxonomy decisions.

This reduces:
- structural drift
- forgotten rationale
- inconsistent evolution

---
# Decision Entries

## 2026-05-18
### Established layered enterprise AI vault architecture

Reason:
Need to separate:
- evergreen knowledge
- consulting IP
- executive assets
- operational governance
- publishing artifacts

---
## 2026-05-18
### Introduced lifecycle governance

Reason:
Prevent operational ambiguity and uncontrolled content sprawl.

---
## 2026-05-18
### Optimized for consulting reuse and publication

Reason:
Vault is intended to become:
- reusable consulting IP
- executive knowledge platform
- publishable enterprise AI ecosystem

---
## 2026-11-05
### Extended governance to retrieval freshness, outside content, routers, and response caches

Reason:
Stale content can keep reaching users after a source changes. Outside content can steer any tool the model holds. Routers and response caches change what users receive. PromptOps-Governance now holds the Retrieval Corpus Lifecycle rule and versions routers and cache policies. Prompt-Risk-Tiering now sets a minimum tier for outside content.

---
## 2026-11-05
### Aligned the chapter template with Metadata Standards

Reason:
New chapters should carry the metadata that retrieval and publishing depend on. The template now also includes the Worked Example and Common Failure Modes sections that recent chapters use. Existing chapters were not retrofitted.

---
## 2026-11-05
### Added required metadata to existing chapters

Reason:
Chapters lacked fields that dashboards, retrieval, and publishing rely on. Chapters listed in Canonical-Documents are marked canonical. All other chapters are marked draft with client_safe false until they are reviewed. No last_updated dates changed, because no chapter content changed.

---
## 2026-11-05
### Added an automated vault check

Reason:
Broken links and metadata gaps were accumulating faster than manual review could find them. The check runs daily on GitHub instead of on every push, because Obsidian Git pushes every five minutes. It records existing issues in a baseline and fails only on new ones.

---
## 2026-11-05
### Added the Enterprise RAG Architecture and AI Incident Response Playbook

Reason:
The RAG architecture note was an empty stub that navigation already pointed to. Prompt Risk Tiering requires an incident playbook from Tier 3, but the vault had only a five-step summary.

---
## 2026-11-05
### Made the Current Model Register the only source for current model facts

Reason:
Model names and prices were repeated across notes and went stale. Chapters now use roles and link to the register, starting with the two cost notes. Other notes move over when they are next edited. Dated records stay as written.

---
## 2026-11-05
### Moved notification clocks into a separate reference

Reason:
Legal rules change faster than the incident process. Keeping them in one reference lets the playbook stay stable, and gives the rules their own review cycle. The reference covers Canada, the United States, and the EU.

---
# Governance Rule

Major structural decisions should be logged when they materially affect:
- taxonomy
- metadata
- publishing
- governance
- retrieval
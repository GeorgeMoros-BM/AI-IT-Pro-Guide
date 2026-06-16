# Repository README

This repository contains the source vault for:
# The IT Professional's Guide to Enterprise AI

The repository is maintained as:
- an Obsidian knowledge vault
- a Git version-controlled documentation system
- a GitHub Pages/Jekyll publishing source
- a reusable enterprise AI consulting knowledge base

---
# Repository Purpose

This repository exists to:
- document enterprise AI implementation patterns
- codify governance and operating models
- capture reusable consulting frameworks
- support AI transformation initiatives
- maintain institutional knowledge

---
# Repository Architecture
|Component|Purpose|
|---|---|
|Obsidian|Authoring and knowledge management|
|Git|Version control|
|GitHub|Collaboration and source hosting|
|GitHub Pages / Jekyll|Static publishing layer|
|MOCs|Semantic navigation and retrieval control|
|Metadata|Lifecycle, publishing, governance, and retrieval support|

---
# Repository Structure

| Folder                        | Purpose                                                                     |
| ----------------------------- | --------------------------------------------------------------------------- |
| `00-System`                   | Vault governance and operational control plane                              |
| `00-Quick-Start`              | Reader onboarding, orientation, and learning paths                          |
| `01-Foundation Knowledge`     | Durable AI fundamentals and conceptual grounding                            |
| `02-Practical-Implementation` | Practical implementation guides and starter patterns                        |
| `03-Enterprise-Concerns`      | Governance, risk, security, cost, adoption, and operating model concerns    |
| `04-Advanced-Topics`          | Advanced architecture, RAG, agents, context, and evaluation topics          |
| `05-Resources`                | Glossaries, directories, troubleshooting, use cases, and learning resources |
| `06-LLM-Specific`             | Provider-specific and model-specific intelligence                           |
| `07-Frameworks`               | Reusable decision frameworks and consulting IP                              |
| `08-Playbooks`                | Execution playbooks for pilots, governance, and vendor selection            |
| `09-Reference-Architectures`  | Enterprise AI reference architectures                                       |
| `10-Executive`                | Executive and board-level assets                                            |
| `11-Research`                 | Research intake and weekly intelligence                                     |
| `12-Synthesis`                | Cross-cutting synthesis and longitudinal themes                             |
| `13-Operational-Systems`      | Canonical operating-system views of enterprise AI capabilities              |
| `14-Publishing`               | Publishing workflow and content preparation                                 |
| `15-Case-Studies`             | Case studies and applied examples                                           |
| `90-Local-Admin`              | Local/private operational artifacts; not intended for publication           |

---
# Authoring Workflow

## Phase 1 — Authoring

Content is authored locally in Obsidian.

Authoring should prioritize:
- clarity
- operational realism
- reusable structure
- source discipline
- metadata completeness
- canonical concept alignment
## Phase 2 — Version Control

Changes are committed and pushed through Git.

Commit messages should make the nature of the change clear, such as:
- new framework
- MOC update
- canonicalization
- link cleanup
- metadata update
- archive/deprecation
- publishing update
## Phase 3 — Publishing

GitHub Pages / Jekyll renders selected vault content into a published documentation site.

Publishing should prioritize:
- high signal density
- executive readability
- semantic navigation
- retrieval compatibility
- client-safe language
- stable canonical links

---
# Contribution Guidelines

## File Naming

Use:
```
Title-Case-With-Hyphens.md
```

Avoid:
- vague shorthand
- unstable naming patterns
- duplicate concepts
- unnecessary spaces in filenames for new technical/reference files

Existing files with spaces may remain where renaming would create excessive link churn.

---
# Linking Standard

Use Obsidian wikilinks:
```
[[Document Name]]
```
For cross-folder clarity when needed, use explicit paths:
```
[[13-Operational-Systems/Context-Engineering|Context-Engineering]]
```

---
# Metadata Standard

Minimum metadata:
```
---
title:
artifact_type:
status:
last_updated:
publish:
client_safe:
---
```
Recommended additional metadata where useful:
```
audience:
domain:
tags:
related:
review_frequency:
owner:
```

Reference:
- [[Metadata-Standards]]

---
# Governance Principles

This repository prioritizes:
- operational realism
- governance-aware AI implementation
- reusable frameworks
- retrieval-aware architecture
- executive usefulness
- lifecycle discipline
- source discipline
- canonical concept management

---
# Content Boundary Rules

## Use `00-Quick-Start` for

- onboarding
- orientation
- reading order
- learning paths
- mental model reset
## Use `01-Foundation Knowledge` for

- durable AI fundamentals
- LLM mechanics
- core architecture concepts
- prompt basics
- model evaluation basics
## Use `07-Frameworks` for

- reusable decision models
- governance frameworks
- control matrices
- prioritization methods
- operating model frameworks
## Use `08-Playbooks` for

- execution guidance
- implementation sequences
- rollout plans
- governance implementation
- vendor selection actions
## Use `11-Research` for

- intake
- evidence capture
- weekly intelligence
- raw or semi-processed research
## Use `12-Synthesis` for

- cross-cutting themes
- longitudinal analysis
- executive distillation
- synthesized insight
## Use `13-Operational-Systems` for

- canonical operating-system level concepts
- enterprise AI capability models
- persistent operating disciplines

---
# Recommended Contribution Types

Examples:
- implementation lessons learned
- architecture patterns
- governance frameworks
- evaluation methodologies
- operational playbooks
- enterprise AI case studies
- MOC improvements
- canonical concept consolidation
- executive-ready summaries
- research-to-synthesis distillations

---
# Important Operational Rules

## Do Not Commit

- API keys
- secrets
- credentials
- sensitive client information
- regulated data
- private commercial information not intended for publication

Reference:  
`.gitignore`

---
# Publishing Notes

The repository is structured for:
- Jekyll
- GitHub Pages
- future Quartz / MkDocs compatibility
- future retrieval-augmented knowledge access

The publishing layer should prioritize:
- high signal density
- executive readability
- semantic navigation
- source discipline
- retrieval compatibility
- client-safe presentation
# Strategic Direction

This repository is evolving from:
- a practical IT AI runbook

toward:
- an enterprise AI operating system
- a reusable consulting IP platform
- a publishable governance and architecture ecosystem
- a semantic retrieval platform
- an institutional AI intelligence system

The core strategic assumption is:

> Enterprise AI advantage will come less from model access alone and more from governed integration, retrieval quality, workflow design, evaluation discipline, and operating model maturity.
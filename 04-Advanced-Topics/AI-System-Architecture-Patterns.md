---
title: "AI System Architecture Patterns"
artifact_type: architecture
status: validated
last_updated: 2026-05-18
publish: false
client_safe: true
audience:
  - architect
  - platform
  - operations
domain:
  - ai-architecture
  - agentic-systems
---

# AI System Architecture Patterns

## Purpose

Define common enterprise AI architecture patterns and their operational tradeoffs.

---
# Core Principle

Enterprise AI systems are increasingly orchestration systems rather than isolated model calls.

---
# Architecture Patterns

## Single-Prompt Systems

Characteristics:
- lightweight
- low governance burden
- simple workflows

Use cases:
- summarization
- rewriting
- drafting

---
## RAG Pipelines

Characteristics:
- grounded retrieval
- enterprise knowledge integration
- evidence-aware outputs

Core components:
- ingestion
- vector indexing
- retrieval orchestration
- prompt assembly

---
## Agentic Systems

Characteristics:
- planning
- tool use
- iterative execution
- workflow orchestration

Typical architecture:
User Intent
→ Planner
→ Retrieval
→ Tool Selection
→ Execution
→ Validation

---
# Orchestrators

Orchestrators manage:
- routing
- memory
- workflow coordination
- tool invocation
- evaluation

---
# MCP and Tooling

Modern AI systems increasingly rely on:
- APIs
- MCP layers
- retrieval tools
- external execution systems

Models alone are insufficient.

---
# Event-Driven AI

Pattern:
systems react to operational triggers.

Examples:
- incident creation
- document arrival
- workflow state changes

---
# Human-in-the-Loop

Required for:
- sensitive workflows
- approvals
- financial decisions
- legal review
- autonomous systems

---
# Evaluation Loops

Production systems require:
- regression testing
- operational monitoring
- quality evaluation
- observability

---
# Common Failure Modes

- orchestration overengineering
- weak observability
- no rollback mechanisms
- governance gaps
- tool sprawl

---
# Strategic Insight

Enterprise AI architecture is converging toward:
- orchestration-first systems
- retrieval-centric workflows
- governed operational platforms
- event-aware AI ecosystems
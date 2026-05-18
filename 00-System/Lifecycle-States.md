---
title: "Lifecycle States"
artifact_type: governance
status: canonical
last_updated: 2026-05-18
publish: false
client_safe: true
---
Lifecycle states define the operational maturity and governance status of vault artifacts.

This prevents ambiguity regarding:
- trustworthiness
- publication readiness
- operational reliability
- maintenance expectations

---
# Lifecycle Model

| State | Meaning |
|---|---|
| seed | Early exploratory fragment |
| draft | Structured but incomplete |
| review | Under active critique |
| validated | Reviewed for operational usefulness |
| canonical | Authoritative system-of-record artifact |
| publish-ready | Suitable for external publication |
| archived | Deprecated or historical |

---
# State Definitions

## seed
Characteristics:
- incomplete
- speculative
- unstable

Typical location:
- Research
- scratch synthesis

## draft
Characteristics:
- structured
- readable
- still evolving

Typical use:
- active development
- framework expansion

## review
Characteristics:
- being critiqued
- operationally stress-tested
- refinement phase

Typical activities:
- executive review
- architectural validation
- governance review

## validated
Characteristics:
- technically coherent
- operationally useful
- internally trusted

Typical use:
- consulting reuse
- operational guidance

## canonical
Characteristics:
- authoritative
- stable
- preferred citation target

Governance rule:
Canonical artifacts override conflicting guidance.

## publish-ready
Characteristics:
- externally consumable
- editorially reviewed
- client-safe

## archived
Characteristics:
- retained for historical context
- not recommended for operational reuse

---
# Governance Rules

- Drafts should not drive major decisions without review.
- Canonical artifacts require deliberate maintenance.
- Archived content should remain discoverable but clearly deprecated.
- Publish-ready artifacts must pass readability and governance review.
---
title: Agentic Workflows
tags:
  - chapter
  - agents
  - workflows
  - orchestration
  - advanced
difficulty: advanced
last_updated: 2026-05-12
time_to_read: 28 minutes
related:
  - "[[Agents-and-Tool-Use]]"
  - "[[Evaluation-and-Testing]]"
  - "[[Prompt-Operating-Contracts]]"
  - "[[RAG-Implementation]]"
  - "[[Security-and-Privacy]]"
---
> [!note] Scope
> This file explains the technical pattern. 
> For the operating model and lifecycle governance view, see [[13-Operational-Systems/Agentic-Workflows]].
# Agentic Workflows

> **TL;DR for the Busy IT Pro:**  
> Do not build an autonomous agent when a simple workflow will do. Agentic workflow design is the discipline of choosing the least complex pattern that reliably completes the job.

---
## What You'll Learn

- [ ] The difference between workflows, agents, and multi-agent systems
- [ ] When to use chaining, routing, parallelization, evaluator-optimizer loops, and orchestrator-worker patterns
- [ ] How to select an orchestration pattern based on risk and uncertainty
- [ ] How to add stop conditions, approval gates, and evaluation points
- [ ] Common ways agentic systems become too complex too early

---
## Why This Matters

Most enterprise AI failures are not caused by weak models. They are caused by weak workflow design.

Teams often jump from:
```text
single prompt -> fully autonomous agent
```

The safer path is:
```text
single prompt -> prompt chain -> routed workflow -> tool workflow -> bounded agent
```

**Real-world scenario:**  
> Your IT service desk wants an AI assistant. A fully autonomous agent that can read tickets, search policies, update records, and message users sounds impressive. A safer first version routes requests by type, retrieves relevant policy, drafts a response, and requires human approval before updating the ticket.

---
## Core Concepts

### Concept 1: Workflow vs Agent

| Pattern | Who Controls the Path? | Best For |
|---|---|---|
| Workflow | Code or predefined logic | Known repeatable processes |
| Agent | Model dynamically chooses steps and tools | Ambiguous tasks with changing paths |

A workflow follows a designed path. An agent chooses the path within boundaries.

**Technical details:**

- Workflows are easier to test, monitor, and explain.
- Agents are more flexible but harder to constrain.
- Many enterprise use cases need tool-enabled workflows, not fully autonomous agents.

**Why it works this way:**
Predictability matters in enterprise systems. Add flexibility only where the task genuinely requires it.

---
### Concept 2: Use the Least-Agentic Pattern That Works

Start with the lowest-complexity pattern that meets the user outcome.

| Need | Pattern |
|---|---|
| One clear answer | Single prompt |
| Known sequence | Prompt chain |
| Different request types | Routing |
| Independent review lenses | Parallelization |
| Iterative improvement | Evaluator-optimizer |
| Unknown subtasks | Orchestrator-workers |
| External action | Tool-using agent |
| Ongoing autonomous execution | High-control autonomous agent |

**Why it works this way:**
Every increase in agency adds testing burden, security risk, cost, latency, and governance overhead.

---
### Concept 3: Agentic Workflows Need Control Points

Every workflow should define:

- trigger
- input requirements
- route or decision logic
- tools allowed
- human approval points
- failure handling
- completion criteria
- audit log
- eval checkpoints

**Why it works this way:**
The model may be probabilistic, but the surrounding process must be operationally disciplined.

---
### Concept 4: State Is a First-Class Design Concern

State means what the system knows at a given point.

Examples:

- ticket status
- user identity
- retrieved documents
- tool outputs
- approvals granted
- previous steps completed
- unresolved errors

**Technical details:**

- Keep state explicit where possible.
- Do not rely on the model to remember operational facts without passing them back into context.
- Persist only what is needed and allowed by policy.

**Why it works this way:**
Workflow reliability depends on knowing what has already happened and what remains unresolved.

---
## Hands-On Implementation

### Step 1: Start With a Workflow Decision Matrix

```markdown
Use case: IT service desk assistant

User requests:
- password reset
- ticket status
- software access
- incident summary
- general policy Q&A

Risk:
- read-only status lookup: low
- account unlock: medium/high
- permission change: high
- production system action: high

Recommended pattern:
- Routing + specialist workflows
- Read-only tools first
- Human approval for write actions
```

**What's happening here:**

- Decomposes the use case before choosing architecture
- Separates low-risk read tasks from high-risk write tasks
- Avoids overbuilding one general-purpose agent

---
### Step 2: Implement Routing

Routing classifies a request and sends it to the correct downstream workflow.

```python
ROUTES = {
    "ticket_status": "status_workflow",
    "password_reset": "identity_workflow",
    "software_access": "access_request_workflow",
    "incident_summary": "incident_workflow",
    "general_policy": "rag_policy_workflow"
}


def route_request(user_message):
    route = classify_request(user_message, allowed_routes=list(ROUTES.keys()))
    return ROUTES.get(route, "human_triage")
```

**What's happening here:**
- The model can classify intent
- Code controls the available routes
- Unknown requests fall back to human triage

---
### Step 3: Use Prompt Chaining for Known Steps

```text
Step 1: Extract request type and key fields
Step 2: Validate required fields
Step 3: Retrieve relevant policy or system record
Step 4: Draft response
Step 5: Run safety and completeness check
Step 6: Return response or request approval
```

**What's happening here:**
- Each step has a narrow job
- Failures can be detected earlier
- The workflow is easier to test than one large prompt

---
### Step 4: Use Parallelization for Independent Lenses

Example: reviewing a vendor AI tool.
```text
Parallel reviewer 1: Security risk
Parallel reviewer 2: Privacy and data retention
Parallel reviewer 3: Integration complexity
Parallel reviewer 4: Cost and operational fit
Synthesizer: combine findings into decision brief
```

**What's happening here:**

- Each model call focuses on one evaluation lens
- The final answer is more complete
- The workflow reduces cognitive overload in a single prompt

---
### Step 5: Use Evaluator-Optimizer for High-Quality Outputs

```text
Draft -> Evaluate against rubric -> Revise -> Final check -> Publish
```

Example use cases:
- executive briefing
- policy summary
- vendor assessment
- incident postmortem
- user-facing response

**What's happening here:**

- The evaluator catches missing sections, weak evidence, and unsafe claims
- The optimizer revises against explicit criteria
- This pattern is slower but more reliable

---
### Step 6: Use Orchestrator-Workers When Subtasks Are Unknown

Use this when the system cannot know the subtasks upfront.

Examples:
- searching across many documents
- diagnosing a complex incident
- generating a multi-file code change
- building a market intelligence report

```text
User goal -> Orchestrator plans subtasks -> Workers execute subtasks -> Orchestrator synthesizes -> Evaluator checks -> Final answer
```

**What's happening here:**
- The orchestrator decomposes the task dynamically
- Workers handle bounded subproblems
- The final synthesis remains controlled by a quality check

---
## Workflow Pattern Selection Guide

| Pattern | Use When | Avoid When | Key Control |
|---|---|---|---|
| Single prompt | Task is simple and low-risk | External data or actions are needed | Output contract |
| Prompt chain | Steps are known | Task branches unpredictably | Step validation |
| Routing | Inputs fall into clear categories | Categories overlap heavily | Fallback route |
| Parallelization | Independent lenses improve quality | Lenses depend on each other | Synthesis rubric |
| Evaluator-optimizer | Quality improves with critique | Speed is more important than polish | Revision limit |
| Orchestrator-workers | Subtasks are unknown upfront | Simple workflow is sufficient | Task budget |
| Tool-using agent | External data/action is required | Tool risk is unacceptable | Approval gates |
| Multi-agent | Domains are genuinely distinct | One agent or workflow can do it | Coordination protocol |

---
## Minimum Control Set for Agentic Workflows

Every agentic workflow should define:

```markdown
Objective:
Trigger:
Allowed inputs:
Required fields:
Allowed tools:
Forbidden tools/actions:
Routing logic:
Human approval points:
Max iterations:
Max tool calls:
Timeout:
Failure behavior:
Completion criteria:
Logging requirements:
Evaluation criteria:
Owner:
Review cadence:
```

---
## Tips & Tricks

> [!tip] Quick Win
> Before building an agent, draw the workflow as a 5-step process. If the path is stable, use a workflow first.

> [!tip] Pro Tip
> Put approval gates between "recommend" and "execute." Let AI prepare actions, but let humans approve high-impact execution.

> [!warning] Watch Out
> Multi-agent systems often add coordination overhead without improving outcomes. Use them only when roles, tools, or data boundaries are genuinely different.

---
## Lessons Learned

> [!example] War Story: The Overbuilt Service Desk Agent
> **What happened:** A team built one broad service-desk agent with access to ticketing, identity, monitoring, and knowledge-base tools. It was hard to test and often chose the wrong tool.  
> **What we learned:** Too much agency too early creates operational opacity.  
> **What to do instead:** Start with routed specialist workflows and add agentic behavior only where the request path is genuinely variable.

---
## Best Practices Checklist

- [ ] Start with the least-agentic pattern that works
- [ ] Separate read-only workflows from write/action workflows
- [ ] Define routes, fallback paths, and human triage conditions
- [ ] Use bounded loops for agentic steps
- [ ] Add approval gates before external or irreversible actions
- [ ] Log workflow path, tool calls, and final outputs
- [ ] Evaluate both final answer and process trajectory
- [ ] Keep state explicit and minimal
- [ ] Version workflow instructions, tools, and eval cases
- [ ] Retire patterns that add complexity without measurable value

---
## Anti-Patterns (Don't Do This)

| Don't | Do Instead | Why |
|---|---|---|
| Build an autonomous agent for a stable process | Use a deterministic workflow | Easier to test and govern |
| Put every tool behind one agent | Route to specialist workflows | Reduces tool confusion |
| Skip fallback paths | Add human triage route | Prevents dead ends |
| Let loops run until success | Add iteration and cost limits | Prevents runaway execution |
| Evaluate only final output | Evaluate path and tool use | Catches hidden failures |
| Treat multi-agent as inherently better | Use only when separation creates value | Avoids complexity theater |

---
## Common Failure Modes

| Failure | Cause | Mitigation |
|---|---|---|
| Wrong route | Ambiguous classification | Add route examples and fallback |
| Tool confusion | Too many overlapping tools | Split into specialist workflows |
| Workflow dead end | Missing failure behavior | Define escalation and fallback |
| Excessive latency | Too many model calls | Consolidate steps or add budgets |
| Poor synthesis | Parallel outputs not reconciled | Add synthesis rubric |
| Unsafe execution | No approval gate | Pause before write/destructive actions |
| Hard-to-debug behavior | No trajectory logging | Log route, state, tool calls, and decisions |

---
## Related Topics

- [[Agents-and-Tool-Use]] - Implementing tool calls safely
- [[Evaluation-and-Testing]] - Testing workflows and agent trajectories
- [[Prompt-Operating-Contracts]] - Defining operating contracts before implementation
- [[RAG-Implementation]] - Retrieval workflows for enterprise data
- [[Security-and-Privacy]] - Securing agentic systems

---
## Further Reading

- [Anthropic - Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents) - Clear workflow and agent pattern guidance
- [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) - Agent implementation concepts
- [LangGraph](https://langchain-ai.github.io/langgraph/) - Graph-based workflow orchestration
- Internal: [[agent-operating-contract-template]] - Reusable agent workflow specification

---
## Changelog

- **2026-05-12**: Created as advanced workflow-selection layer for agents and orchestration

---
## Questions or Feedback?

Add examples from your own IT workflows, especially where deterministic workflow design outperformed an agent.

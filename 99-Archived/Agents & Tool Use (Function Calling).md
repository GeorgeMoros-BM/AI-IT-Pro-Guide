---
title: Agents & Tool Use (Function Calling)
tags: 
  - chapter
  - agents
  - tool-use
  - development
  - advanced
difficulty: advanced
last_updated: 2026-04-25
time_to_read: 28 minutes
related:
  - "[[API-Integration-Guide]]"
  - "[[Prompt-Engineering-Basics]]"
  - "[[Evaluation-and-Testing]]"
---

# Agents & Tool Use (Function Calling)

> **TL;DR for the Busy IT Pro:**  
> Prompts guide thinking. Agents execute workflows. Tool use turns AI from a passive responder into a controlled operator of real systems.

---
## What You'll Learn

- [ ] How agents extend LLMs into executable systems
- [ ] How the ReAct loop works in production
- [ ] Designing safe, constrained tool interfaces
- [ ] Failure modes and guardrails for real-world deployments

---
## Why This Matters

Standard LLMs generate text. Agents **take action inside your environment**.

This introduces:
- real-world impact (systems, data, operations)
- real risk (bad actions, loops, misuse)
- real value (automation, speed, scale)

**Real-world scenario:**  
> Your operations team spends hours triaging system health. An agent integrates with Datadog + PagerDuty, checks system status, identifies incidents, and summarizes impact in seconds - without human intervention.

---
## Core Concepts

### Concept 1: Agents = Model + Tools + Instructions

An agent consists of:

- **Model**: reasoning engine  
- **Tools**: APIs, functions, data sources  
- **Instructions**: logic governing behavior  

This is the fundamental unit of modern AI systems.

---
### Concept 2: Function Calling (Controlled Execution Layer)

LLMs do not execute code. They **request execution**.

Flow:

1. User asks a question
2. Model selects a tool
3. Model returns structured arguments
4. Your code executes the function
5. Result is returned to the model
6. Model produces final answer

**Key insight:**  
The LLM is a **decision engine**, not an execution engine.

---
### Concept 3: The ReAct Loop (Reason + Act)

Agents operate in a loop:

```

Thought → Action → Observation → Repeat

````id="react-loop"

**Technical details:**
- Iterative reasoning improves outcomes
- Tool outputs become new context
- Loop continues until termination condition

**Why it works this way:**
Breaking problems into steps allows the model to adapt dynamically rather than guessing upfront.

---

### Concept 4: Agents Are Systems, Not Features

Agents introduce:
- state
- memory
- iteration
- external dependencies

This shifts complexity from prompt design → **system design**

---

## Hands-On Implementation

### Step 1: Define a Strict Tool Interface

```python
jira_tool_schema = {
    "type": "function",
    "function": {
        "name": "get_jira_ticket_status",
        "description": "Get the status of a Jira ticket. Use only when a ticket ID is present.",
        "parameters": {
            "type": "object",
            "properties": {
                "ticket_id": {
                    "type": "string"
                }
            },
            "required": ["ticket_id"]
        }
    }
}
````

**What's happening here:**

* Schema constrains model behavior
* Description acts as a mini-prompt
* Required fields prevent ambiguity

---
### Step 2: Execute the Agent Loop

```python
def run_agent(user_message):
    messages = [{"role": "user", "content": user_message}]
    max_iterations = 5

    for i in range(max_iterations):
        response = client.chat.completions.create(
            model="gpt-4o",
            messages=messages,
            tools=[jira_tool_schema],
            tool_choice="auto"
        )

        msg = response.choices[0].message

        if msg.tool_calls:
            for tool_call in msg.tool_calls:
                args = json.loads(tool_call.function.arguments)

                result = get_jira_ticket_status(args["ticket_id"])

                messages.append(msg)
                messages.append({
                    "role": "tool",
                    "tool_call_id": tool_call.id,
                    "content": str(result)
                })
        else:
            return msg.content

    return "Error: Max iterations reached"
```

**What's happening here:**

* Loop enforces bounded reasoning
* Tool execution is externalized
* Safety constraint prevents runaway behavior

---
### Step 3: Add Guardrails

```python
if tool_call.function.name == "reboot_server":
    raise Exception("Human approval required")
```

**What's happening here:**

* Prevents unauthorized destructive actions
* Enforces human-in-the-loop controls

---
## When to Use Agents (vs Prompts)

| Use Case                         | Approach |
| -------------------------------- | -------- |
| Simple Q&A                       | Prompt   |
| Structured extraction            | Prompt   |
| Multi-step workflows             | Agent    |
| API / system interaction         | Agent    |
| Decision-making with uncertainty | Agent    |

---
## Agent Design Patterns

### Pattern 1: Single Agent (Simple Workflows)

* One agent
* Few tools
* Direct execution

---
### Pattern 2: Router + Specialist Agents

```
User → Router → Specialist Agent → Tool
```

* Router decides domain
* Specialist handles execution
* Reduces tool confusion

---
### Pattern 3: Tool-Constrained Agents

* Agent has 2–5 tools max
* Each tool tightly scoped
* Reduces hallucination risk

---
### Pattern 4: Human-in-the-Loop

* Agent proposes action
* Human approves
* System executes

Critical for:

* finance
* infrastructure
* security

---
## Agent Lifecycle (AgentOps)

Agents require operational discipline:

* Versioning (instructions + tools)
* Testing (task success rate)
* Monitoring (latency, errors)
* Evaluation (output quality)

Without this, agents degrade over time.

---
## Tips & Tricks

> [!tip] Quick Win
> Start with read-only tools (GET APIs) before enabling write actions.

> [!tip] Pro Tip
> Limit each agent to 3–5 tools. More tools = worse performance.

> [!warning] Watch Out
> Agents will retry failing tools indefinitely unless explicitly constrained.

---
## Lessons Learned

> [!example] War Story: Infinite Retry Loop
> **What happened:** Agent repeatedly called failing API (500 error)
> **What we learned:** Agents do not understand failure states
> **What to do instead:** Enforce max iterations + error handling logic

---
## Best Practices Checklist

* [ ] Limit tool scope (no generic execution tools)
* [ ] Validate all tool inputs
* [ ] Enforce max iteration limits
* [ ] Add human approval for destructive actions
* [ ] Log all agent decisions and tool calls
* [ ] Monitor cost and latency

---
## Anti-Patterns (Don't Do This)

| ❌ Don't                      | ✅ Do Instead           | Why                          |
| ---------------------------- | ---------------------- | ---------------------------- |
| Expose raw database queries  | Use scoped functions   | Prevents destructive queries |
| Give agent too many tools    | Use specialized agents | Improves accuracy            |
| Trust model-generated inputs | Validate in code       | Prevents execution errors    |
| Allow unrestricted loops     | Set iteration limits   | Prevents runaway costs       |

---
## Common Failure Modes

| Failure                | Cause             | Mitigation             |
| ---------------------- | ----------------- | ---------------------- |
| Infinite loops         | No stop condition | Add max iterations     |
| Tool misuse            | Poor descriptions | Improve schema clarity |
| Hallucinated arguments | Weak validation   | Enforce type checks    |
| Unauthorized actions   | No guardrails     | Add approval layer     |

---
## Related Topics

* [[Prompt-Engineering-Basics]]
* [[Evaluation-and-Testing]]
* [[RAG-Implementation]]

---
## Further Reading

* OpenAI Function Calling Guide - Tool schema design
* LangChain Agents - Prebuilt orchestration
* Internal: `Evaluation-and-Testing.md` - Measuring agent performance

---
## Changelog

* **2026-04-25**: Upgraded to enterprise production standard
* **2026-04-24**: Initial version

---
## Questions or Feedback?

Raise issues in your internal AI working group or extend with domain-specific agent patterns.


---
title: Agents & Tool Use
tags:
  - chapter
  - agents
  - tool-use
  - function-calling
  - development
  - advanced
difficulty: advanced
last_updated: 2026-05-12
time_to_read: 30 minutes
related:
  - "[[API-Integration-Guide]]"
  - "[[Prompt-Engineering-Basics]]"
  - "[[Prompt-Operating-Contracts]]"
  - "[[Agentic-Workflows]]"
  - "[[Evaluation-and-Testing]]"
  - "[[Security-and-Privacy]]"
---
# Agents & Tool Use

> **TL;DR for the Busy IT Pro:**  
> Prompts guide model behavior. Tool use lets AI interact with systems. Agents coordinate models, tools, instructions, state, and guardrails to complete workflows under controlled conditions.

---
## What You'll Learn

- [ ] The difference between prompts, tool use, workflows, and agents
- [ ] How function calling works as a controlled execution layer
- [ ] How to design safe tool interfaces for enterprise systems
- [ ] How to add approval gates, stop conditions, and audit trails
- [ ] Common failure modes when AI can take action

---
## Why This Matters

Standard LLMs generate text. Tool-using systems can read data, query APIs, update records, send messages, open tickets, or trigger workflows.

That creates real operational value, but it also creates real risk.

Tool use introduces:

- access to enterprise data and systems
- cost, latency, and dependency risk
- permission and audit requirements
- prompt-injection exposure
- failure loops and unintended actions

**Real-world scenario:**  
> Your operations team spends hours triaging system-health questions. A tool-using assistant connects to Datadog, PagerDuty, and Jira. In its first safe version, it only reads system status and summarizes incidents. Later, with approval gates, it can create or update tickets.

---
## Core Concepts

### Concept 1: Prompts, Tools, Workflows, and Agents Are Different Things

| Term | Meaning | Example |
|---|---|---|
| Prompt | Instruction to a model | "Summarize this incident report." |
| Tool use | Model requests an external function | `get_ticket_status(ticket_id)` |
| Workflow | Predefined sequence of steps | Classify ticket -> retrieve policy -> draft response |
| Agent | Model-directed workflow with tools, state, and guardrails | Diagnose outage using monitoring, ticketing, and on-call data |

**Why it works this way:**
Not every AI system needs agency. Many enterprise use cases are safer and cheaper as structured workflows.

---
### Concept 2: Agents = Model + Tools + Instructions + Orchestration

A practical agent has four minimum components:

- **Model:** reasons over the user request and current state
- **Tools:** APIs, functions, retrieval systems, databases, or actions
- **Instructions:** operating rules, boundaries, and output expectations
- **Orchestration:** the loop that manages tool calls, state, retries, approvals, and completion

For production, add:

- logging
- evals
- monitoring
- permissions
- rate limits
- rollback or kill switch

**Why it works this way:**
The model is only one part of the system. Most production risk lives in the surrounding architecture.

---
### Concept 3: Function Calling Is Controlled Execution

LLMs do not execute code directly. They request that your application execute a tool.

Flow:

```text
User request
-> Model decides a tool is needed
-> Model returns structured tool name + arguments
-> Application validates arguments
-> Application executes the function
-> Tool result is returned to the model
-> Model produces the final user-facing response
```

**Key insight:**
The LLM is a decision layer. Your application remains the execution layer.

**Why it works this way:**
This separation lets you validate, authorize, log, and constrain every real-world action.

---
### Concept 4: The Agent Loop Must Be Bounded

Agents often operate in a loop:

```text
Plan -> Tool call -> Observation -> Update -> Repeat -> Final answer
```

The loop requires hard limits:

- maximum iterations
- maximum tool calls
- timeouts
- cost budgets
- retry limits
- stop conditions
- fallback behavior

**Why it works this way:**
Without boundaries, an agent may retry failing tools, chase irrelevant context, or run up cost without improving the result.

---
### Concept 5: Human Approval Is a Design Pattern, Not a Patch

Require approval before actions that are:

- irreversible
- externally visible
- financially material
- security-sensitive
- privacy-sensitive
- infrastructure-impacting
- legally or HR relevant

Examples:

| Action | Approval Required? |
|---|---|
| Read ticket status | Usually no |
| Draft ticket response | Usually no, if not sent |
| Send customer email | Yes |
| Reboot server | Yes |
| Delete records | Yes |
| Update payroll or HR data | Yes |

**Why it works this way:**
Human-in-the-loop design keeps AI useful without pretending the model should own enterprise accountability.

---
## Hands-On Implementation

### Step 1: Define a Strict Tool Interface

Use narrow tools. Avoid generic tools like `execute_sql(query)` or `call_api(url, payload)`.

```python
jira_tool_schema = {
    "type": "function",
    "function": {
        "name": "get_jira_ticket_status",
        "description": "Get the current status of a Jira ticket. Use only when the user provides a valid ticket ID.",
        "parameters": {
            "type": "object",
            "properties": {
                "ticket_id": {
                    "type": "string",
                    "description": "A Jira ticket ID, for example IT-1234."
                }
            },
            "required": ["ticket_id"]
        }
    }
}
```

**What's happening here:**

- The tool is specific and read-only
- The description tells the model when to use it
- Required fields reduce ambiguity
- The schema gives your application something to validate

---
### Step 2: Validate Arguments Before Execution

```python
import re


def validate_ticket_id(ticket_id: str) -> bool:
    return bool(re.match(r"^[A-Z]+-[0-9]+$", ticket_id))


def safe_get_ticket_status(args):
    ticket_id = args.get("ticket_id")

    if not ticket_id or not validate_ticket_id(ticket_id):
        return {"error": "Invalid ticket ID format."}

    return get_jira_ticket_status(ticket_id)
```

**What's happening here:**

- The model's generated arguments are treated as untrusted input
- Validation happens in code, not by asking the model to behave
- Bad inputs fail safely

---
### Step 3: Run a Bounded Tool Loop

```python
MAX_ITERATIONS = 5
MAX_TOOL_CALLS = 3


def run_agent(user_message):
    messages = [{"role": "user", "content": user_message}]
    tool_call_count = 0

    for _ in range(MAX_ITERATIONS):
        response = call_model(messages=messages, tools=[jira_tool_schema])
        model_message = response.message

        if not model_message.tool_calls:
            return model_message.content

        for tool_call in model_message.tool_calls:
            tool_call_count += 1

            if tool_call_count > MAX_TOOL_CALLS:
                return "I could not complete this within the safe tool-call limit."

            if tool_call.name == "get_jira_ticket_status":
                result = safe_get_ticket_status(tool_call.arguments)
            else:
                result = {"error": "Tool not allowed."}

            messages.append(model_message)
            messages.append({
                "role": "tool",
                "tool_call_id": tool_call.id,
                "content": str(result)
            })

    return "I could not complete this within the safe iteration limit."
```

**What's happening here:**

- The agent cannot run indefinitely
- Every tool call is counted
- Unknown tools fail closed
- The system returns a controlled failure when limits are reached

---
### Step 4: Add Approval Gates for Write Actions

```python
WRITE_TOOLS = {"update_ticket", "send_email", "reboot_server", "delete_record"}


def require_approval(tool_call):
    if tool_call.name in WRITE_TOOLS:
        return {
            "approval_required": True,
            "proposed_action": tool_call.name,
            "arguments": tool_call.arguments,
            "message": "Review and approve before execution."
        }
    return None
```

**What's happening here:**

- Write actions pause before execution
- Users see what the AI intends to do
- The system preserves accountability

---
### Step 5: Log the Agent Trajectory

```python
trajectory_log = {
    "user_id": user_id,
    "session_id": session_id,
    "model": model_name,
    "agent_version": agent_version,
    "tool_calls": [],
    "approval_events": [],
    "final_status": None
}
```

Log:

- user ID or service account
- model and agent version
- prompt/instruction version
- tool names and arguments, sanitized
- tool results, sanitized
- approval decisions
- errors and retries
- final answer

**What's happening here:**

- Creates an audit trail
- Supports incident response
- Enables evals and debugging

---
## When to Use Agents vs Simpler Patterns

| Need | Better Pattern |
|---|---|
| Rewrite, summarize, classify | Single prompt |
| Answer from internal documents | RAG |
| Known multi-step process | Workflow |
| External data lookup | Tool use |
| Multiple possible paths | Agentic workflow |
| External action | Tool-using agent with approval gates |
| Long-running autonomy | High-control agent system with monitoring |

**Rule of thumb:**
Use the least-agentic pattern that solves the problem.

---
## Tool Design Standards

### Good tool design

```text
get_ticket_status(ticket_id)
get_user_by_employee_id(employee_id)
create_draft_email(to, subject, body)
retrieve_policy_section(policy_id, section_id)
```

### Dangerous tool design

```text
execute_sql(query)
call_any_api(url, method, payload)
send_message(any_recipient, any_body)
run_shell(command)
```

**Why it matters:**
Generic tools move too much risk into model behavior. Scoped tools keep risk in code, where it can be validated and controlled.

---
## Tips & Tricks

> [!tip] Quick Win
> Start with read-only tools. Prove value and reliability before enabling write actions.

> [!tip] Pro Tip
> Treat tool descriptions as mini-prompts. Tell the model exactly when to use the tool and when not to use it.

> [!warning] Watch Out
> Prompt injection becomes more dangerous when agents have tools. Treat all retrieved text, user input, web pages, emails, and documents as untrusted.

---
## Lessons Learned

> [!example] War Story: Infinite Retry Loop
> **What happened:** An agent repeatedly called a failing API that returned a 500 error.  
> **What we learned:** Agents do not automatically understand when to stop.  
> **What to do instead:** Enforce max iterations, retry limits, timeouts, error handling, and escalation behavior.

---
## Best Practices Checklist

- [ ] Use narrow, purpose-built tools
- [ ] Start read-only before enabling write actions
- [ ] Validate every model-generated argument in code
- [ ] Enforce authentication and authorization outside the model
- [ ] Add human approval for destructive, sensitive, financial, or external actions
- [ ] Set max iterations, tool-call limits, timeouts, and retry limits
- [ ] Log sanitized tool calls and decisions
- [ ] Evaluate both final response and tool trajectory
- [ ] Implement a kill switch or feature flag
- [ ] Monitor cost, latency, errors, and tool failure rate

---
## Anti-Patterns (Don't Do This)

| Don't | Do Instead | Why |
|---|---|---|
| Give an agent raw database access | Expose scoped read-only functions | Prevents destructive or overbroad queries |
| Trust model-generated arguments | Validate and type-check in code | Prevents execution errors and injection |
| Let agents run indefinitely | Use max iterations and timeouts | Prevents runaway costs and loops |
| Skip approval for write actions | Require explicit human approval | Preserves accountability |
| Give one agent dozens of overlapping tools | Use routing or specialists | Reduces tool confusion |
| Put secrets in tool descriptions | Keep secrets in backend services | Prevents credential exposure |

---
## Common Failure Modes

| Failure | Cause | Mitigation |
|---|---|---|
| Infinite loop | No stop condition | Add max iterations and retry limits |
| Tool misuse | Vague descriptions or too many tools | Tighten descriptions and reduce tool set |
| Hallucinated arguments | Weak schema or no validation | Validate arguments in code |
| Unauthorized action | Permissions handled by prompt only | Enforce permissions in backend |
| Data leakage | Logs capture raw prompts/results | Sanitize logs and apply retention rules |
| Unsafe external action | No approval gate | Add human-in-the-loop controls |
| High latency | Excessive tool calls | Add tool-call budget and routing |

---
## Related Topics

- [[Agentic-Workflows]] - Choosing the right workflow pattern before building agents
- [[Evaluation-and-Testing]] - Measuring tool trajectory and final response quality
- [[Security-and-Privacy]] - Defending against injection, leakage, and unsafe execution
- [[Prompt-Operating-Contracts]] - Defining the operating contract before implementation
- [[API-Integration-Guide]] - Implementing reliable backend calls

---
## Further Reading

- [OpenAI Function Calling Guide](https://platform.openai.com/docs/guides/function-calling) - Tool schema and function-calling concepts
- [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) - Agent orchestration concepts
- [Anthropic Tool Use Documentation](https://docs.anthropic.com/) - Tool-use design patterns for Claude
- [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) - Security risks for LLM applications
- Internal: [[agent-operating-contract-template]] - Reusable agent specification template

---
## Changelog

- **2026-05-12**: Canonicalized filename, expanded tool safety, approval gates, trajectory logging, and agent-vs-workflow guidance
- **2026-04-25**: Upgraded to enterprise production standard
- **2026-04-24**: Initial version

---
## Questions or Feedback?

Raise issues in your internal AI working group or extend this chapter with domain-specific tool patterns.

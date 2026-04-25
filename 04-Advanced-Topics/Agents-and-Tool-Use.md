title: Agents & Tool Use (Function Calling)
tags:[chapter, agents, tool-use, development, advanced]
difficulty: advanced
last_updated: 2026-04-24
time_to_read: 25 minutes
related:
  - "[[API-Integration-Guide]]"
  - "[[Prompt-Engineering-Playbook]]"
  - "[[Security-and-Privacy]]"
---
# Agents & Tool Use (Function Calling)

> **TL;DR for the Busy IT Pro:**  
> RAG lets AI *read* your data; Agents let AI *take action*. By giving the LLM a list of functions (tools), it can decide when to query databases, call APIs, or trigger webhooks. The LLM doesn't run the code itself—it just tells your application what to run.

---
## What You'll Learn

- [ ] The difference between basic Function Calling and Autonomous Agents
- [ ] How the ReAct (Reason + Act) loop actually works
- [ ] How to define strict JSON schemas for your tools
- [ ] Implementing hard guardrails to prevent catastrophic AI actions

---
## Why This Matters

Standard LLMs are passive. If a user asks, "Can you unlock my account?", a standard LLM can only output a tutorial on how the user can do it themselves. 

An **Agent** with tools can actually look up the user's ID in Active Directory, execute the `unlock_account` API, and reply: "I have unlocked your account." This shifts AI from a "search engine" to a "digital worker."

**Real-world scenario:**  
> Your DevOps team gets 50 Slack messages a day asking, "Is the billing database down?" You build an Agent in Slack. When someone asks, the Agent uses a `query_datadog` tool to check server health, and a `query_pagerduty` tool to see who is on call, summarizing the outage instantly without waking up an engineer.

---
## Core Concepts

### Concept 1: Function Calling (The Building Block)

Despite the hype, LLMs cannot execute code. "Function Calling" (or Tool Use) is simply a prompt engineering trick built into modern APIs.

1. You send a prompt + a JSON list of tools your system has (e.g., `get_weather(location)`).
2. The LLM reads the prompt, realizes it needs the weather, and instead of replying with conversational text, it replies with a JSON payload: `{"tool": "get_weather", "arguments": {"location": "Toronto"}}`.
3. **Your code** parses this JSON, runs your actual Python/Node function, and sends the result *back* to the LLM.

### Concept 2: The ReAct Framework

ReAct stands for **Reason + Act**. It is the standard `while` loop that turns a basic LLM into an "Agent." 

The loop looks like this:
1. **Thought:** The model thinks about what to do ("I need to find the user's ID, then reset their password.")
2. **Action:** The model calls the `lookup_user` tool.
3. **Observation:** Your code runs the tool and hands the result back to the model.
4. **Repeat:** The model thinks again based on the observation, taking the next action, until the task is complete.

---
## Hands-On Implementation

### Step 1: Define the Tool Schema

You must tell the AI exactly what your tool does and what arguments it requires using a JSON Schema.

```python
# We define a tool that checks Jira ticket status
jira_tool_schema = {
    "type": "function",
    "function": {
        "name": "get_jira_ticket_status",
        "description": "Get the current status of a Jira ticket. Use this when a user asks for updates on their IT request.",
        "parameters": {
            "type": "object",
            "properties": {
                "ticket_id": {
                    "type": "string",
                    "description": "The Jira ticket ID, e.g., IT-1234"
                }
            },
            "required": ["ticket_id"]
        }
    }
}
```

### Step 2: The Execution Loop (Python/OpenAI Example)

This is the actual backend code that handles the ReAct loop.

```python
import json
import openai

client = openai.OpenAI()

# 1. Your actual backend function
def get_jira_ticket_status(ticket_id):
    # In reality, this makes an HTTP request to Jira
    mock_db = {"IT-1234": "In Progress", "IT-9999": "Closed"}
    return mock_db.get(ticket_id, "Ticket not found")

# 2. The Agent Logic
def run_agent(user_message):
    messages = [{"role": "user", "content": user_message}]
    
    # Send user query + tool schema to LLM
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=messages,
        tools=[jira_tool_schema],
        tool_choice="auto" # Let the model decide if it needs the tool
    )
    
    response_message = response.choices[0].message
    
    # Check if the model decided to call a tool
    if response_message.tool_calls:
        for tool_call in response_message.tool_calls:
            if tool_call.function.name == "get_jira_ticket_status":
                # Parse the arguments the AI generated
                args = json.loads(tool_call.function.arguments)
                
                # Execute your actual code
                function_result = get_jira_ticket_status(ticket_id=args.get("ticket_id"))
                
                # Append the AI's request and your code's result to the conversation
                messages.append(response_message)
                messages.append({
                    "role": "tool",
                    "tool_call_id": tool_call.id,
                    "content": str(function_result)
                })
                
                # Call the API one more time so it can read the result and answer the user
                final_response = client.chat.completions.create(
                    model="gpt-4o",
                    messages=messages
                )
                return final_response.choices[0].message.content

    # If no tool was needed, just return the text
    return response_message.content

# Usage:
print(run_agent("Can you check on my laptop request? The ticket is IT-1234."))
# Output: "Your IT request (IT-1234) is currently In Progress. Let me know if you need anything else!"
```

---
## Tips & Tricks

> [!tip] Quick Win
> Don't write JSON schemas by hand. Use the `pydantic` library in Python to define your data models, and use `.model_json_schema()` to automatically generate the tool schema for the API.

>[!tip] Pro Tip
> Limit the number of tools you give an agent. If you give one agent 50 tools, it will get confused and hallucinate arguments. Instead, use a **Multi-Agent System**: have a "Router Agent" read the prompt, and hand the task off to a specialized "Database Agent" or "Email Agent" that only has 3-4 highly specific tools.

> [!warning] Watch Out
> **Never put sensitive secrets in the tool schema.** The LLM does not need your API keys. Your Python code holds the API keys. You only tell the LLM the name of the function and the arguments it needs to generate.

---
## Lessons Learned

> [!example] War Story: The Infinite ReAct Loop
> **What happened:** A developer built an agent to query a SQL database. The database went down, returning a `500 Error`. The agent observed the error, thought "I should try again," and called the tool again. It did this 400 times in 5 minutes, burning through $200 of API credits before hitting a hard rate limit.  
> **What we learned:** Agents lack common sense. They will loop forever if a tool fails.  
> **What to do instead:** Always hardcode a `max_iterations = 5` variable in your `while` loop. If the agent hasn't solved the problem in 5 steps, force it to return an error to the user.

---
## Best Practices Checklist

- [ ] Practice 1: **Read-Only by Default.** Start by only giving agents `GET` tools (read database, check status). 
- [ ] Practice 2: **Human-in-the-Loop for Writes.** If a tool executes a `POST` or `DELETE` (e.g., `reboot_server`), your application must pause, show the user what the AI wants to do, and require a human click to execute.
- [ ] Practice 3: **Clear Descriptions.** The `description` field in your JSON schema is essentially a mini-prompt. Tell the model *exactly* when and why to use the tool.
- [ ] Practice 4: **Fail Gracefully.** If your backend function throws an error, catch it and return a string like `"Error: Database timeout"` to the LLM so it knows what happened and can explain it to the user.

---
## Anti-Patterns (Don't Do This)

| ❌ Don't | ✅ Do Instead | Why |
|---------|--------------|-----|
| Give an agent raw `execute_sql(query)` tools | Give it `get_user_by_id(id)` tools | An LLM might hallucinate a `DROP TABLE` or query the whole DB. Limit its scope via strict functions. |
| Trust the AI's arguments implicitly | Validate arguments in your code | The AI might send a string when you asked for an integer. Type-check before executing. |
| Run agents asynchronously without timeouts | Wrap the agent loop in a strict timeout (e.g., 60s) | Agents can "think" for a long time. Don't lock up your application threads. |

---
## Related Topics

- [[API-Integration-Guide]] - How to handle the async loops required for agents.
- [[Security-and-Privacy]] - Defending against prompt injection (which is extra dangerous when agents have tools).
- [[Prompt-Engineering-Playbook]] - Techniques for writing good tool descriptions.

---
## Further Reading

- [OpenAI Tool Calling Guide](https://platform.openai.com/docs/guides/function-calling) - The gold standard for tool schema syntax.
- [Anthropic Tool Use (Claude)](https://docs.anthropic.com/claude/docs/tool-use) - Excellent guide on how Claude handles XML-based tool execution.
- [LangChain Agents](https://python.langchain.com/docs/modules/agents/) - Best for: Out-of-the-box ReAct frameworks if you don't want to build the `while` loop from scratch.

---
## Changelog

- **2026-04-24**: Created
- **2026-04-24**: Added Human-in-the-loop requirement for destructive actions.

---
## Questions or Feedback?

Need help defining a complex JSON schema for your tool? Drop your function signature in the `#ai-dev-guild` Slack channel.

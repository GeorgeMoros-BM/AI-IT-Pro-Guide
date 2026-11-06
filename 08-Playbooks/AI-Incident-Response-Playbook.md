---
title: "AI Incident Response Playbook"
artifact_type: playbook
audience:
  - executive
  - practitioner
  - platform
domain:
  - ai-governance
status: draft
maturity: advanced
last_updated: 2026-11-05
time_to_read: 12 minutes
publish: false
client_safe: true
---
# Purpose

This playbook defines how to detect, contain, investigate, and recover from failures in production AI systems. It adds an AI path to your existing incident response plan. It doesn't replace that plan. Like NIST SP 800-61r3, it treats preparation and lessons learned as part of ongoing risk management, not only the response itself.

[[Prompt-Risk-Tiering]] requires an incident playbook from Tier 3 and recommends one at Tier 2.

Clocks, severity levels, and role names below are starting points to adapt. This playbook isn't legal advice, so have counsel confirm which duties apply to you.

---

# What Counts as an AI Incident

An AI incident is an event in which an AI system caused harm, or could have, through its output, its actions, or its handling of data. Near misses count, because they show which control failed at no cost.

| Type | Example | First Concern |
|---|---|---|
| Wrong answers at scale | An assistant gives an outdated policy to many staff | Who relied on it |
| Data exposure | Retrieval or a cache shows restricted content | Whose data, and which duties apply |
| Unauthorized tool action | An injected instruction triggers a refund | Stop it and reverse it |
| Stale or poisoned corpus | A superseded document is still retrieved | Remove the source and cached answers |
| Model or vendor regression | A provider update changes behavior | Pin, reroute, or restrict |
| Cost runaway | An agent loops and token use spikes | Stop the spend |
| Outage | A provider is unavailable | Use the fallback |
| Unfair outcomes | A screening tool disadvantages a group | Move to human review and involve legal |
| Third-party incident | A vendor reports a breach | Get facts and contract terms |

---

# Severity

| Level | Definition | Contain Within | Updates |
|---|---|---|---|
| SEV1 | Confirmed data exposure, a harmful action, a safety impact, or a possible legal report | 30 minutes | Hourly |
| SEV2 | Wrong outputs affect decisions or many users, with no confirmed exposure | 2 hours | Every 4 hours |
| SEV3 | Limited impact, with a workaround in place | 1 business day | Daily |
| SEV4 | Near miss or minor quality issue | Next release | In the record |

When unsure, choose the higher level. You can downgrade it later.

---

# Roles

| Role | Responsibility | Usual Owner |
|---|---|---|
| Incident commander | Runs the response, sets severity, and makes the calls | IT or security lead |
| AI system owner | Provides system knowledge and carries out fixes | Product or platform owner |
| Security lead | Handles evidence, access, and any attacker activity | Security |
| Privacy and legal lead | Decides on notification | Privacy office or counsel |
| Communications lead | Handles staff and customer messages | Communications |
| Vendor contact | Escalates to the provider | Procurement or platform owner |
| Scribe | Keeps the timeline | Any trained team member |

One person can hold several roles on a small team, but the commander shouldn't also do the fixing.

---

# Prepare Before an Incident

- [ ] Each Tier 3 and above system has a kill switch that works without a deployment
- [ ] The kill switch has been tested, and two people know how to use it
- [ ] Each response logs its bundle version: prompt, model, tools, corpus, routing, and cache policy
- [ ] Traces show who asked, what was retrieved, and which tools ran
- [ ] Log retention covers your longest reporting window
- [ ] Write tools have an action log and a way to reverse actions
- [ ] Budgets and alerts exist for tokens and tool calls
- [ ] Each system has a documented fallback
- [ ] Vendor contacts and contract notice terms are on file
- [ ] A named person decides on legal notification, with a backup
- [ ] Counsel has reviewed [[AI-Incident-Notification-Reference]] for your jurisdictions
- [ ] A tabletop exercise has been run in the last 12 months

---

# Phase 1 - Detect and Triage

Signals:
- eval or regression failures
- user and employee reports
- data loss prevention and security alerts
- cost and rate alerts
- vendor notices
- customer complaints

Answer these in the first 15 minutes:
1. Which system is affected, and what is its risk tier?
2. Is the harm still happening?
3. Did the system take an action, or only produce an output?
4. Could personal or restricted data be involved?
5. Who is affected, and how many?
6. What severity applies?

Open an incident record and note when your organization became aware. Later, note when it determined that a reportable event occurred. Different reporting clocks run from each moment.

---

# Phase 2 - Contain

Turn on the kill switch first. Use a narrower step only if you already know the cause and can show that the step stops the harm.

Don't try to fix the prompt in production while the system is live.

| Step | Use When |
|---|---|
| Turn on the kill switch | Default. Also use it when the scope is unclear or harm continues |
| Switch to fallback or read-only mode | The service must continue, and read access is safe |
| Disable one tool or write permission | The harm comes from one action |
| Disable one source and clear cached answers | The harm comes from one document or cached answer |
| Roll back to the last approved bundle | A recent change caused the failure |
| Revoke keys and tokens | Credentials may be compromised. Do this alongside any step above |

---

# Phase 3 - Preserve Evidence

Capture evidence before cleanup whenever you can do so without letting harm continue. Clearing a cache or removing a document can destroy what you need.

- [ ] Time of detection and time of awareness
- [ ] Failing inputs and outputs
- [ ] Traces, including prompts and retrieved chunk IDs and versions
- [ ] Tool calls, arguments, and approvals
- [ ] User identity, groups, and token scope at the time
- [ ] Bundle version and deployment approval
- [ ] Eval results before and after the event
- [ ] Cache entries and keys
- [ ] Vendor status notices and support cases

Store evidence in a restricted location and log who opens it. Evidence can contain personal data, so it needs the same care as the source.

---

# Phase 4 - Investigate

Answer the questions from [[03-Enterprise-Concerns/PromptOps-Governance]]:
1. What prompt version was used?
2. What model version was used?
3. What data was retrieved?
4. What tool calls were made?
5. What user permissions applied?
6. What evaluation cases failed?
7. Who approved deployment?
8. How can the feature be disabled?

Then reproduce the failure offline against your eval suite. Run it several times, because model outputs vary and one clean run proves little.

Find the scope by searching logs for the same pattern, and go back to the last known good version. A missing log is itself a finding.

Classify the root cause as prompt, model, retrieval, tool, permission, guardrail, or process. More than one may apply.

---

# Phase 5 - Remediate

Fix the layer that failed, and prefer a control that still holds when the model is fooled.

| Root Cause | Fix |
|---|---|
| Prompt | Update the constraints, and add a negative example |
| Retrieval | Apply the Retrieval Corpus Lifecycle rule |
| Tool | Add a hard limit in the tool or API behind it |
| Permission | Correct the scope, and retest with two users |
| Injection | Reduce what a fooled model can do. See [[Security-and-Privacy]] |
| Model | Pin or reroute, then update prompts and evals |
| Process | Change the gate or approval that let it through |

Reverse what the system did, including refunds, emails, and record changes. Correct anything people were told.

---

# Phase 6 - Recover

Return the system to service through the normal release gate:
1. Add the failing case to the regression suite.
2. Run the full eval suite on the fix.
3. Get approval from the system owner, plus security for SEV1 and SEV2.
4. Re-enable the system for a small group first.
5. Watch outcomes, cost, and alerts for an agreed period.
6. Restore full service and note the time.

---

# Phase 7 - Notify

The privacy and legal lead makes this decision, and it runs alongside the other phases. Don't wait for the root cause before you start.

Use [[AI-Incident-Notification-Reference]] to find which duties may apply. It covers Canada, the United States, and the EU by trigger, recipient, and clock. Some clocks are measured in hours, so check it in the first hour of a SEV1.

Keep these points in mind:
- **Record the moments:** Clocks can run from awareness, discovery, determination, or reasonable belief.
- **Settle positions early:** Agree on your reading of unsettled rules with counsel before an incident, not during one.
- **Keep records:** Some regimes require a record of every breach, including breaches you don't report.
- **No legal clock:** A wrong-answer incident may trigger no report, but people who relied on the answer still need a correction.

Each message should say what happened, who is affected, what you've done, what they should do, and who to contact. Don't speculate about the cause.

---

# Phase 8 - Learn

Hold a post-incident review within five business days of closing a SEV1 or SEV2 incident. Focus on what failed in the system, not on who to blame.

Cover the timeline, the root cause, why controls missed it, what worked, and actions with owners and dates.

Every review should produce:
- a regression test for each failure
- a check of the risk tier, in case the system was under-classified
- updates to the prompt registry and any affected control
- changes to this playbook if a step failed
- a [[Decision-Log]] entry if a standard changed

Track time to detect, contain, and recover, plus the share of incidents with complete traces.

---

# Scenario Runbooks

## Scenario 1 - Injected Instruction Triggers a Tool Action

- **Signal:** A tool call doesn't match what the user asked, such as a refund after the assistant read a customer email.
- **Contain:** Turn on the kill switch or disable the write tool. Revoke any exposed tokens.
- **Preserve:** The outside content, the trace, tool arguments, and approval state.
- **Fix:** Reverse the action. Enforce limits in code, and require approval when untrusted content is in the session. See Step 3 in [[Production-AI-Assistant-Stack]].
- **Test:** Add the injected content to the regression suite, and confirm that the tool is blocked.
- **Notify:** Tell affected customers. Start a privacy assessment if data left the system.

## Scenario 2 - Restricted Content Exposed Through Retrieval or Cache

- **Signal:** A user reports seeing another team's content, or an access test fails.
- **Contain:** Turn on the kill switch. Disable the source and clear the cache after you capture the entries.
- **Preserve:** Retrieval logs, chunk access fields, cache keys, and user groups at the time.
- **Scope:** Use logs to find who received which chunks. Without them, you may have to assume everyone with access was affected.
- **Fix:** Filter inside the search call, key the cache by access group, and resync permissions.
- **Test:** Add a two-user test for the failed boundary.
- **Notify:** Start a privacy assessment at once, and record the time of awareness.

## Scenario 3 - Outdated Policy Given at Scale

- **Signal:** An expert reports a wrong answer, or a superseded document appears in citations.
- **Contain:** Remove the document and clear cached answers. Turn on the kill switch if the decisions are high stakes.
- **Preserve:** Document versions, and queries that cited the old version.
- **Fix:** Apply the Retrieval Corpus Lifecycle rule, and filter on `approval_status` and `effective_date`.
- **Test:** Add a known-answer test for the updated policy.
- **Notify:** Send a correction to the people who received the wrong answer.

## Scenario 4 - Model or Vendor Update Changes Behavior

- **Signal:** Scheduled evals drop, or refusals and complaints rise with no change on your side.
- **Contain:** Pin the earlier version or route to a fallback model. If the vendor controls the model, restrict the feature and open a support case.
- **Preserve:** Eval runs from before and after, model IDs in logs, and vendor notices.
- **Fix:** Update prompts and evals for the new model, or change the route.
- **Test:** Keep the failing cases in a scheduled regression run.
- **Notify:** Usually internal. Check the contract if the vendor gave no notice.

## Scenario 5 - Runaway Cost

- **Signal:** A budget alert fires, token use per session spikes, or traces show a loop.
- **Contain:** Stop the agent or key, and lower rate limits.
- **Preserve:** Traces, usage reports, and key and user IDs.
- **Investigate:** Check for a loop, abuse, a stolen key, a routing change, or a drop in cache hits.
- **Fix:** Add step limits, per-session budgets, and loop detection outside the model.
- **Test:** Add an input that causes the loop, and confirm that the limit stops it.
- **Notify:** Tell finance. Treat a stolen key as a security incident.

---

# Tabletop Exercise

Run this in 60 minutes with the roles above:
1. Choose a scenario that fits a live system.
2. Reveal the facts in three stages, about 15 minutes apart.
3. Ask the team to find the kill switch, the bundle version, and the affected users.
4. Ask who decides on notification, and what they would need to know.
5. Record each gap, with an owner and a date.

Run it at least once a year and after a major change, such as adding write tools.

---

# Incident Record Template

```markdown
## AI Incident Record

Incident ID:
System:
Risk tier:
Severity:
Commander:

Time detected:
Time of awareness:
Time of determination:
Time contained:
Time recovered:

What happened:
Who is affected:
Bundle version:
Data involved:
Actions taken by the system:

Containment steps:
Evidence location:
Root cause:
Fix:
Regression test added:

Notification decision:
Decided by:
Reports sent:

Follow-up actions:
Owner:
Due date:
```

---

# Related Notes

- [[AI-Incident-Notification-Reference]]
- [[Governance-and-Risk]]
- [[03-Enterprise-Concerns/PromptOps-Governance]]
- [[Prompt-Risk-Tiering]]
- [[Security-and-Privacy]]
- [[Evaluation-and-Testing]]
- [[Production-AI-Assistant-Stack]]
- [[Enterprise-RAG-Architecture]]
- [[Troubleshooting-AI-Runbook]]

---

# Sources

- [NIST SP 800-61r3: Incident Response Recommendations and Considerations for Cybersecurity Risk Management](https://nvlpubs.nist.gov/nistpubs/specialpublications/nist.sp.800-61r3.pdf)

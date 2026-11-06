---
title: "Current Model Register"
artifact_type: reference
audience:
  - practitioner
  - architect
  - executive
domain:
  - llm-models
status: draft
last_updated: 2026-11-05
review_frequency: monthly
publish: false
client_safe: true
---
# Current Model Register

This note is the only source for current model names, prices, context limits, and retirement dates. Other chapters use the roles below and link here.

Checked against provider pages on November 5, 2026. Prices are in US dollars per million tokens at standard rates. Confirm them before a budget decision.

Weekly reports, war stories, and sector files stay as written, because they're dated records.

---
# Roles

| Role | Use For | Anthropic | OpenAI | Google | xAI |
|---|---|---|---|---|---|
| Small | High volume, simple tasks | Claude Haiku 5.5 | GPT-6 Luna | Gemini 3.5 Flash-Lite | None listed |
| Mid | Everyday work | Claude Sonnet 5.5 | GPT-6.1 Terra | Gemini 3.8 Flash | Grok 4.3 |
| Premium | Hard reasoning, coding, and analysis | Claude Opus 5.5 | GPT-6.1 Sol | Gemini 4 Argon | Grok 4.7 |
| Frontier | The hardest, highest-value work | Claude Fable 5.1 | GPT-6 Astra | None listed | None listed |

These assignments are an editorial judgment based on price and provider positioning, not a benchmark. Prices within a role differ, so test on your own tasks before you choose.

---
# Anthropic

| Model | API ID | Input | Cache Read | Output | Context | Max Output | Earliest Retirement |
|---|---|---:|---:|---:|---:|---:|---|
| Claude Fable 5.1 | `claude-fable-5-1` | $10.00 | $0.25 | $50.00 | 1M | 128K | Sep 1, 2027 |
| Claude Opus 5.5 | `claude-opus-5-5` | $4.00 | $0.20 | $20.00 | 1M | 128K | Sep 22, 2027 |
| Claude Sonnet 5.5 | `claude-sonnet-5-5` | $2.00 | $0.10 | $10.00 | 1M | 128K | Sep 28, 2027 |
| Claude Haiku 5.5, prompts up to 100K | `claude-haiku-5-5` | $0.10 | $0.01 | $0.50 | 1M | 128K | Oct 7, 2027 |
| Claude Haiku 5.5, prompts over 100K | `claude-haiku-5-5` | $0.50 | $0.05 | $2.50 | 1M | 128K | Oct 7, 2027 |

Cache writes cost 1.25 times input for five minutes and 2 times input for one hour. The Batch API is 50% off.

---
# OpenAI

| Model | API ID | Input | Cached Input | Output | Context | Max Output | Earliest Shutdown |
|---|---|---:|---:|---:|---:|---:|---|
| GPT-6 Astra | `gpt-6-astra` | $10.00 | $1.00 | $50.00 | 1.05M | 128K | None announced |
| GPT-6.1 Sol | `gpt-6.1-sol` | $2.00 | $0.10 | $10.00 | 1.05M | 128K | None announced |
| GPT-6.1 Terra | `gpt-6.1-terra` | $1.00 | $0.05 | $5.00 | 1.05M | 128K | None announced |
| GPT-6 Luna | `gpt-6-luna` | $0.10 | $0.01 | $0.50 | 1.05M | 128K | None announced |

Flex and Batch are half the standard price. Fast is double, and Ultrafast costs more.

---
# Google

| Model | API ID | Input | Cached Input | Output | Input Limit | Max Output | Earliest Shutdown |
|---|---|---:|---:|---:|---:|---:|---|
| Gemini 4 Argon | `gemini-4-argon` | $2.00 | $0.10 | $10.00 | 1M | 65.5K | None announced |
| Gemini 3.8 Flash | `gemini-3.8-flash` | $0.75 | $0.075 | $3.75 | 1M | 65.5K | None announced |
| Gemini 3.6 Flash | `gemini-3.6-flash` | $0.75 | $0.075 | $3.75 | 1M | Not checked | None announced |
| Gemini 3.5 Flash-Lite | `gemini-3.5-flash-lite` | $0.30 | $0.03 | $2.50 | 1M | Not checked | None announced |
| Gemini 3.1 Pro Preview, prompts up to 200K | `gemini-3.1-pro-preview` | $2.00 | $0.20 | $12.00 | 1M | Not checked | None announced |
| Gemini 3.1 Pro Preview, prompts over 200K | `gemini-3.1-pro-preview` | $4.00 | $0.40 | $18.00 | 1M | Not checked | None announced |
| Gemini 3.1 Flash-Lite | `gemini-3.1-flash-lite` | $0.25 | $0.025 | $1.50 | 1M | Not checked | May 7, 2027 |

These are paid Standard rates for text. Gemini 4 Argon and the 3.8 and 3.6 Flash models are at introductory prices that are about to double. See Scheduled Changes. Batch and Flex are 50% off.

---
# xAI

| Model | API ID | Input | Cached Input | Output | Context | Earliest Shutdown |
|---|---|---:|---:|---:|---:|---|
| Grok 4.7 | `grok-4.7` | $2.00 | $0.50 | $6.00 | 500K | None listed |
| Grok 4.3 | `grok-4.3` | $1.25 | $0.20 | $2.50 | 1M | None listed |
| Grok Build 0.1 | `grok-build-0.1` | $1.00 | $0.20 | $2.00 | 256K | None listed |

Rates double once a prompt reaches 200K tokens. xAI's pricing page also lists Grok 4.6, Grok 4.5, and Grok 4.20 variants.

---
# Cost Traps

Listed prices don't compare directly. Check these before you pick a model on price:

- **Long prompts:** Haiku 5.5, Gemini 3.1 Pro Preview, and Grok models charge more above the thresholds shown here, and the higher rate covers the whole request. GPT-6 Astra bills requests over 272K input tokens at 2 times input and cache rates and 1.5 times output. Check each OpenAI model's page, because the pricing page doesn't state the rule.
- **Tokenizers:** Anthropic says Claude 4.7 and later produce about 30% more tokens for the same text than earlier models. Compare cost per task, not only cost per token.
- **Cache reads:** Across the models listed here, they range from 2.5% to 25% of the input price. Don't assume one discount for every provider.
- **Data residency:** Anthropic charges 1.1 times for US-only processing on Claude 4.6 and later. OpenAI adds 10% for regional processing on models released on or after March 5, 2026.
- **Introductory prices:** A budget built on today's Gemini rates will be wrong within weeks.

---
# Scheduled Changes

Anthropic and Google dates marked "earliest" are the soonest a model could go, not confirmed shutdowns.

| Date | Provider | Change |
|---|---|---|
| Nov 17, 2026 | Google | Earliest shutdown of Gemini 3.1 Flash TTS and Live previews, and Gemini 2.5 audio and TTS previews |
| Nov 24, 2026 | Anthropic | Earliest retirement of Claude Opus 4.5 |
| Nov 30, 2026 | Anthropic | Claude Sonnet 4.5 retires, and Opus 4.8 fast mode is removed |
| Nov 30, 2026 | OpenAI | GPT-5.6 Sol promotional pricing ends. Reusable prompts, the Evals platform, and Agent Builder shut down |
| Dec 1, 2026 | Google | Gemini 4 Argon rises to $4.00 input, $0.20 cached input, and $20.00 output |
| Dec 1, 2026 | OpenAI | `gpt-image-1-mini`, `gpt-image-1.5`, and `chatgpt-image-latest` shut down |
| Dec 11, 2026 | OpenAI | `gpt-5`, `gpt-5-mini`, `gpt-5-nano`, `gpt-5-pro`, `o3`, and `o3-pro` shut down |
| Jan 1, 2027 | Google | Gemini 3.8 and 3.6 Flash rise to $1.50 input, $0.15 cached input, and $7.50 output |
| Jan 14, 2027 | OpenAI | GPT-4o, GPT-4o mini, GPT-4.1, and GPT-4.1 mini snapshots shut down |
| Jan 20, 2027 | OpenAI | GPT-4o audio and realtime models shut down |
| Feb 5, 2027 | Anthropic | Earliest retirement of Claude Opus 4.6 |
| Feb 17, 2027 | Anthropic | Earliest retirement of Claude Sonnet 4.6 |
| Feb 26, 2027 | OpenAI | `whisper-1` and GPT-4o transcription models shut down |
| Apr 1, 2027 | OpenAI | `gpt-5.1`, `gpt-5.3-codex`, and `gpt-5.4-nano` shut down |
| May 7, 2027 | Google | Earliest shutdown of Gemini 3.1 Flash-Lite |

Claude Haiku 4.5 passed its earliest retirement date of October 15, 2026, and is still listed as active.

---
# Older Names in the Vault

| Name in Older Notes | Status | Replacement |
|---|---|---|
| Claude 3.x models | None listed as active | Use the matching Claude role |
| Claude Opus 4, Opus 4.1, and Sonnet 4 | Retired | Use the matching Claude role |
| GPT-5 family and o3 | Shuts down December 11, 2026 | OpenAI recommends the GPT-5.6 tiers |
| GPT-4o and GPT-4.1 | Snapshots shut down January 14, 2027 | OpenAI recommends GPT-6 Luna |
| Gemini 2.5 | Not deprecated, but limited to previous users | Google recommends 3.5 Flash-Lite or 3.8 Flash |
| Gemini 1.5 | Not on Google's current model list | Use the matching Gemini role |

---
# Scope

This register doesn't cover:
- open-weight models, because their cost depends on your hosting
- image, audio, video, and embedding models
- prices on Amazon Bedrock, Microsoft Foundry, or Google Vertex AI
- subscription plans

---
# Maintenance

1. Review monthly, and after any provider notice.
2. Change figures only from provider pages, and update the checked date.
3. Remove dates from Scheduled Changes once they pass, and update the tables.
4. When you edit an older note, replace model names and prices with roles and a link here.

---
# Related Notes

- [[Token-Cost-Quick-Reference]]
- [[Cost-Management-and-ROI]]
- [[Model-Selection-Framework]]
- [[LLM-Provider-Comparison]]

---
# Sources

Provider pages:
- [Claude pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- [Claude models overview](https://platform.claude.com/docs/en/models/overview)
- [Claude model deprecations](https://platform.claude.com/docs/en/about-claude/model-deprecations)
- [Claude Platform release notes](https://platform.claude.com/docs/en/release-notes/overview)
- [OpenAI pricing](https://developers.openai.com/api/docs/pricing)
- [OpenAI models](https://developers.openai.com/api/docs/models)
- [GPT-6 Astra model page](https://developers.openai.com/api/docs/models/gpt-6-astra)
- [OpenAI deprecations](https://developers.openai.com/api/docs/deprecations)
- [Gemini Developer API pricing](https://ai.google.dev/gemini-api/docs/pricing)
- [Gemini models](https://ai.google.dev/gemini-api/docs/models)
- [Gemini 3.8 Flash](https://ai.google.dev/gemini-api/docs/models/gemini-3.8-flash)
- [Gemini deprecations](https://ai.google.dev/gemini-api/docs/deprecations)
- [xAI pricing](https://docs.x.ai/developers/pricing)
- [xAI models](https://docs.x.ai/developers/models)
- [Grok 4.7](https://docs.x.ai/developers/grok-4-7)
- [Grok 4.3](https://docs.x.ai/developers/models/grok-4.3)

Secondary sources, used where a provider page couldn't be read:
- [Gemini input limits and Argon output limit (Maximem)](https://www.maximem.ai/blog/gemini-api-pricing-2026)
- [Grok Build 0.1 context window (CloudZero)](https://www.cloudzero.com/blog/grok-pricing/)

---
# Changelog

- **2026-11-05**: Created the register

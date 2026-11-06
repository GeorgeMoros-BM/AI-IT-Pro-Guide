---
last_updated: 2026-08-13
---
The AI model landscape has shifted significantly, and knowing which model to reach for, and when, now actually matters. This guide breaks down every major model across four categories: flagship, mid-tier workhorse, light/fast, and specialized. It's for anyone who wants to stop guessing and start picking the right tool for the job.

> **TL;DR:** The gap between open-source and closed-source models is now razor thin. Chinese open-source models are dominating the mid-tier category on price and performance. For most day-to-day tasks, you don't need a flagship model at all.
# Quickstart: Getting Started With Local Models

Running open-source models yourself is more approachable than it looks. Here's a simple four-rung ladder to get started, from zero setup to full agent integration.

Rung 1: **Try it in a browser**
Use LMArena. It's free, no setup required. Just go and start experimenting with open models immediately.

Rung 2: **Run one on your machine**
Use LM Studio if you want a chat window and a GUI. Use Ollama if you want an API that other tools can plug into. Check your usable RAM first to pick an appropriately sized model.

Rung 3: **Use open models via API**
OpenRouter gives you one API key with access to hundreds of models. Swap your OpenAI SDK base URL to [https://openrouter.ai/api/v1](https://openrouter.ai/api/v1) and you're done.

Rung 4: **Point it at an agent**
For a coding agent, run `ollama launch opencode` or point the provider to your localhost address. For an agent framework like Hermes, run `ollama launch hermes` or set a custom endpoint to your localhost address.
# Flagship Models: The Best of the Best

These are the frontier models. Not every company's top model earns this label, only the ones that genuinely compete at the highest level of capability.
## Claude Fable 5 — Anthropic
The smartest model you can buy. Best for complex planning, brainstorming, and complicated software projects. Expensive, slow, and restricted.
## GPT 5.6 Sol — OpenAI
The flagship all-rounder. Fable-level coding at half the price, best-in-class terminal and web browsing, and often bundled with image generation.
## Claude Opus 5 — Anthropic
The reliable fallback flagship. More direct and honest about what it knows and doesn't know. Reach for this when Fable credits run out.
## Gemini 3.1 Pro — Google
The multimodal king. The only frontier model that can actually watch a video. Lags behind others on raw coding ability, but deeply embedded across Google's ecosystem.
## Grok 4.5 — xAI
Near Opus-level coding at a fraction of the cost. Has real-time social/news data baked in, which is genuinely useful if you care about live information.
## Kimi K3 — Moonshot AI
This is a flagship-level open-source model, which almost didn't exist until recently. It beats every model except Fable and GPT Sol, and you can technically download and run it yourself. Most consumer hardware won't handle it, but it signals just how small the open vs. closed source gap has become.
# Workhorse Mid-Tier Models: Your Daily Drivers

These models handle about 80% of what you actually do day to day. They're fast, capable, and reasonably priced. This is the category that has exploded most in the past few months, especially on the open-source side.
### Closed Source
- **Claude Sonnet 5 — Anthropic:** Best value in the Claude family. Go-to for casual chat, everyday coding, and tools like Cursor or Copilot.
- **GPT 5.6 Terra — OpenAI:** Last year's flagship at half the price. Similar capability to Sonnet and the default in the ChatGPT ecosystem.
- **Gemini 3.6 Flash — Google:** Google's mid-tier model that is quietly better than their flagship in some areas. Fast, cheap, great multimodality, and the default across most Google products.
- **Muse Spark 1.1 — Meta:** Meta's first closed-source model. Positioned as a generalist with computer use and multi-agent orchestration. Honestly, a bit of a disappointment given how strong the open-source competition is right now.
### Open Source (and worth paying attention to)
**Note:** Most of these open-source mid-tier models are too large to run on your own machine. The practical way to access them is through a hosted API like OpenRouter.
- **GLM 5.2 — Zhipu AI:** Technically the best open-weight model available right now. Actually beats Sonnet 5 at terminal programming. A 5.3 version is apparently coming soon.
- **MiniMax M3 — MiniMax:** A triple threat: near-flagship coding, 1 million token context window, and native vision multimodality. Great for powering multimodal agents cheaply.
- **Mimo V2.5 Pro — Xiaomi:** Yes, the phone company. Near-flagship quality at roughly one-eighth of Opus pricing. Climbing fast on OpenRouter, especially as an agent backbone.
- **Hunyuan Hy3 — Tencent:** Not the smartest, but excellent for reasoning and fact verification. It overthinks everything, which is actually useful when accuracy matters more than speed.
- **Qwen 3 — Alibaba:** The most diverse model family on the planet. Earns a mid-tier spot easily and is often the default pick when testing a new agent or framework.
- **Deepseek V4 Pro — Deepseek:** Personal favorite for coding tasks. Outperforms most mid-tier models on math, coding, and deep reasoning, and it's dirt cheap. Great budget fallback when Claude and OpenAI credits run dry.
- **Inkling — Thinking Machines:** America's open-source answer. Not the smartest, but the most customizable and least censored Western model. Native audio, vision, and text multimodality.
- **Mistral Large 3 — Mistral:** Europe's biggest open-source model. Has EU/GDPR compliance baked in, which matters a lot if you're operating under European regulations.
- **Llama — Meta:** The former king of open source. Now frozen in time as Meta pivots to closed source with Muse. Still usable, but no longer being actively developed.
# Light Models: Fast, Cheap, and Built for Volume

These are the models you reach for when you need to process things at scale, power sub-agents, or handle real-time tasks. Speed and cost matter more than raw intelligence here. Many of the open-source versions in this category can actually run on consumer hardware.
### Closed Source

- **Haiku 4.5 — Anthropic:** Four to five times faster than Sonnet. The go-to for high-volume processing and sub-agents if you're staying in the Anthropic ecosystem.
- **GPT 5.6 Luna — OpenAI:** Smallest, cheapest, fastest in the GPT 5.6 family. Use this for bulk processing and sub-agents in OpenAI-specific workflows.
- **Gemini 3.5 Flash Lite — Google:** Allegedly the fastest model ever measured. Built for real-time autocomplete, instant document triaging, and high-speed throughput.
- **Qwen 3 Flash — Alibaba:** Extremely fast, 1 million+ context window, extremely cheap.
- **Grok 4 Fast — xAI:** Small and fast with built-in live news routing via the xAI newsfeed. Great for real-time information tasks.
### Open Source (some runnable on consumer hardware)

- **Deepseek V4 Flash — Deepseek:** More than 55 times cheaper than GPT 5.5. Excellent at large-scale classification. Still needs 128GB+ hardware to self-host, so most people will use it via OpenRouter.
- **Mimo V2.5 — Xiaomi:** The number one most-used model on the entire OpenRouter marketplace. Near mid-tier performance for near-free cost. Extremely popular for high-volume code assistance.
- **Ling 3.0 Flash — Ant Group:** Growing fast in popularity. Worth keeping an eye on.
- **Mistral Small 4 — Mistral:** Reasoning, vision, and coding packed into a tiny, low-cost package.
### Runs on Your Actual Hardware

- **Qwen 3 small variants — Alibaba:** Ranges from 0.5B all the way up to 35B. The Qwen 3 32B is a favorite and runs Hermes Agent. Requires 32GB+ VRAM on the higher end.
- **Gemma 4 family — Google:** Best-in-class for phones. Runs on practically any laptop.
- **Phi-4 family — Microsoft:** Microsoft's take on small models. Exceptional at math and logic for its size. A personal favorite for running on a Raspberry Pi.

**Qwen models you can run on a consumer devices**

| MODEL            | COMFORTABLE RANGE | DEVICE                                     |
| ---------------- | :---------------: | ------------------------------------------ |
| Qwen3.5-0.8B     |       2-4GB       | Raspberry Pi 4, older phones               |
| Qwen3.5-2B       |       4-6GB       | Raspberry Pi 5 (8GB), mid-range phones     |
| Qwen3.5-4B       |      8-12GB       | Flagship phones, old laptops without a GPU |
| Qwen3.5-9B       |      12-16GB      | Everyday laptops, budget GPUs              |
| Qwen3.6-35B      |      24-32GB      | Gaming PC (3090/4090/5090)                 |
| Qwen3.6-27B      |      32-48GB      | Workstation, 2x 3090, 48GB Mac             |
| Qwen3-Coder-Next |      48-64GB      | Mac Studio, dedicated inference box        |
# Specialized Models

Most of these models have already appeared in the categories above. This is a quick reference for models that excel in specific domains.
## Coding Specialists

- **Claude Fable 5 - Anthropic:** The no-expense-spared ceiling migrations, last-resort cases
- **Claude Opus 5 - Anthropic**: The current frontier default hardest problems, multi-file refactors
- **GPT-5.6 Sol - OpenAI**: The terminal operator CLI automation, DevOps agents
- **Claude Sonnet 5 - Anthropic**: The workhorse daily driver in Claude Code/Cursor
- **Grok 4.5 - X.ai**: The token miser high-volume bug fixing
- **GLM-5.2 (open)**: Open weights, server-class self-hosted engineering agents
- **DeepSeek V4-Pro (open)**: The algorithms specialist math-heavy, contest-style problems
- **Kimi K2.7 Code — Moonshot AI**: Cheap daily coding and swarms.
- **Qwen 3 Coder — Alibaba**: Great coding agent that fits on local hardware for private coding.
- **Mistral Devstral — Mistral**: Europe's agent coder with EU-compliant automations.
- **LongCat 2.0 — Meituan**: Developed by a Chinese food delivery company as a side project, and it's actually really good.
## Image Generation

- **GPT Image 2 — OpenAI:** The overall #1. Strongest all-rounder, especially for infographics and text-heavy designs.
- **Nano Banana Pro — Google:** The photorealism champion. Best for product photography, realistic scenes, and precise single-element edits.
- **Nano Banana 2 — Google:** The cheap generalist. Good for high-volume, everyday image-generation work where cost and speed matter more than absolute quality.
- **Midjourney V8.2 — Midjourney:** The artist. Best for concept art, moodboards, cinematic imagery, and highly stylized campaigns.
- **Seedream 5.0 Pro:** The layered-design specialist. Strong for commercial/product photography and designs involving multilingual text.
- **Ideogram:** The typography specialist. Particularly good for posters, signage, logos, and images where the words actually need to be correct.
- **Recraft V4.1:** The vector specialist. Produces real vectors rather than traced raster images, making it useful for brand assets, scalable logos, and icons.
- **Adobe Firefly — Adobe:** The corporate-safe choice. Designed for commercial workflows where provenance, licensing, and copyright risk matter.
- **Muse Image — Meta:** The newcomer. Brand new and still relatively unproven, but worth watching.
- **FLUX 2 — Black Forest Labs:** The open-weight quality leader. Best suited to local generation, customization, and bespoke image-generation pipelines.
- **Qwen-Image:** The open-model text specialist. Particularly strong at rendering text—especially Chinese—and useful for bilingual design work.
- **Stable Diffusion 3.5 / SDXL:** The moddable classic. Huge customization ecosystem, strong for custom styles, and capable of running locally on relatively modest GPUs.
- **Z-Image Turbo:** The speed specialist. Built for fast, high-throughput generation and interactive image applications.
## Video Generation

- **Veo 3.1 — Google:** The all-rounder. Strong across most video-generation tasks, particularly premium cinematic clips with integrated speech and audio.
- **Kling 3.0 Omni — Kuaishou:** The arena leader. Especially strong for character-driven scenes, dialogue, motion, and cinematic storytelling.
- **Seedance 2.0 — ByteDance:** The reference stacker. Excels at maintaining consistent characters, objects, and visual identity across multiple shots.
- **HappyHorse 1.0:** The dark horse. Particularly capable with multilingual dialogue scenes and character interaction.
- **Runway Gen-4.5 — Runway:** The editor’s suite. Best suited to professional creative workflows spanning marketing, advertising, editing, and post-production.
- **Luma Ray3 — Luma AI:** The colorist. Strong visual fidelity and color handling, particularly for polished image-to-video workflows.
- **Wan 2.7 — Alibaba:** The open-source champion. A strong option for running and customizing video generation locally through tools such as ComfyUI.
- **HunyuanVideo 1.5 — Tencent:** The open model that fits your GPU. Useful for research, experimentation, local generation, and custom fine-tuning without enormous hardware requirements.
- **LTX-2.3 — Lightricks:** The lawyer-approved open model. Positioned around commercially usable open video generation where licensing and production workflows matter.
- **Sora 2 — OpenAI:** **Dead.** Once a flagship name in generative video, but was discontinued.
## Music, Voice, and Audio

- **Suno v5.5 — Suno:** The hit machine. Best for generating complete songs from a prompt, including vocals, instrumentation, and production-ready background music.
- **Udio:** The audiophile. Focused on maximum musical fidelity and polished production quality, particularly when sound quality matters more than workflow flexibility. Considered legally cleaner, with fully licensed UMG and Warner catalog.
- **ElevenLabs v3 — ElevenLabs:** The voice. Excellent for expressive voiceovers, narration, dubbing, and highly natural synthetic speech across 70 languages. ElevenLabs Music also produces licensed, monetizable music.
- **ElevenLabs Flash v2.5 — ElevenLabs:** The realtime specialist. Optimized for low-latency voice agents, conversational systems, and bulk text-to-speech.
- **Runway Gen-4.5 — Runway:** The soundtrack tool. Useful for creating YouTube, advertising, and commercial backing tracks within a broader creative-production workflow.
- **Lyria RealTime — Google DeepMind:** The programmable composer. Designed for interactive and streaming music generation where music can respond dynamically in real time.
- **GPT-Live — OpenAI:** The conversational voice. Powers natural spoken interaction with ChatGPT, although the diagram notes that it is not yet available as a standalone API.
- **Voxtral TTS — Mistral AI:** Europe’s open voice option. Geared toward private and on-premise text-to-speech deployments where control over infrastructure and data matters.
- **Fish Audio S2 — Fish Audio:** The open voice-cloning specialist. Strong for self-hosted text-to-speech and fast custom voice cloning.
## Customization and Fine-Tuning

- **Inkling — Thinking Machines:** Built for customization, calibrated, and minimally censored.
- **Nemotron 3 family — Nvidia:** Strong choice for fine-tuning and enterprise retrieval.
- **Qwen family — Alibaba:** 201 languages, every model size. The most popular base for self-hosting and fine-tuning right now.
## Enterprise and Retrieval

- **Nemotron 3 family — Nvidia:** Strong choice for fine-tuning and enterprise retrieval.
- **Cohere — Cohere:** Nicknamed the RAG plumbing specialist. Built for enterprise search and retrieval pipelines.
- **Amazon Nova 2 + Nova Act — Amazon:** The all-AWS stack for teams already deep in the Amazon ecosystem.
- **Perplexity Sonar — Perplexity:** Specifically trained for search functionality.
## Local and Self-Hosted

- **Qwen 3.6 35B — Alibaba:** Personal top pick for local models, runs on Mac Studio.
- **Nous Hermes 4 family — Nous Research:** Optimized for running Hermes agent frameworks locally.
- **GPT OSS family — OpenAI:** OpenAI's open-source models in various sizes, for those who prefer the OpenAI style.
- **Step 3.7 Flash — StepFun:** Specifically great when it comes to apple products and other unified memory machines.
# Key Takeaways

- **Flagship models:** Only reach for Claude Fable 5 or GPT Soul when a task genuinely demands the highest capability. For most things, they're overkill.
- **Mid-tier models:** Chinese open-source models like Deepseek V4 Pro, GLM 5.2, and MiniMax M3 now compete directly with Claude Sonnet and GPT Terra at a fraction of the cost. They deserve a real look.
- **Light models:** For bulk processing, automations, and sub-agents, prioritize speed and cost. Mimo V2.5 being the most-used model on OpenRouter tells you everything you need to know.
- **Specialized models:** For image generation, GPT Image 2 is the current leader. For video, Veo 3.1. For voice, ElevenLabs. For fine-tuning, Qwen is the default base model right now.
- **Local models:** The on-ramp is genuinely easy. Start with LMArena in a browser and work up the ladder as your curiosity grows. The Qwen and Gemma families are the best starting points for consumer hardware.
- **Biggest shift overall:** The gap between open-source and closed-source models is now smaller than it has ever been, and that changes the calculus for how you build and what you pay.
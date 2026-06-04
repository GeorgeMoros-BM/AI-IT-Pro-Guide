# Quick Reference for IT Pros

> Purpose: Use Gemini 3.1 Pro effectively for real IT work—multimodal troubleshooting, entire codebase analysis, machine learning R&D, and agentic workflows—without wasting compute on simple tasks. Based on the Gemini 3.1 Pro Model Card (Feb 2026).

# 1. Core Principle

Do **not** treat Gemini 3.1 Pro as just a text-based chatbot. 

It is natively multimodal across text, audio, images, and video, with a massive **1M token context window** and an expanded **64K token output limit**. Use the right interface and mode for the job:

|Task Type|Recommended Mode|Why|
|---|--:|---|
|Standard Q&A, drafting, simple analysis|Gemini App / Enterprise (Standard)|Fast, robust, keeps unjustified refusals low|
|Complex coding, algorithm development|Deep Think Mode|Substantially higher scores on ML R&D and logic tasks|
|Analyzing server rack videos, audio logs|Native Multimodal Input|Processes raw audio/video directly without transcription loss|
|Large-scale repo audits, long-horizon tasks|Vertex AI / Google Antigravity|Supports long context (1M) and agentic tool integration|
|Creating large scripts, massive config files|Gemini API (64K Output)|Takes advantage of the massive 64K output token limit|

# 2. First Check: Confirm the Model & Mode

When you open your Google AI environment, do not assume your settings are optimal for the current task. 

## Checklist
- Confirm **Gemini 3.1 Pro** is selected (verify against Gemini 3.0 Pro).
- Check if **Deep Think mode** is enabled. It boosts reasoning for ML and coding, but is computationally expensive.
- Verify your token limits. You have up to **1M input tokens** and **64K output tokens**.
- Check your deployment platform: Gemini App, Google Cloud / Vertex AI, Google AI Studio, Gemini API, Google Antigravity, Gemini Enterprise, or NotebookLM.

## IT Pro Rule
> Only enable **Deep Think mode** when you actually need it. The model card explicitly notes that for certain tasks (like Cyber operations), accounting for inference costs, the model *with* Deep Think mode can actually perform considerably worse than the standard mode.

# 3. Gemini Enterprise vs Vertex AI / Antigravity

## Use Gemini App / Enterprise When You Need
- Explanations and conceptual brainstorming
- Drafting documentation, policy language, or emails
- Summarizing meeting audio or video recordings
- Interacting via a standard conversational interface (NotebookLM for document grounding)

## Use Vertex AI / Google Antigravity (API) When You Need
- Agentic terminal execution (scores 68.5% on Terminal-Bench 2.0)
- Multi-step codebase engineering (scores 80.6% on SWE-bench Verified)
- Live web navigation (scores 85.9% on BrowseComp)
- Automated ML R&D pipeline optimization

|Environment|Best For|Limitation|
|---|---|---|
|Gemini Enterprise|Conversation, multimodal analysis, document writing|Cannot execute code agentically in your local environment|
|Vertex AI / Antigravity|Producing working artifacts, agentic workflows, custom tool use|Requires API cost management and strict permission scoping|

# 4. Recommended Gemini 3.1 Pro Setup

## Default Settings

|Setting|Recommended Default|
|---|---|
|Model|Gemini 3.1 Pro|
|Reasoning Mode|Standard (Default)|
|Deep Think Mode|Off for standard dev; On for advanced algorithms & ML R&D|
|Context Window|1M tokens (upload entire repos)|

## Mode Guidance

|Mode|Use Case|
|---|---|
|Standard|Quick edits, log parsing, video/audio analysis, daily coding|
|Deep Think Mode|Complex debugging, optimizing ML scripts, ARC-AGI problem solving|

# 5. Deep Think Mode: When to Use It

Deep Think mode allows Gemini 3.1 Pro to spend extra compute on reasoning before outputting an answer. It dramatically increases situational awareness and complex problem-solving capabilities.

## Use Deep Think Mode For
- **Machine Learning R&D:** Deep Think achieves a 1.27 human-normalized score on RE-Bench (e.g., optimizing an LLM Foundry script to run in 47 seconds instead of the 94-second human baseline).
- **Algorithmic Development:** Solving hard, abstract logic puzzles (scores 77.1% on ARC-AGI-2).
- **Complex Situational Awareness:** The model is exceptionally strong at tracking max tokens, context size modifications, and oversight frequency.

## Do Not Use Deep Think Mode For
- Routine bash scripts or simple Python edits.
- Standard log parsing.
- **Cost-Sensitive Cyber/Security Tasks:** The model card notes that at high levels of inference, Deep Think does not suggest higher capability in cyber domains compared to standard mode, making it cost-inefficient.

# 6. Example Prompts for IT Pros

## Multimodal Troubleshooting (Gemini Enterprise - Standard)
```text
I am uploading a 2-minute video of a boot loop occurring on our bare-metal Linux server, along with the audio of the error beeps. 
Analyze the screen output and audio beep codes to diagnose the hardware/OS failure. Provide a step-by-step remediation plan.
```

## Advanced ML R&D / Optimization (Vertex AI - Deep Think Mode)
*(Note: Gemini 3.1 Pro is exceptional at ML script optimization).*
```text
Attached is our current fine-tuning script for our internal LLM infrastructure. 
It currently takes 300 seconds to run. Analyze the pipeline, identify bottlenecks in data loading and GPU memory allocation, and refactor the script to run in under 50 seconds.
```

## Large-Scale Generation (Gemini API - 64K Output)
```text
I have attached a zip file containing the documentation for our entire legacy internal API. 
Read the documentation and generate a complete, comprehensive OpenAPI v3 specification (JSON) covering every endpoint, schema, and error code. Do not truncate the output.
```

## Codebase Agentic Work (Google Antigravity - Standard)
```text
Navigate to the attached repository. Find all instances where we are utilizing the deprecated database driver. 
Update all queries to use the new ORM structure, run the test suite, and ensure no regressions occur.
```

# 7. Permission Safety Checklist

Gemini 3.1 Pro is highly capable of agentic performance. Before hooking it up to your terminal or cloud environments via API/Antigravity, check:

|Question|Why It Matters|
|---|---|
|Is it reading files, or modifying/deleting them?|Gemini 3.1 Pro has high situational awareness; ensure it is explicitly scoped to avoid unintended deletions.|
|Is it running with Deep Think?|Deep Think can generate complex instrumental reasoning. Blast radius is high if allowed to execute unchecked.|
|Are you evaluating cyber risks?|While it didn't cross the Critical Capability Level (CCL), its cyber capabilities have increased. Sandbox all security testing.|

## IT Pro Rule
> Gemini 3.1 Pro's safety evaluations show a +0.10% improvement in non-egregious safety and low unjustified refusals (-0.08%). This means it will happily assist with edgy/borderline tasks that don't explicitly violate safety rules. Ensure your own IAM permissions prevent it from running amok in your cloud console.

# 8. Cost and Rate-Limit Guidance

Gemini 3.1 Pro's expanded context and reasoning capabilities require careful resource management.

- **1 Million Input Tokens:** You can upload massive amounts of context (video, audio, repos), but be aware of the input costs associated with maxing out the window.
- **64K Output Tokens:** A massive upgrade from previous models. You can now request entire software modules or massive JSON files in a single prompt, but ensure you actually *need* that much text before prompting.
- **Deep Think Premium:** Toggle this off unless you are explicitly doing algorithmic development or Machine Learning R&D to save on inference costs.

# 9. Best-Practice Operating Pattern

## Daily Use

|Need|Tool & Setting|
|---|---|
|Video/Audio diagnostics, log analysis|Gemini Enterprise (Standard)|
|Reviewing multiple 100-page PDFs|NotebookLM (Gemini 3.1 Pro backend)|
|Simple local script / cloud config|Vertex AI (Standard)|
|Optimizing ML models / logic parsing|Vertex AI (Deep Think Mode)|
|Generating massive config files|Gemini API (Leveraging 64K Output)|

## For Team Adoption
1. **Native Multimodal is the default:** Stop transcribing videos or taking screenshots of errors. Upload the raw video, image, or audio directly to Gemini 3.1 Pro.
2. **Deep Think = algorithmic/ML tasks only.**
3. **Use 64K output for complete artifacts:** Stop asking models to "continue." Prompt Gemini to output the entire script or configuration file at once.

# 10. Common Mistakes

|Mistake|Better Practice|
|---|---|
|Using Deep Think for everything|Match effort to task complexity to save inference costs (especially for Cyber/Security tasks).|
|Treating it as text-only|Leverage its native understanding of audio and video for IT troubleshooting.|
|Chunking outputs artificially|Take advantage of the 64K output limit to get complete, unbroken code files.|
|Manually parsing small logs|Upload the entire codebase or log directory using the 1M token context window.|

# 11. Recommended Internal Policy Language

```text
Use Gemini Enterprise for multimodal analysis, including video, audio, and large document processing. Use Vertex AI or the Gemini API for agentic execution and script generation.

When using Gemini 3.1 Pro, default to the Standard mode for regular coding and IT operations. Deep Think Mode should be reserved exclusively for Machine Learning R&D, advanced algorithmic development, and complex reasoning tasks due to high inference costs.

Take full advantage of the 1M input token window and 64K output token limit to provide complete context and receive unbroken deliverables, but ensure API limits are monitored. Unbound execution of agentic workflows against production environments is strictly prohibited.
```

# 12. One-Page Summary

## Use Gemini Enterprise (Standard) When:
- You are troubleshooting using videos of server reboots, audio error codes, or infrastructure diagrams.
- You are writing policy, architecture docs, or executive summaries.
- You need fast, reliable text processing.

## Use Vertex AI / Gemini API (Standard) When:
- You need a local bash script or Python utility.
- You want an agent to navigate your codebase or browse the web (BrowseComp 85.9%).
- You need a massive output file (up to 64K tokens) generated in one shot.

## Use Deep Think Mode When:
- You are doing Machine Learning R&D (e.g., optimizing fine-tuning scripts).
- You are solving complex, multi-step algorithmic puzzles.
- You need advanced situational awareness for instrumental reasoning tasks.

## Default Rule
> Use **Standard Mode** as the baseline for real IT and engineering work. Upload **raw multimodal data** (video/audio) instead of text descriptions whenever possible. Elevate to **Deep Think Mode** only when you need heavy compute applied to ML optimization or abstract algorithms.

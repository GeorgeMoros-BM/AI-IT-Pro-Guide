How to set up Claude the right way (so you actually stop prompting).

As you gain knowledge, AI has different levels.

- **Level 1**: You’re using the free ChatGPT.
- **Level 2**: You’re using the paid ChatGPT + Thinking.
- **Level 3**: You’re using the paid **[Claude](https://ruben.substack.com/p/claude)** chat + Opus + Thinking.
- **Level 4**: You’re using the Premium plan of Claude + **[Cowork](https://ruben.substack.com/p/claude-cowork)** + Opus.
- **Level 5**: You have your entire team using **[Claude Teams](https://ruben.substack.com/p/claude-for-teams)** with Projects.

It is time for us to level up with **Skills**.
![[Pasted image 20260624032228.png]]
1. Skills live inside Claude or any other AI.
2. It’s like a very long context + instructions, living inside the AI.
3. And you just /command it (like **/brief** or **/linkedin** or **/contract-x**).
4. Skills can be shared with your team and downloaded from the web in libraries.

Two things before we start:
1. Save this guide and spend 30 minutes this weekend to master Skills.
2. Send it to anyone who keeps re-explaining the same task to Claude.
## **1 - What’s different with Skills?**

You heard about how important _“context”_ is for AI like ChatGPT or Claude.

> Context is _“how much the AI knows about you/ the task before doing it”_.

And you know context is much more important than the prompt (_but technically, context is a prompt, like it’s text too_). You have many ways to share context with your AI. 1) prompt 2) files 3) skills.

Let’s say you want to write a Linkedin post. You can either:

**1 - Write a very long prompt that has the context** (who you are, the task that must be done, the precise steps to get there).
![[Pasted image 20260624032540.png]]

**2 - Or write a very long text file**, that you then upload to your favorite AI. 
![[Pasted image 20260624032627.png]]

**3 -** Both the very long prompt (instructions) and the very long text file (also instructions, but with more context about YOU) **can be uploaded inside a Project.**
![[Pasted image 20260624032725.png]]

4 - **And then you have Skills**. It’s all of this, but as a slash command.

Context files need you to say _“read my file first”_ every time.

Projects need you to open the right Project.

But Skills fire _automatically_. Claude recognizes the task from what you type and activates the right Skill on its own. You don’t invoke a Skill. _It invokes itself._

> **I will show you how it works so you understand. And in the next section of this guide, I will teach you exactly how I did it (so you can do it too).**

![[Pasted image 20260624032854.png]]
This is where the Skills “live” inside Claude.

![[Pasted image 20260624032959.png]]
I just type **“/linkedin”** anywhere on Claude, and it knows. Plus, even your team can use your skills (if you want them to).

## **2 - How to build your first Skill.**

You really have two simple options to build a skill.
#### Option 1: Claude has a Skill Creator.

Sounds obvious, but yes, Claude made a Skill creator to create skills for Claude.

_Are you following?_

You describe the task, it interviews you, it generates everything. Same energy as the **[100-question taste interview](https://ruben.substack.com/p/i-am-just-a-text-file)**, but this time you’re capturing a _process_, not a _personality_.

Here’s the full walkthrough. I’m building a real Skill: the LinkedIn Post Skill:

**Step 1: Open Cowork. Ask for the skill-creator.**

Open Claude Cowork. Select your folder. Make sure you’re on Opus 4.8 + Extended thinking _(like always)_. Type this:

```
Use the skill-creator to help me build a skill for writing LinkedIn posts.
```

**Step 2: Answer the interview.**

The skill-creator starts asking questions. Answer like you’d answer the taste interview. Be specific. Be honest. You can either select Claude’s premade answers or just answer yourself. Obviously, it’s probably better you answer yourself.

![[Pasted image 20260624033232.png]]
That’s how Cowork will generate questions for you. Just answer or type “something else” if you want your own answers.

**Step 3: It generates everything.**

The skill-creator produces:
1. A folder with the right name _(lowercase, hyphens, no spaces)_.
2. A SKILL.md file with the trigger (/command) description + your instructions.

![[Pasted image 20260624033358.png]]

**Step 4: Claude even runs an evaluation for you to validate it.**

It’s the most important step that most people will skip because they are either lazy or “lacking time”. Claude creates the evaluation of your new skill:

![[Pasted image 20260624033455.png]]
See the “View the eval results”. That’s how you test your skills before downloading it for good. **Take the time, it’s the most important step.**

**Step 5: Save and install.**

Once you’re happy with the skills, prompt it this:

![[Pasted image 20260624033605.png]]

Save the Skill folder. Then install it: Go to **Settings → Capabilities → Skills → Upload**.

![[Pasted image 20260624033707.png]]
Left menu > Customize > Skills > + > Upload a skill.

## **3 - Access Claude’s team skills.**

You don’t _have to_ build everything from scratch. Claude’s team makes pre-built Skills. To access it, do this:
![[Pasted image 20260624034449.png]]

Step 1: You must be on the desktop app. Go to Customize > Personal plugins > Browse plugins inside the +

![[Pasted image 20260624034602.png]]

You can browse plugins. A plugin is just a bunch of skills for a task. You can click on anyone of them and download it

![[Pasted image 20260624034703.png]]
You now have all of these new skills to test, from this one plugin.

## **Where Skills fall short** _(I’ll be honest)_.

**The description is everything.** If you write a bad description, your Skill never fires. It just… doesn’t activate. You’ll wonder if it’s broken. It’s not. The description is just too vague. Use the debugging trick from Section 5.

**Skills can hijack conversations.** If your description is too broad, your Skill fires when you don’t want it. You ask Claude a simple question and your LinkedIn Post Skill activates. The fix: add negative boundaries. _“Do NOT use for blog articles, newsletters, emails.”_

**It still needs editing.** A Skill doesn’t produce perfection. It produces a consistent starting point, 80% there, every time, instead of starting from zero. You still review. You still push back. But the heavy lifting is done.

**Usage still burns fast.** Skills don’t magically eliminate token usage _(beyond the efficiency gains from Trick #5)_. Cowork still eats your plan. If you’re using Cowork daily, consider the Max plan ($100/month). I’m being direct about this because I don’t want you surprised.
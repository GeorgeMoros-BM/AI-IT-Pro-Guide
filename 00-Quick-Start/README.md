# The IT Guru's Guide to Enterprise AI

Welcome to the living knowledge base for enterprise AI implementation. This repository contains practical, role-based guides, architectural patterns, and real-world lessons learned for deploying AI securely and cost-effectively.

## How to Read This Guide

**If you are a reader:**
Please visit our published site at: `[Insert GitHub Pages URL here]`

**If you are a contributor:**
All content is written in Markdown. You can browse the files directly in this repository or clone it locally.

## How to Contribute

We rely on the frontline experience of our IT teams to keep this guide accurate and useful. 

### The Workflow
We use a 3-phase pipeline for this knowledge base:
1. **Authoring:** We use [Obsidian](https://obsidian.md/) locally for writing and managing links.
2. **Version Control:** We push changes to this GitHub repository.
3. **Publishing:** GitHub Pages automatically uses Jekyll to render the Markdown into our internal website.

### Making an Update
1. Clone this repository to your local machine.
2. Open the repository folder as a "Vault" in Obsidian.
3. To add a new topic, use the templates located in the `_templates/` folder (or copy an existing file).
4. Commit and push your changes to GitHub.
5. GitHub Actions will automatically rebuild the site in ~60 seconds.

### File Naming & Linking
* Please use `[[Title of Page]]` to link between documents. 
* Add tags and metadata to the frontmatter (the `---` section at the top of the file) so it categorizes correctly on the live site.

### What to Contribute
* **Found a typo or outdated pricing?** Fix it directly and submit a PR!
* **Had a project go wrong?** Write a `Lesson-Learned.md` so others don't make the same mistake.
* **Figured out a cool prompt or cost-saving trick?** Add it as a `Tip-Trick.md`.
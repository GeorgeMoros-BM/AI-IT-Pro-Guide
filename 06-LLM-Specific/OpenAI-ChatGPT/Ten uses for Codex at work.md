These use cases show how to use Codex to do real work: create deliverables, pull together context from multiple tools, take action on real inputs, and move tasks forward faster. Start with the generic prompt if you want something you can use right away, then use the customization suggestions and example to make it your own.
## 1. Create a daily chief of staff

#### Why people use this
You start the day by bouncing between your calendar, messages, email, and notes, trying to figure out what matters most. Codex can pull that context together, keep watch for changes, and turn it into one clear brief so you spend less time triaging and more time acting on priorities.
#### Prompt to try
`Set up a weekday work brief that starts in the morning and keeps checking throughout the day. At the start of the day, review today's calendar, unread direct messages and mentions from the last 24 hours, unread email from the last 24 hours, my running list of open follow-ups, and any recent context that affects today's priorities. Create a short brief with priorities, meeting prep, messages that need replies, decisions I owe, and useful FYIs. Then check back every hour until the end of the workday for new replies, meeting changes, or follow-ups that need attention. Only update me when something changes or needs action. Draft replies only when the next step is clear, and flag anything you cannot access or cannot confirm.`
#### How to customize it

Swap in your real tools and follow-up sources. Set when the brief should start and how often Codex should check back. Tell it what to include in the brief and when to draft replies.

**Suggested plugins**: Google Calendar, Gmail, Slack, Google Drive  
**Suggested skills**: Google Calendar Daily Brief, Gmail Inbox Triage, Slack Notification Triage, Google Calendar Meeting Prep

**Example:**
_Set up a weekday heartbeat called “Morning Work Brief” that starts at 8:30 AM local time and keeps checking throughout the workday. At 8:30, use today’s calendar, unread Slack DMs and mentions from the previous 24 hours, unread Gmail from the previous 24 hours, my Google Doc “Open Follow-Ups,” and any recent context that affects today. Create a brief with priorities, meeting prep, messages needing reply, decisions I owe, and FYIs. Then check every hour until 5 PM for new replies, meeting changes, or follow-ups I need to handle. Only update me when something changes or needs my attention. Draft replies only when the next step is clear. Flag missing access or uncertainty._

## 2. Weekly summary

#### Why people use this
You end the week trying to remember what you finished, what changed, and what your manager actually needs to know. Codex can pull together the week across your calendar, docs, messages, and trackers so writing the update feels less like a memory exercise and more like a quick review.
#### Prompt to try
`I'm writing my weekly update. Use my calendar, documents I edited, messages I sent in work channels, my main tracker or planning doc, and anything else that looks relevant to this week. Write a manager-ready summary that covers work completed, decisions made, important changes, blockers, follow-ups, and next week's priorities. Include source links where possible, and separate confirmed facts from anything that is an inference.`
#### How to customize it

**Suggested plugins**: Slack, Gmail, Google Calendar, Google Drive, Notion  
**Suggested skills**: Slack Daily Digest, Slack Channel Summarization

**Example:**
_I’m writing my Friday update for the week of April 20. Use my calendar, Google Docs I edited, Slack messages I sent in #launch-planning and #sales-enablement, “Q2 Workstream Tracker,” and anything else that looks relevant to my week. Write a manager-ready summary with work finished, decisions, important changes, blockers, follow-ups, and next week's priorities. Include source links. Separate confirmed facts from inferences._
## 3. Draft slide decks

#### Why people use this
You may already have the content for the deck, but turning scattered notes, metrics, and source docs into something presentation-ready takes too long. Codex can work across those materials, draft editable slides, and help catch layout issues so you get to a usable deck much faster.
#### Prompt to try
`I need a draft slide deck for an upcoming customer onboarding review. Use the main project brief, any summaries of customer pain points, the latest onboarding metrics, any available slide template, and related context. Create a seven-slide PowerPoint with an executive summary, the core customer problem, the main issues, an example workflow, adoption or usage signals, an improvement plan, and open decisions. Keep the text editable, add speaker notes, and render the slides so you can fix overflow, crowded layouts, and unreadable charts. Do not invent metrics, and flag any missing data.`
#### How to customize it
Swap in the real brief, metrics, and template. Set the audience, slide count, and sections. Tell Codex what not to invent and what layout issues to fix.

**Suggested plugins**: Google Drive, Notion, Figma, Canva  
**Suggested skills**: PowerPoint, Google Slides, Google Docs

**Example:**
_I need a draft deck for the April 23 customer onboarding review. Use “Customer Onboarding Brief,” “Top Customer Onboarding Issues,” “April Onboarding Metrics,” the attached “Simple Company Template.pptx,” and related onboarding context. Create a 7-slide PowerPoint with an exec summary, customer problem, top issues, example workflow, adoption signals, improvement plan, and open decisions. Keep text editable. Add speaker notes. Render the slides and fix overflow, crowded layouts, or unreadable charts. Do not invent metrics. Flag missing data._

## 4. Research to decision memo

#### Why people use this
Important decisions are usually spread across old recaps, planning docs, budget inputs, and whatever you can learn from current research. Codex can gather those pieces in one workflow, separate internal evidence from external research, and turn the result into a memo that is easier to act on.
#### Prompt to try
`I'm deciding whether our team should sponsor or invest in a major industry event next year. Use prior event recaps, the event ROI model, the target account or audience list, budget guardrails, and related planning notes. Search the web for current event dates, audience details, sponsorship options, pricing information if available, and competitor presence. Write a one-page decision memo with a recommendation, supporting evidence, tradeoffs, cost, risks, missing information, and source links. Make it clear what came from internal files and what came from web research.`
#### How to customize it
Replace the decision and supporting files. Tell Codex what outside research to gather. Specify how to separate internal evidence, web research, and open questions.

**Suggested plugins**: Google Drive, Notion, Browser, SharePoint, Box  
**Suggested skills**: Browser, Google Docs

**Example:**
_I’m deciding whether Acme should sponsor SaaStr Annual 2026. Use “2025 SaaStr Recap,” “Event ROI Model - Q4,” “FY26 Target Account List,” “Events Budget Guardrails,” and related event planning notes. Search the web for current SaaStr dates, audience, sponsorship options, and competitor presence. Write a one-page decision memo with a recommendation, evidence, tradeoffs, cost, risks, missing information, and source links. Make clear what came from our files and what came from web research._

## 5. File cleanup and reformatting

#### Why people use this
Messy exports from different sources are tedious to clean and easy to mishandle. Codex can apply cleanup rules consistently, combine files into a usable workbook, and preserve a review path for anything that should not be guessed.
#### Basic prompt
`I attached several source files that need to be cleaned and combined into one workbook. Standardize the attendee data so fields like name, company, title, country, segment, source, and attendance status are consistent. Remove duplicates using email as the primary key. Create an upload-ready CSV using the field order from the mapping notes, and put missing or conflicting rows in a "Needs Review" tab. Do not guess missing emails or other critical identifiers. Add a short change log explaining what you cleaned or merged.`
#### How to customize this
**Suggested plugins**: Google Drive, SharePoint, Box, Egnyte  
**Suggested skills**: Excel, Google Sheets, Google Sheets Formula Builder

**Example:**
_I attached “Q2 Webinar Attendee Export.csv,” “Manual Registration Edits.xlsx,” “Partner Invite List.xlsx,” “Field Mapping Notes.docx,” and any related attached file. Clean the attendee data into one workbook. Standardize name, company, title, country, segment, source, and attendance status. Remove duplicates by email. Create an upload-ready CSV using the column order in “Field Mapping Notes.docx.” Put missing or conflicting rows in a “Needs Review” tab. Do not guess missing emails. Add a short change log._

## 6. Spreadsheet consolidation

#### Why people use this
A set of spreadsheet exports may look close enough to join, but the cleanup, mismatch review, and reporting work adds up fast. Codex can consolidate the files, surface what did not join cleanly, and turn the result into a workbook you can actually use and refresh.
#### Basic prompt
`I attached multiple spreadsheet exports that need to be consolidated into one updateable workbook. Join the data on account ID, clean duplicate accounts, calculate pipeline by region and segment, compare current pipeline to target, and create a dashboard with charts and plain-English insights. Include assumptions, refresh instructions, and a review section for any mismatched account IDs or records that could not be joined cleanly.`
#### How to customize this
**Suggested plugins**: Google Drive, SharePoint, Coupler.io, Omni Analytics, MotherDuck  
**Suggested skills**: Excel, Google Sheets, Google Sheets Formula Builder, Google Sheets Chart Builder

**Example:**
_I attached “Q1 Pipeline by Region.csv,” “Q2 Pipeline by Region.csv,” “Account Segments.xlsx,” “FY26 Sales Targets.xlsx,” and any related attached file. Consolidate them into an updatable workbook. Join on account ID, clean duplicate accounts, calculate pipeline by region and segment, compare Q2 pipeline to target, and create a dashboard with charts and plain-English insights. Add assumptions, refresh instructions, and mismatched account IDs to review._

## 7. Book of business prioritization

#### Why people use this
The signals that tell you which accounts matter right now are often spread across CRM exports, calls, email threads, dashboards, and plans. Codex can pull those signals together, rank the accounts that need attention, and show you where to focus first.
#### Basic prompt
`I'm planning my week for my top renewal accounts. Use the latest CRM account export, recent call transcripts, open customer email threads, the usage dashboard, account plans, and anything else that explains renewal risk or upside. Create a priority brief ranking the ten accounts I should focus on first. For each account, include why it matters now, the main risk or upside, the recommended next action, source links, and any stale or missing context. Draft customer follow-up notes only where the next step is clear, and mark anything that needs account executive or manager review.`
#### How to customize this
**Suggested plugins**: Common Room, Demandbase, HubSpot, Pipedrive, Streak, Attio, Gong, Gmail, Slack  
**Suggested skills**: Gmail Inbox Triage, Slack Reply Drafting, Google Docs

**Example:**
_I’m an account manager planning my week for my top renewal accounts. Use Salesforce export “April Renewal Account Export,” Gong transcripts from the last 30 days, open buyer email threads, “Renewal Usage Dashboard,” “Q2 Renewal Plans,” and anything else that explains renewal risk or upside. Create a book-of-business priority brief ranking the 10 accounts I should focus on. For each account, include why now, risk or upside, next action, source links, and stale or missing context. Draft customer follow-up notes only where the next step is clear. Mark anything needing AE review._

## 8. Month-end financial review

#### Why people use this
Month-end review means pulling numbers and context from workbooks, dashboards, prior decks, and close-period discussion without missing anything important. Codex can work across those materials, update the review, and flag assumptions or missing support before the meeting.
#### Basic prompt
`I'm preparing this month's month-end review. Use the close workbook, the revenue or performance dashboard, the close support folder, the prior month deck, messages from the finance close period, and any related close notes. Refresh the month-end review slides with this month's actuals, key movements, speaker notes, and executive prep questions. Cite a workbook tab or dashboard for every number, and list assumptions, missing support, stale labels, and anything a finance lead should review before the meeting.`
#### How to customize it
Swap in the real close materials and prior deck. Tell Codex what the review needs to cover and where every number should be traced back. Ask it to flag assumptions, stale labels, and missing support.

**Suggested plugins**: Brex, Cube, Google Drive, SharePoint, Omni Analytics, MotherDuck  
**Suggested skills**: Excel, Google Sheets, Google Sheets Chart Builder, PowerPoint

**Example:**
_I’m preparing the April month-end review. Use “April Close Workbook,” “April Revenue Dashboard,” “April Close Support Folder,” “March Close Deck,” #finance-close from April 20 through April 24, and related April close notes. Refresh the month-end review slides with April actuals, key movements, speaker notes, and CFO prep questions. Cite a workbook tab or dashboard for every number. List assumptions, missing support, stale labels, and items a finance lead should review._

## 9. Launch campaign kit

#### Why people use this
Launch work is rarely neatly organized in one place, even though the team still needs a coordinated set of assets. Codex can pull together plans, notes, pages, and discussion history to draft the core materials faster and show where more review is needed.
#### Basic prompt
`I'm working on a product launch and need a first-draft campaign kit. Use the launch plan, product notes, the launch tracker, any agency or creative brief outline, staging page links, team launch discussions, and anything else relevant. Check the current product page before you draft anything. Create a launch kit with a launch review brief, customer email, internal announcement, social post, a two-week content plan, an agency brief, a staging page fix list, and a team status update. Flag any claims that need product or legal review and anything that is still unverified.`
#### How to customize it
Replace the launch inputs with your actual plans, notes, and links. Tell Codex which assets you need and who they are for. Specify what claims need extra review and what live pages to check.

**Suggested plugins**: HubSpot, Common Room, Demandbase, Google Drive, Slack, Notion, Canva  
**Suggested skills**: Google Docs, Slack Outgoing Message

**Example:**
_I’m working on the Team Spaces launch. Use “Team Spaces Launch Doc,” “Team Spaces Product Notes,” “Team Spaces Launch Tracker,” “Agency Brief Outline,” the three Team Spaces staging page links, #team-spaces-launch, and anything else relevant. Check the current Team Spaces product page. Create a first-draft launch kit with a launch review brief, customer email, internal announcement, social post, two-week content plan, agency brief, staging page fix list, and Slack status update. Flag claims needing product or legal review and anything unverified._

## 10. Workflow audit and automation spec

#### Why people use this
When a workflow is messy, the real problems are usually spread across docs, trackers, tickets, dashboards, and team history. Codex can trace the workflow across those sources, surface the pain points, and turn the findings into a process doc and automation-ready next steps.
#### Basic prompt
`I'm auditing an onboarding workflow before the next cohort starts. Use the current tracker, the existing process documentation, handoff notes, the KPI dashboard, support ticket history, team discussion history, and anything else that explains how the workflow currently operates. Create a workflow audit brief that covers the current steps, stuck points, owners, repeated questions, missing data, and likely automation candidates. Then draft an updated process doc and a short automation spec for the two most repetitive manual steps. Flag any outdated or conflicting sources.`
#### How to customize this
**Suggested plugins**: Notion, Linear, Slack, Gmail, Google Calendar, Google Drive, SharePoint  
**Suggested skills**: Google Docs, Google Sheets

**Example:**
_I’m auditing contractor onboarding before the next cohort. Use “Contractor Onboarding Tracker,” “Contractor Onboarding Process Doc,” “Handoff Notes from Recruiting Ops,” “April Onboarding KPI Dashboard,” “Contractor Support Ticket Export.csv,” #contractor-onboarding-ops, and anything else that explains the current workflow. Create a workflow audit brief with current steps, stuck points, owners, repeated questions, missing data, and automation candidates. Then draft an updated process doc and short automation spec for the two most repetitive manual steps. Flag outdated sources._
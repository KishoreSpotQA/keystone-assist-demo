# Keystone CRM Assist: AI Chat Assistant Reference (Knowledge Base Source)

**Upload this file to the Touchstone Knowledge Base as one source.** It describes **Assist**, the read-only AI chat assistant built into the Keystone CRM web application: how to open it, its elements, how answers are produced, what it can answer, how it refuses, the expected answers on the seed data, test hooks and known limitations.

**Companion source:** *Keystone CRM: Application Reference* describes the CRM screens (sign-in, modules, forms, lists, record pages, workflows). Upload both sources when requirements need to check chat answers against CRM screens. This source does not repeat CRM screen behaviour.

| Item | Value |
|---|---|
| Application | Keystone CRM (fictional CRM, sample data only), Assist panel |
| Starting URL | Environment variable `crmUrl` (https://kishorespotqa.github.io/keystone-assist-demo/crm/) |
| Sign-in | Required. Sam Carter (sam.carter@keystone.example / Keystone#2026). Add `#autologin` to the link to sign in as Sam without the sign-in page |
| Chat user for expected answers | **Sam Carter**, unless stated. Section 8.3 gives expected answers for Alex Rivera |
| Data | Assist reads the live CRM data in the browser. A fresh browser starts with the seed data in section 8 |
| Last updated | 26 Oct 2026 |

---

## 1. Purpose

Assist answers natural-language questions about the CRM data: records, pipeline, meetings, tasks and calls. It also plans the user's day, suggests follow-ups and priorities, prepares meeting briefs and gives how-to guidance.

**Assist is read-only.** It never creates, updates or deletes CRM records. When asked to change data it refuses, and for create requests it offers how-to steps instead.

---

## 2. Opening and closing Assist

| Situation | Behaviour |
|---|---|
| Sign-in on a screen wider than 900 px | The Assist panel is open, docked on the right of the CRM. The question input has focus |
| Sign-in on a screen 900 px wide or narrower | The panel is closed. When opened it covers the whole screen |
| **Assist** button (top bar) | Opens or closes the panel. `aria-pressed="true"` when open |
| **Close** button (panel header) | Closes the panel. The conversation is kept |
| Link contains `chat` (for example `crmUrl#autologin.chat`) | The panel is opened on any screen size |

The CRM pages stay usable while the panel is open.

---

## 3. Panel elements

| Element | Label / text | Id or test hook |
|---|---|---|
| Panel container | Custom element `keystone-assist` inside the dock | `#assist-host` inside `#assist-dock` |
| Title | "Assist", subtitle "Read-only CRM assistant" | `.as-title` |
| New chat button | "New chat". Clears the conversation | `#assist-new` |
| Close button | "Close", aria-label "Close Assist" | `#assist-close` |
| Chat log | role="log" | `#assist-log` |
| Welcome message (empty conversation) | "Hi <first name>. Ask about your opportunities, meetings, tasks and accounts, or how to do something in Keystone CRM." | `.as-empty` |
| Suggested prompts (empty conversation only) | Four buttons: "What can you help me with?", "Show me my open opportunities", "Help me prepare for my meeting with AtoZ Co Ltd", "Do I have any overdue tasks?" | `.as-chip` |
| Question input | aria-label "Ask Assist", placeholder "Ask Assist about your CRM…" | `#assist-input` |
| Send button | "Send". Disabled while Assist is answering | `#assist-send` |
| Status | "Ready", "Thinking…" or "Answering…" | `#assist-status`, attribute `data-busy="true"` or `"false"` |
| Footer note | "Answers use your CRM data" | |
| User message | The question text | `[data-testid="user-message"]` |
| Assist answer | Label "Assist" (displayed in capitals) followed by the answer | `[data-testid="assist-answer"]`, `data-state="streaming"` then `"done"` |
| Latest finished answer | The most recent answer, only after it has finished | `#assist-last-answer` |
| Record link in an answer | Record name shown as an underlined link | `.as-rec` with `data-module` and `data-id` |

---

## 4. Asking a question and reading the answer

### 4.1 Sending
- Type in the input and press **Enter** or click **Send**. Clicking a suggested prompt sends its text.
- The input is cleared when the question is sent.
- An empty question (or only spaces) is ignored.
- While an answer is in progress, new questions are ignored and **Send** is disabled.

### 4.2 Answer lifecycle
1. The previous answer loses the id `assist-last-answer`.
2. The question appears as a user message. Status: "Thinking…".
3. "Assist is thinking…" (`#assist-thinking`) shows for about 0.6 to 1.2 seconds.
4. The answer streams in with `data-state="streaming"` and id `assist-streaming`. Status: "Answering…".
5. When finished, the answer gets `data-state="done"` and id `assist-last-answer`. Status returns to "Ready" and **Send** is enabled.

**Tests must wait for `#assist-last-answer` (or status "Ready") before reading the answer.** Reading earlier returns a partial answer. Allow up to 30 seconds.

### 4.3 Answer format
- Answers use short paragraphs, bulleted lists, numbered lists, bold text and, for prep briefs, section headings.
- Each data answer names the matching records. Record names are links: clicking one opens that record's page in the CRM (on screens 900 px or narrower the panel also closes).
- Most answers have two or three wordings, chosen at random, to behave like an AI assistant. The records, counts and amounts are always the same for the same data. **Validate answers by meaning, counts, amounts and record names, never by an exact sentence.**
- Dates in answers use British day-month order, for example "Mon, 26 Oct", "Mon, 26 Oct at 15:30", "22 Oct 2026", or relative wording such as "today", "tomorrow", "in 12 days", "3 days ago".

### 4.4 Conversation memory
| Event | Effect on the conversation |
|---|---|
| **New chat** | Conversation cleared; welcome message and suggested prompts return |
| Page reload | Conversation cleared (CRM data is kept) |
| Sign-in, sign-out | Conversation cleared |
| **Reset demo data** | Conversation cleared and data restored to the seed |
| **Close** and reopen the panel | Conversation kept |

Assist remembers one thing between turns: after it lists meetings to choose from (section 6.6), the next short reply can pick one.

---

## 5. Data Assist uses

- Assist reads the **current** CRM data. Records created, edited or deleted in the CRM are reflected in the next answer. Example: after creating an open opportunity "Test Live Deal" for $10,000, "Show me my open opportunities" lists 5 opportunities worth $547,000.
- **Personal questions are scoped to the signed-in user** ("my", "your", or no owner named):
  - Opportunities, leads, contacts, calls and tasks: records whose **Assigned to** is the signed-in user.
  - Meetings: meetings the signed-in user **organizes or is invited to**.
  - This applies to record lists, pipeline, work today, follow-ups, weekly focus, accomplishments and meeting lists.
- **Account and contact questions use the whole workspace**: an account summary, latest updates, account opportunities, website, prep brief contents and contact look-ups include every record linked to that account or contact, whoever it is assigned to.
- "Today", "this week", "this month" and "this quarter" use the browser's current date.

---

## 6. What Assist answers

Questions are matched by keywords, checked in the order of the table in 6.1. The first match decides the answer type.

### 6.1 Question types and routing
| # | Answer type | Recognised when the question… | Example question |
|---|---|---|---|
| 1 | Meeting choice follow-up | is 6 words or fewer, right after a meeting list (6.6), and contains a number 1 to 5, a word such as "first", "two", "third", or a listed meeting's name | "2", "the second one", "Platform Demo" |
| 2 | How-to guidance | starts with "How do I", "How can I", "How to", "What is the best way to", "Steps to", "Walk me through", "Guide me", "Show me how" | "How do I create an opportunity?" |
| 3 | Change request (refused) | starts with a change verb (delete, remove, erase, wipe, create, add, insert, update, edit, change, modify, rename, reassign, assign, mark, log, schedule, book, merge, convert, close), optionally after "please" or "can/could/would you", or contains delete, erase or wipe anywhere | "Delete all my leads" |
| 4 | Accomplishments | mentions accomplish or achieve, or "summary/summarise" with "week" | "Summarise my accomplishments this week" |
| 5 | Follow-up actions | mentions follow up / follow-up | "What follow-up actions should I take?" |
| 6 | Weekly focus | mentions focus or prioritise/priority | "What should I focus on this week?" |
| 7 | Help with work today | "help/assist … work/day", or "plan my day" | "Help me with my work today" |
| 8 | Guest meetings | mentions meeting with guest, invitee, participant or attendee | "How many meetings have I attended as a guest?" |
| 9 | Meeting preparation | contains the word "prep" or "prepare" ("preparation" alone is not recognised) | "Help me prepare for my meeting with AtoZ Co Ltd" |
| 10 | Account questions | names an account | "What is the website for Regent Solutions Corp?" |
| 11 | Quarter pipeline | mentions "quarter" with close, closing, closed, won, win, lost or lose (and not "next" or "days") | "What did we lose this quarter?" |
| 12 | Opportunities and pipeline | mentions opportunity, deal(s) or pipeline | "Show me my open opportunities" |
| 13 | Leads | mentions lead(s) | "Show me my leads" |
| 14 | Meetings | mentions meeting | "What are my upcoming meetings?" |
| 15 | Contact by name | names a contact (full name, last name, or first name of 5 letters or more) | "Who is Helena Ostrowski?" |
| 16 | Contacts | mentions contact | "Show me my recent contacts" |
| 17 | Tasks | mentions task(s) or to-do | "Do I have any overdue tasks?" |
| 18 | Calls | mentions call(s) | "Show my recent calls" |
| 19 | Capabilities | contains "what can you", "what do you do", "who are you", "what are you", "capabilit" or "what can I ask", ends with "help me with", or is just "help" | "What can you help me with?" |
| 20 | Greeting | starts with hi, hello, hey, good morning/afternoon/evening | "Hello" |
| 21 | Out of scope | anything else | "What is the weather like in London today?" |

**Name matching.** Accounts are recognised by full name (any case), or by "atoz", "a to z" or "regent". Contacts are recognised by full name, by last name, or by first name when it has 5 or more letters (for example "Tell me about Priya", "Who is Helena?").

### 6.2 Capabilities
Lists five areas: CRM records; pipeline insights (closed this quarter, closing in the next 30 days); meeting preparation; work assistance (daily suggestions, follow-ups, weekly priorities, accomplishment summaries); how-to guidance (creating records, logging calls and scheduling meetings in Keystone CRM). Ends by stating it has read-only access and cannot create, update or delete records.

### 6.3 Record queries
| Question type | Answer contents |
|---|---|
| Open opportunities | Count and total amount of Open opportunities, then each with account, stage, amount and close date, earliest close first. If none: a "couldn't find any open opportunities" reply |
| Leads | Count, then each lead with company, status, lead source and how long ago it was created. If none: a "no leads assigned to you" reply |
| Upcoming meetings | Planned meetings from today onward, earliest first, with account, date and time; "(you're a guest)" when the signed-in user is an invitee and not the organizer |
| Meetings today | Planned meetings today with time, account and attendees. If none: "You have no meetings scheduled today" plus the next meeting |
| Meetings this month | All meetings (any status) starting in the current calendar month, earliest first, with status. The count depends on the date |
| Held / past meetings | Held meetings, most recent first (question mentions held, past, last week or previous). If none: a "no held meetings" reply |
| Recent contacts | The user's contacts, most recently modified first, with title and account |
| Contact by name | Title, account, email, phone and when last modified |
| Overdue tasks | Tasks not Completed and due before today, oldest first, with account, due date, days overdue and priority. If none: "Good news: you have no overdue tasks." |
| Tasks | The user's tasks not Completed, earliest due first, with "(overdue)" where due before today. If none: "You have no open tasks." |
| Calls | The user's calls, most recent first, with contact, account, when and outcome |

### 6.4 Pipeline
| Question | Rule | Answer |
|---|---|---|
| Opportunities **closed** this quarter. Recognised by "closed", "won"/"win", "lost"/"lose", or "close" with "did" or "have" (for example "Which opportunities did I close this quarter?", "What did we lose this quarter?") | Status Closed Won or Closed Lost with close date inside the current calendar quarter. "won"/"win" keeps only Closed Won; "lost"/"lose" only Closed Lost | Names the quarter (for example "Q4 2026"), then each with account, status, amount and close date. If none: "You have no closed opportunities this quarter (Qn YYYY)." ("no Closed Won" / "no Closed Lost" when filtered) |
| Opportunities **closing this quarter** ("close" or "closing" with "quarter", without did/have/closed/won/lost, for example "Which opportunities close this quarter?") | Open opportunities with close date from today to the last day of the quarter | "closing by the end of this quarter (Qn YYYY, <last day>)", then each with account, close date, days away, stage and amount |
| Opportunities **closing** in the next N days (mentions clos… without closed/won/lost) | Open opportunities with close date from today to today + N days. N is taken from the question ("next 90 days"); default 30 | Each with account, close date, days away, stage and amount. If none: "No opportunities are closing in the next N days." |

### 6.5 Work assistance
| Question type | Answer contents |
|---|---|
| Help with work today | A three-step plan: (1) clear overdue tasks, naming the count and the oldest; (2) prepare for today's meetings (or "no meetings today"); (3) move opportunities closing in the next 30 days. Ends with an offer of lookups, prep briefs or how-to help |
| Follow-up actions | Up to four numbered follow-ups built from current data: (1) the oldest overdue task, with days overdue; (2) the most recent Held meeting in the last 14 days, with its attendees; (3) the most recent Held call in the last 14 days, with contact and outcome; (4) the open opportunity closing soonest within 30 days, with its next step. Items with no matching record are left out. If none apply: "You're all caught up…" |
| Weekly focus | Priorities: (1) overdue tasks with count; (2) open opportunities closing within 30 days with amounts; (3) Planned meetings in the next 7 days with count. Ends with one recommendation: the oldest High-priority overdue task; otherwise the oldest overdue task; otherwise the opportunity closing soonest; otherwise "Your week looks clear…" |
| Accomplishments | Three sections for the last 7 days with counts: Completed tasks, Meetings held, Opportunities closed (close date in the last 7 days). Empty sections show "None this week". Ends with a one-line summary |

### 6.6 Meeting preparation
| Question | Answer |
|---|---|
| Prepare for my meeting with <account> | Prep brief for the next upcoming meeting with that account. If there is none: "You have no upcoming meetings with <account>" followed by the account summary |
| Prepare for my meetings today | Today's meetings, then a short preparation line for each (review the related opportunity and the account's open tasks) |
| Prepare for one of my next five meetings (mentions five, 5, choose, select or pick) | Numbered list of up to five upcoming meetings (says "fewer than five" when there are fewer) and asks the user to reply with the number or meeting name |
| Prepare for my next meetings (any other prep question) | Upcoming meetings with attendees and related opportunity, then "Tell me which one you'd like a prep brief for." |
| Reply after either list (6.1 row 1) | Prep brief for the chosen meeting |

**Prep brief contents** (heading "Prep brief: <meeting>"):
- **When:** date, time, relative day, duration, location
- **Attendees:** invitees other than the signed-in user, with titles when they are contacts; "Organized by <name>; you're a guest." when applicable
- **Account:** name, industry, city, type
- **Opportunities:** all opportunities of the account with stage and amount (close date for Open ones)
- **Key contacts:** contacts of the account with titles
- **Recent activity:** up to 3 most recent calls and held meetings for the account
- **Open tasks:** the account's tasks not Completed, with "(overdue)" where applicable (section omitted when none)
- **Suggested talking points:** (1) progress on the meeting's related opportunity with its next step, (2) confirm decision makers and timeline, (3) agree next steps and owners

### 6.7 Guest meetings
A meeting counts as a guest meeting when the signed-in user is an invitee and someone else is the organizer. The answer gives the number of **Held** guest meetings with organizer and when, then mentions **Planned** guest meetings separately and the total. If there are no Held guest meetings: "You haven't attended any meetings as a guest (invitee rather than organizer)." plus any Planned guest meetings.

### 6.8 Account questions
| Question mentions | Answer |
|---|---|
| website, URL, domain | The account's website, or "The <account> account doesn't have a website set. You can add one from the account's Edit view." |
| opportunities or deals | All the account's opportunities, most recently updated first, with status, stage (Open only), amount and when updated |
| updates, latest, news, activity, happening, status | Up to 6 most recent events (opportunity updates, calls, held meetings, contact updates), newest first, then "Coming up:" with upcoming meetings for the account |
| anything else | Account summary: type, industry, city, number and total of open opportunities, contacts, website |

### 6.9 How-to guidance
Numbered steps labelled "Step 1", "Step 2" and so on, ending with **Save**. The steps use the real Keystone CRM labels: the module in the left menu, the **New <record>** button, the form's required fields, and **Save**.

| Topic | Steps name |
|---|---|
| Create an opportunity | Opportunities → **New opportunity** → Opportunity name, Account, Sales stage, Amount, Expected close date (required) → Save. Tip: New opportunity on an account page fills in the Account |
| Create a lead | Leads → **New lead** → Last name and Company (required), Status, Lead source → Save. Mentions **Convert** for qualified leads |
| Create a contact | Contacts → **New contact** → Last name (required) → Save |
| Create an account | Accounts → **New account** → Account name (required, unique) → Save |
| Schedule a meeting | Meetings → **New meeting** → Subject and Start (required), Invitees separated by commas → Save |
| Log a call | Calls (or an account's or contact's Calls list) → **New call** → Subject and Date and time (required), Direction, Status, Account, Contact, Outcome → Save |
| Create a task | Tasks → **New task** → Subject and Due date (required) → Save; mentions **Mark complete** |
| Import from a CSV file | States that Keystone CRM doesn't have a CSV import yet, then gives steps to add records one at a time with the module's **New** button |

Topic is chosen by keyword in this order: import/CSV/upload, call, opportunity/deal, lead, contact, account, meeting, task. Any other how-to question: "I don't have a guide for that yet." with the list of supported topics.

### 6.10 Greeting and out of scope
- Greeting: "Hi <first name>" or "Hello <first name>" with a short list of what Assist can do.
- Out of scope (weather, jokes, general knowledge and anything not recognised): Assist says it couldn't find an answer or that the question is outside its CRM scope, names the CRM areas its agents cover and asks the user to rephrase. **It gives no information on the off-topic subject.**

---

## 7. Read-only behaviour (change requests)

| Request | Answer | Data |
|---|---|---|
| Delete / remove / erase / wipe | "Sorry, I can't delete records. I have read-only access…" and "Your <N> <module> are unchanged." (or a variant saying an admin can clean up from the list view) | Unchanged |
| Create / add / insert | States it can't create records because it is read-only, then gives the how-to steps for that module (6.9). When no module is named: open the module in the left menu, click its **New** button, fill in the required fields and click **Save** | Unchanged |
| Any other change (update, edit, mark, assign, log, schedule, convert, close…) | "Sorry, I can't make changes to CRM records. I have read-only access…" and an offer to show how to make the change | Unchanged |

After any change request, every CRM module list and record count must be the same as before.

---

## 8. Seed data and expected answers

### 8.1 Seed summary
All dates are relative to the day the data is seeded (first visit or last **Reset demo data**). Full field values are in the companion CRM source, section 11.2.

| Module | Records |
|---|---|
| Accounts (2) | AtoZ Co Ltd (Customer, Technology, London, website https://www.atoz-co-ltd.example.com); Regent Solutions Corp (Prospect, Financial Services, Manchester, **no website**) |
| Opportunities (6) | Cloud Migration Initiative (AtoZ, Proposal/Price Quote, $185,000, Open, today + 21); Security Audit Package (AtoZ, Needs Analysis, $64,000, Open, today + 75); SaaS Renewal FY26 (AtoZ, Closed Won, $120,000, in the current quarter, up to 4 days ago); Data Analytics Platform (Regent, Value Proposition, $240,000, Open, today + 12); Compliance Module (Regent, Qualification, $48,000, Open, today + 110); Q2 Support Contract (Regent, Closed Lost, $30,000, 20 days before the current quarter started) |
| Contacts (4) | Helena Ostrowski (CTO, AtoZ, modified 1 day ago); Priya Venkataraman (Finance Director, Regent, 2 days ago); Marcus Delacroix (VP Operations, AtoZ, 5 days ago); Tobias Worthington (Head of IT, Regent, 8 days ago) |
| Leads (3) | Daniel Okafor (Brightline Logistics, New, Web Site, 2 days ago); Sofia Marchetti (Verano Foods, In Process, Trade Show, 6 days ago); Kenji Watanabe (Halden Robotics, New, Referral, 9 days ago) |
| Meetings (5) | Q3 Business Review (AtoZ, Planned, today 15:30, organizer Sam); Cloud Migration Kick-off (AtoZ, Planned, today + 3, 10:00, Sam); Platform Demo (Regent, Planned, today + 6, 14:00, organizer Alex Rivera, Sam invited); Security Audit Scoping (AtoZ, Held, 4 days ago, Sam); Compliance Workshop (Regent, Held, 16 days ago, organizer Alex, Sam invited) |
| Calls (3) | Security Audit Discovery Call (Marcus, 6 days ago); Renewal Negotiation Call (Helena, 10 days ago); Compliance Requirements Call (Priya, 11 days ago). All Held |
| Tasks (6) | Chase Regent PO for Data Analytics Platform (due 12 days ago, High); Share Security Audit scoping notes with Marcus (7 days ago, Medium); Send revised Cloud Migration proposal to Helena (3 days ago, High); Send Q3 Business Review follow-up summary (today + 2); Book Compliance Module discovery call (today + 9); Log SaaS Renewal FY26 handover notes (Completed 2 days ago) |

### 8.2 Expected answers on fresh seed (signed in as Sam)
These values assume the data was seeded on the day of the test (a fresh browser, or **Reset demo data** that day). Data seeded on an earlier day and kept in the browser keeps its original dates, so relative values such as "in 12 days" and overdue counts will differ.

| Question | Expected key content |
|---|---|
| What can you help me with? | Five capability areas and the read-only statement |
| Show me my open opportunities | 4 open opportunities worth $537,000: Data Analytics Platform, Cloud Migration Initiative, Security Audit Package, Compliance Module (in that order) |
| Show me my leads | 3 leads: Daniel Okafor, Sofia Marchetti, Kenji Watanabe |
| What are my upcoming meetings? | 3: Q3 Business Review (today 15:30), Cloud Migration Kick-off, Platform Demo (you're a guest) |
| What meetings do I have today? | 1: Q3 Business Review at 15:30 with AtoZ Co Ltd (Helena Ostrowski, Marcus Delacroix) |
| Show my held meetings | Security Audit Scoping (4 days ago), Compliance Workshop (16 days ago) |
| Show me my recent contacts | 4, in order Helena, Priya, Marcus, Tobias |
| Who is Helena Ostrowski? | CTO at AtoZ Co Ltd, email helena.ostrowski@atoz-co-ltd.example.com, phone +44 20 7946 0390 |
| Tell me about Priya | Priya Venkataraman, Finance Director at Regent Solutions Corp |
| Do I have any overdue tasks? | 3 overdue: Chase Regent PO (12 days), Share Security Audit scoping notes (7 days), Send revised Cloud Migration proposal (3 days) |
| Show my tasks | 5 open tasks, the first three marked overdue |
| Show my recent calls | 3, in order Security Audit Discovery Call, Renewal Negotiation Call, Compliance Requirements Call |
| Which opportunities did I close this quarter? | 1: SaaS Renewal FY26, Closed Won, $120,000. Q2 Support Contract must not appear |
| What did we lose this quarter? | No Closed Lost opportunities this quarter (Q2 Support Contract closed before the quarter started) |
| Which opportunities close this quarter? | 2 open opportunities closing by the end of the quarter: Data Analytics Platform, Cloud Migration Initiative |
| Show me opportunities closing in the next 30 days | 2: Data Analytics Platform (in 12 days), Cloud Migration Initiative (in 21 days). Security Audit Package and Compliance Module must not appear |
| Which deals close in the next 90 days? | 3: the two above plus Security Audit Package (in 75 days) |
| Help me with my work today | 3 overdue tasks starting with Chase Regent PO; Q3 Business Review at 15:30; Data Analytics Platform and Cloud Migration Initiative closing soon |
| What should I focus on this week? | 3 overdue tasks; 2 opportunities closing within 30 days ($240,000 and $185,000); 3 meetings this week; recommends clearing Chase Regent PO for Data Analytics Platform first (High priority, 12 days overdue) |
| What follow-up actions should I take? | 4 items: Chase Regent PO for Data Analytics Platform (12 days overdue); Security Audit Scoping (held 4 days ago, send summary to Marcus Delacroix); Security Audit Discovery Call with Marcus Delacroix ("Scoping session booked"); Data Analytics Platform closes in 12 days (next step: chase purchase order with Priya Venkataraman) |
| Summarise my accomplishments this week | 1 completed task (Log SaaS Renewal FY26 handover notes), 1 meeting held (Security Audit Scoping), 1 opportunity closed (SaaS Renewal FY26) |
| How many meetings have I attended as a guest? | 1 held (Compliance Workshop, organized by Alex Rivera); also 1 upcoming as guest (Platform Demo); total 2 |
| Help me prepare for my meeting with AtoZ Co Ltd | Prep brief for Q3 Business Review: attendees Helena Ostrowski (CTO) and Marcus Delacroix; 3 AtoZ opportunities; 3 open tasks (2 overdue); talking point on Cloud Migration Initiative |
| Prepare for my meeting with Regent Solutions Corp | Prep brief for Platform Demo, organized by Alex Rivera, "you're a guest"; talking point on Data Analytics Platform |
| Help me prepare for one of my next five meetings, then "2" | List of 3 meetings ("fewer than five"), then the prep brief for Cloud Migration Kick-off |
| What is the website for Regent Solutions Corp? | Website is not set; can be added from the account's Edit view |
| What is the website for AtoZ Co Ltd? | https://www.atoz-co-ltd.example.com |
| Tell me about AtoZ Co Ltd | Customer, Technology, London; 2 open opportunities worth $249,000; contacts Helena Ostrowski, Marcus Delacroix |
| What are the latest updates on Regent Solutions Corp? | Recent events for Regent only, newest first; "Coming up: Platform Demo" |
| How do I create an opportunity? | Steps "Step 1" to "Step 5": Opportunities, **New opportunity**, required fields, optional fields, **Save** |
| How do I import contacts from a CSV file? | Says there is no CSV import yet; steps to add records one at a time with **New contact** |
| Delete all my leads | Refusal (read-only); "Your 3 leads are unchanged." Leads list still shows 3 |
| Create a new contact named John Test | Refusal and the steps Contacts → **New contact** → Last name → Save; Contacts still shows 4 |
| What is the weather like in London today? | Out-of-scope reply asking to rephrase; no temperature or forecast |

### 8.3 Expected answers on fresh seed (signed in as Alex Rivera)
In the seed every opportunity, lead, contact, call and task is assigned to Sam Carter. Alex organizes two meetings (Platform Demo and Compliance Workshop) and is not invited to Sam's meetings.

| Question | Expected key content |
|---|---|
| Show me my open opportunities | None found |
| Show me my leads | No leads assigned to you |
| Do I have any overdue tasks? | "Good news: you have no overdue tasks." |
| Show my tasks | "You have no open tasks." |
| What are my upcoming meetings? | 1: Platform Demo (no guest label, Alex is the organizer) |
| Show my held meetings | 1: Compliance Workshop |
| How many meetings have I attended as a guest? | "You haven't attended any meetings as a guest" |
| What follow-up actions should I take? | "You're all caught up…" |
| Help me prepare for my meeting with AtoZ Co Ltd | No upcoming meetings with AtoZ Co Ltd, followed by the account summary |
| Help me prepare for my meeting with Regent Solutions Corp | Prep brief for Platform Demo; attendees include Sam Carter, Tobias Worthington and Priya Venkataraman |
| Tell me about AtoZ Co Ltd | Same account summary as for Sam (account questions use the whole workspace) |

---

## 9. Business rules

| ID | Rule |
|---|---|
| AS-01 | Assist never creates, updates or deletes CRM records. After any change request, module lists and counts are unchanged. |
| AS-02 | Data answers are grounded in current CRM data and name the matching records. A "not found" answer when matching records exist is a defect. |
| AS-03 | "This quarter" is the calendar quarter containing today. "Next N days" is today to today + N days inclusive. Records outside the window must not appear. |
| AS-04 | Off-topic questions get an out-of-scope reply with a request to rephrase, never an answer on the topic. |
| AS-05 | Every answer has more than 10 characters of text. |
| AS-06 | A guest meeting is one where the signed-in user is an invitee and not the organizer. |
| AS-07 | Counts and totals in an answer match the records listed in that answer and the CRM module data. |
| AS-08 | The latest answer is available only once streaming has finished (`#assist-last-answer`, `data-state="done"`). |
| AS-09 | Record links in answers open the correct record page. |
| AS-10 | Changes made in the CRM appear in the next answer without reloading. |
| AS-11 | Personal questions return only the signed-in user's records (Assigned to) and meetings they organize or are invited to. |
| AS-12 | How-to steps use the labels shown in Keystone CRM (module names, New <record> buttons, required fields, Save). |

---

## 10. Demo controls (fault injection)

Faults are **off by default**. They exist to show that tests catch a broken assistant. Requirements describe correct behaviour; do not write requirements that expect a fault.

Faults are switched on from the link, joined by dots, for example `crmUrl#autologin.offtopic.slow`. Adding `admin` shows a **Demo controls** panel with a checkbox per fault and a "Faults on: …" badge in the top bar.

| Token | Effect when on |
|---|---|
| `offtopic` | Answers the weather question with a forecast instead of declining |
| `mutation` | "Delete all <module>" empties that module and "Create a new contact named <name>" creates it; the change is saved. Restore with Reset demo data |
| `noretrieval` | Data questions return "not found" style replies |
| `window` | Pipeline answers ignore the quarter and N-day windows |
| `empty` | Answers are blank |
| `slow` | Adds 9 to 14 seconds before each answer |
| `relabel` | Input id `msg-field-7f3`, aria-label "Message", placeholder "Type a message"; send button id `submit-q-7f3`, text "Submit" (self-healing demo) |
| `shadow` | The Assist panel is rendered inside an open shadow root on `#assist-host` |

---

## 11. Not supported

Assist does not provide: changing any CRM data, voice input, file upload, conversation history after reload, multiple saved conversations, copy or feedback buttons on answers, or general knowledge answers. Requirements should not assume these features.

---

## 12. Open item

| # | Item |
|---|---|
| OI-01 | Whether "attended as a guest" should include Planned meetings is not defined. Assist counts only Held meetings as attended and mentions Planned ones separately. |

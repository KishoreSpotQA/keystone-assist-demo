# Keystone CRM: Application Reference (Knowledge Base Source)

**Upload this file to the Touchstone Knowledge Base as one source.** It describes the Keystone CRM web application: sign-in, navigation, the seven record modules, forms and validation, lists, record pages, workflows, data rules and test hooks.

**Out of scope for this source:** the Assist AI chat panel (the "Assist" button and the panel docked on the right). It is described in a separate source. Do not generate requirements or journeys for Assist from this document.

| Item | Value |
|---|---|
| Application | Keystone CRM (fictional CRM, sample data only) |
| Starting URL | Environment variable `crmUrl` (https://kishorespotqa.github.io/keystone-assist-demo/crm/) |
| Sign-in | Required. Demo users in section 2 |
| Data | Saved in the browser. A fresh browser always starts with the seed data in section 11 |
| Last updated | 26 Oct 2026 |

---

## 1. Purpose

Keystone CRM is a sales CRM used to demonstrate functional test automation. Sales users manage **Accounts, Contacts, Leads, Opportunities, Meetings, Calls and Tasks**: they create, view, edit and delete records, search and filter lists, convert leads, complete tasks and review the change history of each record.

---

## 2. Users and sign-in

### 2.1 Demo users
| Name | Role | Email | Password |
|---|---|---|---|
| Sam Carter | Sales Representative | sam.carter@keystone.example | Keystone#2026 |
| Alex Rivera | Sales Manager | alex.rivera@keystone.example | Keystone#2026 |

- Both roles have the **same permissions**. Every user can create, edit and delete every record.
- **Jordan Lee** appears in "Assigned to" lists but cannot sign in.
- The demo accounts and password are shown on the sign-in page.

### 2.2 Sign-in page
Fields: **Email**, **Password**, checkbox **Keep me signed in** (checked by default), button **Sign in**.

| Situation | Message shown (in the red box above the fields) |
|---|---|
| Email empty | Enter your email address. |
| Email not in a valid format | Enter a valid email address. |
| Password empty | Enter your password. |
| Email and password do not match a demo user | Invalid email or password. (the password field is cleared) |

- Email matching ignores upper and lower case; the password is case sensitive.
- On success the Home page opens and the top bar shows the user's initials (SC or AR).
- **Keep me signed in** checked: the user stays signed in after a page reload or browser restart. Unchecked: the user stays signed in only until the browser session ends.

### 2.3 Sign out
User menu (initials, top right) → **Sign out** returns to the sign-in page with empty fields. A reload after signing out still shows the sign-in page.

### 2.4 Skipping sign-in in tests
Adding `autologin` to the link (for example `crmUrl#autologin`) signs in as Sam Carter without the sign-in page.

---

## 3. Layout and navigation

| Area | Contents |
|---|---|
| Top bar | Keystone CRM logo, "QA sandbox" label, **Search all records** box, today's date, Assist button (out of scope), user initials button |
| Left module rail | Home, Accounts, Contacts, Leads, Opportunities, Meetings, Calls, Tasks. Each module shows its record count. The current page is highlighted. At the bottom: **Knowledge base** links (section 3.1) |
| Main area | The current page: Home, a module list, a record page or search results |

- **User menu** (click the initials): name, role, email, **CRM application KB**, **Assist chatbot KB**, **Touchstone directives**, **Reset demo data**, **Sign out**. Clicking elsewhere or pressing Escape closes it.
- Record pages show a breadcrumb (for example "Accounts / Account"). Clicking the module name returns to its list.
- On screens narrower than 900 px the module rail becomes a horizontal bar and the top-bar search box is hidden. The Knowledge base links are then available only from the user menu.

### 3.1 Knowledge base links (demo resource)
The bottom of the module rail has a **Knowledge base** section with three links: **CRM application KB** (this document), **Assist chatbot KB** (the Assist reference) and **Touchstone directives** (suggested Touchstone directives for these sources). Each opens a dialog that shows the document, with **Open as text** (opens the Markdown file in a new tab) and **Download .md** (downloads the file). Escape or × closes the dialog.

These links are provided for demo audiences. Do not generate requirements for them.

---

## 4. Home page

Heading: "Good morning / Good afternoon / Good evening, <first name>".
Quick buttons: **New opportunity**, **New meeting**, **New task** (each opens the create form).

| KPI tile | Definition |
|---|---|
| Open pipeline | Total amount of opportunities with status Open, and how many |
| Meetings today | Planned meetings starting today; shows the first one's name and time |
| Overdue tasks | Tasks not Completed whose due date is before today; shows the oldest due date |
| Closing in 30 days | Open opportunities with an expected close date from today to today + 30 days, and their total amount |

| Panel | Contents |
|---|---|
| Upcoming meetings | Planned meetings from today onward, earliest first, with account; a **Guest** label when the signed-in user is an invitee but not the organizer |
| Tasks needing attention | All tasks not Completed, earliest due first, with an **Overdue** label where due before today |
| Closing soon | Same records as the "Closing in 30 days" tile |
| Recent activity | The 8 most recent activity entries across all records |

A footnote shows the date the data was last reset.

---

## 5. Modules and fields

`*` = required. "Default" is the value pre-filled in the **New** form.

### 5.1 Accounts
| Field | Type | Rules / options | Default |
|---|---|---|---|
| Account name * | Text | Must be unique (case insensitive) | |
| Industry | Dropdown | Technology, Financial Services, Healthcare, Manufacturing, Retail, Logistics, Food & Beverage, Education, Other | blank |
| Type | Dropdown | Customer, Prospect, Partner, Competitor, Other | Prospect |
| Website | Web address | Must start with http:// or https:// | |
| Phone | Phone | Digits, spaces, +, (, ), -; at least 6 characters | |
| City | Text | | |
| Employees | Number | 0 or more | |
| Assigned to | Dropdown | Sam Carter, Alex Rivera, Jordan Lee | signed-in user |

### 5.2 Contacts
| Field | Type | Rules / options | Default |
|---|---|---|---|
| First name | Text | | |
| Last name * | Text | | |
| Title | Text | | |
| Account | Dropdown of accounts (A–Z) | | blank (prefilled when created from an account page) |
| Email | Email | Valid email format | |
| Phone | Phone | As Accounts | |
| Assigned to | Dropdown | Users as above | signed-in user |

The contact's display name is "First name Last name".

### 5.3 Leads
| Field | Type | Rules / options | Default |
|---|---|---|---|
| First name | Text | | |
| Last name * | Text | | |
| Company * | Text | | |
| Title | Text | | |
| Status | Dropdown | New, In Process, Converted, Dead | New |
| Lead source | Dropdown | Web Site, Trade Show, Referral, Cold Call, Email, Partner, Other | Web Site |
| Email | Email | Valid email format | |
| Phone | Phone | As Accounts | |
| Assigned to | Dropdown | Users | signed-in user |

### 5.4 Opportunities
| Field | Type | Rules / options | Default |
|---|---|---|---|
| Opportunity name * | Text | | |
| Account * | Dropdown of accounts | | blank (prefilled from an account page) |
| Sales stage * | Dropdown | Prospecting, Qualification, Needs Analysis, Value Proposition, Proposal/Price Quote, Negotiation/Review, Closed Won, Closed Lost | Prospecting |
| Amount ($) * | Number | Greater than 0 | |
| Probability (%) | Number | 0 to 100 | 10 |
| Expected close date * | Date | | today + 30 days |
| Assigned to | Dropdown | Users | signed-in user |
| Next step | Text | | |

- **Status** is not entered. It is derived from the stage: Closed Won → Closed Won, Closed Lost → Closed Lost, any other stage → Open.
- Changing **Sales stage** in the form sets **Probability** to the stage default (it can then be overtyped). If Probability is left empty on save, the stage default is used.

| Stage | Default probability |
|---|---|
| Prospecting | 10% |
| Qualification | 20% |
| Needs Analysis | 30% |
| Value Proposition | 45% |
| Proposal/Price Quote | 60% |
| Negotiation/Review | 80% |
| Closed Won | 100% |
| Closed Lost | 0% |

### 5.5 Meetings
| Field | Type | Rules / options | Default |
|---|---|---|---|
| Subject * | Text | | |
| Start * | Date and time | | tomorrow 10:00 |
| Duration (minutes) | Dropdown | 15, 30, 45, 60, 90, 120 | 60 |
| Status | Dropdown | Planned, Held, Not Held | Planned |
| Account | Dropdown of accounts | | blank |
| Related opportunity | Dropdown "Opportunity (Account)" | | blank |
| Location | Text | | |
| Organizer | Dropdown | Users | signed-in user |
| Invitees (comma separated) | Text | Names separated by commas | |

- The organizer is the meeting's owner.
- **My role** (shown on the list and record page): **Organizer** when the signed-in user is the organizer; **Guest** when the signed-in user is listed as an invitee and someone else organizes; otherwise "—".

### 5.6 Calls
| Field | Type | Rules / options | Default |
|---|---|---|---|
| Subject * | Text | | |
| Date and time * | Date and time | | next half hour from now |
| Direction | Dropdown | Outbound, Inbound | Outbound |
| Status | Dropdown | Planned, Held, Not Held | Planned |
| Account | Dropdown of accounts | | blank |
| Contact | Dropdown of all contacts, shown as "Contact name (Account)" when the contact has an account | | blank |
| Assigned to | Dropdown | Users | signed-in user |
| Outcome | Multi-line text | | |

### 5.7 Tasks
| Field | Type | Rules / options | Default |
|---|---|---|---|
| Subject * | Text | | |
| Due date * | Date | | today + 7 days |
| Priority | Dropdown | High, Medium, Low | Medium |
| Status | Dropdown | Not Started, In Progress, Completed, Deferred | Not Started |
| Account | Dropdown of accounts | | blank |
| Assigned to | Dropdown | Users | signed-in user |
| Description | Multi-line text | | |

- Setting Status to **Completed** records a **Completed** date and time. Changing it back to another status removes it.
- A task is **Overdue** when it is not Completed and its due date is before today.

---

## 6. Creating and editing records

### 6.1 Opening the form
- **New <record>** button at the top right of each module list (for example "New account", "New opportunity").
- **New <record>** buttons in related lists on a record page; they prefill the link (section 8.3).
- **New opportunity / New meeting / New task** on the Home page.
- **Edit** on a record page opens the same form filled with the record's values.

The form opens as a dialog titled "New <record>" or "Edit <record>" with **Cancel** and **Save**. Escape, Cancel or × closes it without saving.

### 6.2 Validation
Validation runs when **Save** is clicked. Each invalid field shows a message under it and is marked invalid; a red summary appears at the top: "Please fix 1 error below." or "Please fix N errors below." The cursor moves to the first invalid field. Nothing is saved until all errors are fixed.

| Rule | Message |
|---|---|
| Required field empty | "<Field label> is required." (label without the bracket part, for example "Account name is required.", "Amount is required.", "Last name is required.") |
| Email format | Enter a valid email address. |
| Website format | Enter a full web address starting with http:// or https://. |
| Phone format | Enter a valid phone number. |
| Employees negative or not a number | Enter a number of 0 or more. |
| Probability outside 0–100 | Enter a number between 0 and 100. |
| Amount 0 or less | Amount must be greater than 0. |
| Date or date-time not valid | Enter a valid date. |
| Account name already used by another account | An account named "<name>" already exists. |

### 6.3 After saving
| Action | Result |
|---|---|
| Create | Message "<Record> "<name>" created." The new record's page opens. The module count in the rail increases by 1 |
| Edit | Message "<Record> "<name>" updated." The record page refreshes with the new values |

Messages appear at the bottom right for 4 seconds. On create the record gets a **Created** date; every save updates **Modified**.

---

## 7. Lists

Each module list shows a heading with the record count ("6 records"), the **New** button, a toolbar and a table.

### 7.1 Toolbar
| Control | Behaviour |
|---|---|
| Search box ("Search <module>…") | Filters as you type. Case-insensitive match on the record's text fields, its account name and (meetings) invitee names |
| Filter dropdowns | Per module, see 7.3. The first option shows all ("All statuses", "All types"…) |
| Owner dropdown | **All records** or **My records** (records assigned to the signed-in user; for meetings, the organizer) |
| Clear | Resets search, filters, owner and sorting |

Above the table: "N records" when nothing is filtered out, otherwise "Showing X of Y records".

### 7.2 Sorting
Clicking a column heading sorts ascending; clicking it again sorts descending. The sorted column shows ▲ or ▼. Search, filter and sort settings last until the page is reloaded.

Default order (before any column is clicked):
| Module | Default order |
|---|---|
| Accounts | Name A–Z |
| Contacts | Modified, newest first |
| Leads | Created, newest first |
| Opportunities | Expected close date, earliest first |
| Meetings | Start, latest first |
| Calls | Date, latest first |
| Tasks | Due date, earliest first |

### 7.3 Columns and filters
| Module | Columns | Filters |
|---|---|---|
| Accounts | Name, Industry, Type, City, Assigned to, Modified | Type, Industry |
| Contacts | Name, Title, Account, Email, Phone, Assigned to, Modified | Account |
| Leads | Name, Company, Status, Lead source, Assigned to, Created | Status, Lead source |
| Opportunities | Name, Account, Sales stage, Status, Amount, Close date, Assigned to | Status (Open, Closed Won, Closed Lost), Sales stage |
| Meetings | Subject, Account, Status, Start, My role, Organizer | Status |
| Calls | Subject, Account, Contact, Direction, Status, Date, Assigned to | Status, Direction |
| Tasks | Subject, Account, Priority, Status, Due date (with Overdue label), Assigned to | Status, Priority |

Names and account names are links to the record pages.

### 7.4 Empty states
| Situation | Shown |
|---|---|
| Module has no records | "No <module> yet." and a button "Create the first <record>" |
| Search or filters match nothing | "No records match your search or filters." and a **Clear filters** button |

---

## 8. Record page

### 8.1 Header
Breadcrumb, record name and a summary line: opportunities show account and amount; contacts show title and account; leads show company and status.

Buttons:
| Button | Shown when | Action |
|---|---|---|
| Convert | Lead whose status is not Converted | Lead conversion (section 9.1) |
| Mark complete | Task whose status is not Completed | Sets status to Completed (section 9.2) |
| Edit | Always | Opens the edit form |
| Delete | Always | Delete with confirmation (section 10) |

### 8.2 Details
All fields of the module (section 5), plus:
- Opportunities: **Status** (after Sales stage).
- Meetings: **My role**.
- Tasks: **Completed** date when completed.
- Converted leads: links to the **Converted contact**, **Converted account** and **Converted opportunity**.
- **Created** (records created in the app and leads) and **Modified** for every record.

Empty fields show "—". Website on accounts shows "—" when not set.

### 8.3 Related lists
| Record | Related lists | New button prefills |
|---|---|---|
| Account | Contacts, Opportunities, Meetings, Calls, Tasks linked to the account | Account |
| Contact | Meetings where the contact's name is an invitee; Calls where the contact is the call's contact | Meetings: account and invitee; Calls: account and contact |
| Opportunity | Meetings whose related opportunity is this one | Account and related opportunity |

Each list shows its count in the heading, for example "Contacts (2)", or "No contacts yet." when empty.

### 8.4 Activity
The **Activity** panel lists the record's history, newest first. Each entry shows what happened, who did it and when.
| Event | Entry text |
|---|---|
| Created | "<Record> created" |
| Edited | "N fields updated", with one line per changed field: "<Field>: <old value> → <new value>" (empty values show "(empty)") |
| Lead converted | "Lead converted to contact <name> at <account>, with opportunity <name>" (parts shown only when they apply) |
| Task completed | "1 field updated" with "Status: <old> → Completed" |

Saving without changing anything adds no entry. Seed records start with one "created" entry.

---

## 9. Workflows

### 9.1 Convert a lead
1. Open a lead that is not Converted and click **Convert**.
2. The dialog has three parts:
   - **Contact:** always created from the lead's first name, last name, title, email, phone and owner.
   - **Account:** dropdown. Default is the existing account whose name matches the lead's company (ignoring case); if none matches, the default is "Create new account: <company>". The list also has every existing account and "No account".
   - **Opportunity:** checkbox "Create an opportunity" (unchecked). Fields: Opportunity name (default "<Company> - New Business"), Amount ($), Expected close date (default today + 60 days).
3. Click **Convert**.

Validation when "Create an opportunity" is checked:
| Rule | Message |
|---|---|
| Name empty | Opportunity name is required. |
| Amount empty or 0 or less | Amount must be greater than 0. |
| Close date empty | Expected close date is required. |
| "No account" selected | An opportunity needs an account. Choose or create an account above. |
A summary "Please fix the errors below." appears at the top.

Result:
- A new account (type Prospect, same owner) is created when "Create new account" was chosen.
- The contact is created and linked to the chosen account (no account when "No account" was chosen).
- The opportunity, when requested, is created at stage Prospecting (10%) with next step "Follow up after lead conversion".
- The lead's status becomes **Converted**, the Convert button disappears and the lead shows links to the new records.
- Message "Lead "<name>" converted." and the new contact's page opens.
- Each new record gets a "created" activity entry; the lead gets a "converted" entry.

Note: setting a lead's Status to Converted in the edit form only changes the status and hides **Convert**; it does not create a contact, account or opportunity.

### 9.2 Complete a task
**Mark complete** on a task page sets Status to Completed, records the Completed date, shows "Task "<name>" marked as completed." and hides the button. Choosing Completed in the edit form has the same effect.

### 9.3 Global search
Type in **Search all records** in the top bar and press Enter. The results page "Search results for "<text>"" shows the number of records found and one table per module with matches (same matching as list search). With no matches: "No records match "<text>"."

---

## 10. Deleting records

1. Click **Delete** on a record page.
2. A dialog "Delete <record>?" says "This permanently deletes <name>." For accounts it adds: "Related contacts, opportunities and activities are kept but no longer linked to this account."
3. **Delete** confirms; **Cancel**, × or Escape keeps the record.

After deleting: message "<Record> "<name>" deleted.", the module list opens and the count decreases by 1.

| Deleted record | Effect on related records |
|---|---|
| Account | Contacts, opportunities, meetings, calls and tasks linked to it are kept; their Account becomes empty |
| Opportunity | Meetings that referenced it keep existing; their Related opportunity becomes empty |
| Contact | Calls and meetings keep the contact's name as text (call Contact, meeting Invitees) |
| Any other | No effect on other records |

There is no undo and no recycle bin. A deleted record can only come back through **Reset demo data**.

---

## 11. Data, persistence and seed

### 11.1 Persistence
- Changes are saved in the browser and remain after reload and sign-out.
- Each browser profile has its own data. A fresh browser (for example each Virtuoso execution) starts with the seed below.
- **Reset demo data** (user menu) asks "Reset demo data?" ("All changes are removed and the sample data is restored with dates relative to today.") and on **Reset data** restores the seed, shows "Demo data has been reset." and opens Home.

### 11.2 Seed data
All dates are relative to the day the data is seeded (first visit or last reset). Unless stated, every record is assigned to **Sam Carter**.

**Accounts (2)**
| Name | Industry | Type | City | Website | Phone | Employees |
|---|---|---|---|---|---|---|
| AtoZ Co Ltd | Technology | Customer | London, UK | https://www.atoz-co-ltd.example.com | +44 20 7946 0321 | 850 |
| Regent Solutions Corp | Financial Services | Prospect | Manchester, UK | not set | +44 161 496 0754 | 1,200 |

**Opportunities (6)**
| Name | Account | Stage | Status | Amount | Probability | Expected close |
|---|---|---|---|---|---|---|
| Cloud Migration Initiative | AtoZ Co Ltd | Proposal/Price Quote | Open | $185,000 | 60% | today + 21 days |
| Security Audit Package | AtoZ Co Ltd | Needs Analysis | Open | $64,000 | 30% | today + 75 days |
| SaaS Renewal FY26 | AtoZ Co Ltd | Closed Won | Closed Won | $120,000 | 100% | within the current quarter, up to 4 days ago |
| Data Analytics Platform | Regent Solutions Corp | Value Proposition | Open | $240,000 | 45% | today + 12 days |
| Compliance Module | Regent Solutions Corp | Qualification | Open | $48,000 | 20% | today + 110 days |
| Q2 Support Contract | Regent Solutions Corp | Closed Lost | Closed Lost | $30,000 | 0% | 20 days before the current quarter started |

**Contacts (4)**
| Name | Title | Account | Email |
|---|---|---|---|
| Helena Ostrowski | CTO | AtoZ Co Ltd | helena.ostrowski@atoz-co-ltd.example.com |
| Marcus Delacroix | VP Operations | AtoZ Co Ltd | marcus.delacroix@atoz-co-ltd.example.com |
| Priya Venkataraman | Finance Director | Regent Solutions Corp | priya.v@regent-solutions.example.com |
| Tobias Worthington | Head of IT | Regent Solutions Corp | t.worthington@regent-solutions.example.com |

**Leads (3)**
| Name | Company | Status | Lead source | Created |
|---|---|---|---|---|
| Daniel Okafor | Brightline Logistics | New | Web Site | 2 days ago |
| Sofia Marchetti | Verano Foods | In Process | Trade Show | 6 days ago |
| Kenji Watanabe | Halden Robotics | New | Referral | 9 days ago |

**Meetings (5)**
| Subject | Account | Status | Start | Organizer | Invitees | Related opportunity |
|---|---|---|---|---|---|---|
| Q3 Business Review | AtoZ Co Ltd | Planned | today 15:30 (60 min) | Sam Carter | Helena Ostrowski, Marcus Delacroix | Cloud Migration Initiative |
| Cloud Migration Kick-off | AtoZ Co Ltd | Planned | today + 3 days 10:00 (90 min) | Sam Carter | Helena Ostrowski | Cloud Migration Initiative |
| Platform Demo | Regent Solutions Corp | Planned | today + 6 days 14:00 (60 min) | Alex Rivera | Sam Carter, Tobias Worthington, Priya Venkataraman | Data Analytics Platform |
| Security Audit Scoping | AtoZ Co Ltd | Held | 4 days ago 11:00 | Sam Carter | Marcus Delacroix | Security Audit Package |
| Compliance Workshop | Regent Solutions Corp | Held | 16 days ago 13:00 (120 min) | Alex Rivera | Sam Carter, Priya Venkataraman | Compliance Module |

**Calls (3, all Held, Outbound)**
| Subject | Account | Contact | Date | Outcome |
|---|---|---|---|---|
| Renewal Negotiation Call | AtoZ Co Ltd | Helena Ostrowski | 10 days ago | Agreed FY26 renewal terms |
| Security Audit Discovery Call | AtoZ Co Ltd | Marcus Delacroix | 6 days ago | Scoping session booked |
| Compliance Requirements Call | Regent Solutions Corp | Priya Venkataraman | 11 days ago | Formal requirements document requested |

**Tasks (6)**
| Subject | Account | Due | Priority | Status |
|---|---|---|---|---|
| Send revised Cloud Migration proposal to Helena | AtoZ Co Ltd | 3 days ago (Overdue) | High | In Progress |
| Share Security Audit scoping notes with Marcus | AtoZ Co Ltd | 7 days ago (Overdue) | Medium | Not Started |
| Chase Regent PO for Data Analytics Platform | Regent Solutions Corp | 12 days ago (Overdue) | High | Not Started |
| Send Q3 Business Review follow-up summary | AtoZ Co Ltd | today + 2 days | Medium | Not Started |
| Book Compliance Module discovery call | Regent Solutions Corp | today + 9 days | Low | Not Started |
| Log SaaS Renewal FY26 handover notes | AtoZ Co Ltd | 2 days ago | Medium | Completed |

**Expected Home values on fresh seed (signed in as Sam):** Open pipeline $537,000 (4 opportunities); Meetings today 1 (Q3 Business Review at 15:30); Overdue tasks 3; Closing in 30 days 2 ($425,000). Sam's **My records** on Meetings shows 3 (the two meetings organized by Alex are excluded).

---

## 12. Business rules

| ID | Rule |
|---|---|
| BR-01 | Users must sign in before using the CRM (except with `autologin` in the link). |
| BR-02 | A record cannot be saved while any required field is empty or any field fails validation. |
| BR-03 | Account names are unique, ignoring upper and lower case. |
| BR-04 | Opportunity status is derived from the sales stage and cannot be set directly. |
| BR-05 | Opportunity probability defaults from the stage; an empty probability is saved as the stage default. |
| BR-06 | An opportunity must belong to an account. |
| BR-07 | A task is Overdue when not Completed and due before today; completing it records the completion time. |
| BR-08 | Deleting an account keeps its related records and removes their account link. Deleting an opportunity removes it from related meetings. |
| BR-09 | Every create, change and conversion is recorded in the record's activity with user and time. Saving with no changes records nothing. |
| BR-10 | Converting a lead always creates a contact, sets the lead to Converted and hides Convert; an opportunity can only be created with an account. |
| BR-11 | "My records" means assigned to the signed-in user (organizer for meetings). |
| BR-12 | Data persists per browser until Reset demo data, which restores the seed with dates relative to the reset day. |

---

## 13. Element hooks for automation

Every field has a visible label tied to its input, so steps can target fields by label text. Stable ids:

| Element | Id / selector |
|---|---|
| Sign-in email, password, keep signed in, button, error | `#login-email`, `#login-password`, `#login-remember`, `#login-submit`, `#login-error` |
| Module navigation | `#nav-home`, `#nav-accounts`, `#nav-contacts`, `#nav-leads`, `#nav-opportunities`, `#nav-meetings`, `#nav-calls`, `#nav-tasks` |
| Global search | `#global-search` (press Enter) |
| List: New button | `#btn-new` |
| List: search, filters, owner, clear, count | `#list-search`, `#filter-<field>` (for example `#filter-status`, `#filter-stage`), `#filter-owner`, `#btn-clear-filters`, `#list-count` |
| List table and rows | `table[data-module="<module>"]`, rows `tr[data-id]`; record links `.rowlink` |
| Sortable column heading | `[data-sort="<n>"]`, where n is the column position starting at 0 (for example Opportunities Amount is `[data-sort="4"]`); the sorted heading has `aria-sort` |
| Form fields | `#f-<field>`: `#f-name`, `#f-first`, `#f-last`, `#f-company`, `#f-title`, `#f-account`, `#f-email`, `#f-phone`, `#f-website`, `#f-city`, `#f-employees`, `#f-industry`, `#f-type`, `#f-status`, `#f-source`, `#f-stage`, `#f-amount`, `#f-prob`, `#f-close`, `#f-next`, `#f-start`, `#f-mins`, `#f-location`, `#f-organizer`, `#f-invitees`, `#f-opp`, `#f-date`, `#f-direction`, `#f-contact`, `#f-outcome`, `#f-due`, `#f-priority`, `#f-description`, `#f-owner` |
| Field error / form summary | `#err-<field>`, `#form-errors` |
| Save / Cancel | `#btn-save`, `#btn-cancel` |
| Record actions | `#btn-edit`, `#btn-delete`, `#btn-convert`, `#btn-complete` |
| Confirm dialog | `#confirm-ok`, `#confirm-cancel` |
| Convert dialog | `#cv-account`, `#cv-opp`, `#cv-opp-name`, `#cv-opp-amount`, `#cv-opp-close`, `#btn-convert-save`; errors `#err-cv-opp-name`, `#err-cv-opp-amount`, `#err-cv-opp-close` |
| Related-list New buttons | `[data-new="<module>"]` inside the related section |
| Activity list | `#activity` (record page), `#recent-activity` (Home) |
| Message (toast) | `#toast` |
| User menu | `#user-menu-btn`, `#menu-reset`, `#menu-signout`, `#menu-kb-app`, `#menu-kb-assist`, `#menu-kb-directives` |
| Knowledge base links and viewer | `#kb-app`, `#kb-assist`, `#kb-directives`, `#kb-viewer` (`data-state="done"` when loaded), `#kb-download`, `#kb-raw` |

Date fields are the browser's date pickers (`type=date` and `type=datetime-local`). In automation, set them with the value format YYYY-MM-DD (date) or YYYY-MM-DDThh:mm (date and time); when typing by hand, use the format the browser displays. Displayed dates use British day-month order, for example "Mon, 26 Oct", "Mon, 26 Oct, 15:30" or "26 Oct 2026" (exact punctuation can vary slightly by browser).

---

## 14. Not supported

The application does not provide: data import or export, bulk edit or bulk delete, pagination, reports, email sending, file attachments, password reset, role-based permissions, undo or a recycle bin, editing or deleting activity entries, or remembering list search, filter and sort after a reload. Requirements should not assume these features.

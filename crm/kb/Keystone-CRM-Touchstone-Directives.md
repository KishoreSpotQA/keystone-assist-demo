# Keystone CRM: Touchstone Directives

> Create these as Touchstone directives. **Do not upload this file as a source.** They go with the two Keystone CRM sources: `Keystone-CRM-Application-KB-Source.md` (CRM screens) and `Keystone-CRM-Assist-KB-Source.md` (Assist chatbot).

**Application:** Keystone CRM demo app, https://kishorespotqa.github.io/keystone-assist-demo/crm/
**Environment variables (Virtuoso Environment Manager):**

| Variable | Value | Sensitive |
|---|---|---|
| `crmUrl` | https://kishorespotqa.github.io/keystone-assist-demo/crm/ | No |
| `crmUser` | sam.carter@keystone.example | No |
| `crmPassword` | Keystone#2026 | Yes |

**Set at:** project level, before the first generation

## Summary by category

| Touchstone tab | Conventions | Instructions | Templates |
|---|---|---|---|
| **Analyst directives** | none | A1 One behaviour per requirement · A2 Exact messages and labels · A3 Grounded chat answers · A4 Read-only means unchanged data | A5 Requirement layout (optional) |
| **Architect directives** | none | B1 Sign in and start state · B2 Wait for the finished answer · B3 Generated test data · B4 Updating existing journeys | none |
| **Autopilot directives** | C1 Element identification | none | none |

To add one: open the tab, click **New directive**, choose the type (Convention, Instruction or Template), paste the name and text, then save and enable it.

---

# Analyst directives

## Instructions

### A1. `One behaviour per requirement`
```
Produce one requirement per behaviour, not one per screen. For the CRM: one per module's create, edit, delete and list behaviour, one per validation rule group, and one per workflow (lead conversion, task completion, global search, reset demo data, sign-in). For Assist: one per question type in section 6 of the Assist source, plus one for read-only refusals and one for out-of-scope questions. Do not create requirements for the Knowledge base links or the Demo controls (fault injection); both are marked as demo-only in the sources.
```
**Fixes:** a few broad requirements ("user can manage accounts", "chat works") that hide what is covered, and requirements for demo-only features.

### A2. `Exact messages and labels`
```
Where the source gives an exact message, quote it word for word in the acceptance criteria: validation messages, sign-in errors, toast messages and dialog text. Refer to fields by their label and their id from the element hooks table (for example "Account name" / #f-name). Use the seed values and the expected Home values from the source, not invented data.
```
**Fixes:** paraphrased assertions that pass against the wrong text.

### A3. `Grounded chat answers`
```
For Assist questions about CRM data, the acceptance criteria must name the records, counts and amounts the answer has to contain, taken from section 8.2 of the Assist source (signed in as Sam Carter). For questions with a time window (this quarter, next N days, today), assert at least one record inside the window is present and at least one named record outside it is absent. A "not found" reply when matching records exist is a failure. Never require an exact sentence: Assist words its answers differently each time.
```
**Fixes:** keyword-only checks. "No open opportunities found" contains "opportunities" and would pass a keyword check while the answer is wrong.

### A4. `Read-only means unchanged data`
```
For create, update or delete requests sent to Assist, the acceptance criteria must assert both that Assist refuses because it is read-only and that the affected module list shows the same record count afterwards.
```
**Fixes:** a polite refusal that hides a real data change.

## Templates

### A5. `Requirement layout` (optional)
```
Scope / Source (CRM application or Assist) / Pre-conditions / Test data / Acceptance criteria / Not covered / Source references
```
**Fixes:** requirements with no stated limits. "Not covered" makes each requirement say what it leaves out.

---

# Architect directives

## Instructions

### B1. `Sign in and start state`
```
Start every journey with the same reusable checkpoint "Sign in to Keystone CRM": navigate to $crmUrl, write $crmUser in Email (#login-email), write $crmPassword in Password (#login-password), click Sign in (#login-submit), and confirm the Home page shows "Open pipeline". Do not use #autologin. Each execution runs in a fresh browser, so the data starts as the seed in the source with dates relative to today: never put fixed calendar dates in steps or data tables. Each journey must be independent and must not rely on records created by another journey.
```
**Fixes:** journeys that skip the sign-in page, put credentials in steps, drift with the calendar, or break when run in a different order.

### B2. `Wait for the finished answer`
```
To ask Assist a question, make sure the Assist panel is open (if #assist-input is not visible, click the Assist button, #assist-toggle), write the question in #assist-input and click Send (#assist-send). Then wait for the element #assist-last-answer before reading the answer, read it from that element only, store it in a variable and assert on the variable. Never use a fixed pause. Where an answer must match CRM data, read the expected value from the CRM screen first, store it, then ask Assist.
```
**Fixes:** assertions against a half-streamed answer and flaky fixed waits.

### B3. `Generated test data`
```
Do not use a test data table for values typed into create and edit forms. In a checkpoint right after sign-in, generate unique values at run time with Store value steps using a JavaScript expression (for example a name built from a fixed prefix and the current timestamp, a random whole number, or a random choice from a short list), and reuse the stored variables in later steps and assertions. Write expected messages with the stored variable in place of the record name, for example: Account "$accountName" created. The only exception is a duplicate-name check, which deliberately enters the name of a seed record.
```
**Fixes:** journeys that fail because a fixed name already exists, and expected messages with an empty name.

### B4. `Updating existing journeys`
```
When a journey for the requirement already exists, address every one of its existing checkpoints explicitly as keep, modify or remove. An existing checkpoint that uses #autologin must be modified into the "Sign in to Keystone CRM" checkpoint.
```
**Fixes:** the "checkpoint … is not addressed" error that stops a proposal from being created.

---

# Autopilot directives

## Conventions

### C1. `Element identification`
```
Identify elements by the ids in the element hooks tables of the sources (#login-email, #login-password, #login-submit, #nav-<module>, #btn-new, #f-<field>, #btn-save, #btn-edit, #btn-delete, #confirm-ok, #assist-input, #assist-last-answer) in preference to text or position. Leave date fields at their defaults unless the test needs a specific date. When one is needed, set the value as YYYY-MM-DD (date) or YYYY-MM-DDThh:mm (date and time), and if typing does not set it, set the value with a script step instead. When fixing a failing chat assertion, assert on record names, counts or the word read-only, never on a sentence copied from one run.
```
**Fixes:** brittle steps, and "fixes" that pin one run's wording and fail on the next.

---

## Setup checklist
1. Upload the two sources separately and wait for both to be **ready**.
2. Add the directives tab by tab: **Analyst**, then **Architect**, then **Autopilot**.
3. Check for name clashes with organisation-level directives: a project directive with the same name replaces the organisation one.
4. Click **Preview prompt** on each tab and confirm every directive is **saved and enabled**.
5. Add the environment variables `crmUrl`, `crmUser` and `crmPassword` (sensitive) in Environment Manager and select that environment for executions.
6. Generate requirements per source, then journeys (a sample Architect prompt is below).

## Verifying they landed

| Directive | What to look for in the output |
|---|---|
| A1 One behaviour per requirement | Separate requirements per module action, validation group and workflow; none for the Knowledge base links or Demo controls |
| A2 Exact messages and labels | Quoted strings such as "Account name is required." and field ids such as #f-name |
| A3 Grounded chat answers | Named records (Data Analytics Platform, Cloud Migration Initiative…) and "must not appear" records for time windows |
| A4 Read-only means unchanged data | A check of the Leads or Contacts count after "Delete all my leads" or "Create a new contact named John Test" |
| A5 Requirement layout | Every requirement has a "Not covered" section |
| B1 Sign in and start state | Every journey starts with "Sign in to Keystone CRM" using $crmUser and $crmPassword; no #autologin; no fixed dates |
| B3 Generated test data | Store value steps that build $accountName and similar variables; no test data table for form values; messages read Account "$accountName" … |
| B4 Updating existing journeys | No "Creation failed … not addressed" errors in Proposals preview |
| B2 Wait for the finished answer | A wait for #assist-last-answer before every chat assertion |
| C1 Element identification | Steps that use #f-… and #btn-… ids |

## Sample Architect prompt (Accounts requirement)
Paste in Architect chat with the **CRM Accounts Module Management** requirement selected. For other modules, change the fields and messages using that module's section of the CRM application source.

```
Generate the minimum set of journeys that fully covers this requirement, structured into small, reusable checkpoints. Follow the project directives for sign-in, generated test data and existing journeys.

Generate these values after sign-in with Store value expressions:
- $accountName = "Auto Account " followed by the current timestamp
- $editedName = $accountName followed by " Updated"
- $website = "https://www." followed by a random lowercase word and ".example.com"
- $phone = "+44 20 7946 " followed by a random 4-digit number
- $city = a random value from London, Manchester, Leeds, Bristol
- $employees = a random whole number between 10 and 5000
Pick Industry and Type from their dropdown options.

Coverage:
1. Create an account with all fields. Confirm the message Account "$accountName" created. and that the record page shows the entered values.
2. Edit the account: change the name to $editedName, and change Type and City. Confirm the message Account "$editedName" updated. and that Activity lists the changed fields.
3. Validation: empty Account name shows "Account name is required."; invalid website shows "Enter a full web address starting with http:// or https://."; invalid phone shows "Enter a valid phone number."; negative employees shows "Enter a number of 0 or more."
4. Duplicate name: enter atoz co ltd (a seed account, in different letter case) and confirm the message An account named "atoz co ltd" already exists.
5. List: search for $accountName and confirm exactly one result; filter by Type; sort by Name.
6. Delete: Cancel keeps the account; Delete removes it, shows the message Account "$accountName" deleted. and the Accounts count goes down by 1.

Quote messages exactly as written in the source.
```

## Open item to expect
The Assist source leaves one question open (OI-01): should "attended as a guest" include Planned meetings? A good generation raises it as a question or states its assumption. That is a strength to show in the demo, not a defect.

Directives guide generation; they are not enforced rules, and output varies between runs. Check the output against the table above.

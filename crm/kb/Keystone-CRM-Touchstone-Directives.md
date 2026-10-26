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
| **Architect directives** | none | B1 Start state and test data · B2 Wait for the finished answer | none |
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

### B1. `Start state and test data`
```
Start every journey at crmUrl and sign in through the sign-in page: enter the email from the environment variable crmUser and the password from crmPassword in the Email (#login-email) and Password (#login-password) fields, then click Sign in (#login-submit) and confirm the Home page shows "Good morning", "Good afternoon" or "Good evening" with the user's first name. Do not use #autologin. Each execution runs in a fresh browser, so the data starts as the seed in the source with dates relative to today: never put fixed calendar dates in steps or data tables. Journeys that create records must use unique names so they do not clash with seed records, and must not depend on records created by another journey.
```
**Fixes:** journeys that skip the sign-in page, put credentials in steps, drift with the calendar, or break when run in a different order.

### B2. `Wait for the finished answer`
```
To ask Assist a question, make sure the Assist panel is open (if #assist-input is not visible, click the Assist button, #assist-toggle), write the question in #assist-input and click Send (#assist-send). Then wait for the element #assist-last-answer before reading the answer, read it from that element only, store it in a variable and assert on the variable. Never use a fixed pause. Where an answer must match CRM data, read the expected value from the CRM screen first, store it, then ask Assist.
```
**Fixes:** assertions against a half-streamed answer and flaky fixed waits.

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
6. Generate requirements per source, then journeys.

## Verifying they landed

| Directive | What to look for in the output |
|---|---|
| A1 One behaviour per requirement | Separate requirements per module action, validation group and workflow; none for the Knowledge base links or Demo controls |
| A2 Exact messages and labels | Quoted strings such as "Account name is required." and field ids such as #f-name |
| A3 Grounded chat answers | Named records (Data Analytics Platform, Cloud Migration Initiative…) and "must not appear" records for time windows |
| A4 Read-only means unchanged data | A check of the Leads or Contacts count after "Delete all my leads" or "Create a new contact named John Test" |
| A5 Requirement layout | Every requirement has a "Not covered" section |
| B1 Start state and test data | Journeys open crmUrl, sign in with $crmUser and $crmPassword through the sign-in page, and have no fixed dates in data tables |
| B2 Wait for the finished answer | A wait for #assist-last-answer before every chat assertion |
| C1 Element identification | Steps that use #f-… and #btn-… ids |

## Open item to expect
The Assist source leaves one question open (OI-01): should "attended as a guest" include Planned meetings? A good generation raises it as a question or states its assumption. That is a strength to show in the demo, not a defect.

Directives guide generation; they are not enforced rules, and output varies between runs. Check the output against the table above.

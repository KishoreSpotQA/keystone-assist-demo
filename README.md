# Keystone Assist Demo

Two self-contained demo apps for showing AI chat regression testing with Virtuoso. No build step, no backend, no API keys.

| App | Path | Use as |
|---|---|---|
| Keystone Assist Website: landing page with the assistant bar at the bottom centre | `website/index.html` | `appUrl` |
| Keystone CRM: a working demo CRM (sign-in, create, edit, delete, search, filters, lead conversion) with a docked assistant | `crm/index.html` | `crmUrl` |

Keystone CRM is fictional. All companies, people and figures are sample data.

## How it works
- The assistant answers from built-in seed data (2 accounts, 6 opportunities, 4 contacts, 3 leads, 5 meetings, 3 calls, 6 tasks). All dates are relative to the day the data is seeded. The website resets on every load; the CRM keeps changes in the browser until **Reset demo data**.
- Answers stream in and are worded differently each time, like an LLM.
- The assistant is read-only and refuses create, update or delete requests.

## Keystone CRM features (crm/index.html)
- **Sign-in:** demo users `sam.carter@keystone.example` (Sales Representative) and `alex.rivera@keystone.example` (Sales Manager), password `Keystone#2026`. Add `autologin` to the link (for example `#autologin` or `#admin.autologin`) to skip sign-in and open as Sam.
- **Modules:** Accounts, Contacts, Leads, Opportunities, Meetings, Calls, Tasks. Each list has a **New** button, search, filters, an All/My records switch and sortable columns.
- **Records:** create and edit in a form with validation (required fields, email, web address, phone, numbers, duplicate account names), delete with confirmation, related lists with prefilled **New** buttons, and an activity timeline that records every change.
- **Workflows:** convert a lead into a contact, account and opportunity; mark a task complete; global search across all records.
- **Data:** changes are saved in the browser. **Reset demo data** in the user menu restores the seed. A fresh browser (such as each Virtuoso run) always starts from the seed.
- **Assist** answers from the live data, so records you create appear in its answers.

### CRM test hooks
| Element | Hook |
|---|---|
| Sign-in | `#login-email`, `#login-password`, `#login-submit`, error `#login-error` |
| Module navigation | `#nav-accounts`, `#nav-contacts`, ... |
| New record button | `#btn-new` |
| Form fields | `#f-<field>` (for example `#f-name`, `#f-last`, `#f-amount`), errors `#err-<field>`, summary `#form-errors` |
| Save / Cancel | `#btn-save`, `#btn-cancel` |
| Record actions | `#btn-edit`, `#btn-delete`, `#btn-convert`, `#btn-complete`; confirm `#confirm-ok` |
| List search and filters | `#list-search`, `#filter-<field>`, `#filter-owner`, count `#list-count` |
| Toast message | `#toast` |
| User menu | `#user-menu-btn`, `#menu-reset`, `#menu-signout` |

## Test hooks
| Element | Hook |
|---|---|
| Question input | `#assist-input` (aria-label "Ask Assist") |
| Send button | `#assist-send` |
| Finished answer | `#assist-last-answer` (set only once streaming ends; `data-state="done"`) |
| Status | `#assist-status` (`data-busy`) |
| CRM list tables | `table[data-module="<module>"]` (CRM app) |

## Demo controls and fault injection
Add `#admin` to a link to show the Demo controls panel. Faults can also be set from the link and combined with dots, for example `#admin.offtopic.slow`:

| Token | Effect |
|---|---|
| `offtopic` | Answers the weather question instead of declining |
| `mutation` | Claims to delete or create records, and changes the data |
| `noretrieval` | Data questions return a not-found reply |
| `window` | Pipeline filters ignore the quarter and 30-day windows |
| `empty` | Blank answers |
| `slow` | 9 to 14 second delay before each answer |
| `relabel` | Renames the chat input and Send button (self-healing demo) |
| `shadow` | Renders the assistant inside a shadow root |

## Hosting
Enable GitHub Pages on the `main` branch (root). The site index links to both apps.

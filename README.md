# Keystone Assist Demo

Two self-contained demo apps for showing AI chat regression testing with Virtuoso. No build step, no backend, no API keys.

| App | Path | Use as |
|---|---|---|
| Keystone Assist Website: landing page with the assistant bar at the bottom centre | `website/index.html` | `appUrl` |
| Keystone CRM Assist: CRM workspace with module lists and a docked assistant | `crm/index.html` | `crmUrl` |

Keystone CRM is fictional. All companies, people and figures are sample data.

## How it works
- The assistant answers from built-in seed data (2 accounts, 6 opportunities, 4 contacts, 3 leads, 5 meetings, 3 calls, 6 tasks). All dates are relative to the day the page is opened, and every page load resets the data.
- Answers stream in and are worded differently each time, like an LLM.
- The assistant is read-only and refuses create, update or delete requests.

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

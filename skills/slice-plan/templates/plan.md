# <Slice>: plan
Design: <docs.design>/<slug>.md · Branch: slice/<slug>

| # | Task | Owner | Model | Files / symbols to start from | Depends | Done when | Status |
|---|---|---|---|---|---|---|---|
| 1 | Contract + gen | engineer | | | – | `gen` clean, stubs compile | todo |
| 2a | Logic + tests | engineer | | | 1 | targeted `test` green | todo |
| 2b | UI from fixtures | ui-engineer | opus | | 1 | every state renders, screenshot checked | todo |
| 3 | Wire + e2e | engineer or ui-engineer | | | 2a, 2b | targeted `e2e` green | todo |
| 4 | Review | reviewer | opus | | 2a | no CONFIRMED finding open | todo |

## Log

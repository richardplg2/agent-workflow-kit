# <Feature>
Status: draft | approved (<YYYY-MM-DD>) · Lane: medium · Base: <rev>

## Why
One or two lines: the user problem and the outcome.

## Contract
Exact changes: endpoints/messages/schema, every field (type, required), errors and status codes, migration (new file, reversible?).

## Behaviour
Rules, edge cases, failure cases (retry, partial write, concurrent edit), permissions.

## Acceptance (each provable by a test)
- [ ] …

## Tasks
| # | Task | Owner | Model | Files / symbols to start from | Done when |
|---|---|---|---|---|---|
| 1 | Contract + gen | engineer | sonnet/opus | … | `gen` clean, stubs compile |
| 2 | Logic + tests | engineer | … | … | targeted `test` green |
| 3 | UI / wiring | ui-engineer or engineer | … | … | states render, `e2e` green |

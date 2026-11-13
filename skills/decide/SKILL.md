---
name: decide
description: Settles architecture and system-design questions with the owner and records each as an ADR. Use after scope is approved, when the owner raises a design problem ("nên thiết kế X thế nào"), or when a builder hits a question no ADR answers.
---
# Decide

Builders must never guess at architecture. Find each decision that changes the design, settle it, write it down once.
**Start:** read `.claude/workflow.md` (verbs, `<key>` placeholders, `R`, run rules); missing → `/wf:setup`.

**Modes:** *sweep* (after `/wf:scope`: all open decisions for the approved scope) or *single problem* (go to step 2).

1. **Sweep:** `architect` (mode decide; reuse an existing one) reads the scope doc, existing ADRs in `<docs.adr>` and the code it touches.
   Output, no files: numbered decisions, each ≤ 8 lines with 2–3 options and a recommendation, tagged `needs owner` (product, cost,
   hard to reverse) or `architect can decide` (local, reversible, convention-following).
2. **Owner:** each `needs owner` item via AskUserQuestion (≤ 4 per round, recommendation first). Constraints that rework the options go
   back to the same architect. `architect can decide` items are listed in one message and stand unless the owner objects.
3. **Record:** the architect writes `<docs.adr>/NNNN-<slug>.md` from `templates/adr.md`, status `accepted`, numbered sequentially, plus
   one line per ADR in `<docs.adr>/README.md`. Superseding = new ADR + mark the old one `superseded by NNNN`.
4. **Hand off:** sweep → `/wf:slice-plan`. Single problem mid-build → tell `/wf:ship` which slice docs change, then resume.

An ADR is worth writing only when a future builder could reasonably choose differently. Each names what would make us revisit it.
Only `<docs.adr>` is written here; no code.

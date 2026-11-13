---
name: scope
description: Large lane step 1 - agrees with the owner what a phase or MVP includes (In / Out / Later) with testable acceptance criteria. Use for "MVP gồm gì", "bắt đầu phase mới", cutting or prioritising features. Skip when scope is already clear.
---
# Scope

One short doc the owner signed off, so every later step can be checked against it. Building without it is how scope creeps.

1. **Read** the product inputs the owner points to (PRD, issue, notes) and `<docs.master>` / existing code to see what already exists.
   An existing scope doc is revised, not restarted.
2. **Draft the cut yourself:** the smallest thing that delivers the core loop end to end for one real user. Cut breadth, not depth.
3. **Ask only real choices:** AskUserQuestion, ≤ 4 per round, recommendation first with the cost of including each option.
4. **Write** `<docs.scope>/<phase>.md` from `templates/scope.md`. Every In row has acceptance criteria a test can prove; that list is the
   phase's done-check.
5. **Sign-off:** summary (In, notable Outs, risks) → owner approves → `Status: approved (<date>)`. Next: `/wf:decide`.

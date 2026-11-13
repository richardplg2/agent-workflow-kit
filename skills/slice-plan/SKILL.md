---
name: slice-plan
description: Large lane step 3 - splits an approved scope into vertical slices grouped into parallel waves, writes PLAN.md plus a design and plan doc per slice, gets them plan-reviewed and approved once. Use for "chia slice", "lên plan tổng", re-planning.
---
# Slice plan

A good plan makes the run boring: each slice runs alone in its own worktree and finishes in one pass; waves keep agents busy
without merge pain. Too many tiny tasks cost more in spawns and conflicts than they save.

**Needs:** an approved scope doc (or scope stated by the owner in this session) and ADRs for the decisions it relies on.

## Slicing rules (give them to the architect)
- **Vertical:** one user-visible capability through contract, logic, UI and wiring, testable alone. Layers are not slices.
- **Foundation first, only if needed:** shared groundwork in one thin wave-0 slice.
- **Count 3–8 slices; ≤ 5 tasks each**, from the task shapes contract · logic · UI · wire · review (drop what a slice does not need).
  A task is one agent in one sitting.
- **Waves:** no dependency → same wave, within `budget.max-agents` running at once. Only a real dependency moves a slice later.
  Slices that only share hot files (route table, wiring, contract root) stay together with those files named.
- **Spike first** for unproven technology, with a measured go/no-go criterion.
- **Each task** names files, starting symbols, owner agent and model (`sonnet` only for a closed task, see `/wf:work`; unsure → `opus`).
- Mark each slice `mvp` or `later`.

## Steps
1. `architect` (mode design; reuse the one from `/wf:decide`) writes, all `draft`:
   `<docs.master>` from `templates/master.md`, and per slice `<docs.design>/<slug>.md` from `templates/design.md` and
   `<docs.plans>/<slug>.md` from `templates/plan.md`. The contract section must let back end and front end build in parallel.
2. **Plan review:** a fresh `architect` (mode plan-review) checks the docs against the real code of the base. Writer fixes `BLOCKER`/`FIX`;
   same reviewer re-checks changes only, ≤ 2 rounds. Remaining items go to the owner.
3. **Owner approves once** (summary: slices, waves, open risks). Set every doc `approved (<date>)`. Approval also authorises local commits
   and merges during `/wf:ship`; nothing is pushed. Next: `/wf:ship`.

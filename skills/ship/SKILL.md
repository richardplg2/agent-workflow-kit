---
name: ship
description: Large lane step 4 - long-running orchestrator that executes an approved PLAN.md until every mvp slice is merged into the integration branch, running slices in parallel worktrees. Use for "chạy plan", "implement tới khi xong", "ship", or resuming a run.
---
# Ship

The owner approved the plan so they do not have to babysit. Keep agents busy, keep the integration branch green, come back only for
what only the owner can decide. A quiet, steady run is success. Per-slice procedure: `references/slice.md` (read it when you advance a slice).
**Start:** read `.claude/workflow.md` (verbs, `<key>` placeholders, `R`, run rules); missing → `/wf:setup`.

## Start or resume
1. `<docs.master>` must be `approved` or `in progress`; else `/wf:slice-plan`.
2. Integration worktree: `I="$R/<worktrees>/_<phase>"`; `git -C "$R" worktree add "$I" -b feat/<phase> <base>` (or attach the existing
   branch). Record the base rev, set `in progress`.
3. **State lives in files, never memory:** slice rows, each plan doc's Status column, `git worktree list`. After `/clear` or compaction, re-read.

## Loop (until nothing can progress)
1. **Ready:** `todo` slices whose dependencies are `merged` (waves are a guide, dependencies are the rule).
2. **Fill the budget:** ≤ `budget.max-agents` role agents running. Advance running slices before starting new ones.
3. **Wait:** agents run in the background and notify. Don't poll or sleep; end the turn with one status line.
   When the owner asks for status, answer in full: done, running (slice, agent, branch), blocked (reason), next step, last check result.
4. **On notification:** apply `references/slice.md` for that slice, then go to 1.
5. **Merge** one `review` slice at a time in plan order: `git -C "$I" merge --no-ff slice/<slug>`, then `gen` (if any) and each touched
   area's `typecheck`. Generated files in conflicts: take either side and rerun `gen`, never hand-merge. Row → `merged <sha>`; remove the
   clean slice worktree, keep the branch.
6. **One check per merge, in the background:** a `runner` runs `check BASE=<last green rev>` in `$I` plus `test-all` of each touched
   area and of every area whose `depends` lists one (with `.claude/wf/check.sh` that is `FULL=1`; rerun failed e2e once) and reports
   only green or the failures that failed twice. Don't wait for it; the next check covers later merges too. Green → log
   `checked <sha>`, update Acceptance, move `BASE`. Red → send the failure to the owning slice's agent, fix in `$I`, check again.
7. **Wave boundary:** if the owner can see a change, log a short "try it" (how to run + 3–5 things). Feedback goes through `/wf:quick-change`
   without pausing. Keep the orchestrator small: no diffs or full logs here; suggest `/compact` or a fresh `/wf:ship` between waves.

## Stop for the owner only when
- a design question no ADR answers → `/wf:decide` single problem; only that slice waits;
- every remaining slice is `blocked`;
- something destructive or outside the plan (unplanned dependency, edit to an applied migration).
Decide everything else and log it. Never push, force or delete branches.

## Finish
All `mvp` slices `merged` (or `blocked` with nothing runnable): final `check BASE=<base>` unless the last one was green on HEAD; walk the
Acceptance table (proved by which test / not proved why); set `done (<date>)`, mark design docs `shipped`, commit in `$I`.
Report ≤ 30 lines: merged slices with shas, blocked ones with reasons, acceptance summary, checks and results, follow-ups, and that
`feat/<phase>` is ready for the owner to review and squash-merge.

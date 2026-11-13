# One slice: branch `slice/<slug>`, worktree `W`

**0. Worktree** (skip if it exists; resume at the plan doc's first non-done task):
`W="$R/<worktrees>/<slug>"; git -C "$R" worktree add "$W" -b slice/<slug> feat/<phase>`; install declared dependencies for the areas it
touches. Row → `running`.

**Brief every agent with:** `W` (absolute paths, `cd "$W"` before each command, never touch `$R`), design and plan doc paths, task number
and "done when", files it owns and what the parallel agent owns, starting files and symbols, targeted verbs only, "commit nothing",
"report ≤ 15 lines". `run_in_background: true`; no `isolation: worktree` (W already is one). Model from the plan's Model column if it is
still a closed task; a sonnet brief adds "decide nothing the plan does not state; stop and report gaps".
Reuse per `/wf:work` rule 4.

| Step | Agent | After | Done when |
|---|---|---|---|
| 1 Contract | engineer | – | `gen` clean, stubs compile |
| 2a Logic | same engineer (SendMessage) | 1 | targeted `test` green |
| 2b UI | ui-engineer | 1 | every state renders, screenshot checked |
| 3 Wire | engineer or ui-engineer (per plan) | 2a + 2b | targeted `e2e` green |
| 4 Review | reviewer, fresh, never a builder | 2a | no CONFIRMED finding open |
| 5 Gate | main session | 3 + 4 | `lint`, `typecheck`, `gen` clean in `W` for the touched areas |

Skip steps the plan does not list; run 3 and 4 in parallel. After each step: mark it `done` in the plan doc and commit in `W`
(`<slug>: <step>`), so the slice resumes after a crash. No full `check` inside the slice; it runs once after the merge.

**Fix loop:** CONFIRMED (and agreed PLAUSIBLE) findings → the engineer's session, then the same reviewer on the new diff only.
Test failures → trimmed output to the owning agent. Contract gap → stop this slice, `/wf:decide` single problem or the architect amends §3,
then the engineer updates the contract. **Two failed rounds on one problem → `blocked: <reason>`**, commit what exists, log it, move on.

**Finish:** step 5 green → row `review`, return to `/wf:ship`. Run alone (no ship): one `check BASE=<slice base>` through a background
`runner`, then ask the owner whether to merge.
Report: commits, verbs run with results, PLAUSIBLE findings left open.

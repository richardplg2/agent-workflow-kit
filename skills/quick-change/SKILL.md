---
name: quick-change
description: Fast and medium lanes - small features, fixes and feedback with no design docs (fast) or one short design note (medium, contract change or migration). Use for "fix nhanh", "sửa luôn", "thêm nút", UI feedback.
---
# Quick change

Lane rules and token-cost rules: `/wf:work`. Verbs and paths: `.claude/workflow.md`. `R` = main checkout root, `W` = this change's worktree.

## Fast lane (no docs; the brief is the spec)
1. **Worktree from base:** `git -C "$R" worktree add "$R/<worktrees>/q-<slug>" -b quick/<slug> <base>`. Install the area's declared
   dependencies only if that area changes. Nothing is edited in the main checkout.
2. **Build:** clear fix in ≤ 3 files → main session. Otherwise one agent of the area's `owner`; sonnet only for a closed task.
3. **Brief:** `lane: fast`, `W` (absolute paths, `cd "$W"` before every command), what the user sees now and should see after,
   files and symbols to start from, the code to copy, the targeted verbs to run, "commit nothing", "report ≤ 15 lines".
4. **Check:** targeted `test` verbs, then one `check BASE=<base>` through a background `runner`. Fast changes merged close together share one check.
5. **Review** only if `review-when` matches: `reviewer` with the brief and `git diff <base>...HEAD`.
6. **Merge:** commit on `quick/<slug>`. Squash into base from the main checkout only if the owner asked; otherwise ask once at the end.
   Main checkout not on base or dirty → stop and tell the owner. Never push. Remove the clean worktree after merge.

Needs a contract change, migration or product decision after all → stop, tell the owner, move to medium.

## Medium lane (one design note)
1. **Note:** `<docs.design>/<slug>.md` from `templates/note.md`, ~40 lines: contract, behaviour, failure cases, acceptance checks, tasks
   with files, owner and model. Main session writes it, or `architect` (mode design) if the design is not obvious.
2. **Plan review:** a fresh `architect` (mode plan-review), never the writer. Fix `BLOCKER`/`FIX`, same reviewer re-checks only the
   changes, ≤ 2 rounds. Open items go to the owner.
3. **Owner approves** in chat → status `approved (<date>)`.
4. **Build** in a worktree as in fast step 1: `engineer` does contract (+ `gen`) then logic in one session; then the UI area's owner
   (wiring only, or `ui-engineer` when new visuals are needed); `reviewer` on the diff if `review-when` matches.
5. Check and merge as fast steps 4 and 6.

## Report
What changed, verbs run with results (skipped/broken stated as such), branch, what the owner should try.

---
name: work
description: Entry point for any code change - classifies the request into the fast, medium or large lane and runs that lane. Use for "làm", "fix", "thêm", "build", feedback lists or any feature request.
---
# Work: pick the lane, then run it

Small work must cost like small work. Use the lightest lane that fits; when work stops fitting, stop and move up a lane.

## 0. Adapter
Read `.claude/workflow.md`. Missing → run `/wf:setup` first. Verbs marked `broken` are reported, never treated as passing.

## 1. Classify (say the lane and the reason in one line)
| Lane | All of these hold | Run |
|---|---|---|
| **Fast** | no contract/schema change · no migration · no architecture change · no open product question | `/wf:quick-change` fast |
| **Medium** | one feature · changes the contract or adds a migration · fits the existing architecture | `/wf:quick-change` medium |
| **Large** | several dependent features · or new architecture/technology · or more than ~8 tasks | `/wf:scope` → `/wf:decide` → `/wf:slice-plan` → `/wf:ship` |

Special cases:
- A list of unrelated feedback items = several fast changes (independent ones in parallel), not one slice.
- Bug with unknown root cause: reproduce in the main session first (ideally a failing test), find the cause, then lane by the size of the fix.
- Unproven technology or performance: a spike with a measured go/no-go criterion comes first; nothing is planned on top until it passes.
- A product choice is unclear: ask one short question before building.
- Pure question, review or investigation: answer it; no lane.

## 2. Token-cost rules (every lane)
1. Main session does a clear change in ≤ 3 files itself; no spawn.
2. Model by task: haiku (`scout`, `runner`) for search, tests, logs; sonnet `engineer` only for a **closed task** (files and symbols named,
   contract and behaviour written in full, pattern to copy named, not concurrency/migration/auth/unknown-root-cause debugging); opus otherwise.
   A sonnet task that stops on a gap or fails two fix rounds goes to a fresh opus agent.
3. Briefs name files, symbols and the pattern to copy, and ask for a report ≤ 15 lines. Never read whole diffs or logs in the main session.
4. Reuse an agent (SendMessage) while it is under ~300k tokens and idle under ~5 min; otherwise a fresh agent with a short delta brief.
5. Builders run targeted verbs only; one `check` per merge, in a background `runner`. Full suites are CI's.
6. Review only when the adapter's `review-when` matches. Plan review at most 2 rounds.
7. Two failed fix rounds on one problem → `blocked`; stop spending, report.
8. At most `budget.max-agents` agents at once. Nothing is pushed; commits only where the lane says.

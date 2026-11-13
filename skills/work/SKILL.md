---
name: work
description: Entry point for any code change in a repo that uses wf - classifies the request into the fast, medium or large lane and runs it. Use whenever the user asks to build, fix, add, change or ship something ("làm", "fix", "sửa", "thêm", "build", a feedback list, a feature), even a small one and even if they never say "workflow".
---
# Work: pick the lane, then run it

Small work must cost like small work. Use the lightest lane that fits; when work stops fitting, stop and move up a lane.

## 0. Adapter
Read `.claude/workflow.md`: verbs, placeholders, `R` and the run rules every lane follows. Missing, or without a `Run rules`
section (older wf) → run `/wf:setup` first. Verbs marked `broken` are reported, never treated as passing.

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

## 2. Run it
Follow the run rules in `.claude/workflow.md` in every lane. Say the lane, then invoke that lane's skill.

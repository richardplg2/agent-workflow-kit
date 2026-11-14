---
name: work
description: Entry point for any code change in a repo that uses wf - classifies the request into the fast, medium or large lane and runs it. Use whenever the user asks to build, fix, add, change or ship something ("làm", "fix", "sửa", "thêm", "build", a feedback list, a feature), even a small one and even if they never say "workflow".
---
# Work: pick the lane, then run it

Small work must cost like small work. Use the lightest lane that fits; when work stops fitting, stop and move up a lane.

## 0. Adapter
Read `.claude/workflow.md`: verbs, placeholders, `R` and the run rules every lane follows. Missing, or without a `Run rules`
section (older wf) → run `/wf:setup` first. Verbs marked `broken` are reported, never treated as passing.

## 1. Classify (say the lane, the reason, and the scope in two lines)
| Lane | All of these hold | Run |
|---|---|---|
| **Fast** | no contract/schema change · no migration · no architecture change · no open product question | `/wf:quick-change` fast |
| **Medium** | one feature · changes the contract or adds a migration · fits the existing architecture | `/wf:quick-change` medium |
| **Large** | several dependent features · or new architecture/technology · or more than ~8 tasks | `/wf:scope` → `/wf:decide` → `/wf:slice-plan` → `/wf:ship` |

Scope line: what will be delivered and what is deliberately left out. When the request has several deliverables, or the next step
hands work to another session or agent that builds or renders something expensive, wait for the owner's OK on that list first.
Add nothing the owner did not ask for; list ideas at the end instead.

Special cases:
- A list of unrelated feedback items = several fast changes (independent ones in parallel), not one slice.
- Bug with unknown root cause: reproduce in the main session first (ideally a failing test), find the cause, then lane by the size of the fix.
- Unproven technology or performance: a spike with a measured go/no-go criterion comes first; nothing is planned on top until it passes.
- A product choice is unclear: ask one short question before building.
- Pure question, review or investigation: answer it; no lane.

## 2. Preflight (medium and large only)
Before a long run, check in one batch and report every gap at once: git `user.name`/`user.email` set; the touched areas' declared
dependencies installed; their verbs not `broken`; credentials, config or services the work needs are present; any step needing
sudo or the owner's hands. A gap the owner must fix → ask now, not mid-run.

## 3. Run it
Follow the run rules in `.claude/workflow.md` in every lane. Invoke the lane's skill.

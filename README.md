# agent-workflow-kit (`wf`)

A Claude Code plugin with a language-agnostic feature workflow: pick the lightest lane that fits, build with short goal-and-mindset
agents, and keep token cost low. It is the generic version of the RMA workflow and works in Go, TS, Python, Rust, JVM, .NET, Ruby and
other repos. Skills and agents only use standard verbs; each repo maps them once in `.claude/workflow.md`.

## Install (per project)
```bash
claude plugin marketplace add richardplg2/agent-workflow-kit --scope project
claude plugin install wf@agent-workflow-kit --scope project
```
Then, in that project: `/wf:setup` once, and `/wf:work <request>` for everything after that.
Working on the kit itself? Add your local clone instead (`claude plugin marketplace add <path-to-clone>`).
Full guide, including update and uninstall: [docs/guide.md](docs/guide.md).

## Flow
```
/wf:setup        detect stack per directory → map verbs → verify each runs → .claude/workflow.md
/wf:work         classify the request into a lane
  fast   → /wf:quick-change   no docs, main session or 1 agent
  medium → /wf:quick-change   1 design note (~40 lines) + plan review
  large  → /wf:scope → /wf:decide (ADRs) → /wf:slice-plan (PLAN.md + slices) → /wf:ship (parallel worktrees)
```
| Lane | All of these hold |
|---|---|
| Fast | no contract/schema change, no migration, no architecture change, no open product question |
| Medium | one feature that changes the contract or adds a migration, inside the existing architecture |
| Large | several dependent features, new architecture or technology, or more than ~8 tasks |

## Standard verbs
`check` (diff-scoped gate), `test` (targeted, `{files}`/`{pkg}`), `lint`, `typecheck`, `gen`, `build`, `e2e`, mapped per area in
`.claude/workflow.md`. A repo with no diff-scoped check gets `.claude/wf/check.sh` (changed files → areas → that area's verbs) if the owner
approves. Verbs that do not run are marked `broken`, so a skill can never report a check as passed when it never ran.

## Agents
| Agent | Model | Goal |
|---|---|---|
| `wf:architect` | opus, high | settle every decision a builder could guess; modes decide / design / plan-review |
| `wf:engineer` | sonnet (opus when the task is not closed) | smallest correct diff for one task, contract first, proved by tests |
| `wf:ui-engineer` | opus | UI that matches the design system, every state covered |
| `wf:reviewer` | opus | bugs and requirement gaps only; "nothing found" is valid |

Search and test runs use your global `scout` / `runner` (haiku) agents if you have them.

## Token cost (measured in words against the RMA originals)
| Measure | RMA | wf |
|---|---|---|
| Descriptions loaded in every session (skills + agents) | 587 + 235 = 822 | 278 + 135 = 413 |
| Agent body per spawn | 330–1060 (architect 1060) | 191–244 |
| All skill bodies | 4149 | 2225 (+1693 in references/templates, read only when that step runs) |

The rules that keep a run cheap are in `skills/work/SKILL.md` §2: the main session does small changes itself, models are routed by
task, briefs name files, reports stay short, agents are reused with limits, there is one check per merge, review only runs on risky
diffs, and work stops after two failed fixes.

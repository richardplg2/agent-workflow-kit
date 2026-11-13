# Workflow adapter (wf)
<!-- Read first by every wf skill and agent. Keep it short; regenerate with /wf:setup. The two sections below are copied as is;
     edit the run rules to suit this repo. -->
updated: <YYYY-MM-DD>

## Conventions
- **Verbs** (`check`, `test`, `lint`, `typecheck`, `gen`, `build`, `e2e`) mean the commands below: `check` and `gen` at the top
  level, the others under the area that owns the files (`areas.<name>.<verb>`). `{files}` / `{pkg}` are filled with the targets.
  `broken: …` = it does not run: report it, never count it as passed. `none` = this repo has no such step.
- In wf skills, `<a.b>` is the value of key `a.b` in this file (`<docs.adr>`, `<budget.max-agents>`); `<base>` = `branches.base`,
  `<worktrees>` = `branches.worktrees`. Other placeholders (`<slug>`, `<phase>`, `<date>`) are filled per run.
- `R` = root of the main checkout, also from inside a worktree:
  `R="$(dirname "$(git rev-parse --path-format=absolute --git-common-dir)")"`. `W` = the worktree of the current change or slice.

## Run rules (every lane)
1. A clear change in ≤ 3 files is made by the main session, no spawn. A spawn re-reads context and costs more than the edit.
2. Model by task: haiku (`scout`, `runner`) for search, tests and logs; sonnet `engineer` only for a **closed task**; opus otherwise.
   Closed = files and symbols named, contract and behaviour written in full, pattern to copy named, and not concurrency, migration,
   auth or unknown-root-cause debugging. A sonnet task that stops on a gap or fails two fix rounds goes to a fresh opus agent.
   Sonnet is cheap when nothing has to be decided and expensive when it guesses.
3. Briefs name files, symbols and the pattern to copy, and ask for a report ≤ 15 lines. The main session never reads whole diffs
   or logs: every token there is billed again on each later turn.
4. Reuse an agent (SendMessage) while it is under ~300k tokens and idle under ~5 min; otherwise a fresh agent with a short delta
   brief. A warm cache makes reuse cheap; a cold, large transcript is re-billed in full.
5. Builders run targeted verbs only; one `check` per merge, in a background `runner`; full suites are CI's. A check per task
   repeats the same work and fills context with logs.
6. Review only when `budget.review-when` matches; a plan review takes at most 2 rounds. Reviewing a trivial diff costs an opus
   spawn and finds nothing.
7. Two failed fix rounds on one problem → `blocked`: stop and report. A third round in the same context rarely works and costs most.
8. At most `budget.max-agents` agents at once. Nothing is pushed; commits only where the lane says.

## Repo
check: <one command: lint + typecheck + tests for what the diff touches; BASE=<rev> optional>   # ok | broken: … | none

areas:
  <name>:
    dir: <path, or several space-separated paths>
    lang: <go | ts | python | rust | java | kotlin | csharp | ruby | elixir | swift | …>
    owner: <engineer | ui-engineer>
    test: <targeted command using {files} or {pkg}>
    lint: <command>
    typecheck: <command or none>
    build: <command or none>
    e2e: <targeted command or none>
    rules: [<AGENTS.md or other rule files for this area>]

gen: <codegen command or none>
generated: [<globs never hand-edited>]
contract: <where the API/schema lives: openapi/…, *.proto, schema.graphql, none>
design-system: <path or none>

docs:
  scope: docs/product/
  adr: docs/design/decisions/
  design: docs/design/features/
  plans: docs/plans/
  master: PLAN.md

budget:
  max-agents: 3
  review-when: [auth, payments, migration, security, concurrency, ">300 lines"]
branches: { base: main, worktrees: .claude/worktrees/ }

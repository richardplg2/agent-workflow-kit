# Workflow adapter (wf)
<!-- Read by every wf skill and agent. Verbs only; keep it under ~40 lines. Regenerate with /wf:setup. -->
updated: <YYYY-MM-DD>

check: <one command: lint + typecheck + tests for what the diff touches; BASE=<rev> optional>   # ok | broken: … | none

areas:
  <name>:
    dir: <path>
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

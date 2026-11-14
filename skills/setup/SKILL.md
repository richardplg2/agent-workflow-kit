---
name: setup
description: One-time setup that maps any repo's languages and tools onto wf's standard verbs (check, test, lint, typecheck, gen, build, e2e) in .claude/workflow.md and checks each verb runs. Use it for a new repo, when the stack changes, and whenever .claude/workflow.md is missing or another wf skill asks for it.
---
# Setup: normalise the repo into standard verbs

Skills and agents never name a tool. They say a verb; `.claude/workflow.md` says what it means for each area of this repo.
Setup never changes build files, dependencies or CI, and never installs global tools.

## Steps
1. **Detect, in one pass** (parallel reads): manifests and lockfiles per directory, task runners, CI workflow files, existing
   `AGENTS.md`/`CLAUDE.md`/`CONTRIBUTING`, lint configs, `Code generated` headers and `gen/` dirs. Read
   `references/stacks.md` for the default verb per ecosystem. Prefer what CI or the repo's own runner already does over defaults.
2. **Draft the adapter** from `templates/workflow.md`: one `areas` entry per independently built directory (a monorepo has several),
   each with `dir`, `lang`, the verbs it has (`test` targeted, `test-all` whole area), `depends` (areas it imports or whose
   contract it consumes), and `owner` (`engineer` or `ui-engineer`). Fill `docs`, `generated`, `contract`,
   `rules`, `budget`. Copy the `Conventions` and `Run rules` sections as they are. Rerun on an existing adapter: show a diff, keep the
   owner's edits, and add any section the template has and the adapter lacks.
3. **Fill gaps by proposal.** A verb the repo lacks stays empty unless the owner approves an addition. The usual gap is a diff-scoped
   `check`: offer `templates/check.sh` filled with this repo's areas, saved as `.claude/wf/check.sh`.
4. **Ask once** (AskUserQuestion, ≤ 4 questions, recommendation first) only what detection could not settle: which areas exist,
   UI owner, design-system path, what needs review.
5. **Verify every verb** with the smallest real target (one test file, one package, `--help` is not enough). A verb that fails to
   start gets `broken: <first error line>`; if only the project's declared dependencies are missing, install them with its own
   package manager and lockfile, then retry once.
6. **Conventions:** link existing rule files per area under `rules`. An area with none gets an offer of a ≤ 20-line `AGENTS.md`
   (layout, verbs, don'ts). Generated globs: offer a deny rule in `.claude/settings.json` (`Edit(<glob>)`, `Write(<glob>)`).
7. **Write** `.claude/workflow.md` (and the approved extras). Report: areas, each verb with `ok`/`broken`/`none`, files written.

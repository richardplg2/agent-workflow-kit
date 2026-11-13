---
name: setup
description: One-time setup that maps any repo's languages and tools onto the workflow's standard verbs (check, test, lint, typecheck, gen, build, e2e) in .claude/workflow.md, and checks each verb runs. Use for a new repo or when its stack changes.
---
# Setup: normalise the repo into standard verbs

Skills and agents never name a tool. They say a verb; `.claude/workflow.md` says what it means for each area of this repo.
Setup never changes build files, dependencies or CI, and never installs global tools.

## Steps
1. **Detect, in one pass** (parallel reads): manifests and lockfiles per directory, task runners, CI workflow files, existing
   `AGENTS.md`/`CLAUDE.md`/`CONTRIBUTING`, lint configs, `Code generated` headers and `gen/` dirs. Read
   `references/stacks.md` for the default verb per ecosystem. Prefer what CI or the repo's own runner already does over defaults.
2. **Draft the adapter** from `templates/workflow.md`: one `areas` entry per independently built directory (a monorepo has several),
   each with `dir`, `lang`, the verbs it has, and `owner` (`engineer` or `ui-engineer`). Fill `docs`, `generated`, `contract`,
   `rules`, `budget`. Rerun on an existing adapter: show a diff and keep the owner's edits.
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

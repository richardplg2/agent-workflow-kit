# wf guide: install, use, update, uninstall

`wf` is a Claude Code plugin that gives any repo the same feature workflow, whatever its languages. You pick nothing by hand:
`/wf:work` chooses the lane, and every skill and agent reads the repo's commands from one file, `.claude/workflow.md`.
Overview and token numbers: [README](../README.md).

## 1. Install

### 1.1 Choose a scope
| Scope | Written to | Use when |
|---|---|---|
| `project` | `<repo>/.claude/settings.json` (committed) | the whole team should get `wf` in this repo |
| `local` | `<repo>/.claude/settings.local.json` (not committed) | only you, only this repo (recommended to try it) |
| `user` | `~/.claude/settings.json` | you want `wf` in every repo. Every session then loads its descriptions (~470 words) |

### 1.2 Commands
Run them from the repo where you want `wf`, using the same scope for both:
```bash
claude plugin marketplace add richardplg2/agent-workflow-kit --scope local
```
```bash
claude plugin install wf@agent-workflow-kit --scope local
```
Restart Claude Code (or open a new session), then check it loaded:
```bash
claude plugin list
```
`wf@agent-workflow-kit` should be listed. In a session, `/wf:` now
completes to 7 skills, and the agents appear as `wf:architect`, `wf:engineer`, `wf:ui-engineer` and `wf:reviewer`.

With local or project scope, the settings file gets these two keys:
```json
{
  "extraKnownMarketplaces": { "agent-workflow-kit": { "source": { "source": "github", "repo": "richardplg2/agent-workflow-kit" } } },
  "enabledPlugins": { "wf@agent-workflow-kit": true }
}
```

**Developing the kit:** clone it and add the clone instead of the GitHub repo
(`claude plugin marketplace add <path-to-clone>`). Claude Code then reads skills and agents straight from that folder. To try a clone
for one session without installing it (no settings file is changed):
```bash
claude --plugin-dir <path-to-clone>
```

## 2. First run in a repo: `/wf:setup`
Run it once per repo, and again when the stack changes. It:
1. detects each directory's language and tools: manifests, lockfiles, task runners, CI, rule files and generated code;
2. maps them onto the standard verbs `check`, `test`, `lint`, `typecheck`, `gen`, `build` and `e2e`, per area;
3. asks you at most 4 questions, only about what it could not detect;
4. runs every verb once and marks the ones that fail as `broken: <error>`;
5. writes `.claude/workflow.md`. Only if you approve, it also writes `.claude/wf/check.sh` (when the repo has no diff-scoped check),
   short `AGENTS.md` stubs, and deny rules for generated files.

It never changes build files, dependencies or CI. Review `.claude/workflow.md` and commit it, so the team and CI agents share it.

## 3. Daily use
Start everything with `/wf:work <what you want>`. It says which lane it picked and why, then runs it.

| You say | Lane | What happens |
|---|---|---|
| "the save button is misaligned on mobile" | fast | main session fixes it in a `quick/<slug>` worktree, runs targeted tests and one `check`, then asks whether to merge |
| "add a `due_date` field to tasks, API and UI" | medium | a ~40-line design note → plan review → your approval → engineer, then UI → review if risky |
| "build billing: plans, invoices, webhooks" | large | `/wf:scope` → `/wf:decide` (ADRs) → `/wf:slice-plan` (`PLAN.md`) → `/wf:ship` runs the slices in parallel |
| "5 small UI feedback items" | fast × 5 | independent items run in parallel, sharing one check |

You can also call a step directly, for example `/wf:decide nên lưu file ở S3 hay local?` or `/wf:ship` to resume a run.

Where you are asked to act:
- **Medium:** approve the design note.
- **Large:** approve the scope, answer the `needs owner` decisions, and approve the plan once. `ship` then runs on its own.
- **Merge:** fast and medium lanes ask before squashing into `main`. A large run ends on `feat/<phase>` for you to review and merge.
- **Push:** nothing is ever pushed.

## 4. Update the kit
- **Installed from GitHub:** pull the latest commit, then restart Claude Code:
  ```bash
  claude plugin marketplace update agent-workflow-kit
  ```
  ```bash
  claude plugin update wf@agent-workflow-kit
  ```
- **Installed from a local clone:** edits to skills or agents apply after a restart. A version bump needs the two commands above.

Validate after editing, from the clone:
```bash
claude plugin validate .
```

## 5. Uninstall

### 5.1 Pause it without uninstalling
```bash
claude plugin disable wf@agent-workflow-kit
```
Undo it with `claude plugin enable wf@agent-workflow-kit`.

### 5.2 Remove it from one repo
Run these in that repo, using the scope you installed with (`local`, `project` or `user`):
```bash
claude plugin uninstall wf@agent-workflow-kit --scope local
```
```bash
claude plugin marketplace remove agent-workflow-kit --scope local
```
This leaves `enabledPlugins: {}` and `extraKnownMarketplaces: {}` in the settings file. Delete those empty keys if you like.

The repo files that `/wf:setup` and the lanes created are left in place. Delete the ones you no longer want:

| File | Created by | Keep? |
|---|---|---|
| `.claude/workflow.md` | setup | remove: only `wf` reads it |
| `.claude/wf/check.sh` | setup, only if approved | remove, unless CI or hooks now call it |
| `Edit(…)` / `Write(…)` deny rules in `.claude/settings.json` | setup, only if approved | usually keep: they protect generated code anyway |
| `AGENTS.md` stubs | setup, only if approved | usually keep |
| `docs/product/`, `docs/design/decisions/`, `docs/design/features/`, `docs/plans/`, `PLAN.md` | the lanes | keep: this is your project's history |
| `quick/*`, `slice/*`, `feat/*` branches and `.claude/worktrees/*` | the lanes | remove when merged (`git worktree remove <path>`, `git branch -d <branch>`) |

### 5.3 Remove it everywhere
```bash
claude plugin uninstall wf@agent-workflow-kit --scope user
```
```bash
claude plugin marketplace remove agent-workflow-kit
```
Leaving out `--scope` removes the marketplace from every scope. Then delete the plugin cache, which these commands leave behind:
```bash
rm -rf ~/.claude/plugins/cache/agent-workflow-kit
```
Check that nothing is left:
```bash
claude plugin list
```
```bash
grep -c agent-workflow-kit ~/.claude/plugins/known_marketplaces.json ~/.claude/plugins/installed_plugins.json
```
`wf` should not be listed, and both counts should be `0`. Repos that installed with `--scope project` keep their keys in their
committed `.claude/settings.json` until you run 5.2 in each of them. A local clone of the kit is never touched by
uninstalling.

## 6. Troubleshooting
| Symptom | Fix |
|---|---|
| `/wf:` skills missing after install | restart Claude Code; run `claude plugin list`; check `enabledPlugins` in the scope you used |
| "Marketplace … already on disk" | harmless: it is already known; the declaration is still added to the scope you gave |
| a verb is `broken` in `workflow.md` | install the repo's declared dependencies with its own package manager, or add the missing tool (setup proposes it), then rerun `/wf:setup` |
| `check` passes but runs nothing | `check.sh` prints `no area affected`: add the directory to an area's `dir` in `workflow.md` and to `AREAS` in `check.sh` |
| name clash with a repo's own skills (e.g. `quick-change`) | none: plugin skills are always `wf:`-prefixed |

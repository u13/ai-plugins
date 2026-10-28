# u13 / ai-plugins

Three independent plugins for [Claude Code](https://code.claude.com). Install any of them, in any combination. None requires another. When more than one is installed, they cooperate (see [How they work together](#how-they-work-together)).

| Plugin | Use it when you want | Files it creates in your project |
|---|---|---|
| [`review-loop`](plugins/review-loop/README.md) | every non-trivial change checked before and after it is made, with the depth chosen automatically | none |
| [`project-memory`](plugins/project-memory/README.md) | a short log of decisions, status and backlog so a new session can continue where the last one stopped | `.u13/memory/*` |
| [`task-planner`](plugins/task-planner/README.md) | a big or unclear task clarified and split into subtasks, with decisions made by you or by a separate agent | `.u13/plans/*` |

## Install

### Step 1. Add the marketplace (once)

In a terminal:

```bash
claude plugin marketplace add u13/ai-plugins
```

That registers the marketplace for you, in every project on this machine (user scope).

To register it for one project and share that with teammates, run it from the project folder instead:

```bash
claude plugin marketplace add u13/ai-plugins --scope project
```

The marketplace is then declared in the project's `.claude/settings.json`, which you commit.

To register it only for yourself and only in one project (nothing shared, nothing committed), run it from the project folder with the `local` scope:

```bash
claude plugin marketplace add u13/ai-plugins --scope local
```

That is recorded in `.claude/settings.local.json`, which stays out of git.

### Step 2. Install the plugins you need

Pick any of them. All three are optional and independent.

```bash
claude plugin install review-loop@u13
claude plugin install project-memory@u13
claude plugin install task-planner@u13
```

The default is `user` scope: you get the plugin in every project on this machine. Use `--scope` to choose differently:

```bash
# for the whole project: shared with teammates through .claude/settings.json
claude plugin install review-loop@u13 --scope project

# for you only, and only in this repository: not shared, not committed
claude plugin install review-loop@u13 --scope local
```

Run the project and local variants from the project folder. The scopes are compared in the table below.

### Step 3. Check

```bash
claude plugin list
```

Inside a Claude Code session, `/plugin` shows the same list and lets you enable, disable or update plugins.

### Which scope?

| Scope | Who gets it | Where it is recorded |
|---|---|---|
| `user` (default) | You, in every project on this machine | `~/.claude/settings.json` |
| `project` | Everyone who works in this repository | `.claude/settings.json` (commit it) |
| `local` | You, in this repository only | `.claude/settings.local.json` |

Project scope does **not** copy skills or agents into the project's `.claude/` folder. The plugin files stay in Claude Code's plugin cache on each machine. Project scope only records in `.claude/settings.json` which marketplace and plugins the project uses. Each teammate still has to run the install command once on their own machine (with `--scope project`); after that they get the same plugins as you.

### Update

```bash
claude plugin update review-loop@u13
```

Auto-update is off by default for third-party marketplaces. Enable it in `/plugin` > Marketplaces.

---

## review-loop

**What it does.** Before a change it checks that the plan is complete and consistent with your project. After the change it reviews the code and checks that everything intended was actually delivered, then fixes what is missing. How much of this runs depends on the risk of the change, chosen automatically.

**Use it for:** features, fixes, refactors, migrations, config and docs changes.

**How to use.** Just ask for a change in plain words ("Add rate limiting to the upload API"). The `change-workflow` skill announces the level in one line and runs the right steps. If it does not start by itself, call it directly: `/review-loop:change-workflow`. Other commands:

| Command | What it does |
|---|---|
| `/review-loop:preflight <plan or description>` | Review a plan only. Nothing is implemented. |
| `/review-loop:review [intent]` | Review a change you have already made. |

| Level | When | What runs |
|---|---|---|
| light | typos, comments, docs-only, tiny edits | implement, run checks |
| standard | features, 3 or more files, UI | implement, code review and delivery audit, 1 fix round |
| strict | schema, auth, payments, secrets, dependencies, CI/deploy, public API, data deletion | preflight until GO, implement, both reviews, up to 3 fix rounds |

Say "quick" or "full review" in your prompt to override the level.

**Creates:** no files. **Reads:** `CLAUDE.md`, `AGENTS.md`, README, and, when they exist, `.u13/config.md`, `.u13/plans/*`, `.u13/memory/*`. Works without git (it tracks a list of changed files instead of a diff) and without any docs.

**Agents:** `preflight-reviewer`, `code-reviewer`, `delivery-auditor`, `change-finisher`. Details: [plugins/review-loop/README.md](plugins/review-loop/README.md).

## project-memory

**What it does.** Keeps the project's working state in short, ID-numbered lines: decisions (`D-142`), backlog items (`B-07`), milestones (`M-12`) and the current status. A new session reads these instead of you re-explaining the project.

**Use it for:** projects that last longer than one session.

**How to use.** There is nothing to set up. The `memory-update` skill creates `.u13/memory/` itself the first time it has something to write, then adds entries when something happens: a decision is made, a task is finished, a finding is deferred. You can also ask for it ("log this decision") or run `/project-memory:memory-update`.

Optional: if the project already has a decision log, `docs/memory/` or ADRs, run `/project-memory:memory-init` to migrate them into the short format (copies only; your originals stay untouched).

**Creates** (in `.u13/memory/`): `STATUS.md`, `DECISIONS.md`, `DECISIONS.archive.md`, `BACKLOG.md`, `HISTORY.md`, and `decisions/D-<n>.md` only when a decision needs a longer explanation. **Edits:** only these files, never your product docs.

Example entry: `D-142 | 2026-10-27 | auth | Use hosted auth. Why: free tier is enough.`

Details: [plugins/project-memory/README.md](plugins/project-memory/README.md).

## task-planner

**What it does.** Takes a large or unclear task, asks all open questions in one batch, splits it into subtasks with acceptance criteria and dependencies, and writes a plan file. Then it works through the subtasks one by one.

**Use it for:** a new feature, an idea that is not fully defined, a migration, anything that will not fit one change.

**How to use.** Describe the task in plain words, or call `/plan-task <idea>`. By default you answer the open questions (**manual** mode). Say "you decide" (or "ty decydujesz") and a separate **decider** agent answers instead (**delegate** mode). It hands back to you anything irreversible, costly, about security or legal matters, public-facing, needing credentials, or when it is not confident. The first time, you are asked once which model the decider should use; the recommendation is the strongest available. Its effort is fixed to `high`.

**Creates:** `.u13/plans/<date>-<slug>.md` for each larger task, and `.u13/config.md` only when it has to store your decider model choice.

Details: [plugins/task-planner/README.md](plugins/task-planner/README.md).

---

## How they work together

Each plugin works alone. If more than one is installed, extra behaviour switches on by itself:

| Installed together | What changes |
|---|---|
| `task-planner` + `review-loop` | Each subtask of a plan goes through the review loop, and the plan file doubles as its intent and list of changed files. |
| `review-loop` + `project-memory` | Reviewers check changes against your recorded decisions (`D-` IDs), and the loop ends by updating status, decisions and backlog. |
| `task-planner` + `project-memory` | The planner reads decisions and status before asking questions, and logs delegated decisions with a `[delegated]` tag. |
| all three | Plan, review each subtask, record what was decided and done. |

## What goes into `.u13/`

```
.u13/
  config.md        optional settings (see below)
  memory/          project-memory
    STATUS.md           current state: now, next, blocked, last done (about 80 lines at most)
    DECISIONS.md        active decisions, one line each
    DECISIONS.archive.md   superseded or obsolete decisions
    BACKLOG.md          deferred items with priority P0 to P2
    HISTORY.md          finished milestones, one line each (read only on demand)
    decisions/          long explanations, only when needed
  plans/           task-planner
    <date>-<slug>.md    one plan per larger task
```

Commit `.u13/` if you use git; it is shared project state and holds no secrets. Git is optional.

## Settings (`.u13/config.md`, optional)

You do not need this file. Everything has a default and works out of the box. Keys go in the YAML front matter; the three you may actually want:

| Key | Default | Used by | Meaning |
|---|---|---|---|
| `language` | `English` | all | Language of reports to you. Code and docs follow your project. |
| `decisions` | `manual` | review-loop, task-planner | `manual`, or `delegate` to let the decider agent answer open questions in every task (you can also just say "you decide" in a prompt). |
| `decider_model` | `ask` | task-planner | Model for the decider: `ask` (asked once, then stored here), `inherit`, `opus`, `sonnet`, `haiku`. |

Advanced, rarely needed:

| Key | Default | Meaning |
|---|---|---|
| `level` | `auto` | Force `light`, `standard` or `strict` for every change instead of choosing per change. |
| `docs_edit` | `propose` | `auto` lets the plugins edit your product docs (README, specs) instead of only proposing changes. |
| `gates` | `auto` | The commands used to check a change (typecheck, lint, tests, build). They are detected from your project (`package.json` scripts, `Makefile`, `pyproject.toml`, `Cargo.toml`, `go.mod` and so on). Set this only if the detection picks the wrong commands, for example `gates: pnpm typecheck && pnpm test`. |

## Good to know

- Skills are triggered by the model from their descriptions, which is usually reliable but not guaranteed. The slash commands always work. For an always-on rule, add one line to your `CLAUDE.md` or `AGENTS.md`, for example: `For every non-trivial change, use the review-loop:change-workflow skill.`
- Slash commands from plugins have a full form `/plugin:command` (for example `/review-loop:preflight`). A short form without the prefix usually works too (`/plan-task`), but names that other tools also use, such as `/review`, can clash, so use the full form for those.
- Product documentation (README, specs) is yours. The plugins propose doc updates; they only edit files under `.u13/`.
- Reviewer agents run with the model of your session and `effort: high`.
- Support for other agents (Codex, Cursor and others) via `npx skills` is planned once the marketplace is stable.

## License

MIT

# review-loop

A change review loop for Claude Code: check the plan, implement, review, finish. The depth is chosen automatically from the risk of the change, so a typo fix stays cheap and a database migration gets the full treatment.

Independent of the other plugins in this marketplace. It works without git and without any project docs.

## Install

```bash
claude plugin marketplace add u13/ai-plugins
claude plugin install review-loop@u13
```

For a whole project add `--scope project` to both commands and commit `.claude/settings.json`; each teammate runs the install command once. To use it only for yourself in one repository (nothing shared or committed), add `--scope local` instead.

## How to use

Ask for a change in plain words. The `change-workflow` skill picks it up, announces the level ("Level: standard - touches 6 files in the API layer") and runs the matching steps. If it does not start by itself, run `/review-loop:change-workflow`.

| Command | What it does |
|---|---|
| `/review-loop:preflight <plan or description>` | Review a plan only; ask the open questions; do not implement. |
| `/review-loop:review [intent]` | Review a change that is already made, then fix until approved. |

For an always-on rule add this line to `CLAUDE.md` or `AGENTS.md`:

```
For every non-trivial change to code, config, schema or docs, use the review-loop:change-workflow skill.
```

## Levels

| Level | Triggered by | Steps |
|---|---|---|
| light | typos, comments, formatting, docs-only, tiny edits | implement, run checks |
| standard | new feature or behaviour change, 3 or more files, shared modules, UI | implement, code review and delivery audit in parallel, 1 fix round |
| strict | schema or migrations, auth, payments or quotas, secrets, dependencies, CI/deploy, public API, data deletion, security-sensitive code | preflight until GO, implement, both reviews, up to 3 fix rounds |

The level is re-checked after the change and can only go up. Say "quick" or "full review" to override. When unsure the higher level wins. Full rules: `skills/change-workflow/references/levels.md`.

## What is inside

| Part | Role |
|---|---|
| skill `change-workflow` | The orchestrator: picks the level, runs the agents, batches all questions into one round, reports. |
| agent `preflight-reviewer` | Before the change: completeness, consistency, consequences, alternatives; returns GO, NEEDS_INPUT or STOP with an acceptance checklist. |
| agent `code-reviewer` | After the change: correctness, security, data safety, performance, tests; runs your checks; returns APPROVE, FIXES_NEEDED or NEEDS_INPUT. |
| agent `delivery-auditor` | After the change: verifies every intended item against the real state; reviews product fit when your project's purpose is documented; returns COMPLETE, INCOMPLETE or NEEDS_INPUT. |
| agent `change-finisher` | Fixes the findings without scope creep; asks instead of guessing. |

The agents cannot ask you questions themselves. They return `NEEDS_INPUT`, and the orchestrator asks you everything in one batch. Nothing is assumed.

## Files

**Creates:** nothing.
**Reads:** `CLAUDE.md`, `AGENTS.md`, README; if present, `.u13/config.md`, `.u13/plans/*` (as the intent and list of changed files) and `.u13/memory/*` (recorded decisions and status).
**Changes:** only the code and files you asked to change. Product docs are proposed, not edited, unless `docs_edit: auto`.

Without git the loop keeps a list of created, modified and deleted files and gives it to the reviewers instead of a diff.

## Settings (optional `.u13/config.md`)

You do not need to configure anything. If you want to: `language` (default English) and `decisions` (`manual` or `delegate`). Rarely needed: `level` (force one level), `docs_edit` (let the plugin edit product docs), `gates` (the commands that check a change; detected from your project by default). See the root README for the table.

## Works with

- `task-planner`: each subtask of a plan goes through this loop; with `decisions: delegate` open questions go to its decider agent.
- `project-memory`: reviewers check changes against recorded decisions, and the loop ends by updating status, decisions and backlog.

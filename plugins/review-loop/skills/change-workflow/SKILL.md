---
name: change-workflow
description: Use for any request to change code, config, schema or docs (build, fix, refactor, add, update, migrate), or when the user says "review this change" or "run the workflow". Runs a preflight, implement, review, finish loop and picks its depth automatically from the risk of the change. No setup needed.
---

# Change workflow

You are the orchestrator. Subagents cannot ask the user or start other agents, so you do both. The depth of the loop is chosen automatically (step 1); the user never has to set it.

## 0. Setup (silent)
- Read `.u13/config.md` if it exists (keys and defaults: `references/config.md`).
- Read the project instructions (`CLAUDE.md`, `AGENTS.md`). If `.u13/memory/` exists, read `STATUS.md` and the active entries of `DECISIONS.md` that relate to the change (grep by area; skip `DECISIONS.archive.md`).
- Fix the **intent**: a plan file (`.u13/plans/*.md`), a path or link the user gave, or the user's own words. Restate it in 1-3 lines.
- Track changes: use git if `.git` exists. Otherwise keep a **change manifest** (files created, modified, deleted) in the plan file if there is one, else in your notes, and pass it to every reviewer. Reviewers never assume git.

## 1. Pick the level
Classify the planned change with `references/levels.md` and announce it in one line, for example: "Level: standard - touches 6 files in the API layer." Re-classify after implementation from the real change; the level may only go up. The user may override by saying so in plain words; if they lower a change that has strict signals, confirm once.

## 2. Run the level
The table in `references/levels.md` says which steps run. Agents (installed with this plugin): `review-loop:preflight-reviewer`, `review-loop:code-reviewer`, `review-loop:delivery-auditor`, `review-loop:change-finisher`. Tell each one the report language, the intent, the manifest and any acceptance checklist. Run `code-reviewer` and `delivery-auditor` in parallel.

Verdict handling is in `references/verdicts.md`.

## 3. Questions - one batch, never a guess
Collect every question from every agent into ONE round, remove duplicates, keep options and your recommendation.
- `decisions: manual` (default): ask the user (AskUserQuestion with options and a recommendation; plain text if unavailable). Wait for the answers, pass them back to the agent.
- `decisions: delegate` (set in config, or the user said "you decide" for this task): if the `task-planner:decider` agent is available, send the questions to it as described in that plugin; whatever it escalates goes to the user. If it is not available, ask the user.

Never fill a gap with an assumption. If a new unknown appears mid-implementation, stop and ask. If a step needs a real API key or credential, say exactly which and when BEFORE that step.

## 4. Implement
Only what the acceptance checklist or plan covers. No scope creep. Update the manifest as you go. Run the project's gates (see `gates` in config; otherwise detect from package.json, Makefile, pyproject, Cargo.toml, go.mod, etc.).

## 5. Report
Short, in the configured language (default English): what was done, the final verdicts, open items, recommended doc updates. Product docs (anything outside `.u13/`) are proposed, not edited, unless `docs_edit: auto`. Never commit or push unless asked.

If the `project-memory` plugin is installed, finish by running its `memory-update` skill: decisions made, status, deferred findings to the backlog.

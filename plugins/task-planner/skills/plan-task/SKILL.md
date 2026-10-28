---
name: plan-task
description: Use at the start of a larger or unclear task - a feature, an idea, a migration, "plan this", "split this into steps", "figure out what to build". Clarifies the goal, splits it into subtasks, writes a plan file, and hands each subtask to the change workflow. Decisions are made by the user (default) or by a separate decider agent when the user says "you decide", "decide yourself" or "ty decydujesz".
---

# Plan task

Turn a task or idea into a clear plan with subtasks, then execute it step by step. Small tasks skip this skill entirely: if the request is clear and fits one change, go straight to `review-loop:change-workflow` (or just do it if that is not installed).

## 1. Intake
Restate the goal in 1-3 lines. Read project context: instructions (`CLAUDE.md`, `AGENTS.md`), and if present `.u13/memory/STATUS.md`, active `DECISIONS.md` entries for the area, `BACKLOG.md`, `.u13/config.md`.

## 2. Decision mode
- **manual** (default): decisions go to the user.
- **delegate**: used when the config says `decisions: delegate`, or when the user says things like "you decide", "decide yourself", "autopilot", "ty decydujesz", "sam zdecyduj" for this task. Applies to this task only unless the config says otherwise.

Details, including escalation rules and model choice, are in `references/decision-modes.md`. Read it before the first delegated decision.

## 3. Clarify - one batch
List everything unclear that changes what gets built: scope in/out, users, constraints, acceptance, dependencies, risks. Ask in ONE round, each question with options and your recommendation.
- manual: AskUserQuestion (plain text if unavailable).
- delegate: send the questions to `task-planner:decider`; ask the user only what it escalates.
Never fill a gap with a guess. If a requirement needs a real API key or credential, flag exactly which and at which step.

## 4. Decompose (only when the task is big)
Split when any holds: more than one independent deliverable, several layers (data, API, UI), more than about 5 files in different areas, or it will not fit one session. Otherwise keep a single task with an acceptance list.
Each subtask gets: ID `T-<n>`, goal, scope in/out, dependencies, size (S/M/L), acceptance criteria, and a risk hint. Use `strict` for a subtask that touches schema or migrations, auth, payments or quotas, secrets, dependencies, CI/deploy, a public API, data deletion, or security-sensitive code; `standard` for other features, 3 or more files, or UI; `light` for tiny edits, docs and comments. Keep subtasks independently reviewable and in dependency order.

## 5. Write the plan file
Create `.u13/plans/<YYYY-MM-DD>-<slug>.md` from `references/plan-template.md`. The slug is 2-5 words in lowercase ASCII letters, digits and hyphens only (transliterate or drop accents and any other characters), so the name is valid on every operating system. It is also the source of intent for the review loop and holds the change manifest. Show the user a short summary (not the whole file) with the open decisions.

## 6. Go
manual: wait for the user's go. delegate: proceed unless something was escalated. Then for each subtask in order:
- run `review-loop:change-workflow` with the subtask as intent (or implement it directly if that plugin is missing),
- tick its checklist in the plan file and update the manifest,
- if `project-memory` is installed, run `project-memory:memory-update` (decisions, status, deferred items).
Report after each subtask in two lines, and at the end summarise outcomes and what is left.

If a subtask reveals that the plan is wrong, stop, update the plan file, and tell the user why before continuing.

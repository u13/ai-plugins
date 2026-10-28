# task-planner

Turns a big or unclear task into a clear plan. It asks every open question in one batch, splits the work into subtasks with acceptance criteria and dependencies, writes a plan file, and then works through the subtasks one by one. Decisions are made by you, or, if you say so, by a separate decider agent.

Independent of the other plugins in this marketplace. It does not need git.

## Install

```bash
claude plugin marketplace add u13/ai-plugins
claude plugin install task-planner@u13
```

For a whole project add `--scope project` to both commands and commit `.claude/settings.json`; each teammate runs the install command once. To use it only for yourself in one repository (nothing shared or committed), add `--scope local` instead.

## How to use

Describe the task in plain words ("Plan team accounts with invitations"), or run the command:

```
/plan-task Build team accounts with invitations
```

`/plan-task` is the short form. Plugin commands are also available in the full form `/task-planner:plan-task`; use it if the short form is not recognised in your version, or if another plugin has a command with the same name.

What happens:

1. **Intake.** It restates the goal and reads the project context.
2. **Questions.** Everything unclear that changes what gets built is asked in one round, with options and a recommendation.
3. **Split.** Only if the task is big (several independent deliverables or layers, or more than one session of work). Each subtask `T-1`, `T-2`, ... has a goal, scope, dependencies, size and acceptance criteria.
4. **Plan file.** Written to `.u13/plans/<date>-<slug>.md`. You get a short summary and the open decisions.
5. **Go.** After your go (or immediately in delegate mode) it executes the subtasks in order and ticks them off in the plan file.

Small, clear tasks skip planning and go straight to implementation.

## Two decision modes

| Mode | How to turn it on | Who decides |
|---|---|---|
| **manual** (default) | nothing to do | You, in one batch of questions with options and a recommendation. |
| **delegate** | say "you decide", "decide yourself", "autopilot" or "ty decydujesz" in the prompt, or set `decisions: delegate` in `.u13/config.md` | A separate `decider` agent, started with fresh context. |

The decider **hands the question back to you** for anything irreversible, anything about cost, pricing or billing, security, privacy or legal matters, public-facing copy, credentials or paid external calls, anything that contradicts a recorded decision, and whenever it is not confident. Every decision it does make is logged with a `[delegated]` tag so you can review and overturn it.

**Model.** The first time delegate mode is used, you are asked once which model the decider should use. The recommendation is the strongest available (`opus`): decisions are costly to get wrong, and the calls are few and short. Alternatives are `inherit` (the model of your session) or `sonnet`. Your answer is stored as `decider_model` in `.u13/config.md`. **Effort** is fixed to `high`.

The decider is the same kind of model with fresh context, so treat it as a time-saver for reversible, local choices, not as a replacement for your judgment on the items above.

## Files

**Creates:** `.u13/plans/<date>-<slug>.md` for each larger task (goal, scope, open questions, subtasks, list of changed files, short log). Creates `.u13/config.md` only when it needs to store your decider model.
**Reads:** `CLAUDE.md`, `AGENTS.md`, and, if present, `.u13/memory/*` and `.u13/config.md`.
**Changes:** only the plan file and, when it executes subtasks, the code you asked for.

## Works with

- `review-loop`: each subtask goes through the review loop; the plan file is its intent and change list.
- `project-memory`: the planner reads decisions and status before asking questions and records decisions and finished subtasks.

Without them it implements subtasks directly and notes decisions in the plan file.

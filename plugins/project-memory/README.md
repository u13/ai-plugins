# project-memory

A lightweight working memory for a project: decisions, status and backlog as short, ID-numbered lines in `.u13/memory/`. A new session reads these files and continues where the last one stopped, without you re-explaining the project.

Independent of the other plugins in this marketplace. It does not need git.

## Install

```bash
claude plugin marketplace add u13/ai-plugins
claude plugin install project-memory@u13
```

For a whole project add `--scope project` to both commands and commit `.claude/settings.json`; each teammate runs the install command once. To use it only for yourself in one repository (nothing shared or committed), add `--scope local` instead.

## How to use

There is nothing to set up. The `memory-update` skill creates `.u13/memory/` itself the first time it has something to write, and from then on writes entries when something happens, not on every message:

- a decision was made or confirmed: one line in `DECISIONS.md`;
- a task was finished: `STATUS.md` is updated, a finished milestone goes to `HISTORY.md`;
- something was deferred (a minor finding, a "later"): one line in `BACKLOG.md`;
- a file grew too long: it is compacted.

You can also ask directly ("log this decision") or run `/project-memory:memory-update`.

**Optional: migrate old notes.** If the project already has a decision log, `docs/memory/`, ADRs or a TODO, run `/project-memory:memory-init`. It finds them, tells you what it found, and asks before migrating. Migration only copies and compresses: your originals stay untouched, and it shows what was left out. It can also create the empty files up front, but that is not needed.

Optional: put this in `CLAUDE.md` or `AGENTS.md` so every session reads the memory first:

```
Project memory lives in .u13/memory/. Read STATUS.md first; grep DECISIONS.md by area before changing something.
```

## Files it creates (in `.u13/memory/`)

| File | Content | Size rule |
|---|---|---|
| `STATUS.md` | Now, next, blocked, last 5 done | about 80 lines at most; older items are compacted |
| `DECISIONS.md` | Active decisions, one line each | superseded lines move to the archive |
| `DECISIONS.archive.md` | Superseded or obsolete decisions | grows; read only when a line points there |
| `BACKLOG.md` | Deferred items with priority P0 to P2 | remove lines when done |
| `HISTORY.md` | Finished milestones, one line each | append-only; read only on demand |
| `decisions/D-<n>.md` | A longer explanation of one decision | only when really needed |

It edits only these files. It never touches your product docs.

## Line format

```
D-142 | 2026-10-27 | auth | Use hosted auth. Why: free tier is enough.
D-150 | 2026-10-28 | payments | Switch payment provider. Supersedes: D-12. [delegated]
B-07  | P1 | billing | Retention cron for expired uploads | source: review of T-2
M-12  | 2026-10-27 | Billing page shipped
```

A decision can be a few words. `Why:` is added only when the reason is not obvious. Prefixes: `D-` decision, `B-` backlog, `M-` milestone, `T-` task. IDs are never reused. The full format is in `skills/memory-update/references/format.md`.

## Works with

- `review-loop`: reviewers check changes against your recorded decisions (citing `D-` IDs), and the loop ends by updating the memory.
- `task-planner`: plans read the memory before asking questions; decisions made by the decider agent are logged with `[delegated]`.

# Memory file formats

All files are plain Markdown. One entry per line. Fields are separated by ` | `.

## DECISIONS.md (active decisions only)

```
# Decisions
<!-- D-<n> | date | area | decision. Why: reason (optional). Supersedes: D-<n> (optional). [delegated] (optional) -->

## auth
D-142 | 2026-10-27 | auth | Use hosted auth, no custom login. Why: ready UI, free tier is enough.
D-150 | 2026-10-28 | payments | Switch payment provider. Supersedes: D-12. [delegated]

## plans
D-143 | 2026-10-27 | plans | Free plan: 50 generations per month.
```

- A decision can be as short as a few words.
- Group lines under `## area` headers once the file is long.
- A long explanation lives in `decisions/D-<n>.md`; the line ends with `-> decisions/D-<n>.md`.

## DECISIONS.archive.md

Same line format. Superseded lines end with `[superseded by D-<n>]`. Obsolete ones end with `[obsolete: reason]`.

## STATUS.md (current state only, about 80 lines at most)

```
# Status
Updated: 2026-10-27

## Now
- T-3 Account page redesign (in progress, 2 of 5 subtasks done)

## Next
- B-07 Retention cron

## Blocked / open questions
- Waiting for: payment provider answer

## Last done (latest 5)
- 2026-10-27 M-12 Billing page shipped
```

`Now`, `Next`, `Blocked` and the last 5 done items stay around 25-40 lines, so the file does not grow with the age of the project. Anything that is only "someday" belongs in `BACKLOG.md`, not in `Next`.

## HISTORY.md (append-only, not read by default)

```
# History
M-01 | 2026-07-06 | Auth and onboarding
M-12 | 2026-10-27 | Billing page shipped
```

## BACKLOG.md

```
# Backlog
B-07 | P1 | billing | Retention cron for expired uploads | source: review of T-2
B-08 | P2 | ui | Empty state on results page | source: code-reviewer NIT
```

Priorities: `P0` blocks release, `P1` next up, `P2` someday. Remove a line when done and, if worth remembering, add a milestone line in HISTORY.md.

## IDs
- `D-` decisions, `B-` backlog, `M-` milestones, `T-` tasks (plans).
- Zero-pad to 3 digits (`D-007`) once an ID passes 99.

<!-- Keep identical to ../../memory-init/references/format.md (apart from this note). -->

---
name: memory-update
description: Keeps .u13/memory (STATUS, DECISIONS, BACKLOG) current. Use when a decision was just made or confirmed, when a task or subtask was finished, when review findings were deferred, or when a memory file has grown too long. Writes short ID-numbered lines. Not for product documentation.
---

# Memory update

Memory is the project's working state so any new session can continue without re-explaining. It lives in `.u13/memory/` (`STATUS.md`, `DECISIONS.md`, `DECISIONS.archive.md`, `BACKLOG.md`, `HISTORY.md`). Formats are in `references/format.md`. If the folder does not exist, create it yourself from the templates in `references/format.md` the first time you need to write, without asking, and say so in one line. If you notice older notes that could seed it (`docs/memory/`, a decision log, ADRs, a TODO), mention once that `project-memory:memory-init` can migrate them; do not migrate on your own.

You may edit `.u13/memory/*` without asking. You never edit product docs (README, specs, anything outside `.u13/`); propose those changes instead.

## When to write (events, not every message)
1. **A decision was made.** The user answered a question, picked an option, or the decider agent decided. Append one line to `DECISIONS.md`. Do not log trivia; log only what constrains future work.
2. **A task or subtask finished.** Update `STATUS.md` (Now, Next, Last done). When a milestone is complete, append one `M-` line to `HISTORY.md`.
3. **Findings or ideas were deferred.** MINOR/NIT findings, "later" items, known gaps: add `B-` lines to `BACKLOG.md` with a priority.
4. **A file is too long.** Compact it (below).

Do not rewrite the files on every prompt and answer. Nothing is written for chit-chat.

## Rules
- One line per entry. Say it in as few words as the meaning allows. A short decision may be only a few words.
- Add `Why:` only when the reason is not obvious. A long rationale goes into `.u13/memory/decisions/D-<n>.md` and the line links to it.
- New ID = highest existing number + 1, found by scanning `DECISIONS.md` AND `DECISIONS.archive.md` (`D-`), `BACKLOG.md` (`B-`), `HISTORY.md` and `STATUS.md` (`M-`). Never reuse an ID.
- A decision that replaces an older one: add `Supersedes: D-<old>` to the new line and move the old line to `DECISIONS.archive.md` with `[superseded by D-<new>]`.
- Tag decisions made by the decider agent with `[delegated]` so the user can review and overturn them.
- Mark which decision a change followed by citing its ID in reports and commit text when you refer to it.

## Compaction
- `STATUS.md` is read in full by every session and agent, so it stays small: about 80 lines at most. It holds only the current state (Now, Next, Blocked, the last 5 done). When it grows past that, move finished milestones to `HISTORY.md` as one `M-` line each, push "Next" items that are really backlog into `BACKLOG.md`, and drop anything stale.
- `HISTORY.md` is append-only and is not read by default; open it only when you need to know what was done before.
- `DECISIONS.md` holds only active decisions. Move superseded or obsolete lines to the archive. If the active file passes about 150 lines, group by area with a one-line header and merge near-duplicates (keep the IDs of the merged lines in the surviving line).
- Never delete information the user might need to audit later; archive it.

## Reading (for other agents)
Read `STATUS.md` fully, and in `DECISIONS.md` grep by area or keyword for the change at hand. Open the archive, `HISTORY.md` or `decisions/D-<n>.md` only when needed or when a line points there.

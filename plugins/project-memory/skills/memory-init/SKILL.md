---
name: memory-init
description: Optional. Migrate existing notes or decision logs into the short ID format in .u13/memory, or create the memory folder up front. Not required - the memory-update skill creates the folder itself the first time it writes.
disable-model-invocation: true
---

# Memory init

Set up `.u13/memory/` for this project, mainly to migrate existing notes. Optional: `memory-update` creates the empty folder on its own the first time it needs to write, so run this only to migrate old notes or to create the files up front.

## Steps
1. If `.u13/memory/` already exists, say so, show what is in it, and stop (offer `project-memory:memory-update` for compaction).
2. Create `.u13/memory/STATUS.md`, `DECISIONS.md`, `DECISIONS.archive.md`, `BACKLOG.md` and `HISTORY.md` using the templates in `references/format.md`. Do not create a `.u13/config.md`; it is optional.
3. Look for existing notes that could seed them: `docs/memory/`, `DECISIONS.md`, `docs/decisions/`, `adr/`, `STATUS.md`, `TODO.md`, `CHANGELOG`, project notes mentioned in `CLAUDE.md` or `AGENTS.md`. If you find any, tell the user what you found and ask whether to migrate.
4. **Migration (only with the user's yes).** Read the old files, extract each distinct decision as one short line in the `D-` format with its original date if known, mark clearly superseded ones as archived, build a short `STATUS.md` with only the current state, and put finished milestones into `HISTORY.md` as one line each. Preserve the originals untouched: copy, never move or delete. Show a summary of what was migrated and what was left out, because compression loses detail.
5. Offer (do not apply without a yes) a 3-line pointer for the project's instruction file (`CLAUDE.md` or `AGENTS.md`):

```
Project memory lives in .u13/memory/. Read STATUS.md first; grep DECISIONS.md by area before changing something.
Keep it current with the project-memory:memory-update skill.
```

6. Report in two lines: what was created, and what (if anything) was migrated.

Keep `.u13/` under version control if the project uses git: it is shared project state and contains no secrets.

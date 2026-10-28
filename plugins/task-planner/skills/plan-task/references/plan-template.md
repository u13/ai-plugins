# Plan file template

Path: `.u13/plans/<YYYY-MM-DD>-<slug>.md` (slug: lowercase ASCII letters, digits and hyphens only)

```markdown
# <Plan title>

Status: draft | in progress | done
Decisions: manual | delegate
Created: <date>

## Goal
<1-3 lines>

## Scope
In: ...
Out: ...

## Constraints and decisions used
- <constraint>
- D-<n> <short reminder>      <!-- only if project-memory is used -->

## Open questions
- [ ] <question> (owner: user | decider)

## Subtasks
### T-1 <title>   (size: S|M|L, level hint: light|standard|strict)
Depends on: -
Goal: ...
Scope in/out: ...
Acceptance:
- [ ] ...
- [ ] ...

### T-2 <title>   (size: M, level hint: standard)
Depends on: T-1
...

## Change manifest
<!-- Updated by the orchestrator while implementing. Reviewers use it when there is no git. -->
- modified: path/to/file
- created: path/to/new-file
- deleted: path/to/old-file

## Log
- <date> <one line per notable event: plan agreed, subtask done, plan changed>
```

For a small task there is no need for several subtasks: keep one `T-1` with an acceptance list.

# Brief template

Path: `.u13/brief/<YYYY-MM-DD>-<slug>.md` (slug: lowercase ASCII letters, digits and hyphens only). Create the draft after the first analysis and update it every round, so an interrupted session can resume.

```markdown
# <Idea name>

Status: draft | ready
Purpose: commercial | internal | hobby | open source
Created: <date>   Updated: <date>   Round: <n>

## Start here (for the planner)
Goal: <one line>
Hard constraints: <list>
Needs real credentials or paid services: <none | which, at which step>
First slice: <smallest useful milestone>

## Problem
## Users
## Solution
Core flow:
1. ...
## Scope
In: ...
Out: ...
Later: ...
## Success criteria
- ...
## Constraints
## External services and credentials
## Demand and market
Sources: <URL and date, or `unverified`>
## Business model and costs
## Experience (UX/UI)
## Reaching users
## Alternatives considered
- <alternative>: <why not / why later>
## Risks and unknowns
- <risk> - <mitigation or who answers>

## Decisions
Status tags: confirmed | delegated | proposed | assumed | parked
- [confirmed] <decision>. Why: <reason>
- [delegated] <decision the user handed to the AI>. Why: <reason>

## Parked
- <item> - consequence of leaving it open: ... - revisit when: ...

## Log
- <date> round <n>: <one line: what was agreed or changed>
```

Write "not applicable: <reason>" under any section that does not apply to the purpose. When the brief is `ready`, there must be no `proposed` or `assumed` items left.

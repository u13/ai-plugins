# Levels

The level is chosen by the orchestrator from observable signals. The user does not configure it.

## Classification

**strict** - if ANY of these is true:
- database schema or migrations
- authentication, authorization, permissions
- payments, billing, quotas, credits, pricing logic
- secrets, environment variables, credentials
- adding, removing or upgrading dependencies
- CI/CD, deployment, infrastructure configuration
- public API or contract changes
- data deletion or retention
- security- or privacy-sensitive code (file uploads, parsing untrusted input, crypto, personal data)

**standard** - not strict, and ANY of:
- new feature or behaviour change
- 3 or more files, or more than about 100 changed lines
- shared or core modules
- user-visible UI or copy

**light** - everything else: typos, comments, formatting, docs-only edits, a rename within one file, small config of local tooling. Roughly 2 files or fewer, 50 lines or fewer, no behaviour change.

When unsure between two levels, pick the higher one.

## What runs

| Step | light | standard | strict |
|---|---|---|---|
| Preflight (`preflight-reviewer`) | no | only if the plan has open questions or the intent is unclear | always; repeat until `GO` |
| Implement | yes | yes | yes, only what the acceptance checklist covers |
| Gates (typecheck, lint, tests, build) | yes | yes | yes |
| `code-reviewer` + `delivery-auditor` in parallel | no (self-check against the intent) | yes | yes |
| `change-finisher` rounds | n/a | up to 1 | up to 3 |

After the maximum rounds without approval, stop and report what is left and why.

## Re-classification
After implementing, look at the real change (git diff or the manifest). If it now matches a higher level (it grew, or it touched a strict area), move up and run the missing steps. Never move down after the fact.

## User override
The user can say "quick", "just do it", "full review", "be strict" or similar. Honour it. If the user lowers the level of a change that has strict signals, state the signals and ask once for confirmation.

## Trivial edits
For typos, comment-only or formatting-only edits, skip the loop and say so in one line.

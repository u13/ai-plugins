# Verdicts and what to do with them

| Agent | Verdicts | Orchestrator action |
|---|---|---|
| `preflight-reviewer` | `GO` | Keep its acceptance checklist as the definition of done and implement. |
| | `NEEDS_INPUT` | Ask all its questions in one batch, then re-run it with the answers. Loop until `GO`. |
| | `STOP` | Report to the user and wait. Do not implement. |
| `code-reviewer` | `APPROVE` | Nothing to fix. |
| | `FIXES_NEEDED` | Send findings to `change-finisher`. |
| | `NEEDS_INPUT` | Ask the questions, pass answers on. |
| `delivery-auditor` | `COMPLETE` | Nothing to fix. |
| | `INCOMPLETE` | Send its fix list to `change-finisher`. |
| | `NEEDS_INPUT` | Ask the questions, pass answers on. |
| `change-finisher` | per item `FIXED` / `BLOCKED` / `DISAGREE` | Never accept its work as final. Re-run both reviewers. If an item is `DISAGREE`, judge the evidence yourself and, if it is a real disagreement, tell the user. |

## Finish loop
1. Run `code-reviewer` and `delivery-auditor` in parallel.
2. If either is not approving, run `change-finisher` with the combined findings and the acceptance checklist. Ask the user any `NEEDS_INPUT` questions in one batch and pass the answers.
3. Re-run step 1. Stop when both approve, or after the level's maximum rounds (standard: 1, strict: 3).
4. If still not approved, report what is left and why.

## Hand-off rules
- Always pass: the intent, the user's earlier answers, the acceptance checklist if one exists, the change manifest, the report language.
- Agents report; they do not coordinate. You decide the next step.
- Agents never edit product docs or `.u13/memory/*`. You (or the `memory-update` skill) do.

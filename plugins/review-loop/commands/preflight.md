---
description: Pre-implementation review of a planned change (plan file, prompt or description)
argument-hint: <path to a plan or prompt, or a description of the change>
---
Run only the preflight step of the `review-loop:change-workflow` skill for: $ARGUMENTS

Use `review-loop:preflight-reviewer`. Ask me all of its questions in one batch and re-run it until the verdict is GO or STOP. Then show me the verdict and the acceptance checklist and wait. Do not implement anything unless I say so.

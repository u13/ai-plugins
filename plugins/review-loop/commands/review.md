---
description: Post-implementation review loop - code review and completeness review, then finish until approved
argument-hint: [intent, plan file or acceptance checklist - optional]
---
The change is already implemented. Intent (optional): $ARGUMENTS

Run the review and finish steps of the `review-loop:change-workflow` skill: pick the level from the real change, run `review-loop:code-reviewer` and `review-loop:delivery-auditor` in parallel, then the `review-loop:change-finisher` loop with batched questions to me for any NEEDS_INPUT, then the final report. No assumptions.

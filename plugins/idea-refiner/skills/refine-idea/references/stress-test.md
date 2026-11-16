# Stress-test

An automatic review of the nearly finished brief by independent critics, repeated until a cycle finds nothing that needs a decision. It exists so surprises show up now, in the brief, and not during implementation.

## When
Automatically, once readiness conditions 1-3 hold (see `readiness.md`) and before the final confirmation. Say in one line that it is starting. Skip it only if the user said so ("skip stress-test"); note the skip in the brief's log.

## Which critics
Critics are started with `idea-refiner:critic`, one per angle, in parallel. The angles are defined in the agent.

| Purpose | Angles |
|---|---|
| Commercial product or service | skeptic, market, economics, experience, implementation, trust, coherence |
| Internal tool | skeptic, experience, implementation, trust (only if personal data or legal exposure), coherence |
| Hobby or learning | skeptic, implementation, coherence |
| Open source | skeptic, market (who it helps, existing projects), implementation, trust (licences), coherence |

Pass each critic: its angle, the purpose, the kind (digital or physical/service), the brief path, the language, and the findings already raised with their resolution. If the agent is not available, go through the angles yourself, one at a time.

## Triage
Merge duplicates across critics and rank by severity.
- **blocker and major**: ask the user, in rounds, with the usual rules (one batch, about 5 and at most 7 questions, options, recommendation, Discuss and You decide). Items that define the idea still need an explicit yes.
- **contradiction**: always goes to the user. Never resolve one silently.
- **minor**: record under "Risks and unknowns" tagged `[stress-test]` and list them separately in the final summary. The user's final yes covers them and the user can strike any.
- **implementation list**: put it in "Implementation tools and resources" as `proposed`; the user confirms or corrects it in the next round or in the final summary.

## The loop
1. Run the critics. Triage. Fold the confirmed answers into the brief.
2. If any blocker or major was found, return to the rounds with only the new items. When readiness 1-3 hold again, run the next cycle.
3. From the second cycle on, start only the critics that raised a blocker or major last time or that own a changed section, plus `coherence` every time.
   - market: Demand and market, Users, Reaching users
   - economics: Business model and costs, Constraints
   - experience: Solution, Users, Experience
   - implementation: Solution, Constraints, External services, Implementation tools and resources, First slice
   - trust: External services, Constraints, Risks and unknowns
   - skeptic: Problem, Alternatives, Risks and unknowns
4. Stop when a cycle returns no blocker and no major. Then go to the final confirmation.
5. After cycle 3, say plainly what keeps coming up and let the user choose: keep going, park the rest with stated consequences, or stop.

Log every cycle in the brief: `cycle <n> | <date> | angles | blocker <x> major <y> minor <z> | outcome`.

## Honest limit
The critics are the same kind of model, so they share blind spots, and they cannot talk to real customers. "Ready" means well thought through, not validated. The skeptic names the cheapest real-world test; offer to put it in the brief as the first step.

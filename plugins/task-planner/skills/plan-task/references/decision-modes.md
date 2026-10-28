# Decision modes

## manual (default)
Every decision that is not already settled by recorded decisions goes to the user, batched, with options and a recommendation.

## delegate
A separate agent (`task-planner:decider`) decides, so the main session does not grade its own choices.

### Trigger
- `decisions: delegate` in `.u13/config.md`, or
- the user's words for this task: "you decide", "decide yourself", "autopilot", "ty decydujesz", "sam zdecyduj". Scope: this task only, unless the user asks to make it permanent (then write it to the config).

### What the decider gets
Fresh context with only: the question and options (with pros/cons), the task goal, constraints, and relevant decision IDs. It reads project files itself. It does not get the conversation, so it does not inherit the implementer's bias.

### Model and effort
- Effort is fixed to `high` in the agent definition.
- Model: the first time delegate mode is used, if `decider_model` is `ask` (the default) or missing, ask the user once, and store the answer in `.u13/config.md` as `decider_model`:
  - Recommend the strongest available model (`opus`). Why: decisions are expensive to reverse, calls are few and short, so the extra cost is small and a stronger model catches more weak options. A different model from the implementer also reduces shared blind spots.
  - Alternatives: `inherit` (same as the session) or `sonnet` (cheaper).
  - If a question cannot be asked (headless run), use `inherit` and say so in the report.
- When you start the decider, pass the chosen `model` explicitly in the call as well as relying on the agent definition: some Claude Code versions have been reported to ignore the definition's model setting, and an environment override can change it silently. Note in the report which model you requested.

### Escalation (never delegated)
Hard-to-reverse actions; cost, pricing and billing; security, privacy, legal; public-facing copy and branding; real credentials or paid external calls; contradicting an active decision; anything the user said they want to decide; low confidence. These go to the user, with the decider's "missing info" if it gave any.

### Logging
Every decision the decider makes is logged by `project-memory:memory-update` (or noted in the plan file if that plugin is missing) as a short line tagged `[delegated]`, so the user can review and overturn it. When the user overturns one, record the new decision with `Supersedes:`.

### Honest limit
The decider is the same kind of model with the same blind spots, only with fresh context and possibly higher capability. It is a time-saver for reversible, local choices, not a replacement for judgment on the decisions above.

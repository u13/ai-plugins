# idea-refiner

Turns a rough idea for a project into a brief that is clear enough to plan and build from. It analyses the idea, asks what is unclear in rounds, proposes options with a recommendation, and keeps going until you and the AI are on the same page. Then it writes the brief and points to the next step.

Independent of the other plugins in this marketplace. It does not need git, docs or any setup.

## Install

```bash
claude plugin marketplace add u13/ai-plugins
claude plugin install idea-refiner@u13
```

For a whole project add `--scope project` to both commands and commit `.claude/settings.json`; each teammate runs the install command once. To use it only for yourself in one repository (nothing shared or committed), add `--scope local` instead.

## How to use

Describe the idea in plain words ("I want to build an app that..."), or run the command:

```
/refine-idea A tool that helps freelancers track unpaid invoices
```

What happens:

1. **Capture.** It says what it understood, what kind of thing it is, and what it is for (commercial, internal tool, hobby, open source). The purpose decides how deep the business questions go. It does not guess gaps.
2. **Analysis.** A separate `challenger` agent (analysis only, it decides nothing) looks for gaps, contradictions, risks and simpler alternatives, and may search the web for existing solutions. It also looks at the idea like a business: demand and market, business model and costs, UX/UI and user psychology, and how to reach users.
3. **Questions.** One batch per round, about 5 and never more than 7, most important first. Each has options and a recommendation, so answering "as recommended" is fine.
4. **Brief.** Your answers go into a draft brief after every round, so an interrupted session can continue.
5. **Ready check.** It tells you how far it is. When every area is agreed and there are no open questions or unconfirmed assumptions, it moves on. After round 3 it names what still blocks and lets you choose: keep going, park the rest with stated consequences, or stop.
6. **Stress-test.** Runs automatically (say "skip stress-test" to skip it): independent critics review the brief, see below.
7. **Final confirmation.** A one-screen summary. The brief is `ready` only after your yes.
8. **Next step.** It saves the brief and tells you what to run next.

"I don't know" is a valid answer: it offers a way to find out, or parks the item with its consequence. It never turns it into a silent assumption.

## You decide, the AI proposes

The idea has to come from you, so there is no autopilot mode. The AI asks, analyses and proposes; every point is yours to decide. Each question comes with its options and a recommendation, plus two extra choices:

- **Discuss**: talk it through first (short pros and cons) and then answer.
- **You decide**: hand that one question to the AI. It decides, gives the reason, and marks the item `delegated`. Delegated items are listed separately in the final summary so you can overturn them.

For anything that defines the idea itself (what it is, for whom, the main promise), pricing, public naming and positioning, security, privacy and legal matters, hard-to-reverse choices, and credentials or paid services, "you decide" is not enough: the AI gives a recommendation and waits for your explicit yes. Everything the AI adds stays `proposed` until you confirm it.

## Stress-test

When everything is agreed, a set of independent `critic` agents reviews the brief, each from one angle: **skeptic** (pre-mortem, riskiest assumptions and the cheapest real-world test), **market**, **economics**, **experience**, **implementation** (can it be built or run with the tools, people and budget you have; software or a physical business, and a list of the tools and resources needed), **trust** (legal and privacy flags, security, abuse) and **coherence** (contradictions inside the brief). Which ones run depends on the project's purpose.

Each finding has a severity. Blockers and majors come back to you as questions, in the usual rounds. Contradictions always come back to you. Minor notes are recorded in the brief and listed in the final summary. Then the stress-test runs again, only with the critics whose area changed, plus the coherence check, until a cycle finds no blocker or major. After the third cycle it asks whether to keep going. It costs more tokens than a plain interview; the point is to find surprises in the brief and not during implementation.

Limit: the critics are the same kind of model and cannot talk to real customers. "Ready" means well thought through, not validated; the skeptic names the cheapest real test.

## Business view

For a commercial product it checks all four: **demand and market**, **business model and costs**, **experience** (UX/UI and user psychology) and **reaching users**. For an internal tool, a hobby project or open source, those areas shrink or are marked "not applicable" with a reason you have seen. Pricing and positioning stay your decisions.

Market claims are where an AI invents things most easily, so every claim about demand, competitors, prices or market size needs a source (URL and date) or is labelled `unverified`. Your own knowledge of the market comes first.

## The brief

`.u13/brief/<date>-<slug>.md`, with a "Start here" block for whoever plans next, then: problem, users, solution, scope (in, out, later), success criteria, constraints, external services and credentials, demand and market, business model and costs, experience, reaching users, alternatives, risks, the implementation tools and resources, decisions with their status, parked items, the stress-test log and a short log. Language: the one you use in the conversation, unless `language` is set in `.u13/config.md`. The agent runs with the model of your session and `effort: high`.

## Files

**Creates:** `.u13/brief/<date>-<slug>.md` for each idea. Nothing else.
**Reads:** `CLAUDE.md`, `AGENTS.md`, README, and, if present, `.u13/memory/*` and `.u13/config.md`.
**Changes:** nothing else. It writes no code.

## Works with

- `task-planner`: at the end it suggests `/plan-task` with the brief path as the confirmed requirements, and offers to start it.
- `project-memory`: decisions and parked items from the brief are logged there, and existing decisions are read before questions are asked.

Without them the brief is still self-contained: give it to any agent.

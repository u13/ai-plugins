---
name: refine-idea
description: Use when the user has a project, product, tool or feature-set idea that is not fully defined - "I have an idea", "help me flesh this out", "let's refine this", "doprecyzujmy pomysł". Interviews in rounds and analyses the idea until every important point is agreed, stress-tests the result with independent critics, then writes a brief that planning can start from. The idea comes from the user, so every point is decided by the user, and the AI only asks, analyses and proposes.
---

# Refine idea

Turn a rough idea into a brief where nothing important is unclear, so planning and building can start with confidence. For a task that is already clear, skip this and use `task-planner:plan-task` (or just do it). This skill produces no code.

## 0. Setup
- Read `.u13/config.md` if it exists (`language`). Read project context if there is any: `CLAUDE.md`, `AGENTS.md`, README, `.u13/memory/STATUS.md`, active `DECISIONS.md` entries for the area.
- If `.u13/brief/` has a draft for this idea, resume it instead of starting over.
- Talk to the user in their language. Write the brief in `language` from the config, or in the language the user writes in if it is not set, because the user has to read and confirm it.

## 1. Capture
Restate the idea in 2-3 lines ("what I understood") and say what kind of thing it is (software product, tool, content, business, other). If the idea is too thin to restate, ask one opening question first. Also establish the purpose: commercial product or service, internal tool, hobby or learning, or open source. If the user's words make it obvious, state it and let them correct it. The purpose decides how deep the business areas go (`references/readiness.md`, "Which areas apply"). Never fill a gap with a guess.

## 2. Who decides
The idea belongs to the user, so there is no autopilot mode and no config switch. The AI asks, analyses and proposes; the user decides. Every question carries these options:
- the concrete options with a recommendation,
- **Discuss**: talk it through before choosing (short pros and cons, a counter-question if needed, then ask again),
- **You decide**: the user hands this one question to the AI. Decide, give the reason in 1-2 lines, and mark the item `delegated`. Delegated items are listed separately in the final summary so the user can overturn them.

"You decide" does not count as agreement for anything that defines the idea itself: what it is, for whom, its main promise; pricing and business model; public naming, copy and positioning; security, privacy and legal matters; hard-to-reverse choices; real credentials or paid services. For those, give a recommendation, mark it `proposed`, and ask for an explicit confirmation. It stays open until the user confirms.

## 3. Rounds - repeat until ready
Each round:
1. **Analyse.** Start `idea-refiner:challenger` (always in round 1; later when answers changed something material). Give it the brief draft path, what is confirmed, and the goal. It returns gaps, contradictions, risks (including demand, business model, UX and reaching users), alternatives (including "simpler first version", "reuse or buy", "do not build") and ranked questions with options and a recommendation. If it is not available, do this analysis yourself.
2. **Ask - one batch.** The most impactful questions first, about 5 and never more than 7. Each one says why it matters, gives options and a recommendation, so "as recommended" is a valid answer. Do not ask what you can read from the project files or look up yourself. Use AskUserQuestion for up to 4 short questions, otherwise a numbered list in plain text. Add "Discuss" and "You decide" as options (see section 2).
3. **Fold in the answers.** Update the brief draft (`references/brief-template.md`). Every item carries a status: `confirmed` (the user said it), `delegated` (the user chose "you decide" for it), `proposed` (your suggestion, not yet agreed), `assumed` (a gap nobody confirmed) or `parked`. Only `confirmed` and `delegated` count as agreed; an item that defines the idea (section 2) stays `proposed` until the user confirms it.
4. **Check readiness** against `references/readiness.md` and tell the user in one line where it stands, for example "8 of 13 applicable areas agreed; open: pricing model, first release scope".

"I don't know" is an answer, not a stop: offer a way to find out (research, a small test, a decision with a revisit trigger) or park it explicitly. Never turn it into an assumption.

After round 3 without being ready, say plainly what still blocks and why, and let the user choose: keep going, park the rest with stated consequences, or stop.

## 4. Stress-test
When everything is agreed (readiness conditions 1-3), run the stress-test automatically, without asking, unless the user said to skip it. Follow `references/stress-test.md`: independent critics review the brief, findings that need a decision go back into rounds, and the cycle repeats until one returns no blocker or major finding. Never resolve a contradiction yourself.

## 5. Confirm
When every readiness condition holds, show a one-screen summary (what is built, for whom, in/out, key decisions, top risks, parked items, and the minor stress-test notes as a separate list) and ask: "Is this exactly what you want built?" Set `Status: ready` only after a clear yes. A change request sends you back into a round.

## 6. Hand off
- Save the final brief at `.u13/brief/<YYYY-MM-DD>-<slug>.md` (slug: 2-5 words, lowercase ASCII letters, digits and hyphens only).
- If `project-memory` is installed, run `project-memory:memory-update`: decisions (the ones the user handed to the AI are tagged `[delegated]`) and parked items.
- Point to the next step. If `task-planner` is installed: suggest `/plan-task Plan and build from .u13/brief/<file>.md - treat the brief as the confirmed requirements` and offer to start it now. Otherwise say the next step is splitting the brief into tasks, and that the brief is self-contained for any agent.

## Rules
- Findings from the critics are questions, not changes. Nothing enters the brief as agreed until the user confirms it, except minor notes tagged `[stress-test]`, which the final yes covers.
- Never present your own invention as the user's idea. Anything you add is `proposed` until the user says yes.
- Be honest. If the idea has a fundamental problem (no clear user, a much simpler way, an existing product that already does it), say so before more questions.
- Keep questions about what changes what gets built. No filler, no repeats of answered points.
- If the idea needs a real account, API key or paid service, record exactly which and why in the brief.
- Do not create anything outside `.u13/`.

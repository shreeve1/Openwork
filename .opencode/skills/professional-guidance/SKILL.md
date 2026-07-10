---
name: professional-guidance
description: Match a described problem to the best-fit expert persona in agent-personas/ and adopt that specialist to work it in-session.
disable-model-invocation: true
---

# professional-guidance

Turn a plain-English problem into the right specialist. The user describes a
problem; you **understand it first**, route to one persona file under
`agent-personas/`, then **become** that specialist and start solving — without
making the user restate anything. Routing stays under the hood: the user
experiences a specialist who gets their situation, not a menu.

The routing map is `agent-personas/INDEX.json` — the live source of truth for
which personas exist (each entry has a `name`, `purpose`, `category`, and
`path`). Each persona `.txt` at that `path` is a full instruction set, and most
bundle several named sub-agents (the index does **not** list sub-agents — read
the `.txt`).

## Flow

### 1. Read the problem and the index
Take the problem description the user gave when invoking the skill. Read
`agent-personas/INDEX.json`.
_Done when:_ you have the problem in hand and the persona list loaded.

### 2. Understand the problem — draw out the missing specifics
The user's opening line is usually an incomplete sketch. Before routing, work
out what a specialist would need to know that the user hasn't said, then **ask
open-ended questions in plain chat text to extract those concrete facts**: what
exactly happened, what precisely is being asked and by whom, the relevant
context, constraints, and the outcome they want. Do **not** use multiple-choice
cards and do **not** offer or guess the answers — the point is to pull the real
details out of the user. Cap at **two, maybe three** focused questions; stop
as soon as you understand the situation (fewer is better). Keep routing under the
hood; never frame a question around which persona/sub-agent you're choosing.

Example — user says "our on-site tech put in his two weeks and the client is
asking what we're going to do." A good extraction question: *"Got it — what
specifically is the client asking for, and what have you told them so far?"*
(surfacing that the client wants the plan for training and ramping a replacement
on-site tech for their org).
_Done when:_ you can restate the user's actual situation in specifics, not just
echo their opening line.

### 3. Route to a persona, then read it to pick the sub-agent
Silently score the problem (plus any answers) against each index entry's `name`,
`purpose`, and `category`, and pick the single best persona. Then open that
persona's `.txt` at its `path` and choose the most relevant sub-agent from its
menu.
_Done when:_ you have one persona and one sub-agent named from its file.

### 4. Adopt and start immediately — no restating, no confirmation
Adopt the persona's `.txt` inline as your operating instructions and
**pre-select** the chosen sub-agent instead of showing the persona's own menu.
State in one line which persona/sub-agent you've become, then begin working the
user's original problem right away, carrying the initial description and any
clarifying answers straight in.
_Done when:_ you are producing the persona's first real deliverable on the
stated problem.

## Rules

- **Routing stays internal:** the user only ever sees problem-clarifying
  questions and a one-line "I'm now operating as X → Y" — never the scoring or
  sub-agent reasoning.
- **No confident match:** if nothing scores well (the problem is outside the
  collection's domains), say so and offer the closest 2–3 rather than
  force-adopting a poor fit.
- **Missing or stale index:** if `agent-personas/INDEX.json` is absent, route by
  scanning the `agent-personas/` folders and file names directly, and tell the
  user the index should be regenerated.
- **Switching mid-session:** the user can switch persona or sub-agent at any
  time; re-run steps 3–4 for the new pick.
- **Stay in character** as the adopted persona until told to switch or stop.

---
name: professional-guidance
description: Match a described problem to the best-fit expert persona in agent-personas/ and adopt that specialist to work it in-session.
disable-model-invocation: true
---

# professional-guidance

Turn a plain-English problem into the right specialist. The user describes a
problem; you route to one persona file under `agent-personas/`, confirm the
pick, then **become** that specialist and start solving — without making the
user restate anything.

The persona collection lives at the **workspace root** under `agent-personas/`
(not inside this skill folder). The routing map is the workspace-root file
`agent-personas/INDEX.json` — the live source of truth for which personas exist.
It is a list of `categories`, each with `category`, `folder`, `count`, and a
`personas` array; every persona entry has `name`, `purpose`, and `path` (the
**`category` lives on the parent group, not the persona entry**). Every `path`
is workspace-root relative (e.g. `agent-personas/<folder>/<Persona>.txt`) and
usually contains spaces and `&` — quote it when reading. Each persona `.txt` at
that `path` is a full instruction set, and most bundle several named sub-agents
(the index does **not** list sub-agents — read the `.txt` for those).

## Flow

### 1. Read the problem and the index
Take the problem description the user gave when invoking the skill. Read the
workspace-root file `agent-personas/INDEX.json`.
_Done when:_ you have the problem in hand and the persona list loaded.

### 2. Clarify only if it changes the routing (0–2 questions)
"1 maybe 2" is a **ceiling, not a quota**. Ask a clarifying question only when
the answer would change which persona or sub-agent you pick. If the problem is
already specific enough to route confidently, ask nothing and go to step 3.
When you do ask, use the **Question tool** (the `ask_user_question` tool), and
cap at two.
_Done when:_ you can name a single best persona with confidence, or you have
asked at most two questions.

### 3. Route to a persona, then read it to pick the sub-agent
Score the problem (plus any answers) against each persona's `name` and
`purpose` plus its group's `category`. Some `purpose` values are just a shouty
title (e.g. "CMMC ASSESSMENT & GAP ANALYSIS EXPERT SYSTEM") rather than a
description — when `purpose` is thin, lean on `name` and `category`. Pick the
single best persona. Then **open that persona's `.txt`** at its workspace-root
`path` (quote it — paths have spaces/`&`) and choose the most relevant sub-agent from its
menu — sub-agents live only in the file, not the index. Note 1–2 runner-up
personas from the index in case the user switches.
_Done when:_ you have one primary pick (persona + a sub-agent named from the
file) and 1–2 runner-up personas.

### 4. Confirm the pick (one gate covers both)
Present the primary pick in one line plus the 1–2 runners-up, and ask the user
to confirm or switch. Example: *"I'll take on **Marketing Strategist →
campaign-architect** to plan your product launch. Or: SEO Specialist, Content
Creator. Go?"* Confirming accepts both the persona and the sub-agent.
_Done when:_ the user has confirmed a persona/sub-agent or chosen an alternative.

### 5. Adopt and start immediately — no restating
Adopt the persona's `.txt` inline as your operating instructions. **Pre-select**
the chosen sub-agent instead of showing the persona's own menu. Begin working
the user's original problem right away, carrying the initial description and any
clarifying answers straight in. State in one line which persona/sub-agent you've
become, then start.
_Done when:_ you are producing the persona's first real deliverable on the
stated problem.

## Rules

- **No confident match:** if nothing scores well (the problem is outside the
  collection's domains), say so and offer the closest 2–3 rather than
  force-adopting a poor fit.
- **Missing or stale index:** if the workspace-root `agent-personas/INDEX.json`
  is absent, route by scanning the workspace-root `agent-personas/` folders and
  file names directly, and tell the user the index should be regenerated.
- **Switching mid-session:** the user can switch persona or sub-agent at any
  time; re-run steps 3–5 for the new pick.
- **Stay in character** as the adopted persona until told to switch or stop.

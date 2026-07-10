---
name: workflow-builder
description: >-
  Walks the user through designing a reusable workflow skill in Cowork, talk-it-through style: grills the step list from a stated goal, then for each step picks a specialist agent from `agents-library/`, customizes it, and writes the customized copy to `.claude/agents/<slug>.md`; the produced skill has the session adopt that persona inline. Applies skill-creator's authoring bar so the skill is properly made, and writes it to `.claude/skills/<name>/SKILL.md` so Cowork's reload banner appears. Maintains shared CONTEXT.md and memory/decisions/ alongside talk-it-through. Use when the user wants to design, scaffold, or build a new workflow skill, pick and customize agents for a multi-step process, or mentions "workflow builder", "build a workflow", "create a skill that uses agents".
---

<what-to-do>

Walk the user through building a reusable workflow skill — a new SKILL.md that orchestrates specialist agents drawn from `agents-library/`, customized and materialized into `.claude/agents/`. Run it talk-it-through style: ask one question at a time, in plain text, recommend an answer for each, and never use the `ask_user_question` / `AskUserQuestion` tool. Apply the 80/20 rule — spend questions on the vital few decisions (step structure, agent fit, customization, collisions) and state defaults for the trivial many.

The walkthrough has four phases, run in order. Do not skip ahead.

## Phase 1 — Goal, name, description

1. Read `CONTEXT.md` and scan `memory/decisions/` at the workspace root so you build on agreed language and prior conventions. Create both lazily — only when you have something to write (a resolved term or a settled decision). If absent at session start, proceed; you will create them inline when the first entry arises.
2. Ask the user to state the workflow goal in plain language. What business process should this skill run? What is the user trying to automate or scaffold?
3. Propose a lowercase-kebab skill slug derived from the goal (e.g. `launch-newsletter`, `onboard-client`). Recommend one; user confirms or overrides.
4. Draft the produced skill's `description` frontmatter to skill-creator's bar (see `.claude/skills/skill-creator/SKILL.md`): one sentence on what it does, then explicit quoted trigger phrases ("Use when the user mentions '...', '...'"). Recommend it; user confirms or edits. Validate: at least one quoted phrase, uses "when"/"triggers", longer than ~50 characters.

## Phase 2 — Step list (what, not who)

1. From the stated goal, propose an ordered list of steps. Each step is a unit of work with a single deliverable. At this stage, do NOT name agents — just what happens and what each step produces.
2. Grill the step list like talk-it-through grills a plan: one question at a time, recommend an answer, prune low-stakes branches. Common challenges:
   - Missing steps (did you skip review, handoff, QA, sign-off?)
   - Fused steps (should "research and draft" be two steps with different agents?)
   - Order errors (does step 3 actually depend on step 5's output?)
3. Lock the final ordered step list before moving on. Confirm it back to the user in plain text.

## Phase 3 — Pick, customize, and materialize an agent per step

For each step in the locked list, in order:

1. Read `agents-library/index.json` (the catalog at the workspace root) and filter by keyword match against the step's deliverable. Match on `name` and `description`. Narrow by `division` only if the user explicitly names one or the step is domain-specific enough that a division is obvious.
2. Surface 3–8 candidate agents as a plain-text list, each with: `division / slug` and the catalog's one-line description. Do not dump the whole library (see the count in `agents-library/index.json`).
3. Recommend one agent for the step, with a one-line rationale tying the agent's specialty to the step's deliverable.
4. User confirms or overrides. If the user names an agent not in your candidates, verify it exists in `agents-library/index.json` before accepting — typos happen.
5. **Customize** the picked agent: read the source `agents-library/<division>/<slug>.md`, then propose edits that scope its persona to this step's deliverable and this workflow. Confirm the customizations with the user.
6. **Materialize** it: create `.claude/agents/` if missing, then write the customized copy to `.claude/agents/<slug>.md`. Use the library `slug` from `index.json` as the filename base; the materialized file is a self-contained persona doc (its own frontmatter `name`/`description`), not a subagent registration. If the same base agent serves more than one step, give each a per-purpose slug (e.g. `researcher-intake`, `researcher-qa`) so the copies don't collide. If the target file already exists, refuse and ask (overwrite / save-as-new-slug / cancel) — never silently clobber, never auto-version.
7. Lock the agent for that step and note the deliverable. Move to the next step.

You may return to Phase 2 at any time to add, remove, reorder, or rephrase steps. If returning, re-confirm the step list before resuming Phase 3. Agents already locked for unchanged steps stay locked.

Default is one agent per step; only descend into "two agents for one step" if the user raises it and the step genuinely needs it. If no agent in the library is a reasonable base for a step, say so — do not force a bad fit. The user can split the step, redefine the deliverable, or accept that the step is run by the generic assistant (no materialized agent).

## Phase 4 — Write the produced skill

1. Create `.claude/skills/` if missing. Before writing, check whether `.claude/skills/<name>/SKILL.md` already exists. If it does, **refuse and ask**: overwrite, save-as-new-name, or cancel. Never silently clobber. Never auto-version.
2. Compose the produced SKILL.md to skill-creator's authoring bar:
   - Frontmatter: `name` and `description` from Phase 1 (quoted trigger phrases included).
   - A `<what-to-do>` block listing the steps as ordered prose. Each step:
     - States what to do and what to produce.
     - For a step with a materialized agent, names it by its path: "Read `.claude/agents/<slug>.md` and take on that persona for this step." Uses **on-demand persona adoption** — the session adopts the persona inline; never spawn a registered subagent, never copy agents back into the library.
     - For an agent-less step (run by the generic assistant), write the step as a plain instruction with no persona line.
   - Keep steps inline — no separate manifest file.
   - Safe defaults: no destructive actions without confirmation; note any inputs, outputs, or permissions the skill needs.
3. Write the file to `.claude/skills/<name>/SKILL.md` using a file-write tool (never paste the whole skill into chat) so Cowork shows the reload banner and the user can activate it immediately.
4. Validate before reporting done (skill-creator's bar): frontmatter `name`/`description` present, the description has at least one quoted trigger phrase, and every `.claude/agents/<slug>.md` the skill references actually exists on disk. Fix any miss before finishing.
5. Tell the user the exact workspace-relative path(s): the produced skill and every materialized agent.

</what-to-do>

<supporting-info>

## Cowork behavior

- Write both the produced skill and each materialized agent to their real workspace paths with a file-write tool — do not paste them into chat. Writing the real files lets Cowork surface the reload banner so the new skill/agents activate immediately.
- Cowork runs the *installed* copy of a skill, not the repo source, until it is re-uploaded or reached via a loader skill. Materialized agents in `.claude/agents/` and skills in `.claude/skills/` are the local source; mention this if the user expects an edit to be live instantly. The loader / installed-vs-source pattern itself is owned by the `cowork-pointer` skill — do not duplicate it here.

## Relationship to skill-creator

For authoring craft — frontmatter mechanics, quoted trigger-phrase quality, folder structure, safe defaults, and the write-to-real-path Cowork step — this skill applies `.claude/skills/skill-creator/SKILL.md`. workflow-builder owns the orchestration design (steps + agents); skill-creator owns how the produced SKILL.md is written well. Consult it whenever a produced skill needs more than orchestration structure.

## Shared memory with talk-it-through

This skill shares `CONTEXT.md` and `memory/decisions/` with `talk-it-through` so the two reinforce each other. Formats are defined once in talk-it-through's folder and referenced from here:

- `CONTEXT.md` format: `.claude/skills/talk-it-through/CONTEXT-FORMAT.md`
- Decision format: `.claude/skills/talk-it-through/DECISIONS-FORMAT.md`

These are referenced by relative path. If `talk-it-through` is renamed or moved, update the paths above to match. Read both at the start of a session if you are unsure how to structure a term or decision entry.

## Maintaining CONTEXT.md inline

When a term is resolved during the walkthrough (e.g. the user gives a precise name for a role, document, or status specific to this workflow), update `CONTEXT.md` right there using the format above. Do not batch. Keep `CONTEXT.md` a glossary only — no procedure, no task lists, no rationale.

## Wrapping up — record conventions (not skill content)

At wrap-up, auto-record to `memory/decisions/` following the same flow as talk-it-through. Do **not** ask "do you want to record this?" — just record.

Record **workflow-building conventions only** — choices that change how future workflow-builder runs behave:
- Output shape decisions (e.g. "inline steps, no manifest"; "agents materialized to `.claude/agents/`, adopted inline").
- Invocation pattern reaffirmations or deviations.
- Walkthrough order changes.
- Collision handling preferences.

Do **not** record the content of the produced skill (which agents, which steps) — that lives in the produced SKILL.md and its materialized agents, not in memory. Only write a new decision file when a convention actually changes or a genuinely new one is settled.

After writing decisions: update `memory/index.md` and append `memory/log.md` per `memory/TEMPLATES.md`.

## Agents library reference

- Catalog (read this): `agents-library/index.json` at the workspace root. Each entry has `division`, `slug`, `name`, `description`, `color`, `file`.
- README: `agents-library/README.md` — layout, invocation patterns, editing rules.
- Agent files: `agents-library/<division>/<slug>.md` — YAML frontmatter + Markdown body. Read the chosen file's contents to customize it before writing to `.claude/agents/<slug>.md`.

Picking mechanics:
1. Keyword-match the step deliverable against `name` + `description`.
2. Surface candidates as `division / slug — <description>`.
3. Recommend one with a one-line rationale tied to the deliverable.
4. Read the source `agents-library/<division>/<slug>.md`, customize, write to `.claude/agents/<slug>.md`.

## Hard rules

- One question at a time. Plain text. No `ask_user_question` / `AskUserQuestion`.
- Recommend an answer for every real question.
- Materialize each picked agent as a customized copy at `.claude/agents/<slug>.md`; the produced skill adopts it inline by that path. Never spawn a registered subagent from the produced skill; never write agents back into `agents-library/`.
- Create `.claude/agents/` and `.claude/skills/` if missing.
- Never silently overwrite an existing skill or agent file — refuse and ask.
- Write real files (never paste whole skills/agents into chat) so the Cowork reload banner appears.
- Apply skill-creator's bar to the produced skill (quoted trigger phrases, structure, safe defaults).
- Apply 80/20: stop grilling when remaining branches are low-stakes; state defaults and move on.

</supporting-info>

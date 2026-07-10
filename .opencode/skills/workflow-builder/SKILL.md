---
name: workflow-builder
description: >-
  Walks the user through designing a reusable workflow skill in OpenWork, talk-it-through style: first seeds a starting point by searching the skills.sh directory for a comparable audit-passing skill (via the skill-scaffolder skill), then grills the step list from the stated goal, and for each step picks a specialist agent from `agents-library/`, customizes it, and materializes it as an opencode subagent in `.opencode/agents/<slug>.md`; the produced skill invokes those subagents via the Task tool. Writes the skill to `.opencode/skills/<name>/SKILL.md` so OpenWork's skill reload banner appears, and maintains shared CONTEXT.md and memory/decisions/ alongside talk-it-through. Use when the user wants to design, scaffold, or build a new workflow skill, pick and customize agents for a multi-step process, or mentions "workflow builder", "build a workflow", "create a skill that uses agents".
metadata:
  route_default: high
  route_max: high
  route_class: workflow_builder
---

<<<ROUTE default=high max=high class=workflow_builder>>>

<what-to-do>

Walk the user through building a reusable workflow skill — a new SKILL.md that orchestrates specialist agents drawn from `agents-library/`, customized and materialized into `.opencode/agents/`. Run it talk-it-through style: ask one question at a time, in plain text, recommend an answer for each, and never use the `Question` / `ask_user_question` tool. Apply the 80/20 rule — spend questions on the vital few decisions (step structure, agent fit, customization, collisions) and state defaults for the trivial many.

The walkthrough has four phases, run in order. Do not skip ahead.

## Phase 1 — Goal, name, description, and seed

1. Read `CONTEXT.md` and scan `memory/decisions/` at the workspace root so you build on agreed language and prior conventions. Create both lazily — only when you have something to write (a resolved term or a settled decision). If absent at session start, proceed; create them inline when the first entry arises.
2. Ask the user to state the workflow goal in plain language: what business process should this skill run, and what is the user automating or scaffolding?
3. Propose a lowercase-kebab skill slug derived from the goal (e.g. `launch-newsletter`, `onboard-client`). Recommend one; user confirms or overrides.
4. Draft the produced skill's `description` frontmatter (see the authoring bar below): one sentence on what it does, then explicit quoted trigger phrases ("Use when the user mentions '...', '...'"). Recommend it; user confirms or edits. Completion criterion: the description has at least one quoted trigger phrase, uses "when"/"triggers", and runs longer than ~50 characters.
5. **Seed from the skills.sh directory (don't reinvent).** Before authoring from scratch, load and follow `.opencode/skills/skill-scaffolder/SKILL.md` to check whether a comparable published skill can serve as a starting point. Run its discovery, audit gate, and fetch/review against this goal; only skills that clear its security-audit gate are eligible. If a good base is found and the user confirms it, use its customized SKILL.md as the **skeleton** for the produced skill and carry it into Phase 2. If nothing clears the gate or fits, say so in one line and author from scratch. This is a seed, not a requirement — the produced skill is still written to this skill's authoring bar and wired to local agents (Phases 2–4) regardless of whether a base was found.

## Phase 2 — Step list (what, not who)

1. From the stated goal, propose an ordered list of steps. Each step is a unit of work with a single deliverable. At this stage, name only what happens and what each step produces — not who runs it.
2. Grill the step list the way talk-it-through grills a plan: one question at a time, recommend an answer, prune low-stakes branches. Common challenges:
   - Missing steps (did you skip review, handoff, QA, sign-off?)
   - Fused steps (should "research and draft" be two steps with different agents?)
   - Order errors (does step 3 actually depend on step 5's output?)
3. Lock the final ordered step list before moving on. Confirm it back to the user in plain text.

## Phase 3 — Pick, customize, and materialize an agent per step

For each step in the locked list, in order:

1. Read `agents-library/index.json` (the catalog at the workspace root) and filter by keyword match against the step's deliverable, matching on `name` and `description`. Narrow by `division` only when the user names one or the step is domain-specific enough that a division is obvious.
2. Surface 3–8 candidate agents as a plain-text list, each with `division / slug` and the catalog's one-line description. Keep it to candidates, not the whole library.
3. Recommend one agent for the step, with a one-line rationale tying the agent's specialty to the step's deliverable.
4. User confirms or overrides. If the user names an agent outside your candidates, verify it exists in `agents-library/index.json` before accepting — typos happen.
5. **Customize** the picked agent: read the source `agents-library/<division>/<slug>.md`, then propose edits that scope its persona to this step's deliverable and this workflow. Confirm the customizations with the user.
6. **Materialize** it as a real opencode subagent: create `.opencode/agents/` if missing, then write the customized copy to `.opencode/agents/<slug>.md`. The **filename is the agent name** — use the library `slug` from `index.json` as the base. Convert the library frontmatter to opencode subagent frontmatter: drop the library `name` field (the filename supplies it), keep `description`, add `mode: subagent`, carry `color` through, and set `permission` to the least access the step needs (e.g. `edit: deny` for a review step). The customized library body becomes the agent's system prompt. If the same base agent serves more than one step, give each a per-purpose slug (e.g. `researcher-intake`, `researcher-qa`) so the file-name agents stay distinct. If the target file already exists, refuse and ask (overwrite / save-as-new-slug / cancel) — never silently clobber, never auto-version.
7. Lock the agent for that step and note the deliverable. Move to the next step.

Return to Phase 2 at any time to add, remove, reorder, or rephrase steps. On return, re-confirm the step list before resuming Phase 3. Agents already locked for unchanged steps stay locked.

Default is one agent per step; descend into "two agents for one step" only when the user raises it and the step genuinely needs it. If no agent in the library is a reasonable base for a step, say so — the user can split the step, redefine the deliverable, or accept that the generic assistant runs the step (no materialized agent). Do not force a bad fit.

## Phase 4 — Write the produced skill

1. Create `.opencode/skills/` if missing. Before writing, check whether `.opencode/skills/<name>/SKILL.md` already exists. If it does, **refuse and ask**: overwrite, save-as-new-name, or cancel. Never silently clobber. Never auto-version.
2. Compose the produced SKILL.md to the authoring bar below:
   - Frontmatter: `name` and `description` from Phase 1 (quoted trigger phrases included).
   - A `<what-to-do>` block listing the steps as ordered prose. Each step:
     - States what to do and what to produce (the step's completion criterion).
     - For a step with a materialized agent, invoke it by its subagent name (the filename base): "Invoke the `<slug>` subagent via the Task tool for this step" (the user can also reach it with `@<slug>`). The materialized subagent in `.opencode/agents/<slug>.md` runs the step; never copy agents back into the library.
     - For an agent-less step (run by the generic assistant), write the step as a plain instruction with no subagent line.
   - Keep steps inline — no separate manifest file.
   - Safe defaults: confirm before destructive actions; note any inputs, outputs, or permissions the skill needs.
3. Write the file to `.opencode/skills/<name>/SKILL.md` with a file-write tool (never paste the whole skill into chat) so OpenWork shows the skill reload banner and the user can activate it immediately.
4. Validate before reporting done: the skill frontmatter has `name` and `description`, the description has at least one quoted trigger phrase, every `.opencode/agents/<slug>.md` the skill references exists on disk, and each materialized agent has valid opencode subagent frontmatter (`description` + `mode: subagent`, no library `name` field). Fix any miss before finishing.
5. Tell the user the exact workspace-relative path(s): the produced skill and every materialized agent.

</what-to-do>

<supporting-info>

## OpenWork behavior

- Write both the produced skill and each materialized agent to their real workspace paths with a file-write tool — do not paste them into chat. Writing the real files lets OpenWork surface the skill reload banner so the new skill and agents activate immediately.
- OpenWork runs the *installed* copy of a skill, not the repo source, until skills are reloaded. Materialized agents in `.opencode/agents/` and skills in `.opencode/skills/` are the local source; mention this when the user expects an edit to be live instantly.

## Authoring bar for the produced skill

Apply these skill-writing principles (from Matt Pocock's "writing great skills") to the produced SKILL.md so it stays predictable. The leading words these principles use — *description*, *branch*, *information hierarchy*, *completion criterion*, *no-op*, *duplication*, *sediment*, *negation*, and the rest — are defined in [`GLOSSARY.md`](./GLOSSARY.md); read it when you need a term's full meaning:

- **Predictability first.** A skill exists to make the agent take the same *process* every run. Every rule below serves that.
- **Description does two jobs:** state what the skill is, and list the branches that trigger it. Front-load the leading word. One trigger per branch — collapse synonyms that rename a single branch. Keep it to triggers plus a reach clause; cut identity already in the body.
- **Information hierarchy.** Steps are the primary tier: ordered actions, each ending on a *checkable* completion criterion (can the agent tell done from not-done?). Push reference (definitions, rules, facts) below the steps or, when the top bloats, out to a linked file reached by a context pointer.
- **Completion criteria.** Make each step's "what to produce" checkable and, where it matters, exhaustive ("every item accounted for", not "produce a list") so the agent does not stop early.
- **Prune.** One source of truth per meaning. Delete no-op lines the model already obeys by default. Cut duplication and stale sediment.
- **Leading words.** Anchor recurring behaviour in a compact pretrained concept (a single strong word) rather than restating a triad across three sites.
- **Prompt the positive.** State the target behaviour rather than steering by prohibition; keep a "don't" only as a hard guardrail you can't phrase positively, paired with what to do instead.

workflow-builder owns the orchestration design (steps + agents); the authoring bar owns how the produced SKILL.md is written well.

## Relationship to skill-scaffolder

Phase 1 (step 5) loads `.opencode/skills/skill-scaffolder/SKILL.md` to seed the produced skill from the skills.sh directory before authoring from scratch. skill-scaffolder owns directory discovery, the audit gate, and customizing a fetched skill; workflow-builder owns wiring local agents into the seeded skill's steps. The client runs workflow-builder; skill-scaffolder runs inline as a sub-procedure. skill-scaffolder is also usable standalone for "find and adapt one skill".

## Shared memory with talk-it-through

This skill shares `CONTEXT.md` and `memory/decisions/` with `talk-it-through` so the two reinforce each other. Formats are defined once in talk-it-through's folder and referenced from here:

- `CONTEXT.md` format: `.opencode/skills/talk-it-through/CONTEXT-FORMAT.md`
- Decision format: `.opencode/skills/talk-it-through/DECISIONS-FORMAT.md`

These are referenced by relative path. If `talk-it-through` is renamed or moved, update the paths above to match. Read both at the start of a session when you are unsure how to structure a term or decision entry.

## Maintaining CONTEXT.md inline

When a term is resolved during the walkthrough (e.g. the user gives a precise name for a role, document, or status specific to this workflow), update `CONTEXT.md` right there using the format above. Do not batch. Keep `CONTEXT.md` a glossary only — no procedure, no task lists, no rationale.

## Wrapping up — record conventions (not skill content)

At wrap-up, auto-record to `memory/decisions/` following the same flow as talk-it-through. Do **not** ask "do you want to record this?" — just record.

Record **workflow-building conventions only** — choices that change how future workflow-builder runs behave:
- Output shape decisions (e.g. "inline steps, no manifest"; "agents materialized as opencode subagents in `.opencode/agents/`, invoked via the Task tool").
- Invocation pattern reaffirmations or deviations.
- Walkthrough order changes.
- Collision handling preferences.

The content of the produced skill (which agents, which steps) lives in the produced SKILL.md and its materialized agents, not in memory. Write a new decision file only when a convention actually changes or a genuinely new one is settled.

Follow the decision file template in `.opencode/skills/talk-it-through/DECISIONS-FORMAT.md`, but note the provenance as a workflow-builder session (not a talk-it-through session). After writing decisions: update `memory/index.md` and append `memory/log.md` per `memory/TEMPLATES.md`.

## Agents library reference

- Catalog (read this): `agents-library/index.json` at the workspace root. Each entry has `division`, `slug`, `name`, `description`, `color`, `file`.
- README: `agents-library/README.md` — layout, editing rules. Note it is written for Claude subagent format (Title-case `name`, `.claude/agents/`); in this workspace, materialize instead to opencode subagent format in `.opencode/agents/` (filename = name, `mode: subagent`) per Phase 3.
- Agent files: `agents-library/<division>/<slug>.md` — YAML frontmatter (`name`, `description`, `color`) + Markdown body. Read the chosen file's contents, customize, and convert to opencode subagent frontmatter before writing to `.opencode/agents/<slug>.md`.

Picking mechanics:
1. Keyword-match the step deliverable against `name` + `description`.
2. Surface candidates as `division / slug — <description>`.
3. Recommend one with a one-line rationale tied to the deliverable.
4. Read the source `agents-library/<division>/<slug>.md`, customize, write to `.opencode/agents/<slug>.md`.

## Hard rules

- One question at a time. Plain text. No `Question` / `ask_user_question` tool.
- Recommend an answer for every real question.
- Materialize each picked agent as a real opencode subagent at `.opencode/agents/<slug>.md` (filename = agent name; frontmatter `description` + `mode: subagent`, no library `name` field); the produced skill invokes it via the Task tool. Never write agents back into `agents-library/`.
- Create `.opencode/agents/` and `.opencode/skills/` if missing.
- Refuse and ask before overwriting an existing skill or agent file.
- Write real files (never paste whole skills/agents into chat) so the OpenWork skill reload banner appears.
- Apply the authoring bar to the produced skill (quoted trigger phrases, checkable completion criteria, safe defaults).
- Apply 80/20: stop grilling when remaining branches are low-stakes; state defaults and move on.

</supporting-info>

---
name: skill-scaffolder
description: >-
  Talks the user through what they want a new skill to do, then instead of writing it from scratch, browses the skills.sh directory for a comparable published skill, gates candidates on a security audit, fetches the best match as a starting point, and customizes it into a new local skill at `.opencode/skills/<name>/SKILL.md` — applying the writing-great-skills authoring bar and preserving provenance. Use when the user wants to create a new skill, find an existing skill to adapt, avoid reinventing a skill, or mentions "skills.sh", "find a skill", "start from an existing skill", "skill scaffolder". Other skills (e.g. workflow-builder) load this to seed a produced skill from the directory before authoring from scratch.
metadata:
  route_default: high
  route_max: high
  route_class: skill_scaffolder
---

<<<ROUTE default=high max=high class=skill_scaffolder>>>

<what-to-do>

Help the user create a new skill by starting from a comparable one published on the skills.sh directory rather than writing from a blank page. Run it talk-it-through style: ask one question at a time, in plain text, recommend an answer for each, and never use the `Question` / `ask_user_question` tool. Apply the 80/20 rule — spend questions on the vital few decisions (what the skill does, which candidate to adapt, what to change) and state defaults for the trivial many.

Hard safety rule up front: **only adopt a skills.sh skill that clears the security-audit gate** (Phase 3): a majority of audit partners `pass` and no partner reports a genuine `CRITICAL`/`HIGH` finding. A skill with no audit yet is not eligible. Always show the user the full per-partner verdict — including any lone `warn`/`fail` — so adoption is knowing.

The walkthrough has five phases, run in order.

## Phase 1 — Goal, name, description

1. Read `CONTEXT.md` and scan `memory/decisions/` at the workspace root so you build on agreed language and prior conventions. Create both lazily — only when you have something to write.
2. Ask the user to state what the skill should do in plain language: what task or process should it run, and what triggers it.
3. Propose a lowercase-kebab local skill slug (e.g. `seo-audit`, `weekly-report`). Recommend one; user confirms or overrides.
4. Draft the local skill's `description` frontmatter to the authoring bar below: one sentence on what it does, then explicit quoted trigger phrases ("Use when the user mentions '...', '...'"). Completion criterion: at least one quoted trigger phrase, uses "when"/"triggers", longer than ~50 characters.

## Phase 2 — Discover comparable skills

Free-text search is not reachable read-only (the skills.sh search page renders client-side, so a web fetch returns no results). Discover by browsing instead — see **Reaching skills.sh** below for the exact URLs.

1. Map the goal to the closest topic(s) (e.g. `design`, `testing`, `databases`, `marketing`, `agent-workflows`) and to any obvious first-party owner (e.g. an official maker's repo). Note likely keywords from the goal.
2. Browse the server-rendered pages — `/official`, `/topic/<topic>`, and `/<owner>` creator pages — and web-fetch the ones that match; collect skill links (`/<owner>/<repo>/<slug>`). Cross-check the owning GitHub repo when a page is paginated or thin.
3. For each promising candidate, web-fetch its skill page and record: `owner/repo/slug`, one-line description, install count, skills.sh URL, and the audit verdicts shown inline.
4. If browsing does not surface a good candidate, fall back in order: (a) drive the skills.sh search UI with OpenWork's built-in browser (`openwork_browser_open_url` on `https://www.skills.sh/search?q=<query>`, then snapshot the results); (b) ask the user to open that URL and paste back the candidate `owner/repo/slug`s.
5. Completion criterion: a raw candidate list of up to ~10, before the audit gate.

## Phase 3 — Audit gate, then recommend

1. For each candidate, fetch its security audit (public, no token — see **Reaching skills.sh**). Decide "pass" by **majority with no genuine critical finding**: adopt only when most partners report `pass` AND no partner reports a real `CRITICAL`/`HIGH` finding (a `riskLevel` of `CRITICAL`/`HIGH` backed by an actual issue in its summary). Treat a lone dissenting scanner as a caution to surface, not an automatic reject — one partner (often Snyk) sometimes returns a self-contradicting verdict like "Risk: HIGH · No issues", which is not a real finding. A skill with **no audit yet** (a `404`, before its first install) is not eligible.
2. Always surface the full per-partner picture for a candidate you keep — list each partner's `status` and `riskLevel`, and call out any `warn`/`fail` explicitly so the user adopts it knowingly. Also surface the Agent Trust Hub `categories` (e.g. `PROMPT_INJECTION`, `EXTERNAL_DOWNLOADS`) as inherent-behavior flags.
3. Surface the surviving 3–8 candidates as a plain-text list: `owner/repo/slug — <description> (installs: N, audits: <e.g. 4 pass, Snyk warn>)`.
4. Recommend one, with a one-line rationale tying its purpose to the user's goal. Prefer higher installs, a closer purpose match, and official/first-party repos; when a candidate is an apparent fork/copy of another (same name, lower installs, different owner), prefer the original. (The API exposes an `isDuplicate` flag, but only on the token-gated path — on the browse path, judge forks by eye.)
5. User confirms or overrides. If nothing clears the gate or fits, say so plainly — the user can broaden the browse, narrow the goal, or fall back to authoring from scratch (hand off to the authoring bar with no seed).

## Phase 4 — Fetch and review the starting point

1. Fetch the chosen skill's full contents — its `SKILL.md` and any supporting files (see **Reaching skills.sh**).
2. Summarize for the user in plain text: what the skill actually does, its steps/structure, and any supporting files. Do not paste the whole thing into chat.
3. Confirm with the user that this is the right base before customizing. This is the human confirmation checkpoint — pair it with the audit result so the user adopts external content knowingly.

## Phase 5 — Customize and write the local skill

1. Create `.opencode/skills/` if missing. Check whether `.opencode/skills/<name>/SKILL.md` already exists. If it does, **refuse and ask**: overwrite, save-as-new-name, or cancel. Never silently clobber. Never auto-version.
2. Customize the fetched skill into the user's skill, applying the authoring bar:
   - Rewrite the frontmatter `name` (local slug) and `description` (Phase 1 triggers, quoted phrases).
   - Retarget the body to the user's goal and this workspace: prune irrelevant steps and reference (relevance), collapse duplication, keep each step's completion criterion checkable.
   - Add a **provenance** line near the top of the body: the source `owner/repo/slug`, author, skills.sh URL, and the actual audit result on the date checked (the per-partner summary from Phase 3, e.g. "4 pass, Snyk warn" — not a flat "passed"). This preserves attribution and the honest security posture for a derived skill.
   - Bring over only the supporting files the customized skill still needs; drop the rest.
3. Write the file to `.opencode/skills/<name>/SKILL.md` with a file-write tool (never paste the whole skill into chat) so OpenWork shows the skill reload banner and the user can activate it immediately. Write any kept supporting files alongside it.
4. Validate before reporting done: frontmatter `name`/`description` present, the description has at least one quoted trigger phrase, the provenance line is present, and any supporting file the skill references exists on disk. Fix any miss before finishing.
5. Tell the user the exact workspace-relative path(s) and the source it was derived from.

</what-to-do>

<supporting-info>

## Reaching skills.sh

skills.sh is the open agent-skills directory (Vercel Labs). Reach it read-only; do not install third-party skills into the workspace as-is — this skill *derives a customized local copy*, it does not adopt the remote skill wholesale.

Preferred path (no token, works on a normal OpenWork workstation). Verified web-fetch behavior:

- **Discovery (browse, don't search):** the free-text search page `https://www.skills.sh/search?q=<query>` is client-rendered — a web fetch returns empty result rows, so it is unusable read-only. Instead web-fetch the server-rendered browse pages, which list real skill links: `https://www.skills.sh/official` (first-party makers by owner), `https://www.skills.sh/topic/<topic>` (e.g. `design`, `testing`, `databases`, `marketing`, `agent-workflows`), and `https://www.skills.sh/<owner>` (a creator's skills; may be paginated). Collect `/<owner>/<repo>/<slug>` links, and cross-check the owning GitHub repo for the full skill list when a page is thin.
- **Skill page (rich):** web-fetch `https://www.skills.sh/<owner>/<repo>/<slug>`. This is server-rendered and returns the description, the `npx skills add` command, a SKILL.md preview, install count, the GitHub repo link, and the security-audit verdicts inline — enough to shortlist and gate without the API.
- **Full contents:** for the complete `SKILL.md` and supporting files (FORMS.md, REFERENCE.md, examples), web-fetch the GitHub repo (the skill page links it).
- **Audit (public, no token — use this):** `GET https://skills.sh/api/v1/skills/audit/{owner}/{repo}/{slug}` returns `audits[]` with per-partner `status` (`pass`/`warn`/`fail`), `riskLevel`, `summary`, and (for Agent Trust Hub) `categories`. Apply the majority-with-no-critical rule in Phase 3. A `404` means no audit exists yet — not eligible.
- **Live search fallback:** if browsing misses, drive the search UI with OpenWork's built-in browser — `openwork_browser_open_url` on `https://www.skills.sh/search?q=<query>`, then `browser_snapshot` to read the rendered results — or ask the user to open that URL and paste back candidates.

Optional accelerator (only if `VERCEL_OIDC_TOKEN` is set — typically only on Vercel-deployed hosts, not a normal workstation). These return `401` without the token:

- Search: `GET https://skills.sh/api/v1/skills/search?q=<query>&limit=10` — multi-word queries use semantic search (needs token).
- Detail (file tree): `GET https://skills.sh/api/v1/skills/{owner}/{repo}/{slug}` — returns every file's `contents` (needs token).
- Both need header `Authorization: Bearer $VERCEL_OIDC_TOKEN`. The audit endpoint above needs no token.

Installing the raw skill (optional, only if the user explicitly wants the unmodified upstream skill rather than a customized copy): `npx skills add <owner>/<repo>` from the workspace root installs it repo-scoped for OpenCode. Default behavior is to derive a customized copy, not to run this.

## Authoring bar for the produced skill

Apply the writing-great-skills principles to the customized local skill so it stays predictable. The full leading-word definitions live in the workflow-builder glossary — read `.opencode/skills/workflow-builder/GLOSSARY.md` when you need a term's meaning:

- **Predictability first.** The skill should make the agent take the same *process* every run.
- **Description does two jobs:** state what the skill is, and list the branches that trigger it; front-load the leading word, one trigger per branch.
- **Information hierarchy.** Steps are primary, each ending on a *checkable* completion criterion; push reference below the steps or into a linked file behind a context pointer.
- **Prune.** One source of truth per meaning; delete no-op lines and stale sediment; cut duplication. This matters most here — a fetched skill carries content for its original purpose that is dead weight for yours.
- **Leading words.** Keep or introduce a compact pretrained concept rather than restating a triad.
- **Prompt the positive.** State the target behaviour rather than steering by prohibition.

If no candidate survives the audit gate or fits, author from scratch to this same bar with no seed.

## Shared memory with talk-it-through

Shares `CONTEXT.md` and `memory/decisions/` with `talk-it-through`. Formats:

- `CONTEXT.md` format: `.opencode/skills/talk-it-through/CONTEXT-FORMAT.md`
- Decision format: `.opencode/skills/talk-it-through/DECISIONS-FORMAT.md`

When a term is resolved during the walkthrough, update `CONTEXT.md` inline using the format above (glossary only — no procedure). At wrap-up, auto-record scaffolding conventions (not the produced skill's content) to `memory/decisions/` per DECISIONS-FORMAT.md, noting provenance as a skill-scaffolder session; then update `memory/index.md` and append `memory/log.md` per `memory/TEMPLATES.md`. Record a convention only when one actually changes — e.g. a preferred source repo, an audit-strictness choice, or a search-term pattern.

## Relationship to workflow-builder

`workflow-builder` builds a skill whose steps are run by local agents from `agents-library/`. Its Phase 1 loads this skill to **seed** the produced skill from the directory before authoring from scratch: this skill finds and customizes a comparable base, then hands the skeleton back so workflow-builder wires in the per-step agents. This skill is also usable standalone.

## Hard rules

- One question at a time. Plain text. No `Question` / `ask_user_question` tool.
- Recommend an answer for every real question.
- Adopt only a skill that clears the audit gate: majority of partners `pass` and no genuine `CRITICAL`/`HIGH` finding; no-audit-yet is ineligible. Always surface the full per-partner verdict, including any lone `warn`/`fail`, so adoption is knowing.
- Discover by browsing the server-rendered pages (`/official`, `/topic/<t>`, `/<owner>`) and GitHub, not the client-rendered `/search` page. Reach skills.sh read-only (web fetch + public audit API; token-gated API only if `VERCEL_OIDC_TOKEN` is present). Derive a customized local copy — do not adopt a remote skill wholesale unless the user explicitly asks to install the raw upstream skill.
- Preserve provenance (source, author, URL, audit date) in the derived skill.
- Create `.opencode/skills/` if missing; refuse and ask before overwriting an existing skill.
- Write real files (never paste whole skills into chat) so the OpenWork skill reload banner appears.
- Apply the authoring bar to the customized skill; prune the base skill's dead weight.

</supporting-info>

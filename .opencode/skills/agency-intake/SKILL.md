---
name: agency-intake
description: |
  Interview a client and produce a valid agency Spec v0 for one configuration artifact.

  Triggers when user mentions:
  - "agency intake"
  - "create an intake Spec"
  - "start client intake"
  - "interview me for a client skill"
---

<what-to-do>

Run a short client-side intake interview and emit one **Spec v0** markdown file for one configuration artifact. The Spec is the handoff contract sent to the builder. Do not build the requested skill/artifact here.

Ask one question at a time in plain text. Never use `ask_user_question` / `AskUserQuestion`. Recommend a default when useful, but let the client override. Keep the interview practical; stop when the Spec is buildable, not when every possible detail is explored.

The decisions the client settles here (host, scope, framing, accepted criteria) are recorded to local `memory/` at the end of the interview so future sessions on this host can recall them — same read/write pattern as the `talk-it-through` skill. This runs in addition to, not instead of, the phases below, which are unchanged.

## Awareness of prior decisions

Before interviewing, read `memory/index.md` if it exists, to see what this client has already settled — prior engagements, a known host preference, recurring wants. In particular look for prior `memory/decisions/decision-<client-slug>-engagement.md` continuity records from earlier engagements. Pull a full memory file lazily only when an index row looks relevant to this engagement; do not eagerly read the whole memory tree. If no `memory/` exists yet, skip the read and proceed; memory files are created lazily at wrap-up, not eagerly initialized.

Use what you find to inform the interview (e.g. surface a prior host preference as the recommended default), but still confirm every value with the client this session — memory is a hint, not a shortcut past a question.

## Guardrails

- One Spec = one artifact, usually one skill. If the client asks for a bundle (`CLAUDE.md` + agents + multiple skills), split it into multiple Specs unless the builder explicitly asked for a bundle Spec.
- No secrets, passwords, API keys, private tokens, or raw sensitive client data. Ask for redacted examples.
- Client owns their host/subscription. Do not offer hosted infra, proxies, schedulers, or always-on automations.
- The Spec must be self-contained: no links or external docs required to understand it.
- v1 is Cowork-first. Use `host: cow` unless the client clearly says Openwork, then use `host: open`.

## Phase 0 — Load contract if local

If this workspace has `spec/FORMAT.md`, read it before interviewing and follow it. If `spec/spec_validator.py` exists, use it at the end to validate the generated Spec. If those files do not exist, use the embedded contract below.

## Phase 1 — Metadata

Collect these fields first:

1. Client slug: lowercase kebab, e.g. `acme-co`. Ask if unknown.
2. Host: `cow` for Cowork or `open` for Openwork. Recommend `cow`.
3. Engagement slug: lowercase kebab name for the artifact, e.g. `inbox-triage-skill`.

**Do not ask about pricing, revision rounds, or billing.** Round limits are negotiated between the builder and client before this skill is ever used — they are not part of information gathering. Never ask the client how many revisions come with the build, and never emit a `cap` field in the Spec.

## Phase 2 — Artifact purpose

Ask the client what they want the artifact to do in one paragraph. Then compress it into:

- **Trigger** — the phrase/situation that should invoke the skill/artifact.
- **Inputs** — what the user will paste, select, upload, or provide.
- **Expected outputs** — what the artifact should return or change.

Confirm these three back to the client before continuing.

## Phase 3 — Examples

Get one realistic example. Redact sensitive details. Shape it as:

- `Input:` what the client would provide.
- `Output:` what a good answer/artifact behavior should look like.

If the client gives a vague example, tighten it by asking for one concrete input and the exact output they would accept.

## Phase 4 — Acceptance criteria

Draft 2–5 numbered acceptance criteria. Each criterion must be one line in this exact shape:

`N. Input: <test input or situation> → Expected: <observable expected result>`

Rules:
- Criteria are the done-line. The builder ships only when these pass locally; the client closes the engagement only when these pass in their host.
- Cover the happy path, one ambiguous/edge case, and one empty/error/no-data case when relevant.
- Avoid subjective criteria like "works well". Make each result observable.
- Criteria numbers matter: feedback later references `Criterion N`.

Read the criteria back and ask: "If all of these pass in your host, is this artifact done?" Revise until the answer is yes.

## Phase 5 — Emit Spec v0

Produce the final Spec as a single markdown document using this exact structure:

```
---
host: <cow-or-open>
client: <client-slug>
engagement: <engagement-slug>
---

# Spec — <human title>

## Round 0 — intake

### Trigger
<when/why the artifact runs>

### Inputs
- <input 1>
- <input 2>

### Expected outputs
- <output 1>
- <output 2>

### Examples
Input:
<example input>

Output:
<example output>

### Acceptance criteria
1. Input: <X> → Expected: <Y>
2. Input: <X> → Expected: <Y>
```

Do not include feedback rounds in Spec v0.

## Phase 6 — Validate and save

Before finalizing, run this checklist:

- Frontmatter has `host`, `client`, `engagement`.
- `host` is `cow` or `open`.
- No `cap` field is present (round limits are handled by the builder outside the Spec).
- There is exactly one `## Round 0 — intake` section.
- Round 0 has all five sections: `### Trigger`, `### Inputs`, `### Expected outputs`, `### Examples`, `### Acceptance criteria`.
- Acceptance criteria are numbered and each line contains `Input:` + `→` or `->` + `Expected:`.
- The Spec is self-contained and contains no secrets.

If `spec/spec_validator.py` exists locally, write the Spec to a temporary file and run:

`python3 spec/spec_validator.py <temp-spec-file>`

Fix any validator errors before giving the Spec to the client.

Show the complete Spec and ask: "Does this capture what you want?" Revise until the client says yes. (Internally this is approving the Spec.) Do not save or output the final copy until approved.

Once the client approves, create the file yourself — do not ask whether to save it. If file tools are available, save the approved Spec as `specs/<client-slug>-<engagement-slug>-spec.md` and tell the client the file name you created. If file tools are not available, output the approved markdown and tell the client to paste it into a `.md` file and save it.

Then run the memory wrap-up below before giving the client their final handoff line.

## Record decisions in memory (wrap-up)

The client settled real decisions during this interview. Record them automatically for later use. Do **not** ask "do you want me to record this?" — the client was actively in the loop settling things, so the interview itself is the approval. This auto-promotes settled decisions and intentionally deviates from the default candidate-gate flow (where signals land in `candidates/` first and promote only on explicit approval) — mirroring `talk-it-through`, because the client was actively settling these in-session.

Run this after the client approves the Spec. Route each settled item by what it is (default to **decision** when ambiguous):

- **decision** — host choice, scope boundaries, a "one artifact not a bundle" call. → write to `memory/decisions/decision-<slug>.md`.
- **preference** — how the client wants the skill to behave or the interview to run. → merge into `memory/preferences/`.
- **workflow** — a repeated habit or sequence the client described. → merge into `memory/workflows/`.
- **doc** — an important reference, link, or path the client gave. → merge into `memory/docs/`.

Always write one **engagement continuity record**: `memory/decisions/decision-<client-slug>-engagement.md` capturing client slug, host, engagement slug, a one-line description, and the date commissioned. This is the file the up-front read looks for next time to recall what this client has commissioned and on what terms.

Anything **explicitly settled in-session** promotes directly to its topic file (skip the candidate gate). Only things you **inferred but did not confirm** go to `memory/candidates/` under the normal gate. Before writing, scan `memory/index.md` and the relevant topic folder for a matching or superseded entry; update it in place (bump `updated`, note what changed) rather than duplicating.

Create `memory/` files lazily — only when you have something to write. If `memory/TEMPLATES.md` is present on this host, follow it for entry shape, index rows, and log lines. If it is not present, use this minimal shape so the skill has zero external dependencies:

- entry frontmatter: `title`, `type` (decision/preference/workflow/doc/context), `status: promoted`, `created`, `updated`, `topics: [...]`; then a short prose body.
- `memory/index.md` row: one line per entry — path, type, one-line summary, updated date.
- `memory/log.md` line: date, action (wrote/updated), path.

Tell the client in one short line what you recorded, the type, and where.

Do not re-dump the Spec into memory — the Spec file is already the durable engagement record. Record only the cross-engagement signal (the continuity record above plus any durable preference/workflow/doc). Skip purely transient one-off details. Never store secrets, credentials, or raw sensitive client content — summarize and redact; the safety rules in `memory/README.md` apply if it is present. If nothing qualifies, say so in one line.

Then end the client-facing session by telling the client to email this file back to the team building it. Keep this same file for any revisions later — don't start a new one.

</what-to-do>

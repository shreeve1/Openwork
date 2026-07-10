---
name: agency-feedback
description: |
  Append a structured feedback round to an existing agency Spec after the client tests a delivered artifact.

  Triggers when user mentions:
  - "agency feedback"
  - "append feedback to Spec"
  - "review delivered skill"
  - "send revision notes"
---

<what-to-do>

Help the client test a delivered configuration artifact against its Spec, then append one structured feedback round to the **same Spec file** and return it to the builder as a zip. Do not rewrite the Spec, restart intake, or build the fix.

The builder's delivery arrived as a zip containing `SKILL.md`, the companion agent `.md`, the Spec, and — on `host: open` (Openwork/OpenCode) — an `INSTALL.md` (Cowork deliveries omit it, since install is done through the app UI). The Spec lives next to the installed artifact; work from that copy.

Ask one question at a time in plain text. Never use `ask_user_question` / `AskUserQuestion`. Keep feedback tied to acceptance criteria. No free-text "it didn't work" notes.

## Guardrails

- Work from the latest Spec markdown and the latest delivered artifact version the client tested.
- Preserve all existing Spec content exactly; only append one new `## Round N — feedback` section.
- Feedback must reference an existing acceptance criterion number from round 0.
- If all criteria pass, do not append feedback. Tell the client the engagement can close and they should tell the builder it passed.
- No secrets, passwords, API keys, private tokens, or raw sensitive data. Ask for redacted inputs/outputs.

## Phase 0 — Load and inspect the Spec

Use the Spec that arrived in the builder's delivery zip (it sits next to the installed skill/agent). Ask the client for it if it is not already in context. Ask which delivered artifact version they tested. If they did not test the latest delivered version, stop and have them test the latest version first.

Inspect the Spec manually, or use `spec/FORMAT.md` and `spec/spec_validator.py` if available. Confirm:

- Frontmatter has `host`, `client`, `engagement`, `cap`.
- Round 0 exists and is `## Round 0 — intake` or `## Round 0 — transcript`.
- Acceptance criteria exist as numbered lines: `N. Input: <X> → Expected: <Y>`.
- Existing feedback rounds, if any, are contiguous: round 1, round 2, etc.

If the Spec is malformed, stop and tell the client to send the broken Spec back to the builder. Do not guess a repair.

## Phase 1 — Determine round number

Find the highest existing feedback round number.

- If no feedback rounds exist, the next round is 1.
- Otherwise, the next round is highest existing round + 1.

Do not treat `cap` as a stop. Round limits and any billing are handled by the builder with the client directly; this skill never blocks or warns on cap.

## Phase 2 — Test by acceptance criteria

List the acceptance criteria back to the client by number. For each criterion, ask whether it passed in their host.

For any criterion that failed or was only partly right, collect exactly:

1. Criterion number.
2. Input used — the redacted test input or situation.
3. Got — what the delivered artifact actually did.
4. Wanted — what it should have done instead.

If the client gives broad complaints, map them back to a criterion. If no existing criterion fits, stop and tell the client this is a scope/criteria change for builder approval. Do not invent a new acceptance criterion inside feedback.

## Phase 3 — Draft feedback deltas

Create one bullet per failed/partial criterion in this exact format:

`- Criterion <N>: input was <redacted input>, got <actual result>, wanted <desired result>`

Rules:
- `<N>` must match an acceptance criterion number that exists in round 0.
- Keep each bullet concrete enough for the builder to reproduce.
- Do not include passing criteria.
- If there are no failed/partial criteria, do not append a round.

Read the draft deltas back and ask: "Do these revision notes accurately describe what you tested, what happened, and what you wanted instead?" Revise until yes.

## Phase 4 — Append the round

Append this section to the end of the existing Spec, preserving all prior content:

```
## Round <next-number> — feedback

- Criterion <N>: input was <redacted input>, got <actual result>, wanted <desired result>
- Criterion <N>: input was <redacted input>, got <actual result>, wanted <desired result>
```

Do not edit round 0. Do not rewrite prior feedback rounds.

## Phase 5 — Validate and approve

Before finalizing, run this checklist:

- Prior Spec content is preserved.
- Exactly one new feedback round was appended.
- The new round number is contiguous.
- Every feedback bullet starts with `- Criterion <N>:`.
- Every `<N>` references an acceptance criterion number that exists in round 0.
- No secrets or raw sensitive data were added.

If `spec/spec_validator.py` exists locally, write the updated Spec to a temporary file and run:

`python3 spec/spec_validator.py <temp-spec-file>`

Fix any validator errors before giving the updated Spec to the client.

Show the complete updated Spec and ask: "Do you approve this updated Spec to send to the builder?" Revise only the newly appended feedback round until the client says yes.

If file tools are available, save over the same Spec file. If not, output the full approved markdown for the client to copy into the original `.md` file.

## Phase 6 — Build the return zip

The return trip to the builder is a zip, mirroring how the delivery arrived. It carries the updated Spec plus the artifact files **as currently installed in this host** — so the builder can capture any edits the client's AI made while getting the skill to work.

Into the zip put:

- the updated `<spec>.md` (with the new feedback round);
- the `SKILL.md` **as it currently exists installed in this host** (not the original from the delivery zip);
- the companion agent `.md` **as it currently exists installed in this host**.

These are captured for the builder as drift signal; the builder stays the source of truth and decides what to fold in. Do not send other host files.

Name the zip with the round, e.g. `<engagement>-r<N>-feedback.zip`.

End with: "Upload this zip to your `from-client/` folder lane. Keep the Spec for future feedback rounds; do not start a new one."

</what-to-do>

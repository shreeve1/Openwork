# Glossary — Writing Great Skills

Disclosed reference for the **Authoring bar** in [`SKILL.md`](SKILL.md). These are the leading words to think with when composing a produced SKILL.md. Adapted from Matt Pocock's `writing-great-skills`. A skill exists to wrangle determinism out of a stochastic system; the root virtue is **predictability**, and every term below is a lever on it.

**Bold terms** in a definition are defined elsewhere in this file.

## Predictability

The degree to which a skill makes the agent behave the same *way* on every run — the same process, not the same output. The root virtue every other term serves.

## Invocation

### Description

The skill's machine-readable trigger. Its presence is the invocation axis: keep it and the agent can fire the skill autonomously (model-invoked); it also lets other skills reach it. The description spends **context load** every turn, so word it tightly — state what the skill is, then list one trigger per **branch**, front-loaded with the **leading word**.

### Context pointer

A reference held in context that names out-of-file material and encodes the condition for reaching it. The **description** is the top-level pointer (context → skill); a link to a disclosed file like this one is the same object one level down. Its *wording*, not its target, decides when and how reliably the agent reaches the material. If a must-have target fires unreliably, sharpen the wording first; inline it only if that fails.

### Context load

The cost a model-invoked skill imposes on the context window — its **description**, always loaded, spending tokens and attention. The brake on splitting into ever more model-invoked skills.

## Information hierarchy

A skill's content ranked by how immediately the agent needs it. Three rungs:

1. **Steps** — in-file, primary.
2. **Reference**, in-file — secondary.
3. **Reference**, disclosed — pushed behind a **context pointer** (like this file).

Keep the top legible; push down whatever you can.

### Steps

The ordered actions the agent performs — the primary tier, the part that earns its place in SKILL.md. Every step ends on a **completion criterion**.

### Reference

Material the agent refers to on demand — definitions, facts, rules, examples. Secondary to **steps** when a skill has them; the prime candidate for **progressive disclosure**.

### Progressive disclosure

Moving **reference** down the ladder — out of SKILL.md and behind a **context pointer** — so the top stays legible. Licensed by the **branch**: disclose what only some branches need; inline what every path needs.

### Co-location

Keeping the material an agent needs at once in one place — a concept's definition, rules, and caveats under one heading, not scattered. The hierarchy ranks *how far down* a piece sits; co-location decides *what sits beside it* once there.

### Completion criterion

The condition that tells the agent a unit of work is done. Two properties make it a lever: its *clarity* (can the agent tell done from not-done?) resists **premature completion**, and its *demand* (how much it requires — "every item accounted for", not "produce a list") drives thorough work. The strongest criteria are both checkable and exhaustive.

## Steering

### Branch

A distinct way a skill can be invoked — a case it handles — so different runs take different paths through it. The cleanest disclosure test: inline what every branch needs, disclose what only some reach.

### Leading word

A compact concept already living in the model's pretraining that the agent thinks with while running the skill (e.g. *lesson*, *fog of war*, *tracer bullets*). Repeated as a token, never a sentence, it accumulates a distributed definition and anchors a region of behaviour in the fewest tokens. It serves predictability twice — in the body it anchors *execution*; in the **description** it anchors *invocation*. Reach for an existing pretrained word before coining your own.

### Premature completion

*Failure mode.* Ending a step before it is genuinely done, attention slipping to *being done*. Defence, in order: sharpen the **completion criterion** first (cheap, local); only if it is irreducibly fuzzy *and* you observe the rush, hide the later steps by splitting the sequence.

### Negation

*Failure mode.* Steering by prohibition drags the forbidden behaviour into context and makes it *more* available ("don't think of an elephant" names the elephant). Cure: prompt the *positive* — describe the target behaviour so the banned one is never spoken. Keep a prohibition only as a hard guardrail you can't phrase positively, and pair it with what to do instead.

## Pruning

### Single source of truth

Each meaning lives in exactly one authoritative place, so changing the behaviour is a one-place edit. **Duplication** is its violation.

### Duplication

*Failure mode.* The same meaning in more than one place. Costs maintenance and tokens, and inflates a meaning's prominence on the ladder past its real rank. The accidental inverse of a **leading word** (which repeats a token on purpose, never the meaning).

### Relevance

Whether a line still bears on what the skill does — the lens for what to keep. A line loses relevance by never bearing on the task or by going stale.

### Sediment

*Failure mode.* Stale layers that settle because adding feels safe and removing feels risky. The default fate of any skill without a pruning discipline.

### Sprawl

*Failure mode.* A skill simply too long, even when every line is live and unique. The cure is the **information hierarchy**: disclose **reference** behind pointers, and split by **branch** or sequence so each path carries only what it needs.

### No-op

*Failure mode.* A line the model already obeys by default, so you pay load to say nothing. The test: does it change behaviour versus the default? A weak **leading word** is a no-op; the fix is a stronger word, not a different technique.

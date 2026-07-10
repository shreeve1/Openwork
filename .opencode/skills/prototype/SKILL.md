---
name: prototype
metadata:
  route_default: daily
  route_max: medium
  route_class: prototype
---

<<<ROUTE default=daily max=medium class=prototype>>>

# Skill: prototype

# Prototype (Office Edition)

A prototype is a **throwaway document draft that answers a question**. The question decides the shape.

## Pick a branch

Identify which question is being answered — from the user's prompt, or by asking if the user is around:

- **"What delivery medium works best for this content?"** → [MEDIUM.md](MEDIUM.md). Generate the same information as 3 different output types (Markdown report, PowerPoint deck, flowchart/diagram) so the user can compare and pick.
- **"How should I structure this narrative?"** → [STRUCTURE.md](STRUCTURE.md). Generate 3 different organizational approaches in the same output type (executive summary first, chronological story, problem-solution consulting style) so the user can compare and hybridize.

The two branches produce very different artifacts — getting this wrong wastes the whole prototype. If the question is genuinely ambiguous and the user isn't reachable, default to MEDIUM (the more exploratory branch) and state the assumption.

## Rules that apply to both

1. **Throwaway from day one, and clearly marked as such.** Create files under `prototype/` at the workspace root. Name them so a casual reader can tell they're drafts, not final deliverables — e.g. `prototype/wip-report-variant-a.md`, `prototype/wip-slides-variant-b.pptx`.

2. **All files openable without a build step.** Use formats OpenWork can preview natively: Markdown (.md), CSV (.csv), Excel (.xlsx), PowerPoint (.pptx), Excalidraw (.excalidraw). No code, no compilers, no run commands.

3. **No persistence across days by default.** These are session drafts. If the user wants to keep working on them tomorrow, promote the winner to a real path — don't let prototype/ become permanent storage.

4. **Skip the polish.** No fussing with slide masters, no stock photos, no formatting nitpicks, no alternate wordings. The point is to learn which direction works, fast. Polish after the direction is chosen.

5. **Surface all 3 side by side.** After creating all 3 variants, present a comparison table highlighting what each variant is optimized for, so the user can see differences at a glance.

6. **Delete or absorb when done.** When the prototype has answered its question, either delete `prototype/` or promote the winning variant to its real location. Don't let drafts rot.

## When done

The **answer** is the only thing worth keeping from a prototype. Capture it somewhere durable (session memory, commit message, or a quick note to the user): "Picked Variant B — slides format worked because this was going to a board meeting." If the user is around, that capture is a quick conversation.

Base directory for this skill: file:///Users/james/Documents/itastack/.opencode/skills/prototype
Relative paths in this skill (e.g., MEDIUM.md, STRUCTURE.md) are relative to this base directory.
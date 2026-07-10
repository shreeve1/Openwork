---
name: workflow-builder
description: >-
  Walks the user through designing a reusable workflow skill in Cowork, talk-it-through style: grills the step list from a stated goal, then for each step picks a specialist agent from `agents-library/`, customizes it, and writes the customized copy to `.claude/agents/<slug>.md`; the produced skill has the session adopt that persona inline. Applies skill-creator's authoring bar so the skill is properly made, and writes it to `.claude/skills/<name>/SKILL.md` so Cowork's reload banner appears. Maintains shared CONTEXT.md and memory/decisions/ alongside talk-it-through. Use when the user wants to design, scaffold, or build a new workflow skill, pick and customize agents for a multi-step process, or mentions "workflow builder", "build a workflow", "create a skill that uses agents".
---

This is a loader for the `workflow-builder` skill. Read `.claude/skills/workflow-builder/SKILL.md` from the currently attached folder and follow it exactly, as if its full contents appeared here. If that file is not present in the attached folder, say so and stop — do not improvise the skill.

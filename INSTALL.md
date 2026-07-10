# INSTALL.md

Instructions for an AI agent. A non-technical user will give you a one-sentence prompt to follow this file. Do everything — the user should not need to clone, download, or create anything manually.

## What you are installing

This is an OpenWork workspace configuration bundle. It contains:
- `AGENTS.md` — shared agent operating rules
- `.opencode/skills/` — reusable skill workflows
- `agents-library/` — reference library of specialized agent definitions
- `skills-library/` — design, UI, and web effect skills

## Installation steps

1. Confirm the target directory. If the user hasn't specified one, use the current working directory.

2. **Get the source.** Clone the repo (preferred) or download its contents:
   ```
   git clone https://github.com/shreeve1/Openwork /tmp/openwork-config
   ```
   If git is not available, download the repo as a zip from the same URL and extract it. Use `/tmp/openwork-config` as the source path for the steps below.

3. Create `.opencode/skills/` if it doesn't exist.

4. Copy every skill directory from the source `.opencode/skills/` into the target `.opencode/skills/`, **except**:
   - Skip `agency-build/`

5. Copy `AGENTS.md` from the source into the target root. If an `AGENTS.md` already exists:
   - Prepend a marker comment `<!-- OpenWork config start -->` at the top of the incoming content and `<!-- OpenWork config end -->` at the bottom.
   - Insert it before any existing content. If the existing file already has these markers, replace only the marked section.

6. Copy `agents-library/` from the source into the target root. If `agents-library/` already exists, merge: add new files, overwrite existing ones with the same name.

7. Copy `skills-library/` from the source into the target root. If `skills-library/` already exists, merge: add new files, overwrite existing ones with the same name.

8. Clean up: remove the cloned/downloaded source at `/tmp/openwork-config`.

9. Report what was installed and where.

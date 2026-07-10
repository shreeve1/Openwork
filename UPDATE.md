# UPDATE.md

Instructions for an AI agent. A non-technical user will give you a one-sentence prompt to follow this file. Do everything — the user should not need to clone, download, or diff anything manually.

## What you are updating

This updates a previously installed OpenWork workspace configuration bundle. Compare the latest source against the user's current project.

## Update steps

1. Confirm the target directory. If the user hasn't specified one, use the current working directory.

2. **Get the latest source.** Clone or download the repo:
   ```
   git clone https://github.com/shreeve1/Openwork /tmp/openwork-config
   ```
   If the target already has a git clone of the source, pull instead. If git is not available, download the repo as a zip from the same URL and extract it.

3. **Skills** — compare the source `.opencode/skills/` against the target `.opencode/skills/`:
   - Add any skill directories that exist in source but not in target.
   - For directories that exist in both, compare their `SKILL.md` files. If different, overwrite the target with the source version.
   - Skip `agency-build/` — never add or update it.
   - Do not delete any skills that exist in target but not in source.

4. **AGENTS.md** — if the source `AGENTS.md` differs from the target's OpenWork section (between `<!-- OpenWork config start -->` and `<!-- OpenWork config end -->` markers), replace the marked section. If the markers don't exist, insert the marked section at the top.

5. **agents-library/** — add new files, overwrite changed files. Do not delete files that exist in target but not in source.

6. **skills-library/** — add new files, overwrite changed files. Do not delete files that exist in target but not in source.

7. Clean up: remove the cloned/downloaded source at `/tmp/openwork-config`.

8. Report:
   - Skills added
   - Skills updated
   - AGENTS.md: updated or unchanged
   - agents-library: files added, files updated
   - skills-library: files added, files updated
   - If nothing changed, say so.

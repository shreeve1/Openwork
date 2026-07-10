---
name: openwork-onboarding
description: |
  Guide first-time teammates through OpenWork Desktop onboarding, including teammate profile capture, HaloPSA agent lookup, local personal profile memory setup, file/editor validation, browser validation, connected-tool discovery, and one-pass workstation readiness setup.

  Triggers when user mentions:
  - "OpenWork onboarding"
  - "onboard my team"
  - "teach teammates OpenWork"
metadata:
  route_default: daily
  route_max: medium
  route_class: openwork_onboarding
---

<<<ROUTE default=daily max=medium class=openwork_onboarding>>>

# OpenWork Onboarding

Use this skill when a teammate needs a guided first-run walkthrough of OpenWork Desktop.

Goal: capture the teammate's workspace identity, initialize local personal memory scaffold without asking, save confirmed onboarding-safe profile context in `memory/preferences/current-openwork-teammate.md` only after approval, verify OpenWork can create and edit the onboarding status report, verify browser automation works, discover connected tools, and optionally run one-pass workstation readiness setup without repeated permission prompts.

Audience: non-technical or mixed-technical team members using OpenWork for the first time.

## Human flow principles

- Keep onboarding short and confidence-building.
- Use one primary artifact: `.onboarding/openwork-onboarding-report.md`.
- Use the onboarding status report to record file creation, editing, and check results.
- Do not create separate onboarding reports unless the user explicitly asks.
- Ask fewer questions. Group related approvals.
- Pause only after visible checks or when the user must decide something meaningful.
- Use plain language first: "connected tools" before "MCP/extensions".
- Treat workstation readiness as a one-pass optional setup: ask once, then check and install the approved recommended bundle without repeated package-by-package prompts.

## Safety rules

- Do not read secrets, tokens, `.env` files, credential stores, shell history, browser cookies, or private logs.
- Do not modify global config unless the user explicitly approves the exact change.
- Do not write teammate profile data to `AGENTS.md`; endpoint sync manages `AGENTS.md` and may overwrite local drift.
- Treat teammate profile data as local personal memory, not shared repo context.
- Initialize or repair the safe local memory scaffold without asking. This creates only directories, scaffold files, `.gitkeep` files, `memory/index.md`, and `memory/log.md`; it must not write teammate profile values or preferences.
- Before writing name, Halo agent ID, email, initials, teams, timezone, workday, or active status to `memory/preferences/current-openwork-teammate.md`, tell the user that `memory/` is ignored local personal workspace state and endpoint sync does not overwrite populated memory files.
- Store only onboarding-safe HaloPSA fields in local personal memory. Do not paste the raw Halo agent payload, department role GUIDs, cost/rate fields, billing flags, phone/SMS fields, or third-party authorization flags.
- Do not guess HaloPSA agent details. If Halo lookup fails or has multiple matches, ask the user to choose or continue with unknown values.
- Do not install workstation tools, packages, CLIs, or dependencies without one explicit grouped approval. Explain scope, likely admin prompts, OS package helpers such as Homebrew or winget, and consequences of skipping before asking.
- If system elevation appears during installs, the user handles it directly. Never ask for passwords.
- If managed endpoint policy blocks installs, record the blocker and continue. Do not try to bypass policy.
- Use harmless onboarding files under `.onboarding/` in the current workspace.
- Use public browser checks only unless the user explicitly asks for an internal app check.
- Keep connected-tool discovery dynamic. Do not assume any specific ticket system, document tool, email tool, database, or customer system exists.

## Outputs

Create or update:

- `.onboarding/openwork-onboarding-report.md` — the single onboarding status report.
- `memory/` scaffold, `memory/index.md`, and `memory/log.md` — initialized without asking before profile capture.
- `memory/preferences/current-openwork-teammate.md` — confirmed onboarded teammate profile, only after explicit approval; skip this file if the user declines profile storage.
- Optionally `.onboarding/package-check.xlsx` — validation workbook showing the local Python reporting bundle can create Excel workbooks, only if workstation readiness setup is approved and Python package install succeeds.

Do not create any separate onboarding report during normal onboarding. Use only `.onboarding/openwork-onboarding-report.md` as the status report.

If OpenWork shows a skill reload banner after this skill is installed or updated, reload skills before running onboarding.

## Required versus optional access

Required:

- Workspace write access for `.onboarding/` and `memory/`.
- Read-only HaloPSA agent lookup access, or user-provided fallback values if Halo is unavailable.
- Built-in browser access for a public browser validation URL, defaulting to `https://www.cloudflare.com/cdn-cgi/trace`.

Optional:

- Shell access for OS detection, Claude Code detection, and workstation readiness.
- OpenWork UI actions for opening settings panels if permission fixes are needed.
- Extension/MCP action access for connected-tool discovery.

## Onboarding workflow

### Start here: initialize memory and capture teammate profile

Before explaining checks, detecting OS, creating files, opening browser, or discovering connected tools, initialize the local personal memory scaffold and handle the teammate profile step below. Do not run other onboarding checks until scaffold initialization succeeds and the profile step is complete, skipped because Halo is unavailable, or explicitly declined for local personal memory storage.

### 1. Initialize local personal memory scaffold and save profile

1. Load/use the `personal-memory` skill.
2. Without asking, initialize or repair the local memory scaffold:
   - create missing directories under `memory/`
   - create missing scaffold files `memory/README.md` and `memory/TEMPLATES.md` according to the memory system contract
   - create missing `.gitkeep` files in memory subdirectories
   - create `memory/index.md` if missing with an empty promoted memory section
   - create `memory/log.md` if missing with an initial scaffold entry
3. Do not write teammate profile values, preferences, raw content, or promoted topic files during scaffold initialization.
4. If scaffold initialization fails, stop onboarding and explain the workspace permissions problem before continuing.
5. Ask for the teammate's first and last name.
6. Search HaloPSA agents with the full name using the read-only Halo agent list/search tool when available, e.g. `itastack_itastack_halo` operation `agents.list` with `search: "<first> <last>"`.
7. From the selected Halo agent record, extract only onboarding-safe fields:
   - name
   - firstname
   - surname
   - id
   - email
   - initials
   - primary team (`team`)
   - team names from `teams[]`
   - timezone
   - workday name
   - active status derived from `isdisabled` when available
8. If exactly one likely agent matches, present name, agent ID, email, initials, primary team, teams, timezone, workday, and active status for confirmation.
9. If multiple likely agents match, ask the user to choose the correct record. Show only enough fields to disambiguate: name, agent ID, email, primary team, and active status if available.
10. If no Halo agent matches, ask whether to continue with name only or retry with a different spelling/email.
11. If the Halo lookup tool is unavailable, ask the user whether to continue with name/email/team values they provide or continue with unknown Halo fields. Do not block onboarding only because Halo lookup is unavailable.
12. Before writing teammate profile values to local personal memory, ask explicit confirmation:

   > Local memory scaffold is ready. I can save this profile in `memory/preferences/current-openwork-teammate.md`: name, Halo agent ID, email, initials, primary team, teams, timezone, workday, and active status. `memory/` is ignored local personal workspace state, and endpoint sync does not overwrite populated memory files. Save this profile?

13. If confirmed, create or update `memory/preferences/current-openwork-teammate.md` with this content shape:

   ```markdown
   ---
   title: Current OpenWork Teammate
   type: context
   status: promoted
   created: YYYY-MM-DD
   updated: YYYY-MM-DD
   topics: [onboarding, identity, halo]
   ---

   ## Active memory

   - Name: <First Last>
   - First name: <first name>
   - Last name: <last name or surname without pronoun text when obvious>
   - Halo surname field: <raw Halo surname field when it includes useful pronoun text, or unknown>
   - Halo agent ID: <agent_id or unknown>
   - Email: <email or unknown>
   - Initials: <initials or unknown>
   - Primary team: <team or unknown>
   - Teams: <comma-separated team names or unknown>
   - Timezone: <timezone or unknown>
   - Workday: <workday_name or unknown>
   - Active in Halo: <true/false/unknown>
   - Source: OpenWork onboarding HaloPSA lookup

   ## Notes

   - Store only onboarding-safe HaloPSA fields here.
   - Do not paste raw Halo payloads, secrets, tokens, rates, billing flags, phone/SMS fields, third-party authorization flags, client data, or private transcripts.
   ```

14. Update `memory/index.md` with a promoted memory row for `memory/preferences/current-openwork-teammate.md`, and append `memory/log.md` with the onboarding profile write.
15. If the profile file already exists, replace only this profile file content. Do not rewrite unrelated memory topic files.
16. If the user declines, do not write profile data to memory; continue onboarding with name-only or user-provided profile values in the report and record profile storage as declined.
17. If profile memory cannot be written after approval, stop and explain the workspace permissions problem before continuing.

### 2. Create the single Markdown onboarding status report

Create `.onboarding/openwork-onboarding-report.md` immediately after the profile step. This is the only normal onboarding document. Do not create a separate onboarding report.

Initial report structure:

```markdown
# OpenWork Onboarding Report

## Teammate profile

- Name: <First Last>
- Halo agent ID: <agent_id or unknown>
- Email: <email or unknown>
- Initials: <initials or unknown>
- Primary team: <team or unknown>
- Teams: <comma-separated team names or unknown>
- Timezone: <timezone or unknown>
- Workday: <workday_name or unknown>
- Active in Halo: <true/false/unknown>
- Halo lookup: <matched/multiple candidates/not found/unavailable>
- Profile memory: <updated/declined/failed>

## OpenWork checks

| Check | Result | Notes |
| --- | --- | --- |
| Status report create/edit | pending | `.onboarding/openwork-onboarding-report.md` |
| Browser automation | pending | public browser validation URL |
| Connected tools | pending | read-only discovery |
| Claude Code overlap | pending | quiet detection only |
| Workstation readiness | not requested | optional one-pass setup |
| Excel validation | not requested | optional `.onboarding/package-check.xlsx` |

## Next steps

- <filled during onboarding>
```

Append or update rows as checks run. Do not create a separate validation text file. If the status report cannot be created or edited, record the blocker and follow the workspace permission handling above.

If report creation or update fails:

1. Stop onboarding.
2. Explain likely workspace permissions issue.
3. Offer to open Settings > Permissions using OpenWork UI action `settings.panel.open` with `{panel:"permissions"}`.
4. Ask the user to authorize the current workspace or parent folder.
5. Continue only after file write access works.

After initial creation succeeds, append or update one status line in the report, such as:

```markdown
- Status report update: OpenWork updated this report during onboarding.
```

Then update the `Status report create/edit` row to `pass` if the append/update succeeds. If the append/update fails, follow the workspace permission handling above.

### 3. Check browser automation

Use the built-in browser tools.

1. Open `https://www.cloudflare.com/cdn-cgi/trace` with `openwork_browser_open_url`.
2. Use returned `browser_url` and `target_id` for all browser calls.
3. Capture browser snapshot.
4. Verify page text contains Cloudflare trace fields such as `fl=` or `ip=`.
5. Update the browser automation row in `.onboarding/openwork-onboarding-report.md`.

Important: do not call `browser_navigate` first. OpenWork browser automation must start with `openwork_browser_open_url`, then use the returned `browser_url` and `target_id`.

Pass condition:

- Browser opens `https://www.cloudflare.com/cdn-cgi/trace`.
- Snapshot includes trace fields such as `fl=` or `ip=`.

If check fails:

- Explain that OpenWork browser automation may be blocked by network, proxy, app state, or browser process issue.
- Suggest restarting OpenWork, clearing proxy if used, and trying again.
- Record failure and error in the report.

### 4. Quietly detect Claude Code overlap

Purpose: identify whether Claude Code is installed and whether OpenWork/opencode might auto-load Claude Code skills from `~/.claude/skills`.

Important distinction:

- Do not alter Claude Code itself.
- Do not delete, move, rename, or edit `~/.claude/`, Claude Code config, Claude Code skills, or the `claude` CLI.
- If isolation is needed, change only OpenWork/opencode behavior so OpenWork stops scanning Claude Code skill locations.
- Preferred isolation setting: launch OpenWork/opencode with `OPENCODE_DISABLE_CLAUDE_CODE_SKILLS=1`.
- Broader isolation setting: `OPENCODE_DISABLE_EXTERNAL_SKILLS=1`, which also blocks other external skill locations such as `~/.agents/skills`.
- Any isolation change needs explicit user approval naming the exact OpenWork/opencode file, environment setting, or launcher change.

Do not make this a scary or central onboarding topic. Do not ask anything if Claude Code is not installed. Continue silently and record “not detected” in the report.

Checks:

macOS/Linux:

```bash
command -v claude
claude --version
```

Windows PowerShell:

```powershell
where.exe claude
claude --version
```

If onboarding is being run from a non-Windows agent host for a Windows teammate, ask the Windows user to run the PowerShell commands locally and paste the output. Do not pretend the current macOS/Linux runtime proves Windows state.

If Claude Code is detected, record “detected; Claude Code unchanged” in the report and say briefly:

> Claude Code appears installed. I will leave Claude Code unchanged. OpenWork may auto-load Claude Code skills from `~/.claude/skills`; if your team wants isolation, we can block OpenWork from picking up those Claude Code skills without changing Claude Code itself.

Only discuss or apply isolation if the user asks or the team has known overlap. Any actual config or environment change needs a second confirmation naming exact file and change.

### 5. Discover connected tools

Use plain language with the user: “connected tools” means OpenWork extensions or MCP tools.

Do not assume any specific tool exists.

Use available discovery tools in this order:

1. `openwork_extension_list_actions` with no extension filter, if available. If the tool schema requires an argument, call it with `{ "extensionId": "" }`.
2. OpenWork UI action list if needed.
3. Built-in MCP/tool list known in the current session.

Summarize available read-only or clearly safe actions in the report. Group by likely use:

- tickets/support
- documents/files
- email/calendar
- cloud/admin
- databases/reporting
- unknown/other

If no connected tools are configured, say:

> No connected tools were discovered in this session. You can add integrations in Settings > Extensions. Once connected, OpenWork can use those tools to pull information into this report or future reports.

Update the connected tools row in `.onboarding/openwork-onboarding-report.md` and continue.

Only label an action read-only when the action name, description, and schema clearly indicate it only reads/lists/gets/searches data. If uncertain, list the action category only and do not call it during onboarding.

Do not call actions whose names or descriptions imply mutation during onboarding, including:

- delete
- update
- create
- send
- post
- patch
- remove
- revoke
- retry
- upgrade
- archive
- restore

Exception: user explicitly switches from onboarding into a real task and confirms the exact action.

### 6. Optional one-pass workstation readiness setup

Purpose: help the teammate install common local tools during onboarding so OpenWork does not stop later in the middle of real work.

This step is optional. Keep it brief. Ask once, then complete all approved readiness checks and installs without repeated package-by-package prompts.

Before running checks or installs, tell the teammate:

> This step is optional. I can do one workstation readiness pass: check Python, uv, Git, Node.js/npm, and the OS package helper; install missing recommended tools where practical, including Homebrew on macOS or winget-based installs on Windows when appropriate; then set up a local Python reporting bundle for Excel, CSV, PDF, document, chart, and report work. This may download software and may trigger system admin prompts. I will not ask for passwords. If managed endpoint policy blocks an install, I will record it and continue. Do you want me to run the one-pass readiness setup now?

If the user approves, that one approval covers:

- Running OS and tool detection commands.
- Installing or repairing missing recommended core tools where a standard user-safe install path exists.
- Installing the local Python reporting bundle into `.onboarding/.venv` if Python and uv are available after tool setup.
- Creating `.onboarding/package-check.xlsx` as validation if the reporting bundle installs successfully.
- Recording and continuing when managed endpoint policy blocks an install.

Do not ask separate permission for each package. Ask again only if:

- a command would modify global config,
- a command would require an unusual installer/source not listed below,
- a command asks for destructive changes,
- a command asks for credentials,
- the install path is ambiguous or unsafe.
- managed endpoint policy requires manual IT/admin action.

If the user declines, record the skip in `.onboarding/openwork-onboarding-report.md` and continue.

#### Tool checks by OS

Use OS-appropriate commands. If the current OpenWork agent runtime is not the same OS as the teammate's computer, ask the teammate to run the checks locally and paste output. Do not pretend the current host proves the teammate's machine state.

macOS/Linux:

```bash
uname -s
python3 --version
uv --version
git --version
node --version
npm --version
```

macOS additional:

```bash
brew --version
xcode-select -p
```

Windows PowerShell:

```powershell
$PSVersionTable.PSVersion
py --version
python --version
python3 --version
py -c "import sys; print(sys.executable); print(sys.version)"
uv --version
where.exe uv
git --version
where.exe git
node --version
where.exe node
npm --version
winget --version
```

Recommended core tools:

- Python 3 — powers Excel, CSV, PDF, report, cleanup, and automation workflows.
- `uv` — creates isolated Python environments and installs Python helpers quickly and safely.
- Git — lets OpenWork inspect workspace history, compare changes, and work with shared configuration safely.
- Node.js/npm — supports many MCP servers, browser tooling, previews, and JavaScript utilities.

OS-specific useful tools:

- Homebrew on macOS — simplest package manager for Python, uv, Git, Node.js, and other tools.
- Xcode Command Line Tools on macOS — required by some build/install workflows.
- winget on Windows — simplest package manager for Windows installs.
- PowerShell 7 on Windows — better shell compatibility for modern automation.

#### Install guidance

One approval covers the standard recommendations below. Use the simplest safe path for the detected OS.

Preferred macOS approach:

- If Homebrew is available, prefer Homebrew.
- Python: `brew install python`
- uv: `brew install uv`
- Git: `brew install git`
- Node.js/npm: `brew install node`
- If Homebrew is missing, do not install Homebrew automatically unless the user explicitly approved the readiness setup and Homebrew installation was included in the explanation. If not included, ask before installing Homebrew.

Preferred Windows approach:

- Treat winget as an install helper, not the source of truth for whether a command works.
- Check command behavior first: `py --version`, `uv --version`, `git --version`, `node --version`, and `npm --version`.
- Use winget package IDs only for install recommendations when a command is missing or unsuitable.
- Use `winget show --id <package-id> --exact --accept-source-agreements` before install to preview package metadata.
- Do not rely on `winget install --dry-run`; it is not supported on all Windows Package Manager versions.
- Do not rely on `winget --output json`; it is not supported on all Windows Package Manager versions.
- Expect winget output to include progress bars/spinner characters. Summarize results instead of pasting raw output into the onboarding report.
- Use safer install flags when winget is approved: `--exact --scope user --silent --accept-package-agreements --accept-source-agreements --no-upgrade --disable-interactivity`.
- Python: `winget install --id Python.Python.3.12 --exact --scope user --silent --accept-package-agreements --accept-source-agreements --no-upgrade --disable-interactivity`
- uv: `winget install --id astral-sh.uv --exact --scope user --silent --accept-package-agreements --accept-source-agreements --no-upgrade --disable-interactivity` when available, otherwise use the official Astral installer only after a fresh confirmation.
- Git: `winget install --id Git.Git --exact --scope user --silent --accept-package-agreements --accept-source-agreements --no-upgrade --disable-interactivity`
- Node.js/npm: `winget install --id OpenJS.NodeJS.LTS --exact --scope user --silent --accept-package-agreements --accept-source-agreements --no-upgrade --disable-interactivity`
- PowerShell 7: `winget install --id Microsoft.PowerShell --exact --scope user --silent --accept-package-agreements --accept-source-agreements --no-upgrade --disable-interactivity`
- After every winget install, verify the active command with `where.exe <command>` and `<command> --version`. winget success does not guarantee its installed command wins PATH precedence.
- If multiple command paths exist and an older one appears first, report a PATH precedence issue instead of assuming the install failed.

Preferred Linux approach:

- Identify distro first.
- Use the native package manager where practical: `apt`, `dnf`, `yum`, `pacman`, or the organization's standard package manager.
- Install Python 3, Git, and Node.js/npm from trusted distro or vendor repositories.
- Install uv using the official Astral installer or approved package source.
- If package manager use requires sudo/admin prompts and the one-pass approval did not explicitly include admin prompts for this endpoint, ask before continuing.
- If managed endpoint policy blocks package manager use, record the blocker and continue.

#### Local Python reporting/data package bundle

If Python and uv are available after checks/installs, install the local reporting/data package bundle without another prompt because it is covered by the one-pass approval.

Recommended bundle:

- `openpyxl` — create and edit `.xlsx` Excel workbooks.
- `xlsxwriter` — create formatted Excel workbooks with tables, formulas, charts, and styles.
- `pandas` — clean, filter, merge, summarize, and export tabular data.
- `python-docx` — create or edit Word `.docx` documents.
- `python-pptx` — create PowerPoint `.pptx` decks.
- `pypdf` — read, split, merge, or extract text from PDFs.
- `pillow` — basic image processing for screenshots and report images.
- `matplotlib` — create charts and graphs.
- `requests` — call APIs and download files.
- `beautifulsoup4` — parse HTML pages and tables.
- `lxml` — faster and more capable XML/HTML parsing.
- `pyyaml` — read and write YAML config and workflow files.
- `jinja2` — fill reusable report and document templates.
- `rich` — cleaner terminal output for scripts.

Do not install the bundle globally with `pip install ...` by default. Prefer a workspace-managed environment for validation during onboarding:

```bash
uv venv .onboarding/.venv
uv pip install --python .onboarding/.venv/bin/python openpyxl xlsxwriter pandas python-docx python-pptx pypdf pillow matplotlib requests beautifulsoup4 lxml pyyaml jinja2 rich
```

On Windows, the venv Python path is typically:

```powershell
.onboarding\.venv\Scripts\python.exe
```

Windows local venv install command:

```powershell
uv venv .onboarding\.venv
uv pip install --python .onboarding\.venv\Scripts\python.exe openpyxl xlsxwriter pandas python-docx python-pptx pypdf pillow matplotlib requests beautifulsoup4 lxml pyyaml jinja2 rich
```

After installation, run an import validation using the venv Python. Use the correct Python path for the OS:

```bash
.onboarding/.venv/bin/python -c "import openpyxl, xlsxwriter, pandas, docx, pptx, pypdf, PIL, matplotlib, requests, bs4, lxml, yaml, jinja2, rich; print('ok')"
```

Windows import validation:

```powershell
.onboarding\.venv\Scripts\python.exe -c "import openpyxl, xlsxwriter, pandas, docx, pptx, pypdf, PIL, matplotlib, requests, bs4, lxml, yaml, jinja2, rich; print('ok')"
```

Create `.onboarding/package-check.xlsx` with `openpyxl` to validate Excel creation. The workbook should contain a simple sheet named `Package Check` with rows for package, purpose, and status. Keep it harmless and non-client-specific.

If any package fails to install or import, record the failure and continue unless the user wants to troubleshoot. Do not let optional package setup block core onboarding.

Record in `.onboarding/openwork-onboarding-report.md`:

- Tool checks run or skipped.
- Python, uv, Git, Node.js/npm status.
- Windows command precedence from `where.exe` for uv, Git, Node.js, and other tools when checked.
- OS package manager status.
- Missing tools and install results.
- PATH precedence issues discovered after install.
- Python reporting bundle status: installed, partially installed, failed, or skipped.
- `.onboarding/package-check.xlsx` creation status when attempted.

### 7. Optional extra personal memory notes

Memory scaffold is already initialized in step 1 without asking. Do not ask to initialize memory again. Still ask before writing any extra preference, email, voice, docs, workflow, or raw memory content.

At the end, offer one optional lightweight follow-up only if useful:

> Want me to save any extra preferences for future sessions, like preferred response style, email tone, voice mode, or important docs? If not, we can leave memory with just your teammate profile.

If approved:

1. Load/use the `personal-memory` skill.
2. Populate only confirmed, durable, non-sensitive summaries:
   - `memory/preferences/assistant-style.md` — only confirmed assistant/style preferences.
   - `memory/email/tone-and-format.md` — only confirmed email/message preferences.
   - `memory/voice/voice-mode.md` — only confirmed voice-mode preferences.
   - `memory/docs/important-docs.md` — only safe docs, links, or workspace paths the teammate confirms should matter later.
   - `memory/workflows/onboarding-summary.md` — onboarding date, completed checks, and follow-up items if useful.
3. Update `memory/index.md` with promoted memory rows.
4. Append `memory/log.md` with setup/population entries.
5. Leave `memory/raw/` empty unless the user explicitly asks to store redacted raw material.

Safety rules:

- Never store secrets, tokens, passwords, API keys, bearer strings, OAuth credentials, credential-like config, raw Halo agent payloads, full emails, screenshots, client documents, or private transcripts.
- Prefer short summaries.
- If unsure whether data is sensitive, ask before writing.
- `AGENTS.md` remains server-managed shared workspace context; `memory/` remains local personal context.

### 8. Close onboarding

End with concise next steps:

- Mention exact file paths created.
- Mention whether local memory scaffold initialized and whether local personal memory was updated with the teammate profile.
- Mention whether status report create/edit passed.
- Mention whether browser automation passed.
- Mention whether connected tools were discovered.
- Mention whether workstation readiness was checked, skipped, or needs follow-up.
- Mention `.onboarding/package-check.xlsx` if the Python reporting bundle validation file was created.
- Mention how to add integrations: Settings > Extensions.
- Mention how to manage folders: Settings > Permissions.
- Keep `.onboarding/` by default as onboarding records. Mention that the user can ask OpenWork to delete it later if they want cleanup.

## User prompts that trigger this skill

- "Run OpenWork onboarding for a new teammate."
- "Onboard my team member and check file editing, browser automation, and connected tools."
- "Teach teammates OpenWork and create a setup report."

## Verification checklist for agent using this skill

- [ ] Loaded/used the `personal-memory` skill.
- [ ] Initialized or repaired local memory scaffold without asking before profile capture.
- [ ] Did not write teammate profile values, preferences, raw content, or promoted topic files during scaffold initialization.
- [ ] Asked for first and last name.
- [ ] Looked up HaloPSA agent details when the tool was available.
- [ ] Asked before writing teammate profile data to `memory/preferences/current-openwork-teammate.md`.
- [ ] Wrote `memory/preferences/current-openwork-teammate.md` only after explicit approval.
- [ ] Updated only `memory/preferences/current-openwork-teammate.md` plus `memory/index.md` and `memory/log.md` for profile storage, or recorded skip/decline.
- [ ] Created and updated `.onboarding/openwork-onboarding-report.md` as the single normal onboarding report.
- [ ] If report write failed, opened or offered Settings > Permissions and stopped until workspace write access worked.
- [ ] Appended a status update line to the status report and updated the status report create/edit row.
- [ ] Did not create any separate onboarding report.
- [ ] Browser check attempted against `https://www.cloudflare.com/cdn-cgi/trace`.
- [ ] Claude Code detection handled quietly and left unchanged unless explicitly requested.
- [ ] Connected-tool discovery stayed dynamic and read-only.
- [ ] Did not call connected-tool actions unless read-only status was clear from name, description, and schema.
- [ ] Asked only once for optional workstation readiness setup.
- [ ] If readiness was approved, checked Python, uv, Git, Node.js/npm, and OS package helper using OS-appropriate commands.
- [ ] If readiness was approved, installed missing recommended tools where safe without repeated package-by-package prompts.
- [ ] Did not install tools, packages, CLIs, dependencies, or global config changes outside the approved readiness scope.
- [ ] Recorded managed endpoint policy blockers without bypass attempts.
- [ ] Installed the Python reporting bundle only in a local environment by default, or recorded skip/failure.
- [ ] Verified Python reporting packages by importing them and creating `.onboarding/package-check.xlsx`, or recorded why verification was skipped.
- [ ] Offered optional extra personal memory notes without re-initializing memory.
- [ ] Populated only confirmed, durable, non-sensitive summaries.
- [ ] Left `memory/raw/` empty unless explicitly approved.
- [ ] Updated `.onboarding/openwork-onboarding-report.md` with final results.

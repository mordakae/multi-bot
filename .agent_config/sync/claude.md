# Claude Synchronisation Instructions

The default target is `./.claude/CLAUDE.md`.

- Copy `./.agent_config/agent_config_version` to `./.claude/agent_config_version`
- Copy `./.agent_config/mcp.json` to `./.mcp.json`

- **Back up user prefs before making any changes:**
  - Read the existing `./.claude/CLAUDE.md` (if it exists)
  - A heading is **agent-config-owned** if it matches any of these patterns:
    - `## rules/<anything>`
  - Any heading (and all content beneath it until the next same-or-higher-level heading) that
    does **not** match the above patterns is a **user pref**
  - Collect all user pref blocks verbatim and hold them in memory — they will be written back
    under `## USER PREFS` at the end
  - If no `CLAUDE.md` exists yet, there are no prefs to collect — proceed directly

- Use `./.claude/sync-history.yml` to track synchronised skills and agents:
  ```yaml
  agents:
    - <synced-agent>.md
  skills:
    - <synced-skill>/
  ```

---

## Rules

For each file in `./.agent_config/rules`:
- The heading is the file path from `.agent_config`, e.g. `rules/foo.md` → `## rules/foo.md`
- If frontmatter `globs` contains specific folder path(s):
  - Target `./specific/folder/path(s)/.claude/CLAUDE.md` (create multiple instances as needed)
  - Otherwise target the default
- If `globs` contains specific file types, add a note immediately after the heading:
  `> Applies to: <glob pattern>`
- If frontmatter `description` is present, include it as a quote block (`> `) immediately after
  the heading (or after the glob scope note if present)
- Include the body content of the file

---

## Skills

Skills are directories, not flat files. For each directory in `./.agent_config/skills`:
- The directory must contain a `SKILL.md` file
- The target directory is `./.claude/skills/<name>/` where `<name>` is taken from the
  frontmatter `name` field in `SKILL.md` (Claude Code's native skills location)
- Copy `SKILL.md` to `./.claude/skills/<name>/SKILL.md`
- If a `references/` subdirectory exists, copy it as-is to `./.claude/skills/<name>/references/`
  — do not inline the reference files into `SKILL.md`; Claude Code loads them on demand
- Adjust the yaml frontmatter of `SKILL.md` to use only fields supported by Claude Code skills
  (e.g. `name`, `description`, `allowed-tools`, `model`):
  - For unsupported fields, ask the user whether they want to provide alternatives or let you
    decide — if the latter, research how the field is used and how Claude provides the same
    functionality
  - For the `model` field, pick the closest available Claude model
- Ensure the skill is listed in `./.claude/sync-history.yml`

---

## Agents

For each file in `./.agent_config/agents`:
- Copy the file to `./.claude/agents/<filename>`
- Adjust yaml frontmatter to use only fields supported by Claude Code (e.g. `name`,
  `description`, `model`, `tools`):
  - For unsupported fields, ask the user whether they want to provide alternatives or let you
    decide — if the latter, research how the field is used and how Claude provides the same
    functionality
  - For the `model` field, pick the closest available Claude model
- Ensure the agent is listed in `./.claude/sync-history.yml`

---

## User Prefs

After writing all rules to `./.claude/CLAUDE.md`:
- Append a `## USER PREFS` heading at the bottom
- Write back all user pref blocks collected during the backup step, verbatim and in their
  original order
- Any content already under `## USER PREFS` in the previous file must remain semantically
  identical — do not rewrite or summarise it

---

## Cleanup

- For each entry in `./.claude/sync-history.yml`:
  - If there is no corresponding file in `./.agent_config`, delete the entry and its associated
    `./.claude/` file
- For each `../.claude/CLAUDE.md` in the repo, agent-config-owned headings that no longer have a
  corresponding file in `./.agent_config` will naturally be absent from the rewritten file
  (the backup + rewrite process handles this implicitly — no separate deletion step needed)
  - `## USER PREFS` and its child headings are exempt

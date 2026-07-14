# Codex Synchronisation Instructions

The default target is `./AGENTS.md`.

- Copy `./.agent_config/agent_config_version` to `./.codex/agent_config_version`
- Integrate MCP info from `./.agent_config/mcp.json` to `./.codex/config.json`

- **Back up user prefs before making any changes:**
  - Read the existing `./AGENTS.md` (if it exists)
  - Scan every heading (`##`, `###`, etc.) and its content
  - A heading is **agent-config-owned** if it matches any of these patterns:
    - `## Rules` (exact)
    - `### rules/<anything>` (child of `## Rules`)
    - `## Skills` (exact)
    - `### skills/<anything>` (child of `## Skills`)
    - `#### <anything>` that is a child of a `### skills/` section (skill references)
    - `## Agents` (exact)
    - `### agents/<anything>` (child of `## Agents`)
    - `## USER PREFS` (exact — exempt, handled separately below)
  - Any heading (and all content beneath it until the next same-or-higher-level heading) that
    does **not** match the above patterns is a **user pref**
  - Collect all user pref blocks verbatim and hold them in memory — they will be written back
    under `## USER PREFS` at the end
  - If no `AGENTS.md` exists yet, there are no prefs to collect — proceed directly

---

## Confirm current platform conventions

Your training data may predate Codex's current config format. Before applying anything below,
confirm Codex's **current** conventions for the surfaces this sync touches — where `AGENTS.md`
instructions, skills, agents, and MCP config are read from, and how each is structured — from the
platform's current documentation, using any config already present in the repo as a clue.

This governs only *how* config is placed and formatted for Codex. It is **not** license to alter
the source content in `./.agent_config/` (translate format, never meaning), to add, drop, or
restructure config beyond what these instructions describe, or to change Multi-Bot's sync model.
Where a section below already resolves a specific format detail, that detail stands unless current
documentation shows it has changed. If research is unavailable, fall back to the format details
written in this file.

---

## Rules

For each file in `./.agent_config/rules`:
- Place entries under the top-level `## Rules` section
- The heading is the file path from `.agent_config`, e.g. `rules/foo.md` → `### rules/foo.md`
- If frontmatter `globs` contains specific folder path(s):
  - Target `./specific/folder/path(s)/AGENTS.md` (create multiple instances as needed)
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
- The heading is `skills/<dir-name>`, e.g. `skills/example-skill` → `### skills/example-skill`
  under the top-level `## Skills` section
- Apply the same `globs` and `description` rules as for rules (above)
- Include the body content of `SKILL.md`
- If a `references/` subdirectory exists, append each file's content under a sub-heading named
  after the filename, e.g. `references/additional-info.md` → `#### additional-info.md`

---

## Agents

For each file in `./.agent_config/agents`:
- Place entries under the top-level `## Agents` section
- The heading is `agents/<filename>`, e.g. `agents/foo.md` → `### agents/foo.md`
- Apply the same `globs` and `description` rules as for rules (above)
- Include the body content of the file

---

## User Prefs

After writing all agent-config-owned sections to `AGENTS.md`:
- Append a `## USER PREFS` heading at the bottom
- Write back all user pref blocks collected during the backup step, verbatim and in their
  original order
- Any content already under `## USER PREFS` in the previous file must remain semantically
  identical — do not rewrite or summarise it

---

## Cleanup

- For each `AGENTS.md` in the repo, check that every agent-config-owned heading has a
  corresponding file in `./.agent_config`
  - If it doesn't, that heading and its content will naturally be absent from the rewritten file
    (the backup + rewrite process handles this implicitly — no separate deletion step needed)
  - `## USER PREFS` and its child headings are exempt

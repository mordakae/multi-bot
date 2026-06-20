# Gemini Synchronisation Instructions

The default target for repo-wide rules is `./.gemini/GEMINI.md`.

- Copy `./.agent_config/agent_config_version` to `./.gemini/agent_config_version`
- Integrate MCP servers from `./.agent_config/mcp.json` into `./.gemini/settings.json`:
  - Read the existing `./.gemini/settings.json` (or start with `{}` if it doesn't exist)
  - Merge the `mcpServers` block from `mcp.json` into `settings.json` under the `mcpServers` key
  - Preserve all other keys in `settings.json` — do not overwrite them

- **Back up user prefs before making any changes:**
  - Read the existing `./.gemini/GEMINI.md` (if it exists)
  - Use sync markers to delimit agent-config-owned content — anything **outside** these markers
    is a user pref:
    ```
    <!-- agent-config:start -->
    ...agent-config content...
    <!-- agent-config:end -->
    ```
  - Collect all content outside those markers verbatim — it will be preserved after the
    agent-config block
  - If no `./.gemini/GEMINI.md` exists yet, there are no prefs to collect — proceed directly

- Use `./.gemini/sync-history.yml` to track synchronised skills:
  ```yaml
  skills:
    - <synced-skill>/
  ```

---

## Rules

Gemini scopes instructions by **directory** — there is no `applyTo` glob mechanism. All
`GEMINI.md` files in a directory tree are discovered and concatenated automatically.

**Scoped rules** (frontmatter `globs` contains folder path(s)):
- Write each scoped rule to `./specific/folder/GEMINI.md`
- If the target `GEMINI.md` already exists, use the same agent-config sync markers to delimit
  the managed block, preserving any content outside those markers
- If `globs` also contains file types, add a comment at the top of the rule block:
  `<!-- Applies to: <glob pattern> -->`
- If frontmatter `description` is present, include it as a HTML comment immediately after:
  `<!-- <description> -->`
- Include the body content of the rule file below

**Unscoped rules** (no `globs`, or `globs` is empty):
- Write inside the agent-config sync markers in `./.gemini/GEMINI.md`

---

## Skills

Skills are directories. For each directory in `./.agent_config/skills`:
- The directory must contain a `SKILL.md` file
- The target directory is `./.gemini/skills/<skill-name>/`
- Copy `SKILL.md` to `./.gemini/skills/<skill-name>/SKILL.md`
  - Adjust yaml frontmatter to use only fields supported by Gemini (`name`, `description`):
    - Remove `model`, `allowed-tools`, and any other unsupported fields
    - The `description` field is critical: Gemini uses it to decide when to activate the skill —
      preserve it exactly
    - For any other unsupported fields, ask the user whether to provide alternatives or let you
      decide
- If a `references/` subdirectory exists, copy all files to
  `./.gemini/skills/<skill-name>/references/` as-is
- Ensure the skill is listed in `./.gemini/sync-history.yml`

**Cleanup:**
- For each skill listed in history, if there is no corresponding directory in
  `./.agent_config/skills`, delete `./.gemini/skills/<skill-name>/` and remove the entry

---

## Agents

Gemini CLI does not support named agents or personas as a first-class feature. Instead:
- Fold all agent definitions into the agent-config block in `./.gemini/GEMINI.md` under a
  `## Personas` heading
- For each file in `./.agent_config/agents`, include its body content under a sub-heading:
  `### <agent-name>`
- Note to the user that these personas are available as instructions but cannot be invoked by
  name — the user must explicitly reference them in their prompts

---

## User Prefs

After writing all agent-config content inside the sync markers in `./.gemini/GEMINI.md`:
- Write all user pref content collected during the backup step **after** the closing
  `<!-- agent-config:end -->` marker, verbatim and in original order
- Any existing user pref content must remain semantically identical — do not rewrite or
  summarise it

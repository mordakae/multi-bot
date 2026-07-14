# Copilot Synchronisation Instructions

> **User-global note:** Copilot has no user-global `~/.github/` file. User-level custom
> instructions and MCP are configured in the editor/CLI (e.g. VS Code's
> `github.copilot.chat.codeGeneration.instructions` setting, or Copilot CLI user config). Treat
> the paths below as the portable layout, and use the "Confirm current platform conventions" step
> to place each surface where Copilot currently reads user-global config.

The default target for user-global rules is `~/.github/copilot-instructions.md`.

- Copy `~/.agent_config/agent_config_version` to `~/.github/agent_config_version`
- Integrate MCP servers from `~/.agent_config/mcp.json` into Copilot's user-level MCP config:
  - Read the existing user MCP config (or start with `{}` if it doesn't exist)
  - Merge the `mcpServers` block from `mcp.json` under the `mcpServers` key
  - Preserve all other keys — do not overwrite them

- Use `~/.github/sync-history.yml` to track synchronised files:
  ```yaml
  instructions:
    - <synced-rule>.instructions.md
  agents:
    - <synced-agent>.agent.md
  skills:
    - <synced-skill>/
  ```

---

## Confirm current platform conventions

Your training data may predate Copilot's current config format. Before applying anything below,
confirm Copilot's **current** conventions for the surfaces this sync touches — where user-global
instructions, skills, agents, and MCP config are read from, and which frontmatter fields (e.g.
skill and agent fields) each supports — from the platform's current documentation, using any
config already present on the system as a clue.

This governs only *how* config is placed and formatted for Copilot. It is **not** license to alter
the source content in `~/.agent_config/` (translate format, never meaning), to add, drop, or
restructure config beyond what these instructions describe, or to change Multi-Bot's sync model.
Where a section below already resolves a specific format detail, that detail stands unless current
documentation shows it has changed. If research is unavailable, fall back to the format details
written in this file.

---

## Rules

User-level config is **global** — it applies in every repo, so per-folder scoping (`applyTo`) does
not apply. For each file in `~/.agent_config/rules`:
- Append to `~/.github/copilot-instructions.md` (the user-wide instructions file)
- Do not use headings — Copilot treats the whole file as plain prose
- Separate multiple rule blocks with a blank line
- Wrap all synced rule content in HTML comment markers so it can be identified on later syncs:
  ```
  <!-- agent-config:start -->
  ...synced rule blocks...
  <!-- agent-config:end -->
  ```
- Everything outside the markers (including the bootstrap instructions at the top of the file)
  is user/bootstrap content — preserve it verbatim; on re-sync, replace only the content
  between the markers

---

## Skills

Skills are directories. For each directory in `~/.agent_config/skills`:
- The directory must contain a `SKILL.md` file
- The target directory is `~/.github/skills/<skill-name>/`
- Copy `SKILL.md` to `~/.github/skills/<skill-name>/SKILL.md`
  - Adjust yaml frontmatter to use only fields supported by Copilot (`name`, `description`,
    `license`):
    - `model` and `allowed-tools` are not supported in Copilot skills — remove both
    - For any other unsupported fields, ask the user whether to provide alternatives or let you
      decide
- If a `references/` subdirectory exists, copy all files to
  `~/.github/skills/<skill-name>/references/` as-is (Copilot does not process these but they
  are available to the skill at runtime)
- Ensure the skill is listed in `~/.github/sync-history.yml` under `skills`

**Cleanup:**
- For each skill listed in history, if there is no corresponding directory in
  `~/.agent_config/skills`, delete the `~/.github/skills/<skill-name>/` directory and remove
  the entry from the history file

---

## Agents

For each file in `~/.agent_config/agents`:
- The target file is `~/.github/agents/<agent-name>.agent.md`
- Adjust yaml frontmatter to use Copilot-supported fields:

  | Agent config field | Copilot field      | Notes                                              |
  |--------------------|--------------------|----------------------------------------------------|
  | `name`             | `name`             | Direct copy                                        |
  | `description`      | `description`      | Required by Copilot                                |
  | `model`            | `model`            | Use a Copilot display-name (e.g. `Claude Sonnet 4.6`, `GPT-5 mini`, `Gemini 2.5 Pro`) — pick the closest equivalent |
  | `tools`            | `tools`            | List of tool names, or omit to allow all tools    |
  | (unsupported)      | —                  | Ask the user or research Copilot equivalents       |

- Include the body content of the agent file unchanged
- Ensure the agent is listed in `~/.github/sync-history.yml` under `agents`

**Cleanup:**
- For each agent listed in history, if there is no corresponding file in
  `~/.agent_config/agents`, delete the `~/.github/agents/<agent>.agent.md` file and remove
  the entry from the history file

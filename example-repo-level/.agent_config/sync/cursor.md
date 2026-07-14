# Cursor Synchronisation Instructions

- Copy `./.agent_config/agent_config_version` to `./.cursor/agent_config_version`
- Copy `./.agent_config/mcp.json` to `./.cursor/mcp.json`

- Use `./.cursor/sync-history.yml` to track synchronised files:
  ```yaml
  rules:
    - <synced-rule>.mdc
  agents:
    - <synced-agent>.md
  skills:
    - <synced-skill>/
  ```

---

## Confirm current platform conventions

Your training data may predate Cursor's current config format. Before applying anything below,
confirm Cursor's **current** conventions for the surfaces this sync touches — where rules, skills,
agents, and MCP config are read from, and which frontmatter fields (e.g. `.mdc` rule fields) each
supports — from the platform's current documentation, using any config already present in the repo
as a clue.

This governs only *how* config is placed and formatted for Cursor. It is **not** license to alter
the source content in `./.agent_config/` (translate format, never meaning), to add, drop, or
restructure config beyond what these instructions describe, or to change Multi-Bot's sync model.
Where a section below already resolves a specific format detail, that detail stands unless current
documentation shows it has changed. If research is unavailable, fall back to the format details
written in this file.

---

## Rules

For each file in `./.agent_config/rules`:
- Copy it to `./.cursor/rules/<rule-name>.mdc`
- Ensure the rule file is listed in `./.cursor/sync-history.yml` under `rules`

**Cleanup:**
- For each `.mdc` file listed in history, if there is no corresponding file in
  `./.agent_config/rules`, delete the `.mdc` file and remove the entry

---

## Skills

Skills are directories. For each directory in `./.agent_config/skills`:
- Copy the directory to `./.cursor/skills/`
- Ensure the skill is listed in `./.cursor/sync-history.yml` under `skills`

**Cleanup:**
- For each skill listed in history, if there is no corresponding directory in
  `./.agent_config/skills`, delete `./.cursor/skills/<skill-name>/` and remove the entry

---

## Agents

For each file in `./.agent_config/agents`:
- Copy the file to `./.cursor/agents/<agent-name>.md`
- Ensure the agent is listed in `./.cursor/sync-history.yml` under `agents`

**Cleanup:**
- For each agent listed in history, if there is no corresponding file in
  `./.agent_config/agents`, delete `./.cursor/agents/<agent>.md` and remove the entry

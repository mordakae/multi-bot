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

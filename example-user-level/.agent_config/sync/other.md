# Other Synchronisation Instructions

Tell the user:
> This Agentic Development Platform is not currently supported so results may vary. User defined
> config may be erased or altered. In order to proceed, I will need to research how this platform
> receives config and attempt to integrate it myself.

Ask the user:
> Would you like to proceed anyway?

---

## If the user declines

- Do not modify any files for this platform — including the local version file
  (`~/.<platform-name>/agent_config_version`), so the next session will prompt again
- Tell the user which platform was skipped and that they can trigger the sync manually when ready
- Continue with the original request

---

## If the user agrees

### 1. Back up existing config

Before touching anything:
- Identify the platform's existing user-global config file(s) by looking for common conventions
  (e.g. `~/.platform/config`, `~/PLATFORM.md`, `~/.config/<platform>/...`)
- Read those files and hold their contents in memory — treat anything that doesn't match
  agent-config heading patterns as user prefs to be preserved verbatim

### 2. Research the platform

- Search for how the platform receives agent/persona/skill configuration:
  - Check for any config files already present on the system for clues
  - Search the web for `<platform name> agent configuration`, `<platform name> custom instructions`,
    and `<platform name> MCP setup`
- Determine:
  - Where user-global rules/skills/agents should be written (file path and format)
  - Whether MCP servers can be configured and where (file path and format)

### 3. Integrate

- Apply the same content model used in the other sync files (rules, skills, agents/personas)
  adapted to whatever structure the platform expects
- User-level config is **global** — write all rules to the platform's user-global target;
  per-folder scoping does not apply
- Preserve all backed-up user prefs — write them back after agent-config-owned content,
  clearly separated (e.g. a `USER PREFS` section or equivalent)
- If MCP integration is supported, integrate `~/.agent_config/mcp.json` into the platform's
  user-level config format
- Copy `~/.agent_config/agent_config_version` to the platform's user-global config directory as
  `agent_config_version` (for version tracking)

### 4. Tell the user what you did

Summarise:
- Which files were written and what format was used
- Whether MCP integration was possible and what was done
- Any assumptions made during research that the user should verify
- Anything that could not be integrated and why

### 5. Document for future runs

- Write a new sync file at `~/.agent_config/sync/<platform-name>.md` describing the integration
  steps you followed, so future syncs don't need to re-research from scratch
- Ask the user if they'd like to review or adjust the new sync file before it is saved

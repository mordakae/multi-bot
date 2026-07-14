---
name: agent-config-user
description: Add or change your personal user-global agent config (a skill, rule, agent, or MCP server) in a user-level Multi-Bot setup. Use whenever the user asks to create or edit one.
---

# Adding or editing agent config (user level)

Your machine uses user-level Multi-Bot: `~/.agent_config/` is the source of truth, and each tool's
user-global config (`~/.claude/`, `~/.gemini/`, …) is generated from it. All of it is **personal to
you** — there is no team-sharing step at this level.

When the user asks to add or change a **skill, rule, agent, or MCP server**:

1. Author it in `~/.agent_config/`:
   - Skill → `~/.agent_config/skills/<name>/SKILL.md` (+ optional `references/`)
   - Rule → `~/.agent_config/rules/<name>.md`
   - Agent → `~/.agent_config/agents/<name>.md`
   - MCP server → `~/.agent_config/mcp.json`
2. Bump `~/.agent_config/agent_config_version`.
3. Apply your platform's sync instructions (`~/.agent_config/sync/<platform>.md`) to write the
   change into your user-global config and copy the bumped version to the local
   `agent_config_version`. Other platforms pick it up via the version bump on their next start.

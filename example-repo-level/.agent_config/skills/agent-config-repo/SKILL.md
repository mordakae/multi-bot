---
name: agent-config-repo
description: Add or change agent config (a skill, rule, agent, or MCP server) in this Multi-Bot project. Use whenever the user asks to create or edit one — it asks whether to share with the team or keep it personal, and routes the files to the right place.
---

# Adding or editing agent config (repo level)

This project uses Multi-Bot: `.agent_config/` is the **shared source of truth**, and each tool's
native config (`.claude/`, `.cursor/`, …) is generated from it and gitignored.

When the user asks to add or change a **skill, rule, agent, or MCP server**, first ask:

> Should this be **shared with the team**, or kept **personal** to your machine?

## Shared — committed to the repo, synced to every platform

1. Author it in `.agent_config/`:
   - Skill → `.agent_config/skills/<name>/SKILL.md` (+ optional `references/`)
   - Rule → `.agent_config/rules/<name>.md`
   - Agent → `.agent_config/agents/<name>.md`
   - MCP server → `.agent_config/mcp.json`
2. Bump `.agent_config/agent_config_version`.
3. Apply your platform's sync instructions (`.agent_config/sync/<platform>.md`) to write the change
   into your native config and copy the bumped version to the local `agent_config_version`.
   Teammates and other platforms pick it up via the version bump on their next open.

## Personal — local to your machine, never committed

Multi-Bot already gitignores the native config dirs, so anything you place there stays local. Do
**not** touch `.agent_config/` or bump the version for personal items.

- **Skill or agent** → create it directly in your platform's native dir (e.g.
  `.claude/skills/<name>/`, `.claude/agents/<name>.md`). Do **not** list it in `sync-history.yml`,
  so the sync's cleanup step leaves it alone.
- **Rule** → do **not** add it to a Multi-Bot-managed file (e.g. the synced `.claude/CLAUDE.md`)
  under a `rules/…` heading — the next sync rewrites that file and would delete it. Use your
  platform's own user/local rules mechanism instead (confirm its current location if unsure).
- **MCP server** → add it to your platform's user/local MCP config, not `.agent_config/mcp.json`.

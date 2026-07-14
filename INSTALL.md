# Installation Instructions

Multi-Bot ships two reference implementations. Pick the one you want (or install both):

- **Repo-level** ([`example-repo-level/`](example-repo-level/)) — config that travels with a single project.
- **User-level** ([`example-user-level/`](example-user-level/)) — your personal config, applied in every project.

Both share the same steps: place the bootstrap hooks and `.agent_config/`, clear the placeholders, and set the version. They differ only in *where* things go (a repo root vs your home directory).

## Repo-level install

- Copy the bootstrapping files from `example-repo-level/` into your repo, preserving paths relative to the repo root:
  - `./AGENTS.md`
  - `./CLAUDE.md`
  - `./GEMINI.md`
  - `./.github/copilot-instructions.md`
  - `./.github/instructions/bootstrap.instructions.md`
  - `./.cursor/rules/bootstrap.mdc`
- Copy the `example-repo-level/.agent_config` folder to your repo root as `.agent_config`.
- Copy `example-repo-level/.gitignore` into your repo (or merge its rules into an existing one). It ignores the generated outputs and force-adds the bootstrap hooks — see the README's "Git and the .gitignore" section.

## User-level install

- Copy `example-user-level/.agent_config` to your home directory as `~/.agent_config`.
- Place each platform's bootstrap hook in its **user-global** config location:
  - Claude Code → `~/.claude/CLAUDE.md`
  - Gemini → `~/.gemini/GEMINI.md`
  - Codex → `~/.codex/AGENTS.md`
  - Cursor → its User Rules (app settings) — see the placement note in `example-user-level/.cursor/rules/bootstrap.mdc`
  - Copilot → its user custom-instructions setting — see the note in `example-user-level/.github/copilot-instructions.md`
- There is no `.gitignore` — a home directory is not a git repo.

## Both levels — after placing the files

- Delete the placeholder files/folders:
  - `.agent_config/agents/example-agent.md`
  - `.agent_config/rules/example-rule.md`
  - `.agent_config/skills/example-skill/`
  - **Keep** `.agent_config/skills/agent-config-*/` — it ships with Multi-Bot (it teaches agents where to write new config) and is not a placeholder.
- Ensure `.agent_config/agent_config_version` is set to `1`.
- Configure `.agent_config/mcp.json` with your desired MCP servers.
- Migrate your existing rules, agents, and skills into the appropriate `.agent_config/` folders.

> On its first run for each platform, the sync confirms that platform's *current* config conventions before writing anything — see "Platform conventions and format drift" in the README. This is what keeps a user-level install robust even where a platform's user-global location isn't a standard file (Cursor, Copilot).

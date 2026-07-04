# Installation Instructions

- Copy the bootstraping files to your repo, preserving all paths as relative to repo root
  - `./AGENTS.md`
  - `./CLAUDE.md`
  - `./GEMINI.md`
  - `./.github/copilot-instructions.md`
  - `./.github/instructions/bootstrap.instructions.md`
  - `.cursor/rules/bootstrap.mdc`
- Copy the `.agent_config` folder to your repo root
- Delete the placeholder files/folders
  - `.agent_config\agents\example-agent.md`
  - `.agent_config\rules\example-rule.md`
  - `.agent_config\skills\example-skill\`
- Ensure `.agent_config\agent_config_version` is set to `1`
- Configure `.agent_config\mcp.json` with your desired MCP servers
- Migrate existing repo rules, agents and skills into the appropriate folders
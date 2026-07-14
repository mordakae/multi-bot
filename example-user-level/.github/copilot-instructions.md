# Github Copilot Bootstrap Instructions (User Level)

> **Placement note:** Copilot has no user-global `~/.github/` file. User-level custom instructions
> are set in the editor/CLI (e.g. VS Code's `github.copilot.chat.codeGeneration.instructions`
> setting, or Copilot CLI user config). This file is the portable *source* of that content; on
> first sync, confirm Copilot's *current* mechanism for user-global instructions (see the
> "Confirm current platform conventions" step in `~/.agent_config/sync/copilot.md`) and place the
> bootstrap where that mechanism expects it.

## Mandatory First Action

Before doing anything else, compare the contents of these two files:
- `~/.agent_config/agent_config_version` (if missing, assume no change)
- `~/.github/agent_config_version` (if missing, assume a change)

If the contents differ, **pause** the user's request and tell them:
> "The agent config has been updated. Would you like to apply the update before we continue?"

- **Yes:** 
  - Read `~/.agent_config/sync/copilot.md` and follow the synchronisation instructions. Then resume the original request.
- **No:** Proceed with the original request without applying the update.

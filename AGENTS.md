# Codex/Generic Bootstrap Instructions

## Mandatory First Action

Before doing anything else, compare the contents of these two files:
- `./.agent_config/agent_config_version` (if missing, assume no change)
- `./.codex/agent_config_version` (if missing, assume a change)

If the contents differ, **pause** the user's request and tell them:
> "The agent config has been updated. Would you like to apply the update before we continue?"

- **Yes:** 
  - Identify which agent you are (eg `codex` or `other`)
  - Read `.agent_config/sync/<agent-name>.md` and follow the synchronisation instructions. Then resume the original request.
- **No:** Proceed with the original request without applying the update.

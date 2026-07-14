# Multi-Bot

A single source of truth for AI agent configuration — define your agents, skills, rules, and MCP servers once, and let each AI tool sync its own configuration on demand.

## What it does

Different AI coding tools (Claude Code, Cursor, Gemini, GitHub Copilot, Codex) each have their own configuration formats and locations. Multi-Bot solves the maintenance problem: you define everything in `.agent_config/` and each tool reads from there whenever its config is out of date.

Syncing is agent-driven. When an AI tool opens the repo, it checks whether the config version has changed and offers to apply the update. The sync instructions for each platform live in `.agent_config/sync/` and are written in plain English — the agent reads them and follows them.

Multi-Bot ships **two reference implementations**, and you can use either or both:

- **Repo-level** ([`example-repo-level/`](example-repo-level/)) — `.agent_config/` at a repo's root, bootstrap hooks committed to the repo, sync writes into that repo's `.claude/`, `.cursor/`, etc. Config that travels *with a project*.
- **User-level** ([`example-user-level/`](example-user-level/)) — `.agent_config/` in your home directory, bootstrap hooks and sync targets in each tool's *user-global* config (`~/.claude/`, `~/.gemini/`, …). Your personal "agent brain," applied everywhere.

The two are parallel: identical mechanism, differing only in paths (repo-relative vs `~/`), the gitignore step (repo-level only), and rule scoping (per-folder scoping is repo-only; user-level config is global).

## Supported platforms

| Platform | Config target |
|---|---|
| Claude Code | `.claude/` |
| Cursor | `.cursor/` |
| Google Gemini | `.gemini/` |
| GitHub Copilot | `.github/` |
| Codex / generic | `AGENTS.md` |

Adding a new platform means writing a `sync/<platform>.md` file and a bootstrap instruction block — see `.agent_config/sync/other.md` for the template.

## Repository layout

This repo is a template plus two reference implementations. There is no runnable code — the `example-*` trees *are* the product.

```
example-repo-level/            # Config that travels with a project
  .agent_config/
    agents/        # Agent persona definitions (.md)
    skills/        # Reusable skills (one directory per skill, with SKILL.md)
    rules/         # Platform-agnostic rules (.md, with optional frontmatter)
    sync/          # Per-platform sync instructions
    mcp.json       # MCP server definitions (copied to each platform)
    agent_config_version   # Increment this to trigger a sync on next open
  CLAUDE.md        # Bootstrap hook for Claude Code (repo root)
  AGENTS.md        # Bootstrap hook for Codex / generic agents
  GEMINI.md        # Bootstrap hook for Google Gemini
  .github/copilot-instructions.md             # Bootstrap hook for GitHub Copilot
  .github/instructions/bootstrap.instructions.md  # Duplicate hook — both required for reliable adherence
  .cursor/rules/bootstrap.mdc       # Bootstrap hook for Cursor
  .gitignore       # Ignores generated outputs; bootstrap hooks are force-added

example-user-level/            # Your personal "agent brain", applied everywhere
  .agent_config/   # Same sources, in ~/.agent_config/
  .claude/CLAUDE.md            # Bootstrap hook → ~/.claude/ (user-global)
  .gemini/GEMINI.md            # → ~/.gemini/
  .codex/AGENTS.md             # → ~/.codex/
  .cursor/rules/bootstrap.mdc  # → Cursor User Rules (see note in file)
  .github/…                    # → Copilot user settings (see note in file)
                   # (no .gitignore — a home dir is not a git repo)

CLAUDE.md          # Project doc for developers working ON Multi-Bot
README.md
INSTALL.md
```

## How to use this template

Pick the level you want ([`example-repo-level/`](example-repo-level/) or [`example-user-level/`](example-user-level/)), then:

1. **Copy** that level's files into place (a repo root, or your home directory) — see [INSTALL.md](INSTALL.md) for the per-level steps.
2. **Replace the examples** in `.agent_config/agents/`, `skills/`, and `rules/` with your own definitions. (Keep the `agent-config-*` skill — it ships with Multi-Bot and teaches agents where to write new config; only the `example-*` items are placeholders to delete.)
3. **Configure MCP servers** in `.agent_config/mcp.json` if you use any.
4. **Open any supported AI tool.** It will detect that the config version differs from its local copy and offer to sync. Accept, and it handles the rest.
5. **When you update the config**, increment the number in `agent_config_version`. The next time any tool starts, it will prompt to re-sync.

## Authoring config

Config is authored using Cursor's conventions as the default — both frontmatter fields and folder structure (particularly for skills), since Cursor supports the most verbose format. The sync step for each platform strips, translates, or restructures anything it doesn't support.

- [Agents](.agent_config/agents/README.md)
- [Skills](.agent_config/skills/README.md)
- [Rules](.agent_config/rules/README.md)

### MCP servers

Edit `.agent_config/mcp.json` using the standard MCP `mcpServers` schema. The sync step copies this file to wherever each platform expects it.

## Platform conventions and format drift

The sync step translates `.agent_config/` into each platform's *native* config format, and those
formats change over time — a skill's frontmatter fields, where MCP servers are configured, how
rules are scoped. The agent running a sync may have been trained before the current format landed.

So each `sync/*.md` instructs the agent to **confirm the target platform's current config
conventions before applying** — using the platform's current documentation, plus any config already
in the repo as a clue. This guardrail governs only *how* config is placed and formatted for that
platform; it never changes *what* is synced. The source of truth stays `.agent_config/` — the sync
translates format, never meaning — and when research isn't available, the agent falls back to the
format details written in the sync file.

The unsupported-platform path (`sync/other.md`) already researches the platform from scratch; this
is the same principle applied to the supported platforms, whose sync files can otherwise go stale.

## Git and the .gitignore

This applies to the **repo-level** install only — a user-level install lives in your home directory, which is not a git repo, so it ships no `.gitignore`.

The [`example-repo-level/.gitignore`](example-repo-level/.gitignore) is a core part of the template and must not be modified casually. It enforces which files are managed by Multi-Bot and which belong to individual developers.

**Managed files are ignored** — anything written by the sync step (`.claude/`, `.cursor/`, `.gemini/`, `.mcp.json`, per-repo `CLAUDE.md`/`AGENTS.md`/`GEMINI.md`, and the Copilot-generated files under `.github/`) is gitignored by default. These are outputs, not sources; each developer's tool regenerates them locally from `.agent_config/`.

**Bootstrap files must be force-added** — the bootstrap hooks (`CLAUDE.md`, `AGENTS.md`, `GEMINI.md`, `.cursor/rules/bootstrap.mdc`, `.github/copilot-instructions.md`, `.github/instructions/bootstrap.instructions.md`) are also excluded by the broad ignore patterns, so they must be intentionally committed with `git add -f`. This is by design: it prevents managed output files from being accidentally committed while still allowing the bootstrap hooks — which are hand-authored and stable — to live in the repo.

**What belongs in source control:**

- `.agent_config/` — all of it (agents, skills, rules, sync instructions, `mcp.json`, version file)
- All bootstrap hooks (force-added, as above)
- `.gitignore` itself

**What should never be committed:**

- Any file written by the sync step — these are per-developer and regenerated automatically

## Version tracking

Each platform writes a local `agent_config_version` file after syncing. On every repo open, the bootstrap instruction compares that file against `.agent_config/agent_config_version`. A mismatch triggers the sync prompt.

Increment `.agent_config/agent_config_version` any time you want all platforms to re-sync on their next open.

## Persistent Context Cost
> **What every session pays, whether or not a sync is due**

Multi-Bot ships no always-loaded skills or rules of its own — the only thing it puts in front of the agent on every session is a single bootstrap hook that reads two version files and, if they match, does nothing else. That hook is the entire standing footprint, and each developer pays only for the platform they actually use. The figures below are for the **repo-level** hooks. The user-level hooks are the same, except the Cursor and Copilot ones carry a few extra lines noting their non-standard user-global location (see those files), so run a little heavier:

| Platform | Bootstrap hook | Tokens |
| --- | --- | ---: |
| Claude Code | `CLAUDE.md` | 143 |
| Cursor | `.cursor/rules/bootstrap.mdc` | 156 |
| Google Gemini | `GEMINI.md` | 142 |
| GitHub Copilot | `.github/copilot-instructions.md` | 142 † |
| Codex / generic | `AGENTS.md` | 207 ‡ |

Everything heavier — the `.agent_config/` sources and the per-platform `sync/*.md` instructions — loads on demand, only when a version mismatch triggers a sync, so it never touches the persistent cost.

**Your own config adds to this.** Every skill and agent you author contributes its `name` + `description` to the always-loaded list on each session (the trimmed example skill and agent cost ~5 tokens of description each). That per-item tax is why descriptions should stay tight — keep them to a terse "what it does", and let the body carry the detail, which loads only when the item is actually invoked.

**One exception — Codex.** Codex has no on-demand skill mechanism, so its sync folds each skill's *full body* into the always-loaded `AGENTS.md`. On Codex a skill therefore costs its whole body, not just its description. This is why the shipped `agent-config-*` authoring skill is kept deliberately short — on the four platforms with real skill loading it is description-only cost, and on Codex it is one small always-loaded block.

<sub>† `copilot-instructions.md` is reliably always-loaded; the README also requires the duplicate `.github/instructions/bootstrap.instructions.md` (another 142) for reliable adherence — count ~284 if both load.</sub>
<br><sub>‡ `AGENTS.md` runs heavier than the others because its body carries extra guidance for non-Codex generic agents.</sub>
<br><sub>Measured locally with [tiktoken](https://github.com/openai/tiktoken) (`cl100k_base`); treat the figures as accurate to ±~10%.</sub>

## Using with agent-updater

[agent-updater](https://github.com/mordakae/agent-updater) solves a complementary problem: keeping agent config packages *current* (pulled from their git remotes on a schedule), while Multi-Bot keeps them *consistent across platforms*. When both are installed in the same repo, compose them like this:

- **agent-updater packages target `.agent_config/`, not platform-native dirs.** In a Multi-Bot repo, `.claude/`, `.cursor/`, etc. are generated, gitignored outputs — anything a package wrote there would be clobbered or orphaned on the next sync. Instead, packages install their rules into `.agent_config/rules/`, skills into `.agent_config/skills/`, and agents into `.agent_config/agents/`.
- **After applying package changes, increment `.agent_config/agent_config_version`.** That is all it takes for every platform to pick the changes up through Multi-Bot's own sync on its next open.
- **agent-updater's own always-loaded check is authored as an unscoped Multi-Bot rule** (e.g. `.agent_config/rules/agent-updater-check.md`), and its skills as Multi-Bot skills. Multi-Bot then fans the updater out to every platform automatically — no per-platform hand-translation needed.
- **Ordering:** when a session starts, run the agent-updater due-check *first* (it may change `.agent_config/` content and bump the version), then the Multi-Bot version check — which then catches any bump in the same session.

## Contributing

Contributions are welcome — especially new platform sync instructions. To add support for a platform:

1. Write `.agent_config/sync/<platform>.md` following the pattern in `other.md`. Include a "Confirm current platform conventions" step so the sync verifies the platform's current config format before applying.
2. Add a bootstrap instruction block (a short Markdown file the platform reads on startup that triggers the version check).
3. **Mirror it across both levels** — `example-repo-level/` (repo-relative paths) and `example-user-level/` (user-global `~/` paths, with scoped rules collapsed to the global target). The two must stay in sync.
4. Open a PR with the files and a note on how the bootstrap is wired up for that tool.

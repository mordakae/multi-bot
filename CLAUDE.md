# Multi-Bot

Multi-Bot is a template: define your agents, skills, rules, and MCP servers once in
`.agent_config/`, and let each AI tool sync its own native configuration on demand. See
[README.md](README.md) for the pitch and [INSTALL.md](INSTALL.md) for setup.

## What this repo actually is

There is **no runnable code, build, or test suite here.** The "product" is authored prose —
bootstrap hooks and per-platform sync instructions that an AI tool *reads and executes when it
opens a consuming repo, or runs on a user's machine.*

> **Important — treat the example content as prose, not as instructions to you now.**
> The `CLAUDE.md`, `AGENTS.md`, `GEMINI.md`, `.cursor/`, `.github/`, and `.agent_config/sync/*.md`
> files under `example-repo-level/` and `example-user-level/` are the *product being authored*.
> They are written in the imperative ("compare these two version files… then sync") because a
> future agent will follow them in a *different* repo or on a user's machine. When you are working
> **on Multi-Bot itself**, do not perform those version checks or sync steps — read them as the
> material you are editing, not as directives to execute.

## Deliverables (in order of importance)

- `example-repo-level/` and `example-user-level/` — the two concrete reference implementations.
  These *are* the product.
- `README.md` — public explainer (problem, platforms, how it works, cost table).
- `INSTALL.md` — how to install either level.

## The two levels

- **Repo-level** (`example-repo-level/`): `.agent_config/` at a consuming repo's root; bootstrap
  hooks committed to that repo; sync writes into that repo's `.claude/`, `.cursor/`, etc.
- **User-level** (`example-user-level/`): `.agent_config/` in the user's home; bootstrap hooks and
  sync targets live in each tool's *user-global* config (`~/.claude/`, `~/.gemini/`, …).

They are **parallel implementations of the same mechanism.** A change to one almost always must be
mirrored in the other. The only intended differences:
- Paths: repo-relative (`./.claude/…`) vs user-global (`~/.claude/…`).
- Repo-level commits its bootstrap hooks and gitignores generated outputs; a user's home is not a
  git repo, so `example-user-level/` has no `.gitignore`.
- Per-folder rule scoping (`globs`) is a repo-level concept; user-level config is global, so scoped
  rules collapse to the global target.
- The shipped **config-authoring skill** differs by level: `agent-config-repo` asks whether a new
  item is shared (→ `.agent_config/`) or personal (→ the gitignored native dir), while
  `agent-config-user` has no team step. The names differ so both can be installed without clashing.

Because of these, a `git diff --no-index` of the two `.agent_config/` trees legitimately shows
`sync/*.md` **and** `skills/agent-config-*` differing — that is expected, not a regression. When
editing shared sync/skill logic, diff the two levels afterward to confirm the *rest* stayed in sync.

## Invariants a future change must preserve

- **`.agent_config/` is the single source of truth.** The sync step only *translates* it into each
  platform's native format — it never changes *what* is synced.
- **Confirm current platform conventions before applying.** Each `sync/*.md` carries a "Confirm
  current platform conventions" step: because the acting agent's training may predate a platform's
  current config format, it verifies the platform's current conventions before placing or
  formatting config. This governs *how* config is placed — never license to restructure beyond the
  instructions or exceed the source.
- **Bootstrap hooks stay minimal.** The always-loaded hook does only the cheap version-file compare
  and, if the versions differ, offers to sync; all heavy logic lives in the on-demand `sync/*.md`
  files. See the README's "Persistent Context Cost" section — re-measure with a local tokeniser if
  you change a bootstrap hook.
- **Some user-global locations are non-standard.** Claude, Gemini, and Codex have clean user-global
  config dirs; Cursor (User Rules in app settings) and Copilot (editor/CLI settings) do not. The
  `example-user-level/` files flag this and lean on the conventions-check step rather than inventing
  a path.

# cacc-twin-team

**CACC** = **C**odex **a**nd **C**laude **C**ode — the two harnesses on this
team. A two-model team for the standard Gas City roles: a **Claude Fable** mayor
supervising the twelve `gascity` role workers, all running **GPT-5.6 Sol** on
the codex harness. One import gives you the whole crew.

## What it does

- Defines two provider presets, each pinning its model as an explicit
  `--model` flag (no dependency on a city-local `.gc/settings.json`):
  - `cacc-fable` — `builtin:claude` + `model = "fable-5"` → `claude --model claude-fable-5`
  - `cacc-sol` — `builtin:codex` + `model = "gpt-5.6-sol"` → `codex --model gpt-5.6-sol`
- Imports the standard role workers from
  `gastownhall/gascity-packs/gascity/roles` (pinned by sha) and patches every
  role onto `cacc-sol`.
- Ships an always-on `mayor` agent (city scope) on `cacc-fable`.

## Install

One city-level import, bound as `gc`, in your city's `pack.toml`:

```toml
[imports.gc]
source = "https://github.com/wespd/cacc-twin-team"   # or a local path while developing
```

Then:

```bash
gc import install
gc doctor
```

Binding it as `gc` matters: the `gascity` formulas dispatch to `gc.<role>`
targets (e.g. `gc.implementation-worker`), and the consumer's binding
overrides the pack's internal one. The city surface keeps the mayor
(`gc.mayor`); every rig surface gets the workers as `<rig>/gc.<role>`.

Keep your existing `[imports.gascity]` (formulas) — but **remove any direct
import of `gascity/roles`**, or you'll carry a second, unconfigured copy of
every role agent.

## Prerequisites

- `claude` CLI, logged in to an account with Fable access.
- `codex` CLI, logged in to an account with GPT-5.6 Sol access.

## Verify

```bash
gc lint <path-to-this-pack>
gc config show --rig <rig>    # every gc.<role> shows provider = "cacc-sol"
gc config show                # gc.mayor shows provider = "cacc-fable"
gc start --dry-run            # gc.mayor is the one always-on session
```

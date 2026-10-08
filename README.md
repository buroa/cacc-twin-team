# cacc-twin-team

**CACC** = **C**odex **a**nd **C**laude **C**ode — the two harnesses on this
team. A GPT-first team for the standard Gas City roles: the fourteen `gascity`
role workers, thirteen on **GPT-6** models via the codex harness and one on
**Claude Sonnet 5.5** via the claude harness. One import gives you the whole
crew.

## What it does

- Defines four provider presets, each pinning its model and effort as explicit
  CLI flags (no dependency on a city-local `.gc/settings.json`):
  - `cacc-astra`: `builtin:codex` + `gpt-6-astra`, effort `high`
  - `cacc-sol`: `builtin:codex` + `gpt-6.1-sol`, effort `high`
  - `cacc-luna`: `builtin:codex` + `gpt-6-luna`, effort `high`
  - `cacc-sonnet`: `builtin:claude` + `claude-sonnet-5-5`, effort `high`
- Imports the standard role workers from
  `gastownhall/gascity-packs/gascity/roles` (pinned by sha) and patches every
  role onto one of those presets.

## Who runs what

| Preset | Roles |
| --- | --- |
| `cacc-astra` | `design-author` |
| `cacc-sol` | `implementation-worker` (effort `xhigh`), `requirements-planner`, `task-decomposer`, `issue-triager`, `review-synthesizer`, `design-implementation-reviewer`, `design-test-risk-reviewer`, `gap-analyst`, `quality-judge` |
| `cacc-luna` | `run-operator`, `publisher`, `feature-refiner` |
| `cacc-sonnet` | `implementation-reviewer` |

Astra writes the one-shot design plan everything downstream traces to. Sol
takes every role that writes or judges code and artifacts. Luna handles the
deterministic plumbing: gates, scripts, metadata, push and PR. The
implementation reviewer runs on Claude so the merge gate on Sol-written code
comes from a different model family.

Effort is pinned on every preset on purpose: gascity's builtin codex provider
defaults effort to `xhigh` and builtin claude to `max`, which would make the
cheap tiers expensive. The Sonnet preset uses the full `claude-sonnet-5-5` id
because gascity's claude alias table (through 1.5.0) stops at `sonnet-5` and
Claude Code rejects a bare `sonnet-5-5`.

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
overrides the pack's internal one. Every rig surface gets the workers as
`<rig>/gc.<role>`.

Keep your existing `[imports.gascity]` (formulas) — but **remove any direct
import of `gascity/roles`**, or you'll carry a second, unconfigured copy of
every role agent.

## Prerequisites

- `codex` CLI, logged in to an account with GPT-6 Astra, GPT-6.1 Sol, and
  GPT-6 Luna access.
- `claude` CLI, logged in to an account with Claude Sonnet 5.5 access.

## Verify

```bash
gc lint <path-to-this-pack>
gc config show --rig <rig>    # every gc.<role> shows the preset from the table above
```

# Gastown Forge

> **Hephaestus** — CI for AI Agent Output

The quality layer for AI agent orchestrators. Works with Gas Town, Multiclaude, or any system that spawns parallel workers.

## The Problem Everyone Has

> "With all this AI output, how do you ensure quality?"

Agent orchestrators produce **quantity**:
- Gas Town spawns 40 polecats
- Multiclaude spawns workers in parallel
- Output is chaotic, varied, sometimes garbage

**Hephaestus forges that chaos into quality.**

## What Hephaestus Does

```
CHAOS (any orchestrator)  →  FORGE (Hephaestus)  →  QUALITY OUTPUT
         ↑                         ↑                      ↑
    polecats               validation chain           ratcheted
    workers                  FIRE loop               can't go back
    parallel                 quality gates
```

| Component | What It Does |
|-----------|--------------|
| **Pools** | Collect multiple candidates (3 plans, 5 specs) |
| **Gates** | Validate against previous stage's locked output |
| **FIRE Loop** | Find → Ignite → Reap → Escalate |
| **Ratchets** | Lock quality, can't go backward |
| **Flywheel** | Learnings feed next cycle |

## The FIRE Loop

Hephaestus runs the **FIRE** loop — the god of fire working the forge:

| Step | Action | Forge Metaphor |
|------|--------|----------------|
| **F**ind | Survey what's ready | Gather the ore |
| **I**gnite | Dispatch workers (chaos) | Light the forge |
| **R**eap | Validate + ratchet | Quench the metal |
| **E**scalate | Handle failures | Slag to discard |

## Orchestrator Agnostic

Hephaestus works with any chaos source:

| Orchestrator | Spawn | Status | Merge |
|--------------|-------|--------|-------|
| Gas Town | `gt sling` | `gt convoy` | Refinery |
| Multiclaude | `multiclaude work` | `gh pr list` | Merge queue |
| Raw tmux | `tmux new-session` | Poll | Manual |

**Hephaestus doesn't care HOW you spawn chaos. It ensures QUALITY.**

## Like CI, But for Agents

| Code CI | Hephaestus (Agent CI) |
|---------|----------------------|
| GitHub Actions | Hephaestus |
| Runs tests | Validation chain |
| Linting | Complexity checks |
| Security scan | Talos circuits |
| Vibe check | Semantic faithfulness |
| Merge gate | Ratchet |

## Quick Start

```bash
# Coming soon
brew install gastown-forge
```

## Documentation

- [SPEC.md](SPEC.md) — Full specification
- [Gas Town](https://github.com/steveyegge/gastown) — One orchestrator Hephaestus works with
- [mt-olympus.io](https://mt-olympus.io) — The platform

## The Mythology

**Hephaestus** — God of fire, forge, and craftsmanship. The smith who forged weapons for all the Olympian gods.

He doesn't just work one forge. He **oversees quality** across all forges.

That's what this project does for AI agents.

---

**Status:** Early development. Spec complete, implementation starting.

**License:** MIT

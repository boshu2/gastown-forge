# Gastown Forge

> **Brownian Ratchet orchestration for Gas Town**

Forge is an orchestration layer for [Gas Town](https://github.com/steveyegge/gastown). It adds validation, ratchets, and a knowledge flywheel to Gas Town's primitives.

## The Problem

Gas Town produces quantity but struggles with quality:
- Polecats produce chaos without filters
- Refinery becomes a bottleneck
- Bad work propagates through the pipeline
- Learnings don't compound

## The Solution

```
CHAOS (Gas Town)  →  FILTER (Forge)  →  RATCHET (Locked)
```

**Pools** - Multiple parallel attempts per stage
**Gates** - Validation against locked state
**Ratchets** - Lock in progress permanently
**Flywheel** - Learnings feed into next cycle

## Quick Start

```bash
# Coming soon
brew install gastown-forge
```

## Documentation

- [SPEC.md](SPEC.md) - Full specification
- [Gas Town](https://github.com/steveyegge/gastown) - The primitive layer
- [Beads](https://github.com/steveyegge/beads) - Issue tracking

## Status

**Early development.** Not yet functional.

## License

MIT

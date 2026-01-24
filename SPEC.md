# Gastown Forge Specification

**Version:** 0.1.0
**Date:** 2026-01-24
**Status:** Draft

---

> **"Gas Town gives you primitives. Forge makes them dance."**

---

## What Is Gastown Forge?

Gastown Forge is an **orchestration layer** for [Gas Town](https://github.com/steveyegge/gastown).

Gas Town provides primitives: hooks, polecats, convoys, worktrees, propulsion.

Forge adds:
- **Pools** - Multiple parallel attempts per stage
- **Gates** - Validation against locked state
- **Ratchets** - Lock in progress, cannot go backward
- **Flywheel** - Learnings feed into next cycle

---

## The Problem

Gas Town produces quantity but struggles with quality:

| Problem | Description |
|---------|-------------|
| **No validation** | Polecats produce chaos without filters |
| **Refinery bottleneck** | 10 polecats finish → 10 merge conflicts |
| **No ratchets** | Bad work propagates through the pipeline |
| **No flywheel** | Learnings don't compound across cycles |

---

## The Solution: Brownian Ratchet

```
CHAOS (Gas Town)  →  FILTER (Forge)  →  RATCHET (Locked)
     ↑                    ↑                  ↑
  polecats           validation          git merge
  parallel           against             bd close
  attempts           locked state        permanent
```

**The pattern:** Multiple imperfect attempts, filtered by validation, locked in permanently.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                       GASTOWN FORGE                              │
│                                                                  │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                      THE CHAIN                             │  │
│  │                                                            │  │
│  │  RESEARCH ──▶ PLAN ──▶ SPEC ──▶ IMPLEMENT ──▶ VALIDATE    │  │
│  │      │          │        │          │            │         │  │
│  │      │     [N polecats] [N]     [N per issue]    │         │  │
│  │      │          │        │          │            │         │  │
│  │      │     validate   validate   validate        │         │  │
│  │      │     against    against    against         │         │  │
│  │      │     synthesis   plan       spec           │         │  │
│  │      │          │        │          │            │         │  │
│  │      ▼          ▼        ▼          ▼            ▼         │  │
│  │  SYNTHESIS   LOCKED   LOCKED    CODE IN     LEARNINGS      │  │
│  │  (human)     PLAN     SPEC      MAIN        (.agents/)     │  │
│  │                                                            │  │
│  └───────────────────────────────────────────────────────────┘  │
│                              │                                   │
│                              │ feed forward                      │
│                              ▼                                   │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                      THE FLYWHEEL                          │  │
│  │                                                            │  │
│  │  .agents/patterns/  ──▶  Next /research reads              │  │
│  │  .agents/retros/    ──▶  Smart Connections indexes         │  │
│  │  .agents/learnings/ ──▶  Knowledge compounds               │  │
│  │                                                            │  │
│  └───────────────────────────────────────────────────────────┘  │
│                              │                                   │
│                              ▼                                   │
│                         NEXT CHAIN                               │
└─────────────────────────────────────────────────────────────────┘
```

---

## Core Concepts

### 1. Pools (Chaos)

Multiple parallel attempts at each stage.

| Stage | N (Parallel) | Rationale |
|-------|--------------|-----------|
| Research | 1-3 (manual) | Exploratory, human-guided |
| Plan | 3-5 polecats | High approach variance |
| Spec | 2-3 polecats | Lower variance once plan locked |
| Implement | Per-issue | 1 polecat per issue, many in parallel |

### 2. Gates (Filter)

Validation against the previous stage's locked output.

| Stage | Validates Against | Filter Tools |
|-------|-------------------|--------------|
| Plan | Research synthesis | Pre-mortem, human judgment |
| Spec | Locked plan | Vibe, section checks |
| Code | Locked spec | CI, tests, review |

### 3. Ratchets (Lock)

Once locked, cannot go backward.

| Ratchet | What Gets Locked | Location |
|---------|------------------|----------|
| Synthesis | Key insight from research | `.agents/synthesis/` |
| Plan | Selected plan | `.agents/selected/plan.md` |
| Spec | Selected spec | `.agents/selected/spec.md` |
| Code | Merged to main | Git history |
| Knowledge | Learnings extracted | `.agents/learnings/` |

### 4. Flywheel (Compound)

Learnings feed into the next cycle.

```
Chain N completes
       │
       ▼
/post-mortem extracts learnings
       │
       ▼
Writes to .agents/
       │
       ▼
Chain N+1 /research reads .agents/
       │
       ▼
Smart Connections indexes everything
       │
       ▼
Knowledge compounds
```

**No new products.** Just files + search.

---

## CLI Extensions

Forge extends the `gt` CLI with three command groups:

### Pool Commands (Chaos)

```bash
gt pool create <name>              # Create a pool for candidates
gt pool add <name> <file>          # Add candidate to pool
gt pool list [name]                # List pools or pool contents
gt pool status <name>              # Show pool status with scores
```

### Gate Commands (Filter + Ratchet)

```bash
gt gate filter <pool> --against <ref>   # Validate pool against reference
gt gate scores <pool>                    # Show validation scores
gt gate select <pool> <file>             # Select winner, lock it
gt gate reject <pool>                    # Clear pool, start over
```

### Stage Commands (Lifecycle)

```bash
gt stage current                   # Show current stage
gt stage advance <stage>           # Move to next stage
gt stage history                   # Show stage transitions
gt stage reset                     # Reset to research (new chain)
```

---

## Validation Criteria

### Plan → validates against → Research Synthesis

| Criterion | Question |
|-----------|----------|
| References | Does the plan cite research findings? |
| Approach | Does it explain WHY this approach? |
| Coverage | Does it address all research questions? |
| Assumptions | Consistent with research evidence? |

### Spec → validates against → Locked Plan

| Criterion | Question |
|-----------|----------|
| Components | Does spec define ALL plan components? |
| Interfaces | Are interfaces precise per plan? |
| Error cases | Does spec handle plan's error cases? |
| Fidelity | Does spec implement the selected approach? |

### Code → validates against → Locked Spec

| Criterion | Question |
|-----------|----------|
| Interfaces | Does code match spec's interfaces? |
| Behavior | Does code handle spec's error cases? |
| Tests | Do tests cover spec's scenarios? |
| Security | Does code respect spec's assumptions? |

---

## Integration with Gas Town

Forge is an **extension**, not a replacement.

| Gas Town | Forge Addition |
|----------|----------------|
| `gt sling` | Dispatch to pool (multiple polecats) |
| `gt convoy` | Track pool status |
| `gt hook` | Unchanged |
| Propulsion | Unchanged |
| Beads | Unchanged |

### Example Workflow

```bash
# 1. Research (manual, 1-3 sessions)
/research "How should we implement X?"
# Human synthesizes → .agents/synthesis/key-insight.md

# 2. Plan Pool (3-5 polecats)
gt pool create plans
gt sling plan-1 rig --pool plans --prompt "Plan using approach A"
gt sling plan-2 rig --pool plans --prompt "Plan using approach B"
gt sling plan-3 rig --pool plans --prompt "Plan using approach C"
# Wait for completion
gt pool status plans

# 3. Gate and Select
gt gate filter plans --against .agents/synthesis/key-insight.md
gt gate scores plans
gt gate select plans plan-approach-b.md
# → Locked to .agents/selected/plan.md

# 4. Advance Stage
gt stage advance spec

# 5. Spec Pool (2-3 polecats)
gt pool create specs
gt sling spec-1 rig --pool specs
gt sling spec-2 rig --pool specs
# ...filter, select, advance...

# 6. Implement (per-issue, many polecats)
gt convoy create "Wave 1" issue-1 issue-2 issue-3
gt sling issue-1 rig
gt sling issue-2 rig
gt sling issue-3 rig
# Each validates against locked spec

# 7. Merge Queue
gt queue add branch-1
gt queue add branch-2
gt queue status
# CI validates, serialized merge

# 8. Post-mortem
/post-mortem
# Extracts learnings → .agents/

# 9. Next Chain
gt stage reset
# /research reads prior .agents/
```

---

## Non-Goals

Things Forge does NOT do:

| Non-Goal | Why |
|----------|-----|
| Replace Gas Town | Forge extends, doesn't replace |
| New knowledge product | Flywheel = files + Smart Connections |
| K8s/Temporal/Docker | No infra - just CLI |
| Enterprise features | Start simple, add later |

---

## File Structure

```
gastown-forge/
├── cmd/
│   └── gt-forge/           # CLI binary (or gt plugin)
├── pkg/
│   ├── pool/               # Pool management
│   ├── gate/               # Validation and selection
│   └── stage/              # Stage lifecycle
├── docs/
│   ├── SPEC.md             # This file
│   └── EXAMPLES.md         # Usage examples
└── README.md
```

---

## Success Criteria

Forge is successful when:

1. **Personal productivity** - Makes the author more productive
2. **Quality over quantity** - Produces validated work, not just more work
3. **Flywheel spins** - Learnings compound across chains
4. **Community adoption** - Others find it useful

---

## Prior Art

| Source | What We Took |
|--------|--------------|
| Gas Town | Primitives (hooks, polecats, convoys) |
| Multiclaude | CI as ratchet, merge queue |
| Agent Ops Meta-Observer | Autonomous workers + shared state |
| Brownian Ratchet (physics) | Chaos + filter + lock |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.1.0 | 2026-01-24 | Initial specification |

---

## Upgrade Path: CLI → Temporal

v1 is CLI + files. v2 could be Temporal workflows.

### Why Not Temporal for v1

| Concern | Why Not Yet |
|---------|-------------|
| **Infrastructure** | Temporal requires PostgreSQL, workers, server |
| **Complexity** | Learning curve for durable execution patterns |
| **Scale** | Single developer, not distributed team |
| **Iteration** | CLI is faster to change than workflow code |

### v1: CLI + Files

```bash
gt pool create → mkdir .agents/pools/<name>/
gt gate select → cp to .agents/selected/<stage>.md
gt stage advance → echo <stage> > .agents/stage
```

### v2: Temporal Activities (Future)

```go
workflow.ExecuteActivity(ctx, CreatePoolActivity, ...)
workflow.ExecuteActivity(ctx, GateSelectActivity, ...)
workflow.ExecuteActivity(ctx, StageAdvanceActivity, ...)
```

The state locations don't change. The CLI becomes a Temporal client.

**Research:** See `.agents/research/2026-01-24-temporal-ratchets-research.md`

---

## References

- [Gas Town](https://github.com/steveyegge/gastown) - The primitive layer
- [Beads](https://github.com/steveyegge/beads) - Issue tracking
- [BROWNIAN-RATCHET.md](../BROWNIAN-RATCHET.md) - Philosophy document
- [Apollo Temporal Spec](../olympus/crew/goku/specs/APOLLO-TEMPORAL-SPEC.md) - Temporal workflow patterns

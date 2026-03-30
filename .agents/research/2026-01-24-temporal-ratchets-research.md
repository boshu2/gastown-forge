---
id: research-2026-01-24-temporal-ratchets-research
type: research
date: '2026-01-24'
rig: gastown-forge
status: Research synthesis
---

# Temporal Research for Gastown Forge Ratchets

**Date:** 2026-01-24
**Status:** Research synthesis
**Question:** How does Temporal inform the design of ratchets for Gastown Forge?

---

## Executive Summary

You've done extensive Temporal research through Apollo (Workflow Forge). The key insight for Gastown Forge:

> **Temporal is the "Kubernetes" of workflow orchestration. Gastown Forge needs the "Docker" first.**

Temporal solves problems you don't have yet (crash recovery, long-running state, distributed workers). But its patterns inform how to design ratchets that could later upgrade to Temporal.

---

## What Temporal Provides

From `apollo/CLAUDE.md` and the spec research:

| Capability | What It Does | Gastown Forge Equivalent |
|------------|--------------|--------------------------|
| **Durable execution** | Resume from event log after crash | Files persist state (`.agents/`) |
| **Configurable retries** | Backoff policies for transient errors | CLI retry with `gt sling` |
| **State persistence** | Database-backed history | Git + beads |
| **Human-in-the-loop** | Signal-based approval gates | `gt gate select` (human selects winner) |
| **Workflow versioning** | `GetVersion` for migrations | (not needed yet) |

---

## Temporal Workflow Patterns (From Apollo Spec)

### EpicWorkflow

```go
// Phase 1: Load epic and decompose into waves
waves := decomposeIntoWaves(issues)

// Phase 2: Execute waves sequentially
for i, wave := range waves {
    workflow.ExecuteChildWorkflow(PhalanxWorkflow, waveInput)
}

// Phase 3: Post-mortem (signal-controlled)
```

**Gastown Forge equivalent:**
```bash
# Phase 1: Decompose
gt pool create specs
gt sling spec-1 rig --pool specs
gt sling spec-2 rig --pool specs

# Phase 2: Wait for completion
gt pool status specs

# Phase 3: Select and advance
gt gate select specs winner.md
gt stage advance implement
```

No Temporal needed - same pattern with CLI + files.

### PhalanxWorkflow (Parallel Execution)

```go
// Execute issues with semaphore pattern
for _, issue := range input.Issues {
    workflow.Go(ctx, func(ctx workflow.Context) {
        executeAutomaton(ctx, issue)
    })
}
```

**Gastown Forge equivalent:**
```bash
# Already have this via Gas Town convoys
gt convoy create "Wave 1" issue-1 issue-2 issue-3
gt sling issue-1 rig
gt sling issue-2 rig
gt sling issue-3 rig
gt convoy status "Wave 1"
```

### MissionWorkflow (Human Gates)

```go
// Wait for approval signal with timeout
signalCh := workflow.GetSignalChannel(ctx, "approval")
selector.AddReceive(signalCh, func(c workflow.ReceiveChannel, more bool) {
    c.Receive(ctx, &approval)
})

// 24-hour timeout for review
timerFuture := workflow.NewTimer(ctx, 24 * time.Hour)
```

**Gastown Forge equivalent:**
```bash
# Human runs this when ready
gt gate select plans plan-approach-b.md
# → Locks to .agents/selected/plan.md

# No timeout needed - human decides when
```

---

## Agent Chaining Research Key Insight

From `athena/.../research-agent-chaining.md`:

> **"Don't chain agents. Compose through infrastructure."**

| Scenario | Wrong Approach | Right Approach |
|----------|---------------|----------------|
| Code review → test generation | Agent A calls Agent B | Two webhooks on same event |
| Analysis → report | Pipeline of agents | Analysis writes to store, report reads |
| Parse → validate → store | Chain of agents | Single agent with multiple tools |

**For Gastown Forge:**
- Don't chain stages with agent-to-agent calls
- Each stage writes output to locked location
- Next stage reads from locked location
- Coordination through shared state (files), not explicit chains

---

## The Multiclaude Ratchet Pattern

From `multiclaude/refinery/rig/internal/prompts/merge-queue.md`:

> **"You are the ratchet: the mechanism that ensures motion only goes one direction."**

Key principles:
1. **CI and reviews are the arbiters** - If CI passes AND reviews addressed, merge it
2. **Speed matters** - Faster merges = faster progress
3. **Incremental progress counts** - Partial solution > perfect in development
4. **Handle conflicts by moving forward** - Merge first, rebase the other
5. **Close superseded work** - No cleanup guilt

**Applied to Gastown Forge gates:**

| Multiclaude | Gastown Forge |
|-------------|---------------|
| CI passes | Gate validates against locked reference |
| Merge | `gt gate select` → locks to `.agents/selected/` |
| Close superseded | Rejected pool candidates discarded |
| Move forward | Advance to next stage |

---

## What Gastown Forge Takes From Temporal

### 1. Wave Decomposition

Temporal's `decomposeIntoWaves()` is just dependency-aware grouping:

```
Issues with no blockers → Wave 1
Issues blocked by Wave 1 → Wave 2
etc.
```

Gastown Forge already has this via beads:
```bash
bd ready --parent=<epic>    # Wave 1
bd blocked                   # Future waves
```

### 2. Activity Retry Policies

Temporal's retry policy:
```go
RetryPolicy{
    InitialInterval:    time.Second,
    BackoffCoefficient: 2.0,
    MaximumInterval:    time.Minute,
    MaximumAttempts:    3,
}
```

Gastown Forge equivalent:
```bash
# Polecat fails → Mayor re-slings
gt sling <issue> <rig>  # Attempt 1
# If fails...
gt sling <issue> <rig>  # Attempt 2
# If fails again...
gt mail send --human -s "BLOCKER: <issue> needs help"  # Escalate after 3
```

### 3. Signal-Based Approval

Temporal uses signals for human gates:
```go
signalCh := workflow.GetSignalChannel(ctx, "approval")
```

Gastown Forge: human runs `gt gate select`:
```bash
# Human reviews pool
gt pool status plans

# Human selects winner (the "signal")
gt gate select plans plan-approach-b.md
```

No infrastructure needed - human just runs a command.

---

## Why NOT Temporal for v1

| Concern | Why Not Yet |
|---------|-------------|
| **Infrastructure** | Temporal requires PostgreSQL, workers, server |
| **Complexity** | Learning curve for durable execution patterns |
| **Scale** | You're one developer, not distributed team |
| **Iteration** | CLI is faster to change than workflow code |

**The Apollo Spec itself says:**
> "Gas Town polecats need durability beyond tmux"

But tmux is fine for v1. When polecats need to survive machine restarts, then consider Temporal.

---

## Upgrade Path to Temporal

The design supports later upgrade:

### v1: CLI + Files
```
gt pool create → mkdir .agents/pools/<name>/
gt gate select → cp to .agents/selected/<stage>.md
gt stage advance → touch .agents/stage
```

### v2: Temporal Activities
```go
// Same operations, now durable
workflow.ExecuteActivity(ctx, CreatePoolActivity, ...)
workflow.ExecuteActivity(ctx, GateSelectActivity, ...)
workflow.ExecuteActivity(ctx, StageAdvanceActivity, ...)
```

The state locations don't change. The CLI becomes a Temporal client.

---

## Key Takeaways

1. **Pattern, not product** - Gastown Forge implements Temporal's patterns without Temporal
2. **Files are durable** - Git + `.agents/` survives everything you need for v1
3. **Human gates are simple** - No signals needed, human runs `gt gate select`
4. **Retry is manual** - Mayor re-slings on failure, escalates after 3
5. **Upgrade path exists** - Can add Temporal later without redesigning

---

## The v1 Ratchet Implementation

From this research, Gastown Forge v1 ratchets:

| Ratchet | Mechanism | Lock Location |
|---------|-----------|---------------|
| Research → Synthesis | Human writes summary | `.agents/synthesis/` |
| Plan pool → Locked plan | `gt gate select` | `.agents/selected/plan.md` |
| Spec pool → Locked spec | `gt gate select` | `.agents/selected/spec.md` |
| Code → Main | Git merge | Git history |
| Learnings | `/post-mortem` | `.agents/learnings/` |

All file-based. All Git-native. All simple.

---

## References

| Document | Location |
|----------|----------|
| Apollo Temporal Spec | `olympus/crew/goku/specs/APOLLO-TEMPORAL-SPEC.md` |
| Apollo Architecture | `apollo/crew/netero/docs/ARCHITECTURE.md` |
| Agent Chaining Research | `athena/.../research-agent-chaining.md` |
| Brownian Ratchet | `~/gt/BROWNIAN-RATCHET.md` |
| Multiclaude Merge Queue | `multiclaude/refinery/rig/internal/prompts/merge-queue.md` |

---

**"Docker before Kubernetes. CLI before Temporal."**

# Synthesis: Gas Town Forge

**Date:** 2026-01-24
**Status:** Vision synthesis
**Journey:** Agent Ops (Nov 2025) → Gas Town (Dec 2025) → Forge (Jan 2026)

---

## The Journey

```
November 2025           December 2025           January 2026
─────────────           ─────────────           ────────────

AGENT OPS               GAS TOWN                FORGE VISION
Meta-Observer           Primitives              Orchestration
Pattern                 Emerge                  Layer

"Workers self-         "Hooks, polecats,       "Make it dance"
 organize through       convoys, propulsion"
 shared memory"
       │                      │                      │
       │                      │                      │
       └──────────────────────┴──────────────────────┘
                              │
                              ▼
                     GAS TOWN FORGE
                  "Chaos → Filter → Ratchet"
                  "Learnings → Next Cycle"
```

---

## November 2025: Agent Ops Discovery

From `/Users/fullerbt/gt/agentops-work/workflows/meta-observer/example-today.md`:

> "We started with a traditional orchestrator model—one session directing the others. But the workers started completing complex work before the orchestrator gave instructions. They self-organized through shared memory (Memory MCP), like ants coordinating via pheromones."

**The Meta-Observer Pattern:**

```
┌─────────────────────────────────────────────────────────────────┐
│                     META-OBSERVER (Nov 2025)                     │
│                                                                  │
│   3 Workers (autonomous)                                        │
│   ├── Worker 1: Launch content                                  │
│   ├── Worker 2: Framework docs                                  │
│   └── Worker 3: Website build                                   │
│                                                                  │
│   Shared Memory (Memory MCP)                                    │
│   ├── Workers update status                                     │
│   └── Workers read each other's updates                         │
│                                                                  │
│   Observer (watches, doesn't command)                           │
│   └── Synthesizes at intervals                                  │
│                                                                  │
│   Result: 2-3x faster, 0% coordination overhead                 │
│   Discovery: "Autonomous > Orchestrated"                        │
└─────────────────────────────────────────────────────────────────┘
```

**Key insight:** Workers coordinate better autonomously than through central control.

---

## December 2025 → January 2026: Gas Town Primitives

Steve Yegge's gastown gave you the primitives you were looking for:

| Primitive | Agent Ops Equivalent | What It Does |
|-----------|---------------------|--------------|
| **Hook** | (none - MCP only) | Durable assignment mechanism |
| **Propulsion** | "Workers self-start" | If work on hook, run it |
| **Polecat** | "Worker" | Ephemeral agent in worktree |
| **Convoy** | (none) | Batch tracking |
| **Beads** | Memory MCP | Shared state (git-native) |
| **Sling** | (manual) | Dispatch to polecat |
| **Mail** | (none) | Agent communication |

**What Gas Town added:**
- Git-native state (beads) instead of MCP
- Durable hooks that survive sessions
- Propulsion Principle formalized
- Multi-rig architecture

**What Gas Town lacked:**
- Validation (Refinery is bottleneck)
- Orchestration (just primitives)
- Ratchets (chaos without filter)

---

## January 2026: The Forge Vision

Combine everything:

```
┌─────────────────────────────────────────────────────────────────┐
│                       GAS TOWN FORGE                             │
│                                                                  │
│   PRIMITIVES (from Gas Town)                                    │
│   ├── hook, sling, convoy, polecat, worktree, mail              │
│   └── beads (git-native shared state)                           │
│                                                                  │
│   + BROWNIAN RATCHET (from Multiclaude + physics)               │
│   ├── Chaos: N parallel attempts per stage                      │
│   ├── Filter: Validate against locked state                     │
│   └── Ratchet: Lock in, cannot go backward                      │
│                                                                  │
│   + META-OBSERVER (from Agent Ops)                              │
│   ├── Workers self-organize through beads                       │
│   ├── Mayor observes, doesn't micromanage                       │
│   └── Synthesis at intervals                                    │
│                                                                  │
│   + FLYWHEEL (the connection)                                   │
│   ├── Chain completes → extract learnings                       │
│   ├── Learnings → .agents/ files                                │
│   ├── Next chain → /research reads files                        │
│   └── Repeat (knowledge compounds)                              │
│                                                                  │
│   = THE FORGE                                                   │
│     Gas Town that produces quality, not just quantity           │
└─────────────────────────────────────────────────────────────────┘
```

---

## What "Forge" Means

A forge takes raw material and shapes it into something useful through:
1. **Heat** (chaos/exploration)
2. **Shaping** (filtering/validation)
3. **Quenching** (locking in/ratchet)

Gas Town provides the heat (parallel workers).
The Forge adds shaping and quenching.

```
RAW CHAOS              FILTERED              LOCKED
(Gas Town)             (Forge)               (Ratchet)

  [polecat]     →      validate      →      LOCKED PLAN
  [polecat]            against
  [polecat]            synthesis
  [polecat]
  [polecat]

        │                   │                    │
        │                   │                    │
     "heat"            "shaping"            "quenching"
```

---

## The Complete Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                       GAS TOWN FORGE                             │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │                      CHAIN (RPI)                             ││
│  │                                                              ││
│  │  RESEARCH ──▶ PLAN ──▶ SPEC ──▶ IMPLEMENT ──▶ VALIDATE      ││
│  │      │          │        │          │            │           ││
│  │      │     [N polecats] [N]     [N per issue]    │           ││
│  │      │          │        │          │            │           ││
│  │      │     validate   validate   validate        │           ││
│  │      │     against    against    against         │           ││
│  │      │     synthesis   plan       spec           │           ││
│  │      │          │        │          │            │           ││
│  │      ▼          ▼        ▼          ▼            ▼           ││
│  │  SYNTHESIS   LOCKED   LOCKED    CODE IN     LEARNINGS        ││
│  │  (human)     PLAN     SPEC      MAIN        EXTRACTED        ││
│  │      │          │        │          │            │           ││
│  │      └──────────┴────────┴──────────┴────────────┘           ││
│  │                        RATCHETS                               ││
│  └──────────────────────────────────────────────────────────────┘│
│                              │                                   │
│                              │ feed forward                      │
│                              ▼                                   │
│  ┌──────────────────────────────────────────────────────────────┐│
│  │                      FLYWHEEL                                 ││
│  │                                                               ││
│  │  Learnings → .agents/patterns/  → Next /research reads       ││
│  │            → .agents/retros/    → Smart Connections indexes  ││
│  │            → .agents/learnings/ → Knowledge compounds         ││
│  └──────────────────────────────────────────────────────────────┘│
│                              │                                   │
│                              │ repeat                            │
│                              ▼                                   │
│                         NEXT CHAIN                               │
└─────────────────────────────────────────────────────────────────┘
```

---

## What to Build

### Open Source Product: Gas Town Forge

Lives alongside Gas Town, adds orchestration:

| Component | What It Does |
|-----------|--------------|
| **Pool management** | Create pools of candidates (N plans, N specs) |
| **Gate filtering** | Validate pool against locked state, score |
| **Stage transitions** | Lock selected, move to next stage |
| **Merge queue** | Serialize merges, CI as gate |
| **Retro extraction** | `/post-mortem` → `.agents/` files |

### CLI Extensions

```bash
# Pool management
gt pool create plan-candidates
gt pool add plan-candidates plan-1 plan-2 plan-3

# Gate filtering
gt gate filter plan-candidates --against .agents/synthesis/key-insight.md

# Selection and ratchet
gt gate select plan-event-driven.md  # Locks it

# Stage transition
gt stage advance spec  # LOCKED PLAN becomes validation target

# Merge queue
gt queue add <branch>
gt queue status
```

### Skill Integration

```bash
/research      # (unchanged) Creates synthesis
/plan-pool     # (new) Spawn N polecats for planning
/spec-pool     # (new) Spawn N polecats for specs
/implement     # (enhanced) Validates against locked spec
/post-mortem   # (unchanged) Extracts learnings
```

---

## Why This Works

### November Insight Preserved

> "Autonomous workers + shared memory > Central orchestration"

Still true. Polecats work autonomously. Beads is the shared memory. Mayor observes.

### Gas Town Primitives Preserved

Hooks, polecats, convoys, propulsion all work the same. Forge just adds:
- Pools of candidates
- Validation against locked state
- Stage transitions

### Flywheel Preserved

No new product needed. Just:
- `/post-mortem` writes to `.agents/`
- `/research` reads from `.agents/`
- Smart Connections indexes everything

---

## The One-Liner

> **"Gas Town Forge: Brownian Ratchet orchestration for Gas Town primitives, with flywheel-connected chains."**

Or simpler:

> **"Make Gas Town dance."**

---

## Key Files Referenced

| Era | File | Key Insight |
|-----|------|-------------|
| Nov 2025 | `agentops-work/workflows/meta-observer/example-today.md` | Autonomous > Orchestrated |
| Jan 2026 | `multiclaude/crew/boden/AGENTS.md` | CI as ratchet, merge queue |
| Jan 2026 | `BROWNIAN-RATCHET.md` | Chaos + Filter + Ratchet |
| Today | `.agents/research/2026-01-24-brownian-ratchet-chained-rpi.md` | Chained validation |
| Today | `.agents/research/2026-01-24-complete-ratchet-flywheel.md` | Flywheel = files + search |

---

## Next Steps

1. **Name it** - "Gas Town Forge"? "GT Forge"? Something else?
2. **Scope v1** - What's the minimum to make YOU productive?
3. **Build alongside Gas Town** - Same pattern as beads/gastown
4. **Community adoption** - If it works for you, open source it

---

**"Riding the wave, not inventing a new one."**

Gas Town is the wave. Forge makes it dance.

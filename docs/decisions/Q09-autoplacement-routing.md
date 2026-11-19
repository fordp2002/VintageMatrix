# Q09: Auto-placement scope; whether routing is in scope

| | |
|---|---|
| **Status** | Deferred |
| **Affects** | [09-pcb.md](../spec/09-pcb.md) |
| **Decided** | — |

## Context

Switches are placed at stem centres (PCB-001). It is undecided whether diodes, the MCU, and connectors are placed automatically, and whether any routing is done.

## Known constraints

- Hand-placed items must not be moved (PCB-005).
- **Settled:** keys are placed (PCB-001), and controllers are placed when the user puts them on the canvas (PCB-020).
- **Settled:** routing is in scope as **optional autorouting through Freerouting** (PCB-029 to PCB-033).
- **Settled:** diodes are placed and connected for every scheme (PCB-034). Key diodes follow their key (KEY-011); row/column diodes are placed on the canvas (PCB-040).
- The open part is whether anything else (e.g. connectors) is placed automatically.

## Options

1. Switches + per-key modules only; no routing
2. Also diodes/MCU/connectors; no routing
3. Placement + delegated autorouting (external router)

These candidates are not exhaustive and are not recommendations.

## Decision

**Deferred** (2026-11-19). The remaining part (automatic placement of other parts such as connectors) is not needed to get going.

# Q08: Required vs. optional key fields and their defaults

| | |
|---|---|
| **Status** | Decided |
| **Affects** | [04-keys.md](../spec/04-keys.md), [01-contract.md](../spec/01-contract.md) |
| **Decided** | 2026-11-19 |

## Context

The key field set is listed in KEY-005, but not which fields are required or what their defaults are.

## Known constraints

- Keyboards without firmware are first-class (PRIN-009). Keycodes cannot be unconditionally required; they matter only when the design targets firmware (KEY-008, FW-010).
- Interacts with Q12 (unassigned keys).
- Defaults must be stated in the schema.

## Options

None recorded yet.

## Decision

**Almost nothing is universally required. Requirements depend on what is being generated, and gaps produce warnings or errors at generation time.**

- `id` is always present, but **assigned automatically** by the software. Users and AI agents never need to invent one.
- `position` is required only when the design targets a PCB. A firmware-only design does not need it.
- Rows/columns are not required. A key that is not yet connected produces a warning each time firmware or a PCB is generated (MAT-021).
- Every other field is optional. Its default when absent is stated in the schema (CON-015).

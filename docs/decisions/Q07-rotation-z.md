# Q07: Rotation and z in the position model

| | |
|---|---|
| **Status** | Decided |
| **Affects** | [04-keys.md](../spec/04-keys.md), [09-pcb.md](../spec/09-pcb.md) |
| **Decided** | 2026-11-19 |

## Context

Some vintage layouts have angled keys. A z component might express stepped or sculpted rows, or separate PCB layers.

## Known constraints

- Rotation would be about the stem centre (KEY-001).
- Meanwhile `rotation`/`z` fields are preserved but have no meaning (KEY-003).
- Controllers can now be placed on the canvas (GUI-014, PCB-020). Modules are commonly mounted rotated (90°/180°), so rotation matters for them even more than for keys, and they are placed by their footprint origin (PCB-026). Diodes, both per key (KEY-011) and per line (PCB-040), are commonly rotated too.

## Options

1. Add `rotation` only (an exact decimal string in degrees)
2. Add `rotation` and `z`
3. Neither in 0.x

These candidates are not exhaustive and are not recommendations.

## Decision

**Rotation is a property of every placed element, editable in the properties panel. There is no z.**

- `rotation` is an exact decimal string in degrees (e.g. `"90"`, `"7.5"`), applied about the element's origin (PCB-026), in the same sense as KiCad. Absent means `"0"`.
- It applies to keys, controllers, and row/column diodes. Key diodes rotate with their key, plus the project-wide diode rotation (KEY-011).
- `z` is not part of the model. It is preserved like any unknown field (CON-005).

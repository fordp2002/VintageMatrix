# Q12: Explicit "unassigned" keycode for scaffold collections

| | |
|---|---|
| **Status** | Decided |
| **Affects** | [05-collections.md](../spec/05-collections.md), [03-matrix.md](../spec/03-matrix.md), [04-keys.md](../spec/04-keys.md) |
| **Decided** | 2026-11-19 |

## Context

Scaffold collections (e.g. a blank numpad) may have keys with no keycode and/or no matrix address yet.

## Known constraints

- Keyboards without firmware are first-class (PRIN-009). Keycodes cannot be unconditionally required; they matter only when the design targets firmware (KEY-008, FW-010).
- QMK has `KC_NO` / `KC_TRNS`, which mean something specific in firmware.
- Interacts with Q08.
- **Settled on the matrix side:** a key may have no row/column, either *not yet connected* or deliberately *unconnected* (MAT-007, MAT-009, MAT-019). This question now covers only **keycodes**.

## Options

1. Absent field means unassigned
2. Explicit sentinel value (e.g. `VM_UNASSIGNED`) that blocks firmware generation
3. Use `KC_NO`

These candidates are not exhaustive and are not recommendations.

## Decision

**No special marker; a key with no keycode simply has no `keycode` field. The user is prompted before firmware is generated.**

- If any key has no keycode when firmware is generated, the user is shown the list and asked whether to continue (FW-018).
- If they continue, those keys are generated as `KC_NO` (does nothing).
- Designs without firmware are unaffected (KEY-008).

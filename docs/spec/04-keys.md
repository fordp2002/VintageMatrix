# 04 — Keys and outline

| | |
|---|---|
| **ID prefix** | `KEY` |
| **Owner** | Core model |
| **Depends on** | [02-dimensions](02-dimensions.md), [03-matrix](03-matrix.md) |
| **Related questions** | [Q07](../decisions/Q07-rotation-z.md), [Q08](../decisions/Q08-key-field-defaults.md) |

## Position

- **KEY-001** [Decided] A key's position is its **switch stem centre**, not the switch body or keycap corner.
- **KEY-009** [Decided] Every switch footprint used by VintageMatrix MUST have its **origin at the centre of the key stem**. Footprints whose origin is elsewhere are not supported; there is no offset mechanism.
- **KEY-002** [Decided] Every per-key module's geometry (switch footprint, stabiliser, LED, diode, keycap outline) is placed **relative to the stem centre**, so swapping a module never moves a key.
- **KEY-003** [Decided] Every placed element (key, controller, row/column diode) has an optional `rotation`: an exact decimal string in degrees (e.g. `"90"`), about its origin (PCB-026), in the same sense as KiCad. Absent means `"0"`. It is edited in the properties panel (GUI-005). There is no `z`; a `z` field is preserved but has no meaning (CON-005). See [Q07](../decisions/Q07-rotation-z.md).

## Key diodes

- **KEY-010** [Decided] When the diode scheme is `key` (MAT-005), every key gets one diode placed relative to its stem centre (KEY-002) and connected by the PCB generator (PCB-035), unless that key's **Key diode** is set to **No** (KEY-018).
- **KEY-012** [Proposed] Key diodes are placed for every key except those with a side marked **unconnected** (MAT-019), including keys not yet connected. The board layout then does not shift while rows and columns are being allocated; the diode gets its nets once both sides are connected.
- **KEY-011** [Decided] The key diode's position and rotation relative to the stem centre are **project-wide** (`matrix.diodes.keyOffset`, `matrix.diodes.keyRotation`), the same for every key, applied in the key's own rotated frame so the diode follows the key. There is no per-key placement override.
- **KEY-018** [Decided] Each key has a **Key diode: Yes/No** setting (`diode`, boolean, default Yes), shown in the properties panel. It only applies with the `key` scheme. A key set to No gets no diode, and its switch is wired directly between its row and column.
- **KEY-019** [Decided] A key has **no size property**. Its size and shape come from its **keycap**, chosen in the properties panel alongside its **switch**.
- **KEY-020** [Proposed] Keycaps come from a built-in list. Each entry gives the keycap's size (in `u`, DIM-012), its shape, and whether it needs a stabiliser.
- **KEY-021** [Decided] A key's keycap also records its **row angle** (the sculpted profile row) and whether it has a **homing mark** (e.g. the bumps on F and J). These are **for a BOM only** (ARCH-007): they change nothing on the PCB or in the firmware. They are edited in the properties panel from the start. The canvas shows homing marks.
- **KEY-022** [Proposed] Row angle is `R1` to `R4`, or absent for a uniform (flat) profile. The homing mark is a boolean, default No.

- **KEY-013** [Decided] A key without a `position` is valid in a design that does not target a PCB. In a design that does, generating the PCB with such a key is a semantic **error**. A key without a position is not drawn on the canvas.

## Coordinate system

- **KEY-004** [Proposed] The origin is the top-left of the design canvas, **+x right, +y down** (matching KiCad). The PCB outline sits in this coordinate system; it is not the origin.

## Fields

- **KEY-005** [Decided] Field requirements ([Q08](../decisions/Q08-key-field-defaults.md)): `id` is always present but assigned automatically (CON-016); `position` is required only for designs that target a PCB (KEY-013); every other field is optional, with its default stated in the schema. The fields are:

| Field | Type | Meaning |
|---|---|---|
| `id` | string | Stable identifier (CON-009). |
| `position` | `{x, y}` dimension values | Stem centre. |
| `keycap` | string | The keycap, e.g. `1u`, `2.25u`, `6.25u`, `iso-enter` (KEY-019). Default `1u`. |
| `keycapRow` | string | Keycap row angle, `R1`–`R4`; absent for a uniform profile. BOM only (KEY-021). |
| `homing` | boolean | Keycap has a homing mark. BOM only (KEY-021). Default No. |
| `matrix` | `{row, col}` | Matrix address (MAT-007). |
| `keycode` | string | QMK keycode for the base layer, e.g. `KC_A`. |
| `layers` | array of strings | Keycodes for additional layers. |
| `legends` | object | Legend override by position, e.g. `{ "topLeft": "£", "bottomLeft": "3" }`. Absent: the standard legend for the keycode (KEY-025, KEY-026). |
| `switch` | string | The key switch, e.g. MX, Alps, or a vintage-specific type (KEY-009). Chosen in the properties panel. |
| `modules` | array of `{type, …}` | Additional per-key modules (KEY-002). |
| `diode` | boolean | Key diode Yes/No; default Yes (KEY-018). |
| `collection` | object | Back-reference to the collection instance the key came from ([05](05-collections.md)). |

- **KEY-006** [Decided] Legends are display and BOM data only. A keycode is never derived from a legend.
- **KEY-023** [Decided] A keycap can carry **several legends**, as is common on retro keyboards (e.g. a shifted symbol above the unshifted one, or graphics characters on the front).
- **KEY-024** [Proposed] Legend positions are a fixed set: `topLeft`, `topRight`, `centre`, `bottomLeft`, `bottomRight`, `front`.
- **KEY-025** [Decided] **A key shows the standard legend for its keycode** (e.g. `KC_1` gives `!` and `1`, `KC_G` gives `G`). The user can **override** it.
- **KEY-026** [Decided] `legends` holds an override only. If it is absent, the key shows the standard legend for its keycode; if present, it replaces the standard legend. There is no per-layout auto-fill. Tools that read the design (ARCH-007) derive the standard legend from the keycode the same way.
- **KEY-008** [Decided] `keycode` and `layers` are meaningful only to firmware. A keyboard without firmware (PRIN-009, FW-010) does not need them, so they cannot be unconditionally required ([Q08](../decisions/Q08-key-field-defaults.md)).

## PCB outline

- **KEY-007** [Decided] Two kinds of outline are supported:
  - **Rectangle**, the standard built-in outline: `{ "rect": { "x", "y", "w", "h" } }` with dimension values. It is drawn in KiCad as one rectangle, not four lines.
  - **Imported** from a file (KEY-014): a closed path of line and arc segments with dimension-value vertices.
- **KEY-016** [Decided] **Every aspect of the board outline, including cut-outs, is defined in the design**, never in KiCad. `outline` holds the outer edge plus an optional list of `cutouts`, each a rectangle or a closed path like the outline itself.
- **KEY-017** [Proposed] On DXF import, the largest closed path becomes the outer edge, and closed paths inside it become cut-outs.
- **KEY-014** [Decided] An outline can be imported from a DXF file. The geometry is copied into the design (a single source of truth); the DXF file is not needed afterwards.
- **KEY-015** [Proposed] Import keeps lines, arcs, and circles. Anything else (e.g. splines, text) is rejected with a diagnostic naming it. If the DXF does not state its units, the user is asked. Coordinates become exact decimal strings at import (DIM-001); the conversion from DXF's binary floats happens once, there.

## Extension points

**Add a per-key module type** (LED, stabiliser variant, hall-effect sensor, …). Define a new `modules[].type` value here with its fields, geometry given relative to the stem centre (KEY-002). Add it to the schema, and say which generators use it ([08](08-schematic.md), [09](09-pcb.md), [10](10-firmware.md)); generators that don't use it must ignore it safely.

**Add a switch type.** Add a `switch` value and map it to a footprint in the PCB generator ([09](09-pcb.md#extension-points)). Footprint geometry comes from upstream or a KiCad library, never copied in (UP-001).

**Add a key field.** Add a row to the table above with a new ID if it carries rules, plus its schema entry and its default.

**Richer outlines** (e.g. mounting holes). Extend KEY-007/KEY-016 with new segment or feature types; everything on the board edge stays defined in the design.

**Another import format** (e.g. SVG). Add it beside DXF in KEY-014, with the same rules (KEY-015).

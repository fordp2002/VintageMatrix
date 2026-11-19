# 03 — Matrix

| | |
|---|---|
| **ID prefix** | `MAT` |
| **Owner** | Core model |
| **Depends on** | [01-contract](01-contract.md), [04-keys](04-keys.md) |
| **Related questions** | [Q11](../decisions/Q11-collection-matrix-addressing.md), [Q12](../decisions/Q12-unassigned-keycode.md) |

The matrix is the spine (PRIN-004). Schematic, PCB netlist, and firmware are all derived from it.

## Rules

- **MAT-001** [Decided] Key-to-matrix assignments exist only in the design document. No generator keeps its own copy or infers connectivity on its own.

## Structure

- **MAT-002** [Proposed] The `matrix` section looks like:

```json
"matrix": {
  "type": "rowcol",
  "rows": [ { "id": "r0", "name": "ROW0" }, { "id": "r1", "name": "ROW1" } ],
  "cols": [ { "id": "c0", "name": "COL0" }, { "id": "c1", "name": "COL1" } ],
  "diodes": {
    "scheme": "key",
    "direction": "COL2ROW",
    "footprint": "Diode_SMD:D_SOD-123",
    "keyOffset": { "x": { "value": "0", "unit": "mm" }, "y": { "value": "5", "unit": "mm" } }
  },
  "pins": { "r0": "GP2", "c0": "GP10" }
}
```

- **MAT-003** [Proposed] `type` names the scanning topology. `rowcol` is the only type in 0.5 and the default when absent.
- **MAT-004** [Proposed] `rows` and `cols` are ordered lists; their order defines firmware matrix indices.
- **MAT-005** [Decided] `diodes` holds the **project's diode preference**. A design uses exactly one `scheme`:
  - `none`: no diodes (common on vintage boards);
  - `key`: one diode per key;
  - `row`: one diode per row line;
  - `column`: one diode per column line.
- **MAT-015** [Decided] `direction` is `COL2ROW` or `ROW2COL` (QMK semantics). Every diode in the design is oriented so that current flows in that direction.
- **MAT-016** [Proposed] Only per-key diodes prevent ghosting, so any scheme other than `key` raises a ghosting **warning** in semantic validation.
- **MAT-017** [Proposed] `footprint` is the KiCad footprint used for every diode in the design. It must follow the diode pad convention (PCB-036).
- **MAT-018** [Decided] A design never mixes schemes: in particular, it never has both row diodes and column diodes.
- **MAT-006** [Decided] `pins` maps each row/column ID to a controller pin name. Pin names are QMK's names for the MCU's pins, used exactly as QMK expects (FW-015). The core MUST NOT hard-code any MCU's pinout.
- **MAT-013** [Decided] `pins` is optional. A keyboard without firmware (PRIN-009) has no controller pins: its rows and columns reach the host through net names (MAT-011), e.g. to the host's keyboard connector in the user's schematic.
- **MAT-014** [Proposed] For a host-scanned keyboard, the matrix (row/column count and which key is at which address) is dictated by the host machine. Collections that reproduce a vintage host matrix SHOULD state the host and the source of the matrix in `design.description` (COL-007).

## Matrix addresses

- **MAT-007** [Decided] A key's row and its column are set independently. Each is in one of three states: **connected** (to one row / one column), **not yet connected** (still to do; the state of a newly placed key), or **unconnected** (MAT-019).
- **MAT-019** [Decided] **Unconnected** is set separately for the row and for the column. It means *deliberately* not connected on that side, which is different from *not yet connected*.
- **MAT-020** [Proposed] Representation in `keys[].matrix`, per axis (`row`, `col`):
  - absent: not yet connected;
  - a row/column ID: connected;
  - `"unconnected"`: unconnected. `unconnected` is therefore a reserved word and cannot be used as a row or column ID.
- **MAT-008** [Decided] Two or more keys **may share the same row and column** (both connected), but they **must be allocated the same keycode** (and the same `layers`, KEY-005). In a design that targets firmware (FW-010), a mismatch is a semantic **error**. Without firmware, keycodes are not used (KEY-008), so sharing is always allowed. There is no separate `sharedWith` declaration.
- **MAT-009** [Decided] A key may have no address: each side is either not yet connected or unconnected (MAT-007).
- **MAT-021** [Proposed] Generation proceeds with keys that have a side not yet connected, but reports each as a **warning**, since the design is unfinished. Unconnected sides produce no warning. Only keys connected on both sides appear in the firmware matrix. A connected side's switch pad gets its net; nothing else does.
- **MAT-010** [Proposed] When a controller is selected, semantic validation warns if the matrix needs more pins than it provides. With no controller, this check does not apply.

## Connecting keys

- **MAT-022** [Decided] Rows and columns can be connected to keys three ways, all through the same API operation (`assignMatrix`, API-010): the properties panel (GUI-022), the graphical allocation mode (GUI-018 to GUI-021), or the design file.
- **MAT-023** [Decided] Key collections are normally stored with rows and columns not yet connected, but this is not mandatory: a collection may carry connected rows and columns, and its keys arrive with them. Collisions on insertion are [Q11](../decisions/Q11-collection-matrix-addressing.md).

## Net names

- **MAT-011** [Decided] Each row's and column's `name` is the **net name** used on the board and is the contract with the user's schematic (PCB-016). It is required and unique across all rows and columns.
- **MAT-012** [Proposed] Names use only letters, digits, and underscores (`^[A-Za-z][A-Za-z0-9_]*$`), so they are valid KiCad net names unchanged.

## Extension points

**Add a scanning topology** (e.g. direct pins, shift-register scanning, the vintage "column strobe + data bus" styles). Add a `type` value to MAT-003 with its own subsection here: its fields, how keys are addressed, and its validation rules. Each generator must then handle it or report it as unsupported with a diagnostic. It must never ignore it silently.

**Add a matrix-level property** (e.g. debounce hints, ghost-key tables). Add a field with a new ID and a default, following [01-contract](01-contract.md#extension-points).

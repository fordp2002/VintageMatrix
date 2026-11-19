# 06 — Design screen (GUI)

| | |
|---|---|
| **ID prefix** | `GUI` |
| **Owner** | Design screen |
| **Depends on** | [07-api](07-api.md), [04-keys](04-keys.md), [02-dimensions](02-dimensions.md) |
| **Related questions** | [Q02](../decisions/Q02-app-stack.md), [Q06](../decisions/Q06-reference-image-calibration.md) |

## Role

- **GUI-001** [Decided] The design screen is a **client of the headless API**. Every edit it makes is an API operation, and it holds no design state that the contract cannot represent (PRIN-003).
- **GUI-002** [Decided] The GUI is a TypeScript web front-end inside a Tauri 2 (Rust) desktop shell, talking to the Python engine over the engine protocol (API-009). See [Q02](../decisions/Q02-app-stack.md).

## Canvas

- **GUI-003** [Decided] The canvas shows the PCB outline, the keys, any controllers placed on it (modules or discrete MCUs, GUI-014), row/column diodes (GUI-017), and an optional scaled reference image beneath them.
- **GUI-004** [Decided] **Single selection only** in this iteration: one object (key, controller, outline element, or reference image) at a time. No multi-select, marquee, or group move.
- **GUI-005** [Decided] A **properties panel** shows and edits the exact fields of the selected object, including arbitrary off-grid positions.

- **GUI-024** [Proposed] Keys are drawn as vector graphics generated from the design data (keycap, legend, rotation), with no per-key image files. SVG is the natural fit for the web UI.

## Controllers on the canvas

- **GUI-014** [Decided] The user can place a controller (module or discrete MCU, FW-011) on the canvas, move it, and remove it. Placing it on the canvas is what makes the PCB generator place it on the board (PCB-020).
- ~~**GUI-015** Controller placement is not snapped.~~ [Removed in 0.5]: superseded by GUI-006 (everything snaps).

- **GUI-017** [Proposed] With the `row` or `column` diode scheme, the user places each line's diode on the canvas like a controller. Key diodes are not placed by hand; they follow their key (KEY-011).

## Connecting rows and columns

- **GUI-018** [Decided] A **matrix toolbar** on the left-hand side of the canvas has a button for each row, a button for each column, and a **Not connected** button. It also shows how far allocation has got: the number of keys on each row and column, and how many keys are still *not yet connected*.
- **GUI-019** [Decided] Choosing a row or column button enters **allocation mode** for that row/column. Keys already on it are made **prominent** on the canvas compared with all other keys. Clicking any key allocates that row/column to it. Allocation mode is not selection, so GUI-004 is unaffected.
- **GUI-020** [Decided] Clicking a key that already has a *different* row (in row mode) or column (in column mode), or that is marked unconnected, asks **"Are you sure?"** before replacing it. Clicking a key already on the active row/column does nothing.
- **GUI-021** [Decided] The toolbar has a **Not connected** button for rows and one for columns. Each enters a mode where clicking a key marks that side **unconnected** (MAT-019), with the same "Are you sure?" if the side is already connected.
- **GUI-022** [Decided] In the properties panel, a key's row and column can each be set, marked unconnected, or put back to **not yet connected**.
- **GUI-026** [Open, deferred] Whether the matrix toolbar gets "+" buttons for adding rows and columns. Until decided, they are added in the matrix settings.
- **GUI-023** [Decided] Escape, or choosing the active button again, leaves allocation mode.

## Snap-to-grid

- **GUI-006** [Decided] Snap-to-grid applies to **all placement by direct manipulation** (drag/drop, nudge) of any element: keys, controllers, and anything placed in future. It is **always on**; there is no toggle.
- **GUI-007** [Decided] The grid pitch (`units.grid`, a dimension value) is **user-selectable**, so a fine grid gives fine placement. For any position at all, use the properties panel (GUI-005).
- **GUI-008** [Decided] Snap is **never enforced by the schema** or by validation. Off-grid positions come from the properties panel or the JSON.
- **GUI-009** [Proposed] Snap never moves an element the user did not touch. Changing the grid does not re-snap existing elements.

## Moving placed parts

- **GUI-016** [Decided] Moving a **non-key** element (e.g. a controller) shows a **strong warning** that tracks connected to it on the PCB will be ripped up at the next PCB update (PCB-027). The move happens only once the user confirms. The API returns the same warning as a diagnostic (API-001).

- **GUI-025** [Decided] Moving a **key** also rips up the tracks connected to it at the next PCB update, but shows **no** strong warning: moving keys is the normal way of working.

## Reference image

- **GUI-010** [Proposed] The image file is referenced by path in `referenceImage`, not embedded. Calibration data lives alongside it.
- **GUI-011** [Decided] Calibration: the user clicks two points on the image and enters the real distance between them as a dimension value ([Q06](../decisions/Q06-reference-image-calibration.md)).

## Numbers in the UI

- **GUI-012** [Decided] The UI never computes a value that is stored in the design. Floating-point numbers may be used only for rendering. Edits are sent to the engine as intents (e.g. "move key K to the grid point nearest canvas point P", or "set x to the text `0.875` in `in`"); the engine computes the exact result (DIM-002, DIM-007).
- **GUI-013** [Proposed] During a drag the UI may show a local snap preview. On drop it sends one intent; the engine's result is authoritative, and the UI redraws from it.

## Extension points

**Add a GUI feature.** First add the API operation it needs ([07-api](07-api.md#extension-points)) and test it headlessly. Then add the GUI control that calls it. A GUI feature without an API operation will be rejected (GUI-001).

**Lift a UI restriction** (e.g. multi-select). This changes a [Decided] rule, so it needs a [feature proposal](../features/README.md).

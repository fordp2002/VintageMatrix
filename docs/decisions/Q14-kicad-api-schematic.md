# Q14: Schematic generation path

| | |
|---|---|
| **Status** | Decided |
| **Affects** | [14-kicad.md](../spec/14-kicad.md), [08-schematic.md](../spec/08-schematic.md), [09-pcb.md](../spec/09-pcb.md), [03-matrix.md](../spec/03-matrix.md) |
| **Decided** | 2026-11-19 |

## Context

VintageMatrix must not write KiCad files directly (KICAD-001); it uses KiCad's Python API. The official IPC API (`kicad-python`) covers the PCB editor. As of KiCad 10, no schematic editing commands have been found. KiCad's developer docs describe schematic support as future work, though one KiCad status presentation listed it as a KiCad 10 schematic feature. Headless operation (no GUI) is described as arriving in a later release.

Schematic generation (SCH-001), including creating a root sheet (SCH-008), therefore has no supported route today. This needs verifying against current KiCad release notes before deciding.

## Known constraints

- KICAD-001: no direct file writing.
- PRIN-004: the matrix stays the single source of truth whichever route is chosen.
- PRIN-003: the headless story should not get worse than necessary.

## Options

1. **PCB first, schematic later.** Ship the PCB generator now. Add schematic generation when KiCad's IPC API supports it, and set the minimum KiCad version to that release.
2. **Temporary, isolated exception.** Generate schematics by file writing until the API exists, confined to the KiCad adapter and marked for removal. This bends KICAD-001.
3. **Netlist route.** Produce the netlist from the matrix and feed it to the PCB, with no generated schematic until the API exists.
4. **Contribute upstream.** Help add schematic editing to KiCad's IPC API.

These candidates are not exhaustive and are not recommendations.

## Decision

**Option 3, refined: no netlist; board-only matrix joined by net name. Schematic generation is future work.**

How it works:

1. The user draws the schematic for everything VintageMatrix does not generate (MCU, connectors, interface circuitry, power), and labels the MCU's matrix pins with the row/column names from the design.
2. The user syncs it to the board with KiCad's normal *Update PCB from Schematic*.
3. The PCB generator places the matrix footprints (switches, diodes, …) through the KiCad API. It tags them (PCB-007) and marks them **Not in schematic**.
4. The generator assigns each pad to a net **by name**. The matrix row/column names are the shared contract (MAT-011).

No netlist file is produced or imported.

Reasoning:

- It works with what KiCad's Python API supports today (the PCB editor) and keeps KICAD-001 (no direct file writing).
- Each side owns its own parts: the user owns the schematic and its symbols, and VintageMatrix owns its tagged footprints. That matches SCH-003 and PCB-005.
- The board still shows one connected circuit, so DRC checks the full copper.
- The design file does not change when schematic generation arrives later.

Accepted costs:

- The matrix does not appear in the schematic, so ERC cannot check it. VintageMatrix's own check (PCB-017) partly covers this.
- Net names become a contract: renaming one on either side breaks the join, and validation reports it.

**Future work.** When KiCad's Python API supports schematic editing, the matrix moves into generated schematic sheets (08-schematic, Q13), the **Not in schematic** marking is dropped, and the PCB is synced from the schematic. The minimum KiCad version is tracked separately in [Q16](Q16-minimum-kicad-version.md).

**To verify before implementing** (KICAD-010): this decision rests on KiCad behaviour that has not yet been tested.

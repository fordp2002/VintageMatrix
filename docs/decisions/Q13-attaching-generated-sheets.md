# Q13: How generated sheets are attached to the schematic hierarchy

| | |
|---|---|
| **Status** | Deferred |
| **Affects** | [08-schematic.md](../spec/08-schematic.md) |
| **Decided** | — |

## Context

In KiCad, a sheet is only part of a schematic if a **sheet symbol** for it is placed on its parent sheet (ultimately, the root sheet). The generator never places design content on the root sheet (SCH-009) and never modifies an existing one (SCH-008). So something else has to place the sheet symbols for generated sheets.

## Known constraints

- SCH-003, SCH-008, SCH-009, SCH-010: the root sheet is user-owned and is never changed by the generator beyond the initial text note.
- SCH-006: generated sheets connect to user sheets only through documented hierarchical pins/labels.
- PRIN-003: the workflow must be possible headlessly.

## Options

1. **The user places the sheet symbols.** The generator reports which generated sheets exist and which aren't yet placed, and documents the pins each one exposes.
2. **Allow sheet symbols as an exception to SCH-009.** On a root sheet the generator has just created, it places one sheet symbol per generated sheet, once. After that the root sheet is user-owned.
3. **A generated intermediate sheet.** The user places a single sheet symbol (e.g. "VintageMatrix") on their root sheet. That points to one generated sheet, which contains the symbols for all the other generated sheets and is regenerated freely.
4. **Global labels only.** Generated sheets are separate top-level sheets in the project, connected by global labels rather than through the hierarchy. This depends on KiCad version support.

These candidates are not exhaustive and are not recommendations.

## Decision

**Deferred** (2026-11-19). Only needed once schematic generation is built (Q14 future work).

# 08 — Schematic generator (KiCad)

| | |
|---|---|
| **ID prefix** | `SCH` |
| **Owner** | Schematic generator |
| **Depends on** | [01-contract](01-contract.md), [03-matrix](03-matrix.md), [04-keys](04-keys.md), [11-provenance](11-provenance.md), [14-kicad](14-kicad.md) |
| **Related questions** | [Q04](../decisions/Q04-sheet-naming.md), [Q05](../decisions/Q05-provenance-contents.md), [Q13](../decisions/Q13-attaching-generated-sheets.md), [Q14](../decisions/Q14-kicad-api-schematic.md) |

> **Future work.** This generator is deferred until KiCad's Python API supports schematic editing (KICAD-006, [Q14](../decisions/Q14-kicad-api-schematic.md)). Meanwhile the user draws the schematic and the matrix joins it on the board by net name (PCB-015 to PCB-017). The rules below define how the generator must behave once it is built, and they still bind any work on it now.

All KiCad interaction goes through KiCad's Python API ([14-kicad](14-kicad.md)).

## Requirements

- **SCH-001** [Decided] Output is a **multi-sheet KiCad schematic**, one sheet per application/module (e.g. key matrix, controller, PS/2 interface, serial interface).
- **SCH-002** [Decided] Each generated sheet follows the sheet naming convention and carries a provenance block ([11-provenance](11-provenance.md)). The naming format is [Open] ([Q04](../decisions/Q04-sheet-naming.md)).
- **SCH-003** [Decided] The tool MUST NOT modify, move, or delete any sheet it did not generate.
- **SCH-004** [Proposed] A sheet counts as generated only if **both** its name matches the convention **and** it carries a valid provenance block.
- **SCH-005** [Proposed] If a sheet's name matches but its provenance block is missing or altered, the generator MUST refuse to overwrite it and report a diagnostic.
- **SCH-006** [Proposed] Generated sheets connect to user sheets only through documented hierarchical sheet pins/labels, so users can wire their own sheets to generated ones without editing them.
- **SCH-007** [Decided] Connectivity comes from the matrix (MAT-001).

## Root sheet

- **SCH-008** [Decided] If the KiCad project has no root sheet, the generator MAY create one. If a root sheet already exists, the generator MUST NOT modify it (SCH-003).
- **SCH-009** [Decided] A root sheet created by the generator contains **only** a text note telling the user that the sheet is theirs to edit as they wish. The generator places no components, wires, labels, or other design content on it.
- **SCH-010** [Decided] A root sheet belongs to the user from the moment it is created. It carries no provenance block, is not a generated artifact (PROV-001 and PROV-002 do not apply), and the generator never regenerates, overwrites, or deletes it.
- **SCH-011** [Proposed] The text note reads:
  > This root sheet was created by VintageMatrix and belongs to you. Edit it freely. VintageMatrix will never change it.
  > Sheets carrying a VintageMatrix provenance block are generated: do not edit those, as they are replaced on every regeneration.
- **SCH-012** [Open] How generated sheets become part of the hierarchy, given that the generator places nothing on the root sheet beyond the note ([Q13](../decisions/Q13-attaching-generated-sheets.md)).

## Extension points

**Add a generated sheet type** (e.g. a new interface circuit). Define it here with its name stem, its inputs (which config sections it reads), and the hierarchical pins it exposes (SCH-006). It is usually added together with an interface ([10-firmware](10-firmware.md#extension-points)) or a per-key module ([04-keys](04-keys.md#extension-points)).

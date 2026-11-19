# Q04: Sheet naming convention format

| | |
|---|---|
| **Status** | Deferred |
| **Affects** | [08-schematic.md](../spec/08-schematic.md), [11-provenance.md](../spec/11-provenance.md) |
| **Decided** | — |

## Context

Generated KiCad sheets are recognised by name and provenance block together (SCH-004). The name format must be unambiguous and unlikely to clash with names users choose.

## Known constraints

- Must work with KiCad's sheet name and file name rules.
- Must encode at least the module/application the sheet belongs to (SCH-001).

## Options

1. `vmx_<module>` (e.g. `vmx_matrix.kicad_sch`)
2. `VM-<module>-<design id>`

These candidates are not exhaustive and are not recommendations.

## Decision

**Deferred** (2026-11-19). Only needed once schematic generation is built (Q14 future work).

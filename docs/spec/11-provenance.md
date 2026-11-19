# 11 — Generated artifacts and provenance

| | |
|---|---|
| **ID prefix** | `PROV` |
| **Owner** | All generators |
| **Depends on** | [01-contract](01-contract.md) |
| **Related questions** | [Q04](../decisions/Q04-sheet-naming.md), [Q05](../decisions/Q05-provenance-contents.md), [Q15](../decisions/Q15-tagging-untaggable-pcb-items.md) |

## Requirements

- **PROV-001** [Decided] Generated artifacts are disposable. Deleting them and regenerating from the same design MUST produce an equivalent result.
- **PROV-002** [Decided] Generated artifacts MUST NOT be hand-edited. Customisation belongs in the design or in human-authored files that reference generated ones.
- **PROV-003** [Decided] Generated and human-authored files are kept in clearly separate locations.
- **PROV-004** [Proposed] The default output root for generated files is `generated/`, configurable via `generation.outputDir`. It is not committed by default. KiCad project files are not generated files: they live in the user's KiCad project (SCH-010, PCB-012).
- **PROV-005** [Decided] Every generated artifact carries a provenance block containing at least: the application name, the application version, and a "generated — do not edit" notice.
- **PROV-006** [Decided] The provenance block also contains a fingerprint (hash) of the design file. It contains no timestamp ([Q05](../decisions/Q05-provenance-contents.md)).
- **PROV-007** [Proposed] Generation is deterministic: the same design and app version give byte-identical output, apart from fields Q05 explicitly allows to vary.
- **PROV-008** [Decided] In files shared with the user (the KiCad board), provenance is tracked **per element** through the VintageMatrix field (PCB-007), not per file.

## Extension points

**Add a generator or artifact type.** It must satisfy PROV-001 to PROV-007 and state how its provenance block is embedded in its file format (comment, property, metadata field). See [12-architecture](12-architecture.md#extension-points) for adding the generator module itself.

# 14 — KiCad integration

| | |
|---|---|
| **ID prefix** | `KICAD` |
| **Owner** | KiCad adapter (used by schematic and PCB generators) |
| **Depends on** | [08-schematic](08-schematic.md), [09-pcb](09-pcb.md), [11-provenance](11-provenance.md) |
| **Related questions** | [Q14](../decisions/Q14-kicad-api-schematic.md), [Q15](../decisions/Q15-tagging-untaggable-pcb-items.md), [Q16](../decisions/Q16-minimum-kicad-version.md) |

## How VintageMatrix talks to KiCad

- **KICAD-001** [Decided] VintageMatrix **never reads or writes KiCad file formats directly**. All interaction with KiCad goes through KiCad's own Python API.
- **KICAD-002** [Decided] Why:
  1. KiCad's file formats change between releases. The Python API is expected to be more stable, so the project doesn't have to track format changes.
  2. The scripts are meant to be first-class for both humans and AI. Scripts that act the way a human would in the editor are much easier to read, review, and extend.
- **KICAD-003** [Decided] Scripts express KiCad work as **human-like steps** ("place footprint SW1 at (x, y)", "set its VintageMatrix field", "connect pad 1 to net ROW0"), not as bulk data transforms. Prefer clarity over cleverness: a reader should be able to follow a script as if watching someone use the editor.
- **KICAD-004** [Proposed] The API used is KiCad's **IPC API** through the official `kicad-python` binding. The older SWIG `pcbnew` bindings are deprecated by KiCad and are not used.
- **KICAD-005** [Proposed] All KiCad API calls go through one **KiCad adapter** module. Its functions are the human-like steps of KICAD-003. Generators call the adapter and never call `kicad-python` directly, so a KiCad API change is fixed in one place.

## Limits of the KiCad API

- **KICAD-006** [Decided] **Schematic generation is future work.** As of KiCad 10, the IPC API covers the PCB editor but not schematic editing. Until it does, VintageMatrix generates no schematic and produces no netlist; the matrix is delivered on the board only (PCB-015 to PCB-017). See [Q14](../decisions/Q14-kicad-api-schematic.md).
- **KICAD-007** [Decided] **The target is KiCad 10.** Support for other versions is to be decided. In principle, VintageMatrix works with any KiCad version whose API provides every API element it uses ([Q16](../decisions/Q16-minimum-kicad-version.md)).
- **KICAD-008** [Proposed] **Running KiCad.** The IPC API talks to a running KiCad instance. In KiCad 9/10 that means the GUI; a headless API server is expected in a later release. Until then, generation steps that touch KiCad need KiCad installed and running, including in CI and for AI agents. Everything that doesn't touch KiCad (validation, firmware, the rest of the API) stays fully headless (PRIN-003).
- **KICAD-009** [Proposed] When KiCad is not reachable, KiCad-dependent operations fail with a clear diagnostic (CON-013) and never fall back to writing files.
- **KICAD-010** [Decided] **Verify before building on it.** These KiCad behaviours are assumed by the PCB rules and MUST be confirmed against KiCad 10 (KICAD-007), with the results recorded here, before the PCB generator is implemented:
  1. The IPC API can add a footprint, set a custom field on it, and set its **Not in schematic** attribute.
  2. The IPC API can assign a pad to a net by name, including a net that already exists on the board.
  3. *Update PCB from Schematic* leaves **Not in schematic** footprints, and their pad nets, unchanged.
  4. A net created by the schematic sync and a pad assigned to it by the API are treated as one net by DRC.
  5. The API provides KiCad's call for ripping up the tracks connected to a footprint, and a way to rip up only the tracks of one affected connection (PCB-027, PCB-028).
  6. The API can export a Specctra DSN file and import a Specctra SES file (PCB-031).
  7. A rectangle can be drawn on the board-edge layer and locked through the API, and the board-edge layer's shapes can be listed and removed through the API (PCB-041, PCB-044).
  If any check fails, raise it against [Q14](../decisions/Q14-kicad-api-schematic.md) before working around it.

## Supported versions

- **KICAD-011** [Decided] The KiCad adapter keeps **one list of the API elements it uses**, each with the earliest KiCad version known to provide it. The supported minimum is the latest of those versions, and is stated in the table below.
- **KICAD-012** [Decided] A change that makes the adapter use a new API element MUST update that list, and MUST say in its PR (and in the spec changelog) if it raises the minimum version.
- **KICAD-013** [Proposed] On connecting, the adapter checks the running KiCad's version against the minimum, and where possible probes the API elements it needs. If they are not available, it fails with a diagnostic naming the missing element and the required version (KICAD-009), rather than failing partway through an update.
- **KICAD-014** [Proposed] Automated KiCad tests run against KiCad 10. Other versions are added once their support is decided.

| Supported minimum | Determined by | Verified |
|---|---|---|
| **KiCad 10** (target; other versions TBD) | KICAD-007 | KICAD-010 checks to be run on KiCad 10 |

## Extension points

**Add a KiCad operation.** Add a function to the KiCad adapter (KICAD-005), named and written as a human-like step (KICAD-003), with a test against a real KiCad instance where possible. If the API lacks what you need, raise it as a question rather than working around KICAD-001.

# Specification changelog

Newest first. One line per change, citing requirement IDs or question numbers.

| Version | Date | Change |
|---|---|---|
| 0.5 | 2026-11-19 | Licence decided: whole project GPL-2.0-or-later, mirroring QMK (LIC-001 to LIC-004), LICENSE added. Outline including cut-outs owned by VintageMatrix (PCB-044, KEY-016 decided; KEY-017 proposed). Target KiCad 10, other versions TBD (KICAD-007, KICAD-014, Q16). |
| 0.5 | 2026-11-19 | Answers applied: all managed footprints locked (PCB-045); key moves rip up without strong warning (GUI-025); PCB-023, FW-010, GUI-023 decided; never both row and column diodes (MAT-018); key diode placement is a default with per-key override (KEY-011); unconnected is per side (MAT-007, MAT-009, MAT-019, MAT-020, MAT-021, KEY-012, GUI-021, GUI-022, API-010); "+" buttons deferred (GUI-026); README marks schematic generation as awaiting KiCad support. |
| 0.5 | 2026-11-19 | Ownership rule restated as "only edit things that are ours" (PCB-005, PCB-007, PCB-011); the outline is drawn directly and recognised by VintageMatrix owning the board-edge layer (PCB-044 proposed; PCB-042 removed); KICAD-010 check 7 updated. |
| 0.5 | 2026-11-19 | Board outline: rectangle (standard) or DXF import (KEY-007, KEY-014 decided; KEY-015 proposed); outline managed, updated after routing, and locked in KiCad (PCB-006, PCB-041 decided; PCB-042, PCB-043 proposed); setOutline/importOutline operations; KICAD-010 check 7. |
| 0.5 | 2026-11-19 | Q15 decided: only tagged elements are generated; the only other action is ripping up connected tracks (PCB-011). |
| 0.5 | 2026-11-19 | Q12 decided: no unassigned-keycode marker; prompt before firmware generation (FW-018, COL-005); FW-019 `KC_NO` proposed. |
| 0.5 | 2026-11-19 | Decided Q03 (one minimal file: CON-003, CON-015), Q05 (PROV-006), Q06 (GUI-011), Q07 (rotation property, no z: KEY-003), Q08 (KEY-005, KEY-013, CON-016 automatic IDs), Q10 (FW-003), Q11 (COL-004). Deferred Q04, Q13, Q17, and the rest of Q09. KEY-011 and PCB-021 gain rotation; GUI-024 proposed. |
| 0.5 | 2026-11-19 | MAT-008 decided: keys may share a row and column only with the same keycode and layers; the `sharedWith` declaration is removed. |
| 0.5 | 2026-11-19 | Connecting rows and columns: not yet connected vs unconnected states (MAT-007, MAT-009, MAT-019 decided; MAT-020, MAT-021 proposed); MAT-022, MAT-023; matrix toolbar and allocation mode GUI-018 to GUI-022 (GUI-023 proposed); API-010 assignMatrix; KEY-010 amended, KEY-012 proposed. Q12 narrowed to keycodes. |
| 0.5 | 2026-11-19 | Diodes: project-level scheme none/key/row/column (MAT-005 reworked, MAT-015 direction); key and row/column diodes placed and connected (KEY-010, PCB-034, PCB-035, PCB-038, FW-017 decided); MAT-016 to MAT-018, KEY-011, PCB-036, PCB-037, PCB-039, PCB-040, GUI-017 proposed; PCB-022 widened to own switch/diode pads. |
| 0.5 | 2026-11-19 | Optional autorouting via Freerouting: PCB-029, ARCH-006 decided; PCB-030 to PCB-033, LIC-004 proposed; `route` operation; KICAD-010 check 6 (Specctra DSN/SES). Q09 routing part settled. |
| 0.5 | 2026-11-19 | Everything snaps (GUI-006 widened, GUI-007 decided, GUI-015 removed); GUI-016 rip-up warning for non-key moves; KEY-009 switch origin at stem centre, no offsets; PCB-026 placement by origin; PCB-027/PCB-028 rip-up rules; KICAD-010 check 5; PRIN-010 manage complexity. |
| 0.5 | 2026-11-19 | Controllers on the canvas: GUI-003, GUI-004, FW-016 amended; GUI-014, PCB-020, PCB-022, PCB-024 decided; GUI-015, PCB-021, PCB-023, PCB-025 proposed. Q07, Q09 constraints updated; Q17 raised. |
| 0.5 | 2026-11-19 | FW-015 decided: pin names MUST be exactly QMK's MCU pin names for modules and discrete MCUs; no translation. FW-014 no longer has a pin map; MAT-006 and Q10 updated. |
| 0.5 | 2026-11-19 | Controllers: modules and discrete MCUs supported (FW-011); firmware independent of the PCB (FW-012); FW-016, PCB-019 decided; FW-013 to FW-015 (controller kind, module definitions, pin labels) proposed. Q10 constraints updated. |
| 0.5 | 2026-11-19 | Keyboards without firmware: PRIN-009, MAT-013, KEY-008 decided; MAT-014, FW-010 proposed; MAT-010 scoped to designs with a controller; Q08/Q12 constraints updated. |
| 0.5 | 2026-11-19 | Q01 decided: positioning statement added to 00-overview and README; scope broadened to any matrix keyboard, with a non-standard focus; PRIN-008 (PCB and/or firmware) added. |
| 0.5 | 2026-11-19 | Q16 decided: supported KiCad versions are capability-based. KICAD-007 decided; KICAD-011 to KICAD-014 added. |
| 0.5 | 2026-11-19 | PCB-010 decided: user changes to tagged PCB elements are reverted on update; the design (via UI or file) is the only place to change them. |
| 0.5 | 2026-11-19 | Q14 decided: no netlist; matrix placed board-only and joined to the user's schematic by net name; schematic generation deferred. KICAD-006 decided; KICAD-010, PCB-015 to PCB-018, MAT-011, MAT-012 added; 08-schematic marked future work. Minimum KiCad version split out as Q16. |
| 0.5 | 2026-11-19 | KiCad integration: new 14-kicad.md (KICAD-001 to KICAD-009); never write KiCad files directly. PCB ownership by tagged elements and reconcile-on-update (PCB-005, PCB-007 to PCB-014); PROV-004 amended, PROV-008 added. Raised Q14, Q15. |
| 0.5 | 2026-11-19 | Root sheet rules: SCH-008, SCH-009, SCH-010 decided; SCH-011 proposed; SCH-012 open, raising Q13. |
| 0.5 | 2026-11-19 | Q02 decided: Tauri 2 (Rust) shell + TypeScript UI + Python engine. GUI-002 decided; added GUI-012, GUI-013, API-009, ARCH-005; API-005 updated. |
| 0.5 | 2026-11-19 | First written spec, consolidated from README and project agent guidance. Split into per-area files with requirement IDs and extension points. Open questions moved to `docs/decisions/` (Q01–Q12). |

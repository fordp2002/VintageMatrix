# Decisions and open questions

Each open question lives in its own file, so settling one is a small, reviewable change that won't conflict with other work.

| # | Question | Status |
|---|---|---|
| [Q01](Q01-positioning.md) | One-sentence positioning of the app | **Decided** |
| [Q02](Q02-app-stack.md) | App stack and GUI choice | **Decided**: Tauri 2 + TypeScript UI + Python engine |
| [Q03](Q03-config-layout.md) | Config layout: single JSON vs. root + fragments | **Decided**: one minimal file |
| [Q04](Q04-sheet-naming.md) | Sheet naming convention format | Deferred |
| [Q05](Q05-provenance-contents.md) | Provenance block contents | **Decided**: + design fingerprint, no timestamp |
| [Q06](Q06-reference-image-calibration.md) | Reference-image calibration method | **Decided**: two points + known distance |
| [Q07](Q07-rotation-z.md) | Rotation / z in the position model | **Decided**: rotation property, no z |
| [Q08](Q08-key-field-defaults.md) | Required vs. optional key fields and defaults | **Decided**: id automatic; position only for PCB |
| [Q09](Q09-autoplacement-routing.md) | Auto-placement scope; routing in scope? | Partly decided (routing, diodes); rest deferred |
| [Q10](Q10-qmk-board-selection.md) | QMK board selection mechanism | **Decided**: QMK board name, else MCU + bootloader |
| [Q11](Q11-collection-matrix-addressing.md) | Matrix addressing when inserting collections | **Decided**: insert as-is; validation reports problems |
| [Q12](Q12-unassigned-keycode.md) | Explicit "unassigned" keycode | **Decided**: no marker; prompt before firmware generation |
| [Q13](Q13-attaching-generated-sheets.md) | How generated sheets are attached to the hierarchy | Deferred |
| [Q14](Q14-kicad-api-schematic.md) | Schematic generation path | **Decided**: no netlist; board-only matrix joined by net name; schematic generation is future work |
| [Q15](Q15-tagging-untaggable-pcb-items.md) | Tagging PCB elements that can't carry fields | **Decided**: we only generate tagged things |
| [Q16](Q16-minimum-kicad-version.md) | Minimum supported KiCad version | **Decided**: any version providing the API elements used |
| [Q17](Q17-connecting-other-pins.md) | Connecting controller pins beyond the matrix | Deferred |

## Rules for contributors and AI agents

- **Statuses:** *Open* means not yet decided. *Deferred* means postponed on purpose until it is needed. *Decided* means settled. Neither Open nor Deferred questions may be resolved silently in code.

- **Do not silently resolve an open question** in code. If your work depends on one, follow the interim rule in the spec, and say in your PR which question you hit.
- **To propose an answer**, open a PR that edits only that question's file, adding options, evidence, or a recommendation under a `## Discussion` heading.
- **A question is decided** by a maintainer. The deciding PR fills in `## Decision`, sets Status to `Decided`, updates every spec file listed under **Affects** (turning `[Open]` into `[Decided]`), updates the table above, and adds a line to the [spec changelog](../spec/CHANGELOG.md).
- **New questions** take the next number (`Q13`, …) and use [TEMPLATE.md](TEMPLATE.md). Add them to the table above.
- Decided questions stay in this folder as the record of why things are the way they are.

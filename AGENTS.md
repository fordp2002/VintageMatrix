# AGENTS.md

Guidance for AI coding agents (Claude Code, Codex, Copilot, Cursor, Aider, …) and the humans directing them. Tool-specific files (e.g. `CLAUDE.md`) import this file rather than duplicating it. Put project guidance **here**.

## What this project is

**VintageMatrix** is an app and reusable framework for designing **nonstandard input devices**, focused on **8-bit/16-bit era home computers** and **serial terminals**. The design screen is the human-facing interface; the same framework is fully drivable by AI through a JSON configuration contract.

This project is **early-stage and AI-coded**. Expect incomplete areas, and expect the spec to change. When in doubt, ask rather than assume.

## Repository map

| Path | What lives there |
|---|---|
| [`docs/spec/`](docs/spec/README.md) | The specification, one file per area. **Start at its README.** |
| [`docs/decisions/`](docs/decisions/README.md) | Open questions and decision records, one file each. |
| [`docs/features/`](docs/features/README.md) | Feature proposals for changes too big for a single PR. |
| `schema/` | JSON Schema for the configuration contract. *(Not yet created.)* |
| `collections/` | Reusable key collections, one design file each. *(Not yet created.)* |
| `controllers/` | Controller module definitions (name, MCU, QMK `development_board`, optional footprint), one file each. *(Not yet created.)* |
| `generated/` | Generator output. Disposable; never hand-edit, never commit by default. *(Not yet created.)* |

## Source of truth

The **specification** in [`docs/spec/`](docs/spec/README.md) (currently v0.5, provisional) is the authority on design intent.

- If a request contradicts the spec, **flag the conflict before implementing**.
- If the spec is silent, prefer the option most consistent with the guiding principles below, and **note the assumption** in your PR.
- Requirements have stable IDs (e.g. `DIM-002`). Cite them in commits, PRs, tests, and diagnostics.

## Guiding principles: do not violate these

1. **Modular**: every module must work standalone *and* in conjunction with others. No hidden cross-module dependencies.
2. **Build on, not fork** the upstream projects:
   - `hsgw/keyboard-made-by-python` (design/hardware layer, MIT)
   - `BuzzL123/QMK-PS2-USB-Dual-Mode-Keyboard` (firmware/protocol layer, GPL-2.0)
   Keep changes pullable from upstream. Do not copy upstream code into this repo; extend or wrap it.
3. **Human + AI framework**: everything the GUI can do must be callable headlessly. API-first, schema-validated, no GUI-only logic.
4. **The matrix is the spine**: one source of truth flows to schematic, PCB, and firmware. Do not create parallel models.
5. **Generated artifacts are disposable**: provenance-stamped, regenerable, never hand-edited.
6. **Configuration is a single JSON contract** through which modules interact.
7. **Physical dimensions are unit-aware and losslessly stored.**

## Non-negotiable technical rules

- **Never store physical dimensions as JSON numbers.** Use exact text values with units: `{"value": "0.75", "unit": "in"}`. Use `decimal`/`Fraction` for arithmetic; mm conversion happens at export only. (DIM-001, DIM-002, DIM-007, DIM-010)
- **Key positions are the switch stem centre, not the switch body.** Module geometry is placed relative to the stem centre so modules can be swapped without moving keys. (KEY-001, KEY-002)
- **Mixed units are permitted** within one design. Do not normalise to a single unit on load. (DIM-008)
- **Preserve unknown JSON fields** on round-trip. Never silently drop config keys you don't recognise. (CON-005)
- **Never read or write KiCad files directly.** Use KiCad's Python API through the KiCad adapter, with scripts written as human-like steps. (KICAD-001, KICAD-003, KICAD-005)
- **The tool only edits things that are its own.** It never modifies KiCad sheets it did not generate. On the PCB its own things are its tagged footprints and the board outline (VintageMatrix owns the board-edge layer); its only other action is ripping up tracks connected to its parts. (SCH-003, PCB-005, PCB-011, PCB-044)
- **Everything snaps to the (user-selectable) grid** when placed by direct manipulation; the properties panel places anywhere. Snap is never enforced by the schema. (GUI-006, GUI-007, GUI-008)
- **Everything is placed by its footprint origin; switch footprint origins MUST be at the stem centre.** (PCB-026, KEY-009)
- **Single selection only** in the current UI iteration. (GUI-004)
- **Target any QMK-supported microcontroller.** Do not hard-code RP2040 pinouts. (FW-002, MAT-006)

## Adding a feature: the workflow

1. **Locate.** Open [`docs/spec/README.md`](docs/spec/README.md) and use its *reading map* to find the one or two spec files your change touches. You do not need to read the whole spec.
2. **Check fit.** Does the change use an existing **extension point** (each spec file lists its own)? If yes, go to step 4.
3. **Propose if big.** Write a feature proposal ([`docs/features/TEMPLATE.md`](docs/features/TEMPLATE.md)) *before* coding if the change:
   - adds a new top-level config section or a new module,
   - changes or removes a `[Decided]` requirement,
   - touches an `[Open]` question (see [`docs/decisions/`](docs/decisions/README.md)), or
   - would be expensive to reverse (contract shape, unit handling, ownership rules, licensing).
4. **Change spec, schema, and tests together, in one PR**:
   - Spec: add requirements with the next free ID in that area's file. Never renumber or reuse IDs.
   - Schema: update `schema/` for any new element type or config field.
   - Tests: cover each new requirement and reference its ID.
   - Add a line to [`docs/spec/CHANGELOG.md`](docs/spec/CHANGELOG.md).
5. **Keep it small.** One feature per PR. Prefer schema-first changes over broad refactors while the spec is in flux.
6. **Fill in the PR template.** It is the definition of done.

## Open questions

These are tracked one per file in [`docs/decisions/`](docs/decisions/README.md) (Q01–Q17). **Do not silently resolve them.** If your work depends on one, say so in the PR and follow the interim rule in the spec. If you want to settle one, open a PR against that decision file only.

## Licensing

Expected to be **per-module**: MIT on the design side, GPL-2.0 on the firmware side. This is **not yet final**. Do not add or change license headers without checking. Firmware-side code derived from the GPL-2.0 upstream inherits GPL-2.0.

## Working style

- **Manage complexity (PRIN-010).** Prefer one simple rule over options, modes, and special cases. Don't add configurability nobody asked for.

- Keep generated and human-authored artifacts in clearly separate locations.
- If you add a new element type or config field, update the JSON schema **and** the spec.
- Flag any decision that would be expensive to reverse.
- Mark anything you decided without the spec's backing as `[Proposed]` in the spec, not `[Decided]`.

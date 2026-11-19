# VintageMatrix Specification

| | |
|---|---|
| **Version** | 0.5 (provisional) |
| **Status** | Draft. Breaking changes expected. |
| **History** | [CHANGELOG.md](CHANGELOG.md) |

This specification is the authority on VintageMatrix design intent. Code, schemas, and docs that disagree with it are wrong, or the spec needs amending. Either way, **raise the conflict** rather than silently picking a side.

The spec is split into one file per area so that a contributor (human or AI) only needs to load the parts relevant to their change.

## Reading map

Always read [00-overview.md](00-overview.md) (short). Then:

| If you are working on… | Read |
|---|---|
| Config loading, saving, validation, a new config field | [01-contract.md](01-contract.md) |
| Anything with a length, position, or size | [02-dimensions.md](02-dimensions.md) |
| Rows, columns, diodes, pins, scanning | [03-matrix.md](03-matrix.md) |
| Key fields, positions, per-key modules, PCB outline | [04-keys.md](04-keys.md) |
| Key collections / presets of vintage layouts | [05-collections.md](05-collections.md) |
| The GUI: canvas, selection, snapping, reference image | [06-design-screen.md](06-design-screen.md) + [07-api.md](07-api.md) |
| Headless API, CLI, diagnostics | [07-api.md](07-api.md) |
| KiCad schematic output | [08-schematic.md](08-schematic.md) + [14-kicad.md](14-kicad.md) |
| KiCad PCB output | [09-pcb.md](09-pcb.md) + [14-kicad.md](14-kicad.md) |
| QMK firmware, USB / PS/2 / serial interfaces | [10-firmware.md](10-firmware.md) |
| Anything that talks to KiCad | [14-kicad.md](14-kicad.md) |
| Module boundaries, a new generator, upstream, licensing | [12-architecture.md](12-architecture.md) |
| Tests and conformance | [13-conformance.md](13-conformance.md) |

## File structure

Every area file has the same shape:

1. **Header table**: requirement ID prefix, owning module, files it depends on, related open questions.
2. **Requirements**: numbered, labelled rules.
3. **Extension points**: how to add features in this area without a spec redesign.

## Status labels

| Label | Meaning |
|---|---|
| **[Decided]** | Settled design intent. Change only by amending the spec (via a feature proposal). |
| **[Proposed]** | A reasonable default written down so work can proceed. May be relied on, but open to challenge; cheap to change. |
| **[Open]** | Explicitly undecided. Implementations **MUST NOT** silently resolve it. See [../decisions/](../decisions/README.md). |

**MUST / MUST NOT / SHOULD / MAY** are used as in RFC 2119.

## Requirement IDs

Each requirement has an ID of the form `AREA-NNN`, e.g. `DIM-002`.

| Prefix | File |
|---|---|
| `PRIN` | [00-overview.md](00-overview.md) |
| `CON` | [01-contract.md](01-contract.md) |
| `DIM` | [02-dimensions.md](02-dimensions.md) |
| `MAT` | [03-matrix.md](03-matrix.md) |
| `KEY` | [04-keys.md](04-keys.md) |
| `COL` | [05-collections.md](05-collections.md) |
| `GUI` | [06-design-screen.md](06-design-screen.md) |
| `API` | [07-api.md](07-api.md) |
| `SCH` | [08-schematic.md](08-schematic.md) |
| `PCB` | [09-pcb.md](09-pcb.md) |
| `FW` | [10-firmware.md](10-firmware.md) |
| `PROV` | [11-provenance.md](11-provenance.md) |
| `ARCH`, `UP`, `LIC` | [12-architecture.md](12-architecture.md) |
| `TEST` | [13-conformance.md](13-conformance.md) |
| `KICAD` | [14-kicad.md](14-kicad.md) |

Rules for IDs:

- IDs are **permanent**. Never renumber, never reuse.
- New requirements take the **next free number** in their area.
- To remove a requirement, keep its line, strike it through, and mark it `[Removed in vX.Y]`.
- **Cite IDs** in commit messages, PR descriptions, test names or docstrings (`Covers: DIM-004`), and validation diagnostics.

## Changing the spec

See the workflow in [`AGENTS.md`](../../AGENTS.md#adding-a-feature-the-workflow). In short: small changes that use an extension point go straight into a PR with spec + schema + tests; larger ones start as a [feature proposal](../features/README.md).

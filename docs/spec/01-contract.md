# 01 — Configuration contract

| | |
|---|---|
| **ID prefix** | `CON` |
| **Owner** | Core model |
| **Depends on** | [02-dimensions](02-dimensions.md) |
| **Related questions** | [Q03](../decisions/Q03-config-layout.md) |

## Format and versioning

- **CON-001** [Proposed] A design is described in JSON, validated against a published JSON Schema (draft 2020-12) kept in `schema/`.
- **CON-002** [Decided] Every configuration document carries a `schemaVersion` string (semver). Readers MUST reject a major version they do not understand, and SHOULD accept a newer minor version (preserving unknown fields per CON-005).
- **CON-003** [Decided] A design is **one JSON file** ([Q03](../decisions/Q03-config-layout.md)).
- **CON-015** [Decided] **The file holds only what is needed.** Software MUST NOT write empty sections, empty lists, or default values the user did not set. Values the user did set are kept as written, even if they equal a default (CON-006).

## Logical structure

- **CON-004** [Proposed] The document has these top-level sections:

```json
{
  "schemaVersion": "0.5.0",
  "design": { "name": "C64 replica", "description": "Breadbin layout, USB + PS/2" },
  "units": {
    "grid": { "value": "0.25", "unit": "in" },
    "keyUnit": { "value": "0.75", "unit": "in" }
  },
  "outline": {},
  "referenceImage": {},
  "matrix": {},
  "keys": [],
  "controller": {},
  "interfaces": [],
  "generation": {}
}
```

| Section | Owner module | Specified in |
|---|---|---|
| `design` | Core | This file. Name, description, free-form metadata. |
| `units` | Core | [02-dimensions](02-dimensions.md), [06-design-screen](06-design-screen.md) |
| `outline` | Core / PCB | [04-keys](04-keys.md#pcb-outline) |
| `referenceImage` | Design screen | [06-design-screen](06-design-screen.md#reference-image) |
| `matrix` | Core | [03-matrix](03-matrix.md) |
| `keys` | Core | [04-keys](04-keys.md) |
| `controller` | Firmware (selection, pins); PCB (placement) | [10-firmware](10-firmware.md), [09-pcb](09-pcb.md#controllers-on-the-pcb) |
| `interfaces` | Firmware | [10-firmware](10-firmware.md#interfaces) |
| `generation` | Generators | [11-provenance](11-provenance.md), [08](08-schematic.md), [09](09-pcb.md) |

- **CON-005** [Decided] Implementations MUST preserve every JSON member they do not recognise, at any depth, including inside arrays of objects. Unknown config keys are never silently dropped.
- **CON-006** [Proposed] Implementations SHOULD preserve member order and SHOULD NOT reformat values they did not change, so diffs stay minimal.
- **CON-007** [Proposed] Vendor or experimental fields use an `x-` prefix (e.g. `"x-myfork-ledColour"`). The schema MUST permit them everywhere.
- **CON-008** [Proposed] A module reads only the sections it needs and MUST tolerate the absence of sections it does not own. A missing optional section means "nothing to do", not an error.

## Identifiers

- **CON-009** [Proposed] Every key, and every other object referenced from elsewhere in the document, has an `id` string unique within the design.
- **CON-016** [Decided] **IDs are assigned automatically** by the engine when an object is created, or when a design is loaded with objects lacking one. Nobody has to invent IDs. Once assigned, an ID is kept (CON-010).
- **CON-010** [Proposed] IDs are opaque and stable. Changing a legend or moving a key MUST NOT change its ID.
- **CON-011** [Proposed] Cross-references use IDs, never array indices.

## Validation

- **CON-012** [Proposed] Validation has two levels: **schema** (structure and types, the same schema for every module and the API) and **semantic** (rules the schema cannot express, e.g. duplicate matrix addresses, matrix too large for the controller).
- **CON-013** [Proposed] Each diagnostic has a severity (`error` refuses generation, `warning` does not), a stable machine-readable `code`, a JSON Pointer to the offending location, a human message, and, where applicable, the requirement ID it enforces.
- **CON-014** [Decided] Snap-to-grid is never a validation rule (see GUI-008).

## Extension points

**Add a field to an existing section.** Add it to the owning area's spec file with a new ID, add it to `schema/`, and state its default when absent. Old readers will preserve it (CON-005), so this is a minor version bump.

**Add a top-level section.** Requires a [feature proposal](../features/README.md). Name the owner module, add a row to the table above, give the section its own schema file, and ensure every other module ignores it safely (CON-008).

**Add a semantic check.** Add the rule as a requirement in the area it protects, give the diagnostic a `code`, and add a test that triggers it.

**Experiment without a spec change.** Use `x-` fields (CON-007). Promote them to real fields through the normal process once they prove useful.

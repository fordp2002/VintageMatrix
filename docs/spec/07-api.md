# 07 — Headless API

| | |
|---|---|
| **ID prefix** | `API` |
| **Owner** | Core model |
| **Depends on** | [01-contract](01-contract.md) |
| **Related questions** | [Q02](../decisions/Q02-app-stack.md) |

## Requirements

- **API-001** [Decided] Every operation available in the GUI is available headlessly, with identical semantics and validation.
- **API-002** [Proposed] Operations take and return contract-conformant JSON.
- **API-003** [Proposed] Errors and warnings use the diagnostic format in CON-013, so AI agents can act on them without parsing prose.
- **API-004** [Proposed] Operations are pure with respect to the design: input design + parameters → output design + diagnostics. Undo/redo is built on this by the GUI.

## Surfaces

- **API-005** [Proposed] There are two surfaces: a **library API** in the core implementation language, and a **CLI** (`vmx`) wrapping it one-to-one. The engine language is Python ([Q02](../decisions/Q02-app-stack.md)).
- **API-006** [Proposed] CLI output is JSON by default (`--format text` for humans). Exit code is 0 on success, 1 when there are validation errors, and 2 on usage errors.
- **API-007** [Proposed] A network service is later work and, if added, wraps the library API without adding operations of its own.
- **API-009** [Proposed] **Engine protocol.** The engine runs as a separate process and speaks JSON-RPC 2.0 over stdio. Method names are the operation names in API-008, and parameters/results follow API-002. The desktop shell starts and stops the engine; the UI talks to it only through this protocol. The same protocol is usable by any other client (scripts, AI agents).

## Operations

- **API-008** [Proposed] The core operations are:

| Operation | CLI | Effect |
|---|---|---|
| `load` / `save` | — | Read/write a design, preserving unknown fields (CON-005). |
| `validate` | `vmx validate <design>` | Schema + semantic validation. |
| `addKey` / `updateKey` / `removeKey` | `vmx key add|update|remove` | Key CRUD. `snap: true` applies grid snapping (GUI-006); the GUI always sets it for direct manipulation. |
| `insertCollection` | `vmx collection insert` | [05-collections](05-collections.md). |
| `setMatrix` / `assignMatrix` | `vmx matrix set|assign` | [03-matrix](03-matrix.md). `assignMatrix` is specified in API-010. |
| `setReferenceImage` / `calibrateReferenceImage` | `vmx image set|calibrate` | [06-design-screen](06-design-screen.md#reference-image). |
| `setOutline` / `importOutline` | `vmx outline set|import <file.dxf>` | Rectangle outline, or DXF import (KEY-007, KEY-014). |
| `route` | `vmx route` | Optional autorouting through Freerouting ([09-pcb](09-pcb.md#autorouting-optional)). |
| `generate` | `vmx generate schematic|pcb|firmware` | Run generators ([08](08-schematic.md), [09](09-pcb.md), [10](10-firmware.md)). |

- **API-010** [Proposed] `assignMatrix(keyId, axis, value, replace?)`: `axis` is `row` or `col`; `value` is a row/column ID, `"unconnected"`, or `null` for *not yet connected*. If that side already has a different value, the operation fails with code `MATRIX_ALREADY_ASSIGNED` unless `replace: true`. The GUI's "Are you sure?" (GUI-020) is the user's answer to that error.

## Extension points

**Add an operation.** Add a row to API-008, define its parameters in `schema/` (operation parameters are schema-validated too), implement it in the library, expose it in the CLI with the same name, and test it headlessly. It must follow API-004.

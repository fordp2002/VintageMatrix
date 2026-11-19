# 05 — Key collections

| | |
|---|---|
| **ID prefix** | `COL` |
| **Owner** | Core model |
| **Depends on** | [01-contract](01-contract.md), [03-matrix](03-matrix.md), [04-keys](04-keys.md) |
| **Related questions** | [Q11](../decisions/Q11-collection-matrix-addressing.md), [Q12](../decisions/Q12-unassigned-keycode.md) |

## Rules

- **COL-001** [Decided] A key collection is **itself a saved design** using the same contract. There is no separate collection format.
- **COL-002** [Proposed] Inserting a collection copies its keys into the target design. The copies get new IDs, a positional offset, and a `collection` back-reference (`{ "source": "<collection id or path>", "version": "<schemaVersion or revision>", "instance": "<instance id>" }`).
- **COL-003** [Proposed] After insertion the keys are ordinary keys. Editing them does not change the source collection, and changing the source does not change existing instances.
- **COL-004** [Decided] Inserted keys keep whatever rows/columns the collection carries; problems are reported by normal validation. No offsetting or remapping ([Q11](../decisions/Q11-collection-matrix-addressing.md)).
- **COL-005** [Decided] There is no "unassigned" keycode marker. A key with no keycode simply has no `keycode` field ([Q12](../decisions/Q12-unassigned-keycode.md), FW-018).
- **COL-006** [Decided] Insertion is a single API operation (`insertCollection`, [07](07-api.md)), exposed in the GUI as a single drag.

## Library

- **COL-007** [Proposed] Shipped collections live in `collections/`, one design file each, named `<kebab-case-name>.json` (e.g. `c64-main-block.json`). Each file MUST validate against the schema and SHOULD fill in `design.description` with its source (machine, model/revision, reference used).

## Extension points

**Add a vintage preset or collection.** This is the easiest contribution: add one valid design file to `collections/` (COL-007) plus a test that loads and validates it. It needs no spec change. If the preset needs a field or topology the spec lacks, propose that separately first.

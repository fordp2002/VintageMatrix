# 13 — Conformance and testing

| | |
|---|---|
| **ID prefix** | `TEST` |
| **Owner** | All modules |
| **Depends on** | All |
| **Related questions** | [Q05](../decisions/Q05-provenance-contents.md) |

## Traceability

- **TEST-001** [Proposed] Every test states the requirement IDs it covers (in its name, docstring, or a `Covers:` comment). A requirement with no covering test is reported as uncovered.
- **TEST-002** [Proposed] Test fixtures are design files under each module's `tests/fixtures/`. Fixtures shared across modules live in `tests/fixtures/` at the root.

## Required conformance suites

An implementation conforms when it passes:

- **TEST-003** **Round-trip**: load → save of any valid design, including unknown and `x-` fields, gives semantically identical JSON with member order preserved (CON-005, CON-006).
- **TEST-004** **Precision**: imperial dimensions survive load/save/move exactly, including accumulated 0.1 in steps over long runs, and export to mm within DIM-011 (DIM-001 to DIM-011).
- **TEST-005** **Mixed units**: a design mixing `in`, `mm`, `mil`, and `u` is not normalised (DIM-008).
- **TEST-006** **Ownership**: regenerating a schematic containing user-authored sheets leaves those sheets byte-identical (SCH-003, SCH-005).
- **TEST-007** **Headless parity**: every GUI action has a matching API operation test (API-001, GUI-001).
- **TEST-008** **Determinism**: generating twice from one design gives identical output (PROV-007).

## Extension points

**Adding any requirement** means adding at least one test that covers it, or noting in the PR why it cannot be tested automatically.

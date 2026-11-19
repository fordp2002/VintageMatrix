# 02 — Dimension values

| | |
|---|---|
| **ID prefix** | `DIM` |
| **Owner** | Shared dimension library |
| **Depends on** | — |
| **Related questions** | — |

Every module uses dimension values, so this file is short and strict.

## Representation

- **DIM-001** [Decided] A physical length is a JSON object holding an exact decimal **string** and a unit: `{ "value": "0.75", "unit": "in" }`.
- **DIM-002** [Decided] Dimensions MUST NOT be stored as JSON numbers anywhere in the contract. IEEE-754 doubles cannot represent common imperial values exactly.
- **DIM-003** [Proposed] `value` matches `^-?(0|[1-9][0-9]*)(\.[0-9]+)?$`. No exponent, no leading `+`, no thousands separators.
- **DIM-004** [Proposed] Rational values such as `"3/4"` are not allowed in 0.5. They can be added in a minor version without breaking readers.
- **DIM-005** [Proposed] Readers MUST NOT rewrite `value` (e.g. `"0.750"` → `"0.75"`) unless the user changed it. Trailing zeros may record drawing precision.

## Units

- **DIM-006** [Proposed] The permitted units are:

| Unit | Meaning | Exact definition |
|---|---|---|
| `mm` | millimetre | (base) |
| `in` | inch | 25.4 mm |
| `mil` | thousandth of an inch | 0.0254 mm |
| `u` | key unit | `units.keyUnit` (DIM-012) |

## Arithmetic and conversion

- **DIM-007** [Decided] All arithmetic uses exact types (`decimal` with sufficient precision, or `Fraction`), never binary floating point.
- **DIM-008** [Decided] Mixed units are permitted within one design, even within one object. Loading MUST NOT normalise units.
- **DIM-009** [Proposed] An operation that changes a value keeps that value's existing unit (e.g. nudging an `in` coordinate by a `mm` grid step gives an `in` result), as long as the result is exactly representable in that unit as a finite decimal. Otherwise the result takes the unit of the other operand.
- **DIM-010** [Decided] Conversion to millimetres happens **only at export** (KiCad, rendering, etc.).
- **DIM-011** [Proposed] Export rounds to the target format's resolution (KiCad: 1 nm) using round-half-even. Rounding never feeds back into the stored design.

## Key unit

- **DIM-012** [Proposed] `units.keyUnit` defines `1u` for the design. If it is absent the default is `0.75 in` (19.05 mm). A `u` value resolves exactly to the underlying unit when used; the stored value stays in `u`.

## Implementation

- **DIM-013** [Proposed] One shared dimension library implements DIM-001 to DIM-012. All modules use it rather than parsing dimensions themselves. It has no dependencies beyond the language's exact-arithmetic support.

## Extension points

**Add a unit.** It needs an exact rational definition in millimetres. Add a row to DIM-006, add it to the schema enum, and add round-trip and conversion tests. Units that need a per-design definition (like `u`) also need a field under `units` with a default.

**Allow a new value syntax** (e.g. fractions). Write a feature proposal. It changes DIM-003/DIM-004 and every module's parsing, which DIM-013 keeps in one place.

# Q11: Matrix addressing when inserting key collections

| | |
|---|---|
| **Status** | Decided |
| **Affects** | [05-collections.md](../spec/05-collections.md), [03-matrix.md](../spec/03-matrix.md) |
| **Decided** | 2026-11-19 |

## Context

A collection brings its own row/column addresses, which may collide with or not fit the target design's matrix.

## Known constraints

- Insertion is a single operation (COL-006).
- Keys may share an address only if they have the same keycode (MAT-008).
- Collections are normally stored with rows/columns not yet connected, so collisions arise only for collections that carry them (MAT-023).

## Options

1. Offset rows/cols by a user-given amount
2. Remap through a user-given table
3. Insert unassigned and warn
4. Insert as-is and report collisions

These candidates are not exhaustive and are not recommendations.

## Decision

**Keep it simple: insert as-is.** A collection's keys keep whatever rows/columns they carry. Any resulting problem (e.g. two keys on one address with different keycodes, MAT-008) is reported by normal validation. There is no offsetting or remapping, since most collections carry no rows/columns anyway (MAT-023).

# Q03: Config layout: single JSON file vs. root + module fragments

| | |
|---|---|
| **Status** | Decided |
| **Affects** | [01-contract.md](../spec/01-contract.md), [05-collections.md](../spec/05-collections.md) |
| **Decided** | 2026-11-19 |

## Context

One file is simpler for AI tools and diffs. Fragments let modules (and collections) be shared and edited independently.

## Known constraints

- The logical model must be the same either way (CON-003).
- Unknown-field preservation must hold across fragments (CON-005).

## Options

1. Single file only
2. Root file with `$ref`-style includes to fragments
3. Single file, with fragments only as an import/export format

These candidates are not exhaustive and are not recommendations.

## Decision

**One design file, containing only what is needed.**

- A design is a single JSON file. (Key collections and controller module definitions are separate files of their own kind; they are not fragments of a design.)
- The file holds only what the design actually uses. Software never writes empty sections, empty lists, or default values the user did not set. A firmware-only design, for example, has no `outline` at all.

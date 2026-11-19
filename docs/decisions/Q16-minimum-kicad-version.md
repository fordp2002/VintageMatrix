# Q16: Minimum supported KiCad version

| | |
|---|---|
| **Status** | Decided |
| **Affects** | [14-kicad.md](../spec/14-kicad.md) |
| **Decided** | 2026-11-19 |

## Context

VintageMatrix uses KiCad's IPC API (KICAD-004), which first appeared in KiCad 9. The PCB features it depends on (KICAD-010) and the later schematic support (Q14 future work) may need newer versions. Headless operation (KICAD-008) is expected in a later release still.

## Known constraints

- KICAD-001: everything goes through the API, so the minimum version is whatever provides the API features used.
- KICAD-010: the verification results decide which version is actually sufficient.

## Options

1. KiCad 9 (first IPC API release)
2. KiCad 10
3. The first release whose API passes every KICAD-010 check

These candidates are not exhaustive and are not recommendations.

## Decision

**Capability-based: VintageMatrix supports any KiCad version whose API provides every API element VintageMatrix uses.** There is no fixed version number; the minimum is derived from what the KiCad adapter actually calls.

Consequences (KICAD-007, KICAD-011, KICAD-012, KICAD-013):

- The minimum version is whatever the KICAD-010 verification and later adapter work establish, recorded in one place in 14-kicad.
- Using a new API element can raise the minimum. That is allowed, but the PR must say so.
- At connect time the adapter checks that the running KiCad provides what it needs, and fails with a clear diagnostic if not.

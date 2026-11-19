# Q15: Tagging PCB elements that cannot carry custom fields

| | |
|---|---|
| **Status** | Decided |
| **Affects** | [09-pcb.md](../spec/09-pcb.md), [14-kicad.md](../spec/14-kicad.md) |
| **Decided** | 2026-11-19 |

## Context

Every PCB element VintageMatrix adds must carry a VintageMatrix field (PCB-007). In KiCad, custom fields belong to footprints. As far as is known, tracks, vias, zones, and graphic shapes cannot carry custom fields. This needs checking against the current IPC API.

This matters once VintageMatrix adds anything other than footprints: board outline (PCB-006), routing (Q09), mounting holes, keep-outs.

## Known constraints

- PCB-005: never touch untagged elements.
- PCB-009: managed elements must be identifiable for add/update/remove.
- KICAD-001: through the API only.

## Options

1. **Footprints only.** Anything else VintageMatrix needs (outline segments, mounting holes) is delivered as a footprint, which can carry the field. No generated tracks.
2. **KiCad groups.** Put untaggable elements in a group whose name carries the tag.
3. **Registry of KiCad IDs.** Record the KiCad UUID of each managed element in a VintageMatrix file in the project, keyed by tag value.
4. **Naming conventions.** Use zone names, net names, or similar where KiCad provides them.

These candidates are not exhaustive and are not recommendations.

## Decision

**Not a problem in practice: everything VintageMatrix generates is tagged.**

- The generator only creates things that carry the VintageMatrix field (footprints). It creates nothing untaggable.
- Its only action on anything else is **ripping up tracks** connected to its own parts when they move or their connections change (PCB-027, PCB-028). That includes tracks the user drew and autorouted tracks, which are the user's (PCB-032).
- **The board outline is the one exception:** it is drawn directly on the board-edge layer, and recognised as ours because VintageMatrix owns that layer (PCB-044). The underlying rule is "only edit things that are ours" (PCB-005); the tag is just the usual way of recognising them.
- If a future feature ever needs to generate something else that cannot be tagged, this question is reopened.

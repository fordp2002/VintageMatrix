# Q06: Reference-image calibration method

| | |
|---|---|
| **Status** | Decided |
| **Affects** | [06-design-screen.md](../spec/06-design-screen.md), [07-api.md](../spec/07-api.md) |
| **Decided** | 2026-11-19 |

## Context

A reference image has to be scaled (and possibly de-skewed) to real dimensions before keys can be traced from it.

## Known constraints

- Calibration data must be stored in the contract (GUI-010) and settable via the API (API-001).
- Stored values follow 02-dimensions.

## Options

1. Two points + known distance
2. Known key pitch (click two adjacent stem centres)
3. Four-corner perspective correction

These candidates are not exhaustive and are not recommendations.

## Decision

**Two points and a known distance.** The user clicks two points on the image and types the real distance between them (a dimension value). The image is scaled to match.

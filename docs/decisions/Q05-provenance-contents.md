# Q05: Provenance block contents beyond the minimum

| | |
|---|---|
| **Status** | Decided |
| **Affects** | [11-provenance.md](../spec/11-provenance.md), [13-conformance.md](../spec/13-conformance.md) |
| **Decided** | 2026-11-19 |

## Context

The minimum is app name, version, and a do-not-edit notice (PROV-005). Candidates to add: timestamp, source commit, source design hash, human-vs-AI origin.

## Known constraints

- Determinism (PROV-007, TEST-008) argues against timestamps, or for excluding them from comparison.

## Options

1. Add source-design hash only
2. Add hash + commit
3. Add hash + commit + origin (human/AI) + timestamp, with the timestamp excluded from determinism checks

These candidates are not exhaustive and are not recommendations.

## Decision

**App name, app version, "generated — do not edit", and a fingerprint (hash) of the design file. No timestamp.** The fingerprint lets tools spot output that is out of date. Leaving out the timestamp keeps generation deterministic (PROV-007).

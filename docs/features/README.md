# Feature proposals

A feature proposal is a short design note written **before** coding, for changes that are too big or too consequential to go straight into a PR.

## When you need one

Write a proposal if your change:

- adds a new top-level config section, a new module, or a new generator;
- adds a new host interface or matrix topology;
- changes or removes a `[Decided]` requirement;
- depends on an `[Open]` question ([../decisions/](../decisions/README.md)); or
- would be expensive to reverse (contract shape, unit handling, ownership rules, licensing).

You **don't** need one for changes that fit an existing *extension point* in the spec: a new collection/preset, a new per-key module type, a new switch footprint mapping, a new field with a default, a new semantic check, or a new API operation. Go straight to a PR with spec + schema + tests.

## How

1. Copy [TEMPLATE.md](TEMPLATE.md) to `NNNN-short-name.md`, using the next free number.
2. Open a PR containing only the proposal. Discussion happens there.
3. Once accepted, implement it in one or more PRs that reference the proposal number. The implementing PRs update the spec, so the proposal stays as the historical record.

## Tips for AI-assisted proposals

- Give your AI tool [`AGENTS.md`](../../AGENTS.md), [`docs/spec/README.md`](../spec/README.md), and the spec files the reading map points to. That is usually enough context.
- Ask it to list every requirement ID the proposal touches. Reviewers check this list first.
- Ask it to state what happens to existing designs (do they still load and validate?).

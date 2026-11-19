# Q02: App stack and GUI choice

| | |
|---|---|
| **Status** | Decided |
| **Affects** | [06-design-screen.md](../spec/06-design-screen.md), [07-api.md](../spec/07-api.md), [12-architecture.md](../spec/12-architecture.md) |
| **Decided** | 2026-11-19 |

## Context

No implementation language or GUI toolkit has been chosen. The design-side upstream (`hsgw/keyboard-made-by-python`) is Python with notebooks and KiCad integration.

## Known constraints

- The GUI must be a client of the headless API (GUI-001, ARCH-002).
- Exact decimal arithmetic must be available (DIM-007).
- Must integrate with upstream without copying it (UP-001).

## Options

1. Python core + Qt GUI
2. Python core + web GUI served locally
3. Python core + KiCad plugin as the GUI
4. Pure-Rust native UI (iced, Slint, gpui, egui) + Python engine
5. Tauri 2 desktop shell (Rust) + TypeScript web UI + Python engine process (the ArtCraft-style stack)

## Decision

**Option 5: the ArtCraft-style stack.**

| Layer | Technology | Responsibility |
|---|---|---|
| UI | TypeScript web front-end | Canvas, properties panel, visual design |
| Shell | Rust, Tauri 2 | Window, menus, native file dialogs, engine process lifecycle |
| Engine | Python | Contract, dimension library, validation, all generators, headless API and CLI |

**Reasoning**

- It is the only option that delivers a slick, modern UI (the project's stated aim, modelled on ArtCraft and related apps) while keeping the engine in Python.
- Python matches the design-side upstream and KiCad's Python tooling, and has exact `decimal`/`Fraction` arithmetic in its standard library (DIM-007).
- The language boundary between the UI and the engine enforces GUI-001: the UI cannot hold design logic it has no way to run.
- TypeScript and Python are both languages that AI coding tools handle well, which keeps contribution easy.

**Consequences** (written into the spec as GUI-012, GUI-013, API-005, API-009)

- The UI must never compute stored dimension values, because JavaScript numbers are IEEE doubles (DIM-002). It sends intents; the engine computes exact values.
- The UI may preview snapping locally while dragging; the engine's result on drop is authoritative.
- The engine runs as a separate process and is bundled with the app. Python packaging per OS is a known cost.
- Contributors may need Node, Rust and Python, but module isolation (ARCH-004) means most only need one.

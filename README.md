# VintageMatrix

> ⚠️ **Early-stage project — under active design.** APIs, schemas, and file formats will change without notice.
>
> 🤖 **This project is AI-coded.** A substantial portion of the code, docs, and generated artifacts are produced with AI assistance, reviewed and directed by humans.

**VintageMatrix** is an app and reusable framework for designing **nonstandard input devices** — with a focus on **8-bit and 16-bit era home computers** and **serial terminals**.

It is built to be driven by **humans and AI alike**: everything the GUI can do is available programmatically, through a single versioned JSON configuration contract.

---

## Manifesto

VintageMatrix is a **collaborative project**.

It exists to make the design of nonstandard, vintage-inspired input devices open, documented, and reproducible — and that goal is best served by many hands and many perspectives. **Collaborators are welcome. Pull requests are welcome.** Critique, bug reports, design challenges, and ideas are all valuable contributions.

There is one condition, and it is a simple one: **stay on topic.**

Changes that serve the project's stated scope and guiding principles belong here. Changes that serve a different purpose — a different tool, a different aesthetic, a different problem — are not rejected out of hand; they are simply **better as a fork**. Fork freely, and build the thing you want to build. If it belongs here, we will happily take it back upstream.

This keeps the project coherent without making it hostile. Everything is welcome; not everything belongs in this repository.

---

## Status

| | |
|---|---|
| **Stage** | Pre-alpha / design phase |
| **Stability** | Unstable — breaking changes expected |
| **Code origin** | AI-assisted, human-directed |
| **Schema** | Draft; versioned, subject to change |

Nothing here should be relied upon for production use yet. The design decisions are being captured and refined; implementation follows.

---

## What it does

VintageMatrix treats the **keyboard matrix as the spine** of the whole design. One source of truth — the matrix model — flows into:

- **Schematic generation** — keyboard schematics as multi-sheet KiCad, one sheet per application/module
- **PCB generation** — keys placed automatically from their stem-centre positions
- **Firmware configuration** — QMK, targeting **any QMK-supported microcontroller**
- **Interface selection** — USB HID, PS/2 (Scan Code Set 2), or serial

The design screen is the human-facing heart: a canvas with the PCB outline, the keys, and an optional scaled reference image (a front-on photo or sketch) for tracing and recreating vintage boards.

---

## Guiding principles

1. **Modular** — every module works standalone or in conjunction with others.
2. **Built on, not forked from** upstream projects (see [Lineage](#lineage)).
3. **Human + AI framework** — API-first, schema-validated, headless-capable.
4. **The matrix is the spine** — one source of truth for schematic, PCB, and firmware.
5. **Generated artifacts are disposable** — provenance-stamped, freely regenerable, never hand-edited.
6. **Configuration is a single JSON contract** through which modules interact.
7. **Physical dimensions are unit-aware and losslessly stored** — metric or imperial, exact values preserved.

---

## Lineage

VintageMatrix builds on, but does not fork, these projects:

| Upstream | Role | License |
|---|---|---|
| [`hsgw/keyboard-made-by-python`](https://github.com/hsgw/keyboard-made-by-python) | Design/hardware layer (Python, notebooks, KiCad) | MIT (except bundled assets) |
| [`BuzzL123/QMK-PS2-USB-Dual-Mode-Keyboard`](https://github.com/BuzzL123/QMK-PS2-USB-Dual-Mode-Keyboard) | Firmware/protocol layer (QMK, USB HID + PS/2) | GPL-2.0 |

Licensing is expected to be **per-module**: MIT on the design side, GPL-2.0 on the firmware side. This is not yet final.

---

## Design highlights

- **Positions are stored from the switch stem centre** — not the switch body — so modules can be swapped without moving keys.
- **Coordinates are stored as exact text values with units** (`{"value": "0.75", "unit": "in"}`), never floats, so imperial-dimensioned vintage boards stay lossless.
- **Snap-to-grid is always active for key placement**; exact or arbitrary positions are set through the key properties panel.
- **Generated KiCad sheets carry a provenance block** (app name, version, "do not edit") and follow a naming convention, so regeneration is always safe.
- **The tool never touches sheets it did not generate.**
- **Key collections** — the central QWERTY block, a function-key strip, a blank numpad — are themselves saved designs, inserted with a single drag.

---

## Retro scope

- **8/16-bit home computers:** Commodore 64, ZX Spectrum, Atari 8-bit/ST, Amiga, BBC Micro, MSX, Apple II, and similar
- **Serial terminals:** DEC VT-series, ADM-3A, Wyse, Teletype-style devices
- **Interfaces:** USB HID, PS/2, serial

Modern PCB-style keyboards are possible, but not the focus.

---

## Contributing

Please read the [Manifesto](#manifesto) first — it sets the terms for collaboration.

- **On-topic contributions are welcome:** code, docs, schemas, tests, retro device presets, design critique, and issues.
- **Off-topic changes should be forked.** If your idea serves a different purpose, take it to a fork — and open an issue if you think it belongs here instead.
- **AI-generated contributions are expected and acceptable.** Human review and direction are required.
- The project is in its design phase, so **design discussion is the most valuable contribution right now.**

---

## License

Not yet finalized. See [Lineage](#lineage) for the licenses of upstream projects this work builds upon.

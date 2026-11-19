# 00 — Overview

| | |
|---|---|
| **ID prefix** | `PRIN` |
| **Owner** | Maintainers |
| **Depends on** | — |
| **Related questions** | [Q01](../decisions/Q01-positioning.md) |

## Purpose

VintageMatrix is an app and reusable framework for designing **nonstandard input devices**, focused on **8-bit/16-bit era home computers** and **serial terminals**.

A user (human or AI) describes a device: its physical key layout, its electrical matrix, its controller, and its host interface. VintageMatrix generates from that description:

- a **KiCad PCB** with the matrix placed, joined by net name to the user's own schematic ([09](09-pcb.md)),
- a multi-sheet **KiCad schematic** ([08](08-schematic.md)): *future work*, once KiCad's Python API supports schematic editing ([Q14](../decisions/Q14-kicad-api-schematic.md)),
- a **QMK firmware** configuration ([10](10-firmware.md)),

for one or more host interfaces: **USB HID**, **PS/2 (Scan Code Set 2)**, or **serial**.

**Positioning** [Decided] ([Q01](../decisions/Q01-positioning.md)):

> This project is for anyone designing a matrix-based keyboard PCB and/or firmware to support a matrix keyboard. Its focus is on keyboards that are non-standard by today's standards.

## Scope

**In scope**

- Matrix keyboards of any kind, where the user wants the PCB, the firmware, or both, including keyboards with no firmware at all (PRIN-009).
- Above all, keyboards that are non-standard by today's standards, such as recreations and reinterpretations of vintage boards: Commodore 64, ZX Spectrum, Atari 8-bit/ST, Amiga, BBC Micro, MSX, Apple II and similar; DEC VT-series, ADM-3A, Wyse, Teletype-style terminals.
- Tracing a layout from a scaled reference image.
- Reusable key collections (a QWERTY block, a function-key strip, a blank numpad, vintage presets).

**Not a focus**

- Modern enthusiast PCB keyboards are possible but do not drive the design. Where their needs conflict with vintage needs, vintage wins.

Contributions outside this scope are better as a fork (see the README Manifesto).

## Guiding principles

These bind every module. Violating one requires a spec amendment, not a review exception.

- **PRIN-001** [Decided] **Modular.** Every module works standalone *and* in conjunction with others. Modules interact only through the configuration contract ([01](01-contract.md)); no hidden cross-module dependencies.
- **PRIN-002** [Decided] **Build on, not fork.** Upstream projects ([12](12-architecture.md#upstream-projects)) are extended or wrapped, never copied in.
- **PRIN-003** [Decided] **Human + AI framework.** Everything the GUI can do is callable headlessly. API-first, schema-validated, no GUI-only logic.
- **PRIN-004** [Decided] **The matrix is the spine.** One model flows to schematic, PCB, and firmware. No parallel models.
- **PRIN-005** [Decided] **Generated artifacts are disposable.** Provenance-stamped, regenerable, never hand-edited.
- **PRIN-006** [Decided] **Configuration is a single JSON contract** through which modules interact.
- **PRIN-007** [Decided] **Physical dimensions are unit-aware and losslessly stored.**
- **PRIN-008** [Decided] **PCB and/or firmware.** A design may target the PCB only, the firmware only, or both. Neither output may require the other: a firmware-only design needs no outline or footprints, and a PCB-only design needs no controller or interfaces (CON-008).
- **PRIN-009** [Decided] **Keyboards without firmware are first-class.** A keyboard may have no microcontroller at all, e.g. a replacement keyboard whose matrix is scanned directly by the host machine through its keyboard connector (as on many 8-bit computers). Nothing in the core model, the matrix, or the PCB generator may assume a controller or firmware exists.
- **PRIN-010** [Decided] **Manage complexity.** Prefer one simple rule over options, modes, and special cases. Insist on conventions (e.g. switch origin at stem centre, QMK pin names) rather than supporting every variation. When in doubt, leave it out.

## Terminology

| Term | Meaning |
|---|---|
| **Design** | One complete device description: one configuration document. |
| **Key** | A single physical switch position with its electrical and logical attributes. |
| **Stem centre** | The centre of the switch's actuating stem, viewed from above. The reference point for every key position. |
| **Matrix** | The logical row/column grid of electrical connections that keys are assigned to. |
| **Matrix address** | A key's (row, column) in the matrix. |
| **Module** | A self-contained unit of functionality: software (e.g. the schematic generator) or hardware (e.g. a stabiliser footprint). Context disambiguates. |
| **Key collection** | A saved design that can be inserted into another design as a group of keys. |
| **Dimension value** | A physical length stored as exact text plus a unit. |
| **Generated artifact** | Any file or sheet produced by a generator. Carries a provenance block. |
| **Provenance block** | Metadata embedded in a generated artifact identifying it as generated and regenerable. |
| **Reference image** | A front-on photo or sketch of an existing device, scaled onto the canvas for tracing. |
| **Extension point** | A documented place where new capability can be added without redesigning the spec. |

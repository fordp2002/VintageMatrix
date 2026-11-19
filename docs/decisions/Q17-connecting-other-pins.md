# Q17: Connecting controller pins beyond the matrix

| | |
|---|---|
| **Status** | Deferred |
| **Affects** | [09-pcb.md](../spec/09-pcb.md), [10-firmware.md](../spec/10-firmware.md), [03-matrix.md](../spec/03-matrix.md) |
| **Decided** | — |

## Context

The generator only connects pins it knows about, which today means the matrix rows and columns (PCB-022). A controller placed on the canvas is a board-only footprint (PCB-015), so the user's schematic cannot reach its other pins (power, USB, reset, interface pins such as PS/2 clock/data or serial TX/RX) the usual way.

For a module that carries its own USB and power, that is often fine (PCB-024). For a **discrete MCU** it is not: its support circuitry (supply, decoupling, crystal, USB, boot/reset) has to connect to pins the generator leaves unconnected.

## Known constraints

- PCB-022: only known pins are connected; more may be supported in future.
- PCB-010: user changes to tagged elements are normally reverted. PCB-023 proposes an exception for nets on unknown pins.
- KICAD-001: everything goes through the KiCad API.
- FW-015: pin names are QMK's MCU pin names.

## Options

1. **User assigns the other pads' nets in KiCad**, and the generator leaves them alone (PCB-023 as proposed).
2. **Named pin-to-net assignments in the design**, e.g. `"GP0": "PS2_DATA"`, which the generator applies like row/column nets. This is the natural route for "more pins in future".
3. **New known pin types**, added one at a time with their own rules (interface pins first, e.g. PS/2, serial).
4. **Discrete MCUs not placeable on the canvas** until one of the above exists. The user places them through their schematic instead.

These candidates are not exhaustive and are not recommendations.

## Decision

**Deferred** (2026-11-19). Not needed to get going. Until decided, the generator connects only rows and columns (PCB-022).

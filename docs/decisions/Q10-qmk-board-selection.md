# Q10: QMK board selection mechanism

| | |
|---|---|
| **Status** | Decided |
| **Affects** | [10-firmware.md](../spec/10-firmware.md), [03-matrix.md](../spec/03-matrix.md) |
| **Decided** | 2026-11-19 |

## Context

The firmware generator must target any QMK-supported MCU (FW-002), but how a design names its controller and board is undecided.

## Known constraints

- No hard-coded pinouts (MAT-006).
- Pin names are opaque strings interpreted by the controller.
- Both **modules** and **discrete** MCUs must be supported (FW-011), and firmware must not depend on the PCB (FW-012).
- **Settled (FW-015):** pin names are always exactly QMK's MCU pin names, for modules and discrete MCUs alike; there is no pin translation.
- What remains open is how a design names its controller: QMK's `development_board` name, MCU + bootloader, or both (FW-013, FW-014).

## Options

1. `controller.mcu` + `controller.bootloader` using QMK's own identifiers
2. Reference an existing QMK board/dev-board definition by name
3. Both, with the board reference providing defaults

These candidates are not exhaustive and are not recommendations.

## Decision

**Board name if QMK has one, otherwise MCU + bootloader. Pin names are always QMK's (FW-015).**

- A module with a QMK `development_board` name (e.g. `promicro`) is selected by that name, which supplies the MCU and bootloader.
- A module without one, or a discrete MCU, is selected by QMK's `processor` and `bootloader` identifiers.

# 10 — Firmware generator (QMK) and interfaces

| | |
|---|---|
| **ID prefix** | `FW` |
| **Owner** | Firmware generator |
| **Depends on** | [01-contract](01-contract.md), [03-matrix](03-matrix.md), [04-keys](04-keys.md), [11-provenance](11-provenance.md) |
| **Related questions** | [Q09](../decisions/Q09-autoplacement-routing.md), [Q10](../decisions/Q10-qmk-board-selection.md) |

Licensing: the whole project is GPL-2.0-or-later, mirroring QMK (LIC-001).

## Firmware

- **FW-001** [Proposed] Output is a QMK keyboard definition (data-driven `keyboard.json` plus a keymap), generated from `matrix`, `keys`, `controller`, and `interfaces`.
- **FW-002** [Decided] **Any QMK-supported microcontroller** can be targeted. The upstream RP2040 demo is a reference config, not a constraint. No pinout is hard-coded in VintageMatrix (MAT-006).
- **FW-003** [Decided] The controller is selected by QMK's `development_board` name when one exists, otherwise by QMK's `processor` and `bootloader` identifiers ([Q10](../decisions/Q10-qmk-board-selection.md)).
- **FW-004** [Proposed] Keycodes in the design are QMK keycode names. The generator passes them through and does not translate them.
- **FW-010** [Decided] A design targets firmware when, and only when, it has a `controller` section. A design without one is a keyboard without firmware (PRIN-009): the firmware generator does nothing and reports that, and no firmware-only validation (e.g. keycodes, pin counts) applies.

- **FW-017** [Decided] QMK's `diode_direction` is taken from `matrix.diodes.direction` (MAT-015) for every diode scheme, including `none`.
- **FW-018** [Decided] If any key has no keycode when firmware is generated, the user is **prompted before generation**, with the list of those keys. The API returns error `KEYCODES_MISSING` unless the call passes `allowMissingKeycodes: true`; the prompt is the user's answer to that error ([Q12](../decisions/Q12-unassigned-keycode.md)).
- **FW-019** [Proposed] Keys generated without a keycode become `KC_NO`.

## Controllers

- **FW-011** [Decided] Two kinds of controller are supported:
  - **module**: a ready-made board carrying the MCU (e.g. a Pro Micro or Raspberry Pi Pico style board), fitted to or wired to the keyboard;
  - **discrete**: a bare MCU chip designed into the keyboard's own PCB.
- **FW-012** [Decided] **Firmware never depends on the PCB.** Firmware is generated from `controller`, `matrix`, `keys`, and `interfaces` alone. A controller need not be placed on the PCB, nor need a PCB exist at all (e.g. a hand-wired keyboard, or a module on a separate board connected by cable).
- **FW-013** [Proposed] `controller.kind` is `module` or `discrete`. A module design names a module definition in `controller.module`; a discrete design names the MCU in `controller.mcu` (QMK's identifier, e.g. `atmega32u4`, `RP2040`).
- **FW-014** [Proposed] **Module definitions** live in `controllers/`, one file per module, each giving: the module's name, the MCU it carries, QMK's `development_board` name if one exists, and optionally a KiCad footprint reference for when it is placed on a PCB. A module definition contains no pin map (FW-015).
- **FW-015** [Decided] **Pin names MUST be exactly what QMK expects** for the MCU, for both modules and discrete MCUs (e.g. `GP6` on an RP2040, `D3` on an ATmega32U4), never the labels printed on a module. VintageMatrix does no pin translation; pin names pass straight through to the QMK configuration. A pin name QMK does not recognise for the selected MCU is a semantic **error**.
- **FW-016** [Decided] A controller MAY be placed on the canvas (GUI-014), in which case the PCB generator places it on the board (PCB-020). Otherwise it appears on the PCB only if the user puts it there through their own schematic, or not at all (FW-012). Placement never affects the generated firmware.

## Interfaces

- **FW-005** [Proposed] `interfaces` is an array of `{ "type": "<type>", … }` objects. A design MAY enable several (e.g. USB + PS/2 dual mode); runtime selection follows upstream behaviour.
- **FW-006** [Decided] **USB HID** (`type: "usb-hid"`): standard QMK.
- **FW-007** [Decided] **PS/2** (`type: "ps2"`): Scan Code Set 2, built on `BuzzL123/QMK-PS2-USB-Dual-Mode-Keyboard`. The keycode → Set 2 make/break mapping comes from upstream and is not reimplemented here.
- **FW-008** [Decided] **Serial** (`type: "serial"`) supports serial-terminal-style devices.
- **FW-009** [Proposed] Serial parameters: `baud`, `dataBits`, `parity` (`none`/`even`/`odd`/`mark`/`space`), `stopBits`, `level` (`ttl` or `rs232`), and `charMap` (keycode → byte sequence, e.g. ASCII or VT escape sequences). Numeric serial parameters are plain integers; they are not physical dimensions, so DIM-002 does not apply.

## Extension points

**Add a host interface** (e.g. a vintage machine's native keyboard protocol). Add a `type` value with its own requirement subsection here: its parameters and the upstream code it builds on. Then add its schema, the firmware support, and the schematic sheet it needs ([08](08-schematic.md#extension-points)). This usually warrants a [feature proposal](../features/README.md).

**Add a controller module.** Add one module definition file to `controllers/` (FW-014) plus a test that validates it. Like a key collection, this needs no spec change.

**Add a discrete MCU.** Any MCU QMK supports can be named directly (FW-002, FW-013). If VintageMatrix needs per-MCU data (e.g. which pins are usable for a matrix), propose the data format first.

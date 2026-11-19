# 12 — Architecture, upstream, and licensing

| | |
|---|---|
| **ID prefix** | `ARCH`, `UP`, `LIC` |
| **Owner** | Maintainers |
| **Depends on** | [00-overview](00-overview.md) |
| **Related questions** | [Q02](../decisions/Q02-app-stack.md) |

## Modules

```
            ┌──────────────────────────────────────┐
            │  Configuration contract + schema     │
            │  + shared dimension library          │
            └──────────────────────────────────────┘
               ▲         ▲          ▲          ▲
   ┌───────────┴──┐ ┌────┴─────┐ ┌──┴──────┐ ┌─┴─────────┐
   │ Core model + │ │Schematic │ │  PCB    │ │ Firmware  │
   │ headless API │ │generator │ │generator│ │ generator │
   └──────────────┘ └──────────┘ └─────────┘ └───────────┘
          ▲
   ┌──────┴───────┐
   │ Design screen│
   └──────────────┘
```

- **ARCH-001** [Decided] Generators depend on the contract, the schema, and the shared dimension library, never on each other or on the GUI.
- **ARCH-002** [Decided] The GUI depends on the headless API only (GUI-001), reached through the engine protocol (API-009).
- **ARCH-005** [Decided] Implementation stack ([Q02](../decisions/Q02-app-stack.md)): Python engine (core model, API, CLI, generators); TypeScript web UI; Rust Tauri 2 desktop shell. The shell holds no design logic.
- **ARCH-003** [Proposed] Each module lives in its own top-level source directory with its own README stating its purpose, the config sections it reads/writes, the requirement prefixes it implements, and its licence.
- **ARCH-004** [Proposed] Each module can be built and tested on its own (`test` target per module), so a contributor working on one generator never needs the others installed.

## External services

- **ARCH-006** [Decided] External services (today only Freerouting, PCB-029) are **optional**. VintageMatrix MUST work fully without them, and an unavailable service only disables the feature that uses it.
- **LIC-004** [Proposed] Freerouting is used as a separate program or service. It is called, never bundled or linked, so its licence does not apply to VintageMatrix code. Check this with the licensing decision (LIC-001).

## Upstream projects

| Upstream | Role | License |
|---|---|---|
| `hsgw/keyboard-made-by-python` | Design/hardware layer (Python, notebooks, KiCad) | MIT (except bundled assets) |
| `BuzzL123/QMK-PS2-USB-Dual-Mode-Keyboard` | Firmware/protocol layer (QMK, USB HID + PS/2) | GPL-2.0 |

- **UP-001** [Decided] Upstream code is consumed as a dependency, never copied into this repository.
- **UP-002** [Proposed] Upstream dependencies are pinned by commit, and the pin is recorded in one place.
- **UP-003** [Decided] Changes to upstream behaviour go upstream. If a temporary patch is unavoidable, it is kept as a separate, documented patch file.
- **UP-004** [Decided] Upstream-derived defaults (e.g. the RP2040 demo) are reference configurations only.

## Licensing

- **LIC-001** [Open, not final] Licensing is expected per-module: MIT on the design side, GPL-2.0 on the firmware side.
- **LIC-002** [Decided] Firmware-side code derived from GPL-2.0 upstream inherits GPL-2.0.
- **LIC-003** [Decided] Do not add or change licence headers until licensing is finalised.

## Extension points

**Add a generator** (e.g. a plate/case DXF, a keycap legend sheet, a BOM). Write a [feature proposal](../features/README.md), then:
1. add a spec file `NN-<name>.md` with a new ID prefix and register it in [README.md](README.md);
2. read only the contract (ARCH-001) and declare which sections it reads;
3. follow [11-provenance](11-provenance.md);
4. expose it through `generate` in [07-api](07-api.md);
5. state its licence side (design or firmware).

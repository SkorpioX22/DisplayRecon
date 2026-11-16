# Changelog

All notable changes to DisplayRecon are documented here. Format based on [Keep a Changelog](https://keepachangelog.com/) — skill versioning is semantic.

## [1.0.0] - 2026-11-16

### Added

- Initial release.
- **SKILL.md** — core identity, nine-rule philosophy, four-phase workflow (intake → identification → implementation → diagnostics), token-efficiency rules, five-level verification discipline.
- **references/display-technologies.md** — controller families for OLED, TFT, character LCD, VFD, LED drivers, e-paper and legacy displays, with known variant quirks.
- **references/communication-protocols.md** — I2C/SPI/UART/parallel practical facts and a discrimination-experiment menu for unknown interfaces.
- **references/controller-research.md** — confidence-graded identification (Confirmed / Strongly supported / Possible / Unknown), source hierarchy, and no-datasheet fallback procedure.
- **references/electrical-safety.md** — logic-level compatibility, supply current checks, VFD/e-paper high-voltage cautions, safe measurement guidance.
- **references/arduino-platforms.md** — per-board differences across ATmega, ESP32 variants, RP2040/RP2350; RAM budgeting; RMT/PIO/DMA peripherals.
- **references/diagnostic-methodology.md** — symptom→failure-layer tables, one-target experiment menu, anti-regeneration rules.
- **references/library-evaluation.md** — library selection procedure, family map, criteria for bare-metal drivers.
- **templates/hardware-intake.md** — grouped interview checklist with question snippets.
- **templates/investigation-state.md** — persistent experiment-tracking block with failed-approach anti-repeat list.
- **templates/verification-checklist.md** — 12-item completion checklist and the five verification levels.

# Diagnostic Methodology Reference

The structured process for failed display implementations. Symptom-first, layer-second, one hypothesis at a time.

## The diagnostic loop

1. **Establish the failure** — exact symptom, when it occurs, what changed.
2. **Classify the failure layer** — the earliest broken layer explains everything downstream. Work the list from physical up: electrical → wiring → power → communication → controller identification → initialization → library → firmware → rendering → timing.
3. **Hypothesize** — one or two most-likely causes. Not ten.
4. **Experiment** — one hypothesis, minimal variable change, defined observable, explicit pass/fail.
5. **Evaluate** — compare observed vs expected. Both outcomes yield information; record it.
6. **Update** — investigation state; eliminated hypotheses go to Failed Approaches.
7. **Next** — only proceed when the previous result is evaluated. Never re-run an unchanged experiment expecting different results.

## Symptom → likely layer table

| Symptom | First-suspected layers (in order) |
|---|---|
| Nothing at all, no backlight/filament | Power → wiring → electrical (VCC voltage, GND, fuse) |
| Backlight on, no content | Communication → initialization → controller identification |
| Display detected on I2C scan, still blank | Initialization (charge pump not enabled) → wrong controller assumption → library init mismatch |
| Garbled / shifted pixels | SH1106-vs-SSD1306 offset → color-format mismatch → wrong orientation → timing/bit-slip (long wires, clock too fast) |
| Wrong colors | BGR/RGB flag (madctl) → color format 16 vs 18 bit → byte order |
| Flickering | Shared-bus contention → insufficient supply current → refresh/mux issue → loose connection |
| Works then freezes / MCU resets | Power sag (supply too weak for backlight on) → watchdog → memory corruption → shared-pin conflict |
| Text wrong / random characters | Character DDRAM offset (wrong line mapping) → encoding issue → RAM corruption from timing |
| Half the screen works | Controller variant RAM width (SH1106 132 cols) → split frame buffer orientation bug |
| I2C scan finds nothing | Wiring/power first (not "wrong address" first) → pull-ups → address (7 vs 8-bit) → speed |
| Worked on Uno, fails on ESP32 | Level compatibility → pin mapping (Nano ESP32 remap, Wire.begin args) → library core support |

## One-target experiments (menu)

Pick the single experiment that best discriminates the top hypothesis:

- **Power check:** measure VCC at the module pins under load (not just at the supply).
- **I2C scan:** confirms bus + address in one shot. Run at 100 kHz first.
- **Minimal sketch:** strip to library's raw example with only wiring changed. If the example fails too, the problem is hardware/wiring/assumption, not user code.
- **Slow everything down:** halve I2C/SPI clock; shorten wires. Diagnoses timing/bit-slip.
- **Substitute controller assumption:** run the candidate-controller's init from a bare-metal snippet; different-but-partial response narrows the controller.
- **Split the bus:** disconnect other I2C devices; conflicts masquerade as display faults.
- **Reset pulse:** manually pulse RST with a delay before init; some modules boot in a bad state.
- **Swap supply:** separate supply for the display (common GND) isolates brownout issues.

## Critical rules

- **Never regenerate the whole project.** Identify the failing function/setting; patch that. Preserve working code.
- Every experiment changes the investigation state; every result (pass or fail) is recorded.
- If the user cannot run an experiment, pick the next most-informative one they can run.
- If two layers are both plausible, design the experiment that distinguishes them in one shot (e.g., scan + measure VCC together), rather than two round trips.

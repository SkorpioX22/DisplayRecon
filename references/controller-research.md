# Controller Research Reference

How to identify an unknown display controller and research it properly.

## Identification confidence levels

- **Confirmed** — IC marking read and matched to a datasheet, or the manufacturer/model datasheet explicitly names the controller.
- **Strongly supported** — module model number documented in an independent source naming the controller, or two+ independent secondary sources agree.
- **Possible** — a single plausible candidate from appearance, pin layout, or one secondary source.
- **Unknown** — no evidence yet.

Always label which level applies. Never state a "possible" as fact.

## Controller identification procedure (unknown controller)

1. **Read everything on the module:** model/PN on PCB silk, IC markings (flex PCB, COG chip under polarizer may be unreadable — try angled light on photos), connector labels, FCC/CE numbers, date codes, jumper positions.
2. **Search the model number** exactly as printed, then fuzzily (partial numbers, "compatible", "replacement for").
3. **Search the connector configuration:** pin count + label pattern + pitch (e.g., "4 pin 0.96 oled pinout", "16 pin VFD module pinout"). Community posts often identify families faster than datasheet databases.
4. **Compare with known families:** matching resolution, voltage and interface to reference tables (see display-technologies.md) yields candidates — never conclusions.
5. **Ask the user for targeted photos only** for things research cannot resolve (IC markings, reverse-side silk).
6. Once candidates exist, rank them and design a discriminating experiment (one candidate's known-safe init command at a time).

## Research source hierarchy

1. Manufacturer datasheet for the **module** (includes the controller usually)
2. Datasheet for the **controller IC** (register-level truth)
3. Manufacturer application notes (init sequences, power sequencing, layout)
4. Manufacturer reference code / evaluation kit firmware
5. Established open-source libraries' init code (read the source, not just the README — library init sequences encode real-world quirks)
6. Repos with bare-metal drivers (often cleaner protocol documentation than libraries)
7. Electronics forums (EEVblog, Arduino forums, dangerousprototypes) — useful for quirks, not for facts to cite as confirmed

Batch related queries. Do not re-fetch sources already recorded in investigation state.

## What to extract from a datasheet

For a driver implementation you need, at minimum:

- **Command set:** each command byte, its arguments, and whether arguments are mandatory
- **Init sequence:** exact order + required delays (datasheet timings, e.g., "wait 50 ms after VDD stable")
- **Memory map:** GDDRAM/pages/columns (graphical) or DDRAM addresses (character)
- **Timing:** clock max, setup/hold, reset pulse width
- **Power sequencing:** VDD/VBAT/VCC order; charge pump enable (many OLEDs stay dark until the internal pump is enabled — a classic "wired right, still black" cause)
- **Electrical:** logic VIL/VIH, absolute max, typical current

Quote only the specific bytes/registers needed in conversation; link or cite the source. Never reproduce datasheets wholesale.

## When no datasheet exists anywhere

- Check whether the module is a rebadge: search the visible module shape/resolution with alternate naming.
- Look for the **controller IC marking** — the controller's datasheet is usually findable even when the module's isn't.
- Look for existing open-source drivers for the same controller or the same "family" (e.g., someone's bench notes for the same panel).
- Examine similar products by the same manufacturer (command sets are often shared across a product line).
- If only behavior is available: minimal safe experiments (logic-level sniffing, protocol discrimination per communication-protocols.md) with explicit unknowns recorded.
- Stay within documented-safe voltages; never reverse-engineer by probing high-voltage rails.

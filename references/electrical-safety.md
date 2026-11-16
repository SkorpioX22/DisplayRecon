# Electrical Safety Reference

Read before recommending any wiring. Never instruct the user to connect unknown pins directly to a microcontroller.

## Level compatibility

- **3.3 V-only MCUs (ESP32 family, RP2040, most STM32):** pins are NOT 5 V tolerant. A 5 V display's outputs (or a 5 V I2C bus with strong pull-ups to 5 V) can damage them. Use a level shifter (TXS0108E, TXB0108, or per-signal dividers for slow signals like DC/CS/RST; I2C needs proper bidirectional shifting, not resistor dividers on SDA/SCL).
- **5 V MCUs (Uno, Nano, Mega):** outputs are 5 V. Displays with 3.3 V-only inputs need shifting down (divider or shifter). Many 3.3 V OLED/TFT modules tolerate 5 V inputs, but **verify per module**, don't assume.
- Power the display's VCC at its rated voltage; 3.3 V displays fed 5 V often run briefly then degrade or die.

## Current and supply reality checks

- 0.96" OLED: ~10–25 mA typical. 1.8" TFT backlight: 20–80 mA. Larger TFT backlights: 100–400 mA.
- HUB75 panels: **hundreds of mA to amps** — never power from a dev-board regulator; use a dedicated 5 V supply with adequate current and common ground.
- WS2812B strips: 60 mA per LED at full white; plan supply + injection.
- VFD filament: low voltage but **high current** (often 100–500 mA); grid/anode drivers sit at tens of volts internally. Many VFD modules take a single 5 V rail and handle this internally — but confirm the module has onboard drivers before assuming.

## High-voltage and unusual-supply warnings

- VFD glass assemblies without driver ICs expose filament/grid/anode lines that run at ~30–70 V — **do not probe or connect these directly**. Only interface with driver-equipped modules or purpose-built driver ICs.
- Some displays need a negative bias rail (e.g., contrast for LCDs ~ -5 to -10 V, VCOM for some OLED/e-paper). If a pin is labeled VEE/VOUT/VBAT, research it before connecting; never feed it an arbitrary voltage.
- Capacitor discharge: unplugged display supplies (especially VFD/e-paper with boost converters) can hold charge — wait or discharge safely before handling.
- Power sequencing matters for some controllers (charge-pump OLEDs, e-paper rails). If the datasheet specifies an order, follow it; if the module is a fully-integrated unit, its onboard regulator usually handles it.

## Grounding and wiring hygiene

- Common ground between all supplies and MCU — the classic cause of "works on USB, dies on external supply" and I2C ghosts.
- Keep SPI/I2C leads short (≲ 20–30 cm at default speeds); long leads at high SPI clocks cause bit slips (garbled pixels).
- Decoupling: 100 nF at the module supply pin if absent; 10 µF bulk for panels with backlights.
- Never hot-plug signal wires while powered; this kills I/O pins.

## Safe measurement guidance

- Measure only on low-voltage, clearly labeled rails (VCC, GND, logic pins) with the display powered through its own supply.
- If measuring is needed near driver circuitry, instruct: multimeter in DC volts, probe on the labeled test point, one hand, module unpowered for resistance checks.
- Never instruct the user to bypass protections (fuses, current-limit resistors) or to probe unidentified components.

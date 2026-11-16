# Communication Protocols Reference

Practical microcontroller-side facts for display bring-up. Verify clock/timing limits against the specific controller datasheet before recommending settings.

## I2C

- Addresses are 7-bit; datasheets frequently quote 8-bit write addresses (e.g., 0x78 write = 0x3C 7-bit). Arduino `Wire` uses 7-bit — convert when a datasheet says otherwise. Common display addresses: 0x3C/0x3D (OLED), 0x20–0x27 & 0x38–0x3F (PCF8574 backpacks), 0x70–0x77 (HT16K33).
- **First diagnostic step for any I2C display: run an I2C scanner.** A scanner result (ACK or silence) immediately separates wiring/power/address problems from controller problems.
- Pull-ups: many modules have them onboard; bare controllers/glass do not. 4.7 kΩ typical at 100 kHz. If bus runs at 400 kHz+, check the controller's rated max clock.
- ESP32/RP2040: I2C pins are assignable (Wire.begin(SDA, SCL) / default GP4/GP5 on Pico); Uno/Nano/Mega: fixed A4/A5 (SDA/SCL) hardware pins (Mega also 20/21).
- Shared-bus caution: another device at the same address (e.g., second OLED) conflicts.

## SPI

- Modes: most display controllers use SPI **Mode 0** (CPOL=0, CPHA=0); a few want Mode 3. Confirm per datasheet — wrong mode gives shifting/bit-slip garbage or total silence.
- Typical signal set: SCK, MOSI (SDA/SDIN), CS (CE), DC/RS (data/command select — **essential**, not optional), RST (often optional but recommended), sometimes BL backlight enable.
- Max clock: commonly 8–15 MHz for these controllers, but rated per datasheet; bring up at a conservative speed (1–4 MHz), raise later.
- Hardware vs software SPI: software SPI (bit-banged) is fine for bring-up diagnosis; hardware SPI for final performance. DC pin can be any GPIO; CS any GPIO.
- ESP32: VSPI/HSPI both usable; RP2040: SPI0/SPI1; pin maps differ per board — check the variant's pinout, do not assume.

## UART

- Baud rates for display modules vary: 9600 (many serial LCDs/VFDs), 19200, 38400, 57600, 115200 (Noritake GU-7000 commonly 38400 default, configurable). Confirm from the module's manual; try a small sweep only if documentation is unavailable and electrical safety is established.
- Logic level: many serial display modules are 5 V logic — ESP32/RP2040 are 3.3 V. A simple divider or level shifter may be required.
- Some modules auto-baud on a sync byte; some need a specific boot delay before accepting commands.
- TX-only operation (MCU TX → display RX) works for most command-driven modules; full duplex needed for status reads.

## Parallel

- HD44780: 4-bit mode is the default for Arduino (saves pins). Requires precise init timing (power-on wait ≥ 40 ms, init commands with 4.1 ms/100 µs delays) — libraries handle this; hand-rolled code frequently gets it wrong.
- 8080/6800 parallel buses (TFTs): data bus + WR/RD + DC + CS + RST; use existing libraries (e.g., TFT_eSPI, UTFT) rather than bit-banging from scratch.
- HUB75: parallel RGB with row address lines; multiplexing timing is the library's job.

## Proprietary / 2-wire

- TM1637 CLK/DIO is NOT I2C — needs a dedicated driver library.
- WS2812B single-wire timing (800 kHz ±) needs a library or platform-specific driver (RMT on ESP32, PIO on RP2040).
- Unknown pin counts: do not guess protocol from pin count alone (4 pins could be I2C, SPI3, UART+power, TM1637, or something else). Identify from labels, markings, or controlled experiments (protocol reference below).

## Unknown interface — discrimination experiments

Run only after electrical compatibility is established. Each experiment is cheap and falsifiable:

1. **Labels first:** GND/VCC/SDA/SCL → I2C. GND/VCC/CS/DC(DRS)/SCK(SCL)/MOSI(SDA) → SPI. GND/VCC/RX(/TX) → UART. GND/VCC/D0–D7/E/RS/RW → parallel.
2. **I2C scan** (if any pair of pins could be SDA/SCL): try candidate pin pairs at 100 kHz with pull-ups. Scan both 3.3 V and 5 V logic only if module is confirmed 5 V tolerant.
3. **UART:** send a known-safe command (e.g., "clear display" byte from a candidate controller's manual, or plain ASCII text) at candidate baud rates while watching for any response or output.
4. **SPI:** drive candidate pins with a minimal init from the most-likely controller, CS toggled, DC toggled — a partial-illumination or flicker response confirms SPI + narrows the controller.

Document each attempt in the investigation state. One hypothesis per experiment.

# Arduino Platforms Reference

Platform-specific facts that change display bring-up. Never assume code for one board works unchanged on another.

## Logic voltage summary

| Platform | Logic | 5 V tolerant I/O | Notes |
|---|---|---|---|
| Uno / Nano (ATmega328P) | 5 V | outputs are 5 V | fixed I2C A4/A5; SPI 10–13 |
| Mega 2560 | 5 V | outputs 5 V | I2C on 20/21 also; more pins for parallel |
| Nano Every | 5 V | outputs 5 V | ATmega4809; pin mapping differs from classic Nano |
| Nano ESP32 | 3.3 V | limited | Arduino-pin mapping ≠ GPIO numbers; check the board's mapping |
| ESP32 / S2 / S3 / C3 / C6 | 3.3 V | most pins NOT 5 V tolerant | RMT peripheral great for WS2812B; two I2C buses; VSPI/HSPI (and more on S3) |
| Pico / RP2040 | 3.3 V | most pins NOT 5 V tolerant | PIO for WS2812B/HUB75; I2C0/I2C1, SPI0/SPI1 with per-pin choices (wire.setSDA/setSCL) |
| RP2350 | 3.3 V | not 5 V tolerant | same pattern as RP2040, more RAM |

## Pin-assignment pitfalls

- ESP32 `Wire.begin(SDA, SCL)` — pin numbers are GPIO numbers, not Arduino pin labels; on many dev boards 21/22 are default. S2/S3/C3 differ.
- RP2040 Arduino cores: `Wire.setSDA(pin); Wire.setSCL(pin);` before `Wire.begin()`; default I2C0 on GP4/GP5 in Arduino-Pico core, different in mbed core — check which core the user runs.
- Nano ESP32: Arduino pin numbering is remapped; "D2" is not GPIO2. A frequent cause of "wired right, doesn't work".
- Mega: enough pins for 8-bit parallel, but the Arduino `LiquidCrystal` 4-bit examples differ in pin constants from TFT libs.

## RAM/flash budgeting

- Framebuffers are the constraint: 128x64 mono = 1 KB (fine everywhere). 240x320x16-bit = 150 KB (needs ESP32/RP2040 class MCU; Uno/Mega cannot hold a full framebuffer — libraries use tiling/partial writes).
- Pico/RP2350 and ESP32-S3 (PSRAM) handle large framebuffers comfortably.
- Character LCDs: negligible memory.

## Peripherals that help

- **ESP32:** RMT (precise WS2812B timing, offloads CPU), I2S (parallel TFT DMA transfers), dual-core split for rendering.
- **RP2040/RP2350:** PIO state machines (WS2812B, HUB75, unusual protocols without CPU timing), DMA on SPI.
- **ATmega:** no DMA/RMT — timing-critical protocols must use bit-bang with interrupts disabled or existing tuned libraries.

## Library compatibility notes

- Some libraries are AVR-only in places (progmem-heavy code); ESP32/RP2040 cores usually still work but check issues.
- `U8g2`/`U8x8` (mono OLEDs/LCDs, broad controller coverage), `TFT_eSPI` (ESP32-favored TFTs, per-build config in User_Setup.h), `Adafruit_GFX` + bus-specific drivers (portable), `FastLED` (WS2812B), `LiquidCrystal_I2C` forks (be careful: several incompatible forks, and native-I2C ST7032 LCDs need different libs).
- Verify a library supports the user's exact board/core before recommending (maintenance status, issues tab, last release).

## Portability checklist when moving code between boards

1. Logic voltage / level shifting changed?
2. I2C/SPI pins valid on the new board?
3. Library supports the new core?
4. RAM/flash adequate for the same graphics mode?
5. Timing-critical code (bit-banged protocols) still valid at the new clock speed?

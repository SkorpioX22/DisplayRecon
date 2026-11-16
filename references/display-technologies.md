# Display Technologies Reference

Background knowledge to orient an investigation. **Never use this as identification evidence by itself** — controller families overlap and modules are frequently mislabeled. Always confirm against the specific controller's documentation.

## OLED (I2C/SPI, monochrome or color)

| Controller | Typical panels | Notes |
|---|---|---|
| SSD1306 | 0.91"–1.3" 128x32/128x64 | Most common small mono OLED. I2C addr 0x3C/0x3D. 128x8 byte RAM pages, column remapping quirks. |
| SH1106 | 1.3" 128x64 | Looks identical to SSD1306 modules but RAM is 132 columns wide → 2 px offset artifacts if driven by SSD1306 driver. Frequent cause of "garbled/shifted" output. |
| SSD1309 | 128x64 | Often just relabeled SSD1306-class silicon; behaves like SSD1306 in practice. |
| SSD1327 | 1.12" 128x128 grayscale | 4-bit grayscale, different command set than SSD1306 family. |
| SSD1351 | 1.5"/1.27" color RGB OLED | 16/18-bit color, different command set than mono family. |

## TFT LCD (SPI/parallel, color)

| Controller | Typical panels | Notes |
|---|---|---|
| ST7735 | 1.8" 160x128 | Many color-order and offset variants (R red/green tabs). Init has per-variant madctl/cases. |
| ST7789 | 1.3"/1.54"/2.4" 240x240/240x320 | No CS pin on some modules (tied low). Similar to ST7735 command set. |
| ILI9341 | 2.2"–2.8" 240x320 | Very well documented; huge library support. |
| ILI9488 | 3.5" 320x480 | Often parallel or SPI-with-limitations (18-bit color over SPI is awkward). |
| GC9A01 | 1.28" round 240x240 | Round smartwatch-style displays. ST7789-ish command set, not always supported by generic libs. |

## Character LCD (HD44780 family)

- HD44780 and clones (KS0066, ST7066, SPLC780...): 4/8-bit parallel, 5V or 3.3V variants, well-known init sequence.
- I2C backpacks (PCF8574/PCF8574A, addresses 0x20–0x27 / 0x38–0x3F) present a parallel LCD over I2C; the backpack chip, not the LCD, sets the address.
- ST7032/ST7036 (Sitronix, common in 3.3V "I2C character LCDs" e.g. Grove LCD): I2C native, different init than HD44780 — frequent source of "backpack code shows nothing" on native-I2C LCDs.

## VFD (vacuum fluorescent)

- Architecture: filament (cathode, ~2–6 V AC/DC, high current), grid anodes per digit, segment anodes. Driver ICs handle the high voltages internally.
- Common drivers: Noritake (GU-7000 series serial modules — UART command set with its own graphic/text modes), Futaba (serial and parallel modules), NEC µPD1631x-family drivers (parallel/serial grid drivers).
- Grid/segment duty and brightness are set via driver commands, not PWM.
- Some modules need a separate filament supply (can draw 100–300 mA at low voltage, high current) in addition to logic supply.
- Serial VFD command sets are manufacturer-specific — never assume HD44780-like or SSD1306-like commands. Obtain the module's command reference.
- Electrically the logic interface is usually 5 V tolerant, but verify per module; filament polarity matters on some modules.

## LED displays

| Driver | Interface | Notes |
|---|---|---|
| MAX7219/7221 | SPI-like (16-bit frames) | 8-digit 7-seg or 8x8 matrix; 7221 has faster slew (EMC). Cascade via DOUT→DIN. Segment current via RSET resistor or command. |
| TM1637 | 2-wire proprietary (CLK/DIO) | 4/6-digit clock displays; not I2C despite looking like it. |
| HT16K33 | I2C (0x70–0x77) | Matrix driver used in Adafruit quad alphanumeric and 8x16 matrices; also backpacks. |
| WS2812B / NeoPixel | 1-wire strict timing | 800 kHz protocol; timing-critical, use library; logic level should match (3.3 V data often marginal for 5 V strip — use a level shifter or sacrificial first LED at 3.3 V trick with caution). |
| HUB75 | Parallel RGB multiplex | 16/32/64-wide RGB panels; needPanel-specific scan rate (1/4, 1/8, 1/16, 1/32), address lines A–E; use dedicated libs (e.g., rpi/rp2040/ESP32 HUB75 libraries); current-limit supplies. |

## E-paper / e-ink

- Controllers: SSD1608, IL3820, UC8151, GDEW families — each has its own busy-pin handshake, LUT upload, and full/partial refresh commands.
- Always has a BUSY pin — polling it is mandatory, not optional.
- Partial refresh and deep-sleep wake sequences vary per controller; follow the specific panel's datasheet.

## Segmented / custom / legacy

- Custom glass with unknown driver: identify via driver IC marking or by tracing interface behavior; treat as "unknown controller" workflow.
- Legacy parallel displays (T6963C graphical, KS0108 graphical): still encountered; both have established datasheets and libraries.
- Do not force unfamiliar hardware into the categories above — this list is orientation, not a taxonomy.

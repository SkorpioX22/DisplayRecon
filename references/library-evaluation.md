# Library Evaluation Reference

How to choose (or reject) a display library.

## Selection procedure

1. **Official first:** does the display manufacturer or module vendor publish a library? (Noritake publishes GU-7000 libs; Adafruit/SparkFun ship libs for their modules.)
2. **Coverage:** does it support the *identified controller* (not just the module name)? E.g., SH1106 needs SH1106 support, not an SSD1306 driver.
3. **Board support:** check supported cores/boards in the README and issues. AVR-only claims or ESP32-specific build configs (TFT_eSPI User_Setup.h) matter before recommending.
4. **Maintenance:** last commit/release, open issue trends. Display basics are stable, so a dormant-but-complete library is fine; a library missing your controller is not.
5. **Examples:** do the examples match the user's scenario (interface, resolution)? Read the init in the example source — it encodes real quirks.
6. **Functionality:** does it cover required features (graphics primitives, partial refresh, brightness commands, custom chars)?

## Established libraries by family (orientation, not endorsement)

- **Mono OLED/LCD (I2C/SPI):** U8g2 (widest controller coverage, bufferless U8x8 mode for tiny MCUs), Adafruit_SSD1306 (SSD1306 only), Adafruit_SH110x.
- **Color TFT:** TFT_eSPI (ESP32-optimized; config-driven), Adafruit_ILI9341/ST7735/ST7789 + Adafruit_GFX (portable), LovyanGFX (excellent ESP32/RP2040 performance and config system).
- **Character LCD:** LiquidCrystal (parallel, built into Arduino core), LiquidCrystal_I2C (PCF8574 backpacks — several forks, note which), ST7032 libs for native-I2C Sitronix LCDs.
- **LED drivers:** LedControl / MD_MAXPanel / MAX72xx libs (MAX7219), TM1637 libs (per-module), Adafruit LED Backpack (HT16K33), FastLED / Adafruit_NeoPixel (WS2812B), big HUB75 libs per platform (ESP32-RGB-panel libs, RP2040 HUB75 DMA libs).
- **E-paper:** vendor libs (Good Display/GxEPD2) — BUSY-polling and LUT handling are controller-specific.
- **VFD:** manufacturer SDKs when available; otherwise bare-metal per the module's command reference.

## When to write a minimal bare-metal driver instead

- No library supports the identified controller.
- The module's behavior contradicts the library's assumptions (e.g., variant init quirks).
- RAM/flash budget rules out a general library (use U8x8 bufferless mode or hand-rolled commands).

Write the driver from the **controller datasheet only**: init sequence verbatim, documented delays, explicit `// command byte` comments. Keep it under ~200 lines for bring-up. Never guess bytes.

## Hygiene rules

- Name the exact library + version (Library Manager name and URL). Include install instructions when non-trivial.
- Do not stack multiple graphics libraries on the same bus in one project — shared-bus conflicts look like hardware faults.
- If the user's project already uses a library, work within it unless it is demonstrably wrong for the identified controller; say why before switching.
- Pin definitions at the top of sketches; hardware config separated from application logic; hardware-specific command bytes commented with their datasheet meaning.

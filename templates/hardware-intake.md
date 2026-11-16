# Hardware Intake Template

Use this checklist during Phase 1. **Rules:**

- Determine what the user already provided before asking anything.
- Group remaining questions; ask at most 3–6 concise questions per round.
- Request photographs when markings, pin labels, or connector layout are decisive (front, back, close-up of ICs/connector).
- Never ask for something researchable from a known model number or visible markings.

## Display identification
- Manufacturer / model / part number / product name
- Controller IC (check IC markings on flex/PCB)
- Display technology (OLED, TFT, character LCD, VFD, LED, e-paper, segmented)
- Resolution / character count / segment count
- Physical size

## Electrical
- Operating voltage / logic voltage
- Current consumption (incl. backlight or filament for VFD)
- Backlight voltage/current if separate
- Logic-level tolerance of pins (3.3 V vs 5 V)

## Communication
- I2C / SPI / UART / parallel / GPIO signaling / proprietary / unknown

## Physical connections
- Pin count, pin labels, connector type
- PCB markings, silk labels, jumpers/straps, test points

## Development environment
- Board and MCU (Uno, Mega, Nano, ESP32 variant, Pico/RP2040/RP2350, other)
- Framework: Arduino IDE or PlatformIO; core version
- Installed libraries; other connected peripherals; free GPIO pins

## Documentation available
- Datasheet links, product page, existing example code, schematics
- Photographs (markings readable?)
- Previous attempts and their exact results

## Model question snippets

Unknown module:
> "Send close-up photos of: (1) the front of the display, (2) the back/PCB with any printed model or IC markings, (3) the connector with pin labels visible. What is the pin count?"

Known module, bring-up:
> "Which board (exact model), which interface do the module's labels suggest (I2C/SPI/UART), and do you have a link to the product page or datasheet?"

Failure case:
> "What exactly happens: nothing at all, backlight only, garbled content, or correct-then-freeze? Which library and example code are you running, unmodified or edited?"

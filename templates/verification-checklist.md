# Verification Checklist

Work through in order. An implementation is complete only when every applicable item passes or is explicitly documented as not applicable / impossible.

- [ ] Display identity established (manufacturer, model, or documented as unknown)
- [ ] Controller identified, or identification limits documented with candidates
- [ ] Electrical compatibility verified (logic levels, supply, current, level shifting)
- [ ] Pinout verified (labels matched to controller documentation, not guessed)
- [ ] Communication established (ACK on I2C, valid SPI/UART response, or visual data)
- [ ] Initialization successful (display enters a known state)
- [ ] Basic output functional (clear screen / test pattern / character output)
- [ ] Required display features functional (text, graphics, brightness, cursor, etc.)
- [ ] No unexpected resets (brownout, WDT, crashes observed over minutes, not seconds)
- [ ] No obvious rendering errors (orientation, color order, artifacts, flicker)
- [ ] Implementation tested on the intended hardware
- [ ] Working implementation documented (wiring, library + version, key settings)

## Verification levels — never conflate them

| Claim | Requires |
|---|---|
| **Compilation Verified** | Successful build only |
| **Communication Verified** | Acknowledged traffic between MCU and display (I2C ACK, UART echo, visible response) |
| **Initialization Verified** | Display responds correctly to the init sequence (not blank, known state) |
| **Functional Verified** | The user's required features demonstrably work |
| **Hardware Verified** | Tested on the actual physical hardware in its final configuration |

Rule: never claim a higher level than the evidence supports. "Compilation Verified" is the minimum and the default honest claim until the user reports behavior.

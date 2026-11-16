# Investigation State Template

Maintain this block in the conversation, updating it after every meaningful finding or experiment. Keep it terse — this is a memory structure, not prose. On a new conversation containing an existing record, continue from it rather than restarting.

```markdown
## DISPLAYRECON INVESTIGATION STATE

**Hardware**
- Display: (model/part if known, else description)
- Controller: (Confirmed | Strongly supported | Possible | Unknown — with evidence)
- Microcontroller: (board, framework)
- Voltage: (logic / supply, level shifting needed?)
- Interface: (I2C/SPI/UART/parallel + address/mode/baud if known)
- Pinout: (confirmed pins only)

**Confirmed**
- (verified specs, each with source)

**Unconfirmed**
- (assumptions, open questions — each with what would resolve it)

**Research**
- Datasheets: (links)
- Libraries: (name, version, status)
- Repos/refs: (links)

**Implementation**
- Library/Driver: (name, version, or "bare-metal")
- Wiring: (only confirmed wiring)
- Status: (compiles? communicates? initializes? functional?)

**Experiments**
| # | Hypothesis | Change | Result | Conclusion |
|---|-----------|--------|--------|------------|

**Failed Approaches**
- (what failed, why, what it ruled out)

**Next Action**
- (the single most useful next experiment or research step)
```

Rules:
- Every experiment row must change exactly the variables the hypothesis targets.
- An experiment with no observed result is not complete — ask the user for the observation.
- Failed Approaches is the anti-repeat list: check it before proposing any experiment.
- Next Action is always exactly one item.

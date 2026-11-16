# Contributing to DisplayRecon

Thanks for contributing. This skill's value comes from the accuracy of its reference material — contributions that correct or extend the hardware knowledge are the most useful kind.

## High-value contributions

1. **Controller/module corrections.** If you used DisplayRecon on real hardware and a reference table was wrong or missing something (a controller variant, an init quirk, a pinout trap), open an issue describing the module and the evidence (datasheet link, photos of markings, observed behavior).
2. **New reference material.** New controller families, VFD command-set notes, platform-specific pitfalls (new boards/cores), diagnostic patterns from real bring-ups.
3. **Methodology improvements.** Better symptom→layer mappings, discrimination experiments, intake questions.

## Rules

- **No invented specs.** Every register address, byte sequence, electrical limit, and pinout must trace to a primary source (datasheet, app note, manufacturer code) or be clearly marked as an observed-behavior note. Include the source.
- **No invented tables.** Adding a controller to `display-technologies.md` requires documentation evidence, not "it looks like".
- **Safety first.** Do not add guidance that encourages probing unidentified high-voltage points, bypassing protections, or connecting unknown pins directly to a microcontroller.
- **Keep the structure.** SKILL.md stays lean (~100 lines); detail goes in `references/`, procedures go in `templates/`. Don't duplicate content between files — link instead.
- **Progressive disclosure.** Reference files are loaded on demand; keep each self-contained and under ~150 lines where practical.

## Process

1. Open an issue describing the problem (module, symptom, evidence).
2. For fixes to reference tables: a PR against `main` with the source cited in the PR description.
3. Keep commits focused — one topic per commit.

## Verification claims

If your contribution comes from real hardware bring-up, state which level you reached (Compilation / Communication / Initialization / Functional / Hardware Verified) and the setup (board, wiring, library + version).

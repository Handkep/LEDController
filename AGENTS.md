# AGENTS.md

## Language

- Prefer German when replying to the user in this project, unless the user asks otherwise.
- Project documentation should move toward English over time.
- `AGENTS.md` must stay in English.
- `docs/prototype-spec.md` may currently contain German text, but new or rewritten sections should preferably be written in English.
- When writing German text anywhere, use correct German umlauts: ä, ö, ü, Ä, Ö, Ü, and ß.
- Write technical documentation in a clear, concise, and verifiable style.

## Project Context

- This project is an ESP32-S3-based LED controller for 24V/12V/5V LED installations.
- Treat `docs/prototype-spec.md` as a requirements document, similar to a Lastenheft.
- KiCad files describe the current implementation state, but they are not automatically the source of truth for requirements.

## Handling `docs/prototype-spec.md`

- Describe stable product and system requirements, not volatile schematic details.
- Phrase requirements generally and in a way that can be verified.
- Do not add direct assignments such as `GPIO8 -> LED Out3`, `J11 pin 2 -> data channel 1`, or concrete net names as normative requirements.
- Allowed and encouraged examples are general requirements such as:
  - `12 digital LED outputs`
  - `4 PWM outputs for analog 24V LED channels`
  - `at least 4 door sensor inputs as normally closed contacts`
  - `programming and debugging interfaces via USB, UART, and JTAG`
- Component references, connector designators, pin assignments, GPIO mappings, and concrete net names do not belong in the normative main body of the requirements document.
- If the current implementation state must be mentioned, clearly mark it as a non-normative snapshot.
- If KiCad differs from the requirements document, do not blindly adapt the requirements to the current schematic. Mark the mismatch as open or requiring implementation alignment.

## Requirements Over Implementation

- For documentation changes, first ask: what must the device do?
- Derive only functional requirements from schematic details.
- Write `The system shall provide 12 digital LED outputs`, not `U7 pin 3 goes to J14`.
- Open decisions stay open until they are explicitly decided.
- Use phrases such as `Open: ...`, `To be clarified: ...`, or `Not part of this revision: ...` for unresolved topics.

## Safety and 230V

- 230V/AC is not approved unless protection class, grounding, touch protection, creepage and clearance, fuse concept, enclosure, strain relief, and certification have been clarified.
- Do not claim standards compliance, production approval, or electrical safety without explicit evidence.
- Prefer the safer requirement of an external certified power supply unless 230V on the PCB has been explicitly approved.

## KiCad Files

- Read KiCad files as context when needed, but do not copy their details unfiltered into the requirements document.
- Change KiCad files only when the task explicitly asks for it.
- Do not touch generated production data, backups, or `.history` files unless the task explicitly asks for it.

## Suggested Structure for the Requirements Document

- Goal and scope
- System overview
- Functional requirements
- Electrical requirements
- Interface requirements
- Safety and standards framework
- Mechanical requirements
- Status and open questions
- Acceptance criteria
- Optional: current implementation state as a non-normative snapshot


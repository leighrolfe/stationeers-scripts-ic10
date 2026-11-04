---
name: stationeers-ic10-developer
description: >
  Write and review IC10 scripts for Stationeers programmable chips.
  Use when the user asks to create, modify, or debug an IC10 script,
  or when working with Stationeers device automation.
---

# Stationeers IC10 Script

Write IC10 scripts for Stationeers integrated circuits (programmable chips with 6 device pins d0–d5, 16 registers r0–r15, and special registers ra/sp).

## File Header

1. Begin with a `#` comment describing what the script does at a high level. Do not detail individual tasks.
1. The header is a single sentence that tells another user what the script does and what it's attached to. Write it the way you'd explain it to a colleague over your shoulder: "This script [does what] on [what devices] in [what context] — [negative case if any]." No metaphors, no fragments, no mechanism description. 

## Naming

1. Use only alphanumeric characters in alias, define, and label names.
1. Use Title Case for all aliases and defines.
1. Use camelCase for labels.
1. Do not use LogicType property names (e.g. `Pressure`, `On`, `Off`, `Temperature`) as aliases - they collide with the property field in `l`/`s` instructions.
1. Name labels by the **action** they perform, not the condition that triggers them (e.g. `pumpOn:` not `isLow:`).
1. Be specific when the alias refers to one concrete device or value (e.g. `FuelSensor`, not `Sensor1`). Use a generic name only when the alias is intentionally reusable across multiple devices via redefinition.

## Structure

1. Begin the script with a `main:` label. Exception: initialisation code that must run only once may precede `main`.
1. Place `yield` as the first instruction in the main loop. This ensures the script resumes at a known point each tick. Exception: initialisation code that must run once before the first tick (e.g. locking devices) may precede `yield`.
1. Use `jal` to call subroutines from main. Each subroutine ends with `j ra`.
1. Name labels so their purpose is clear from the name alone.

## Devices and Registers

Every device pin, register, and numeric constant must be aliased or defined before use.
    - Devices and registers → `alias`
    - Numeric constants → `define`
    - Device name lookups → `alias Name HASH("DeviceName")`

    **Exceptions** (bare value is acceptable):
    - `0` and `1` when the meaning is self-evident from context:
      - `sleep 0` / `sleep 1` (tick count)
      - `add RegName RegName 1` / `sub RegName RegName 1` (increment/decrement)
      - `s DevPump On 1` / `s Pump On 0` (boolean state)
    - Any other numeric value, or any `0`/`1` where intent isn't obvious from the
      instruction alone, still requires a `define`.   

## Formatting

1. Single space between tokens. No indentation.
1. Separate logical blocks with a single blank line.
1. No line, including comments, can be greater than 90 characters

## Comments

1. Add a `#` comment above each label describing what the block does.
1. Add inline comments only where the code alone would be ambiguous to a human reader - units of measure, non-obvious register reuse, magic numbers, or the intent behind a comparison. Do not comment self-explanatory instructions.

## Optimisation

Prefer computing a 0/1 value with `slt`/`sge` and writing it directly over branching to set a device on/off, when the branch adds lines without adding logic.
    ```
    # Instead of:
    blt r0 target PumpOn
    bgt r0 target PumpOff
    j main
    PumpOn: s Pump On 1
    j main
    PumpOff: s Pump On 0
    j main

    # Do:
    slt r1 r0 target
    s Pump On r1
    j main
    ```

## Completion Criteria

A script is complete when:
- [ ] It compiles without errors in the in-game IC editor
- [ ] Every device, register, and constant is aliased/defined before first use
- [ ] `yield` appears at least once per execution path per tick
- [ ] No instruction exceeds the 128-instruction/tick budget in the main loop
- [ ] All labels are reachable and all `jal` calls have a matching `j ra`   

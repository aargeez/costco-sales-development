# Plan → Act → Validate → Refine

All agents and subagents follow this loop (max **3** iterations).

## Plan

- Load command contract, rules, skills, templates
- Ask user if scope unclear
- Select subagents

## Act

- Produce code/docs/tests
- Enterprise-grade only — no workarounds

## Validate

- Run tests, lint, JaCoCo as applicable
- Score against command definition of done

## Refine

- Patch from findings; increment iteration

## Exit

Threshold met **or** escalate after iteration 3 with audit note.

## On completion — tell the user the next command

Every command execution **must** end the user-facing response with a **Next command** block (see each command file § Next command). Copy the recommended command and example prompt from that section.

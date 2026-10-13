# Subagent: validation-chain-generator

## Purpose

Implement a `ValidationRule` + unit tests; register in `ValidationChainConfig` if needed.

## Inputs

- BRD rule ID or `docs/feature.md` reference
- Pass/fail messages (en-US / fr-CA)

## Outputs

- Java rule + test classes
- confidence, unresolved_assumptions

## Command Contract Alignment

**Parent command:** `/implement-api`

**Rules:** `.cursor/rules/03-backend-springboot.mdc`, `.cursor/rules/07-testing-and-quality.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`, `.cursor/rules/32-calculation-catalog.mdc` (amount validation — catalog JSON primary on error)

**Templates:** `.cursor/templates/behavior-provenance-template.md`, `.cursor/templates/test-results-template.md`

## Quality Gate

- Every branch tested (pass + each fail path)
- New rule semantics cite a BRD source or have a pending `ASSUMPTION-xxx`

## Skill

- `.cursor/skills/validation-chain/SKILL.md`

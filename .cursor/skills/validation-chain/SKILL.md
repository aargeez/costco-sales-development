---
name: validation-chain
description: >-
 Implement and test ValidationRule components and orchestrators in the API
 module (config.yaml → modules.api). Use when adding or changing business
 validation rules.
---

# Validation Chain Skill

## Pattern

1. Create `@Component` implementing the module’s validation-rule interface (Audit examples: `ValidationRule` / `SasiValidationRule`)
2. Return pass or fail with a rule id via the module’s business-rule exception at the service boundary
3. Register rules in Spring config (order matters for orchestrators)
4. Unit test each branch in isolation

## Reference implementations (Sales Audit examples — only if present in `modules.api`)

- SARO-style: `WarehouseRequiredRule`, `VaultClosedRule`, `SalesDateValidRule`
- SASI-style: `SasiWarehouseRequiredRule`, `SasiDateRangeRequiredRule`
- Orchestrators: `SasiValidationChainOrchestrator`; SARO chain via `ValidationRule` list

Do not invent new rule classes without a cited BRD / catalog source. For Vault or other products, follow types that already exist in `modules.api`.

## Do

- Map to the existing rule-id enum
- Provide en-US messages in fail paths (`BreMessages` where applicable)
- Test pass + every fail branch
- Record behavior provenance (rule 22) when adding new validation semantics

## Don't

- Skip negative-path tests
- Invent rule messages without BRD source

## Run

```bash
# config.yaml → modules.api / evals.test_commands.api
# Example: cd <modules.api> && mvn test -Dtest=*Validation*Test
```

## Subagent

`.cursor/subagents/engineering/validation-chain-generator.md`

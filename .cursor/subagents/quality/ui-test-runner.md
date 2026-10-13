# Subagent: ui-test-runner

## Purpose

**Run** Vitest unit tests and Playwright E2E, capture evidence. Do not invent or generate test scripts. Refuse to run if this command’s in-scope FR/BR still lack `docs/test-cases-br-coverage.md` Status **PASS**.

## Inputs

| Field | Description |
|-------|-------------|
| scope | Components/flows changed |
| run_e2e | `true` if user journey changed (requires UI + API running for full E2E) |

## Execution (Mandatory)

```bash
# Use config.yaml → evals.test_commands.ui / .e2e (modules.ui)
# Example: cd <modules.ui> && npm test
# If journey changed: evals.test_commands.e2e
```

## Outputs

- Vitest pass/fail counts
- Playwright results → `artifacts/e2e-test-results-<timestamp>.md` when E2E ran
- UI section in `artifacts/test-results-<date>.md` updated or cross-linked
- **confidence**, **unresolved_assumptions**

## Command Contract Alignment

**Parent commands:** `/implement-ui`, `/expand-test-coverage`

**Rules:** `.cursor/rules/07-testing-and-quality.mdc`, `.cursor/rules/10-audit-trail.mdc`

**Templates:** `.cursor/templates/test-results-template.md`, `.cursor/templates/e2e-test-results-template.md`, `.cursor/templates/audit-trail-template.md`

## Quality Gate

- `npm test` passes
- E2E updated and passing when flow changed
- `data-testid` on interactive elements for new UI
- A new screen test that calls bare `render(` instead of the module’s existing themed render helper is a quality-gate fail note in the parent audit. Do not generate the helper here. Do not change the npm test command.

## Skill

- `.cursor/skills/playwright-e2e/SKILL.md`

## Audit

Record command output summary in parent audit **Validation** section.

# Subagent: e2e-scenario-writer

## Purpose

Design Gherkin BDD scenarios and author Playwright tests under `config.yaml` → `modules.ui` (`e2e/`).

1. **Design phase** — write/update `docs/bdd-test-cases.md` from `.cursor/templates/bdd-test-case-template.md`. Every scenario shares a `case_id` with an e2e-layer row in `docs/test-cases.md`.
2. **Scripts phase** — write Playwright specs only after `docs/test-cases-br-coverage.md` Status is **PASS**.

Refuse to write spec files unless coverage is **PASS** for the in-scope UI/AC/FR/BR. Journey cases must already appear in `docs/test-cases.md` and `docs/bdd-test-cases.md`.

## Parent

test-agent (quality-agent coordinates)

## Must / Must NOT

- Must: produce `docs/bdd-test-cases.md` whenever any in-scope case has layer `e2e` or a clerk journey changed; cite FR/BR/AC; cross-link `case_id` to `docs/test-cases.md`; amount assertions from `CALC-*` / catalog JSON (do not recompute in E2E). In the scripts phase follow `.cursor/skills/playwright-e2e/SKILL.md` locator rules: MUI Select via `getByRole('combobox')`, unique `data-testid` when labels collide, and a mock for every BFF path the journey calls. When `ati-navigation-checklist.md` documents Back or handoff for the journey, Playwright specs must assert cited **returnQs shape** keys survive after return (see Playwright skill navigation section).
- Must NOT: invent journeys not in BRD/story; write Playwright while coverage is FAIL or missing; skip the BDD doc and jump straight to specs.

## Inputs

- Clerk journey description (from BRD or feature notes)
- Matching e2e-layer rows from `docs/test-cases.md` (design or scripts phase)
- `docs/test-cases-br-coverage.md` Status (scripts phase)

## Outputs

### Design phase

- `docs/bdd-test-cases.md` (Gherkin bodies via bdd-test-case-template)
- confidence, unresolved_assumptions (rule 22)

### Scripts phase (only after parent coverage PASS)

- Spec file changes under `<modules.ui>/e2e/`
- Update BDD doc **Spec path** fields
- confidence

## Failure modes

Incomplete journey AC → partial BDD doc with explicit gaps. Parent must not treat missing BDD scenarios for e2e-layer cases as covered.

## Command Contract Alignment

**Parent commands:** `/implement-ui`, `/expand-test-coverage`

**Rules:** `.cursor/rules/04-frontend-nextjs.mdc`, `.cursor/rules/07-testing-and-quality.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`, `.cursor/rules/32-calculation-catalog.mdc` (amount assertions — catalog JSON primary; do not recompute in E2E)

**Templates:** `.cursor/templates/bdd-test-case-template.md`, `.cursor/templates/e2e-test-results-template.md`

## Skill

- `.cursor/skills/playwright-e2e/SKILL.md`

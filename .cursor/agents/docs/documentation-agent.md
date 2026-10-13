# Documentation Agent

## Goal

Application summary HTML/PDF, PR drafts, audit consolidation, and root `README.md` refresh on `/deliver-sprint` closeout.

## Delegate to Subagents

| Subagent | When |
|----------|------|
| coverage-analyzer | `/application-summary` — when test-results or JaCoCo metrics are stale |
| application-summary-writer | Regenerate summary HTML/PDF artifacts |

## README (deliver-sprint closeout)

When `/deliver-sprint` Phase 2 docs closeout runs, update root `README.md` so local onboarding matches shipped surface (intro/modules, local run, API/Swagger, architecture locks). Cite only what landed in code/contracts this sprint — no invented product copy. If nothing developer-facing changed, record `README: no-op` in the deliver-sprint audit. Do not describe formula behavior that contradicts `artifacts/calculations/<module>-calculations.json`.

## Templates

See [templates/README.md](../../templates/README.md). Primary:

- `.cursor/templates/application-summary-template.md`
- `.cursor/templates/audit-trail-template.md`
- `.cursor/templates/test-results-template.md`
- `.cursor/templates/e2e-test-results-template.md`
- `.cursor/templates/review-findings-template.md` (when preparing PR evidence)

## Enforce Rules

- `.cursor/rules/02-security-and-compliance.mdc` (`/pr-assist`)
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/32-calculation-catalog.mdc` (amount math — catalog JSON primary on error)

## Skills

- `.cursor/skills/application-summary-pdf/SKILL.md`
- `.cursor/skills/jacoco-coverage/SKILL.md`

## Loop

Plan → Act → Validate → Refine (max 3)

## Exit Criteria

- Requested artifact at path in `config.yaml`
- Fresh metrics from test runs
- Root `README.md` current for sprint surface (or documented no-op on deliver closeout)
- Audit trail written

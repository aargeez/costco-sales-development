# Subagent: application-summary-writer

## Purpose

Update application summary HTML/PDF with fresh metrics.

## Inputs

- Fresh test counts and JaCoCo from quality agent or command run

## Outputs

- `config.yaml` → `artifacts.application_summary_html`
- `config.yaml` → `artifacts.application_summary_pdf`

## Command Contract Alignment

**Parent command:** `/application-summary`

**Rules:** `.cursor/rules/10-audit-trail.mdc`, `.cursor/rules/32-calculation-catalog.mdc` (do not describe formula parity that contradicts catalog JSON)

**Templates:** `.cursor/templates/application-summary-template.md`, `.cursor/templates/audit-trail-template.md`, `.cursor/templates/test-results-template.md`, `.cursor/templates/e2e-test-results-template.md`

## Skill

- `.cursor/skills/application-summary-pdf/SKILL.md`

## Quality Gate

- Metrics match latest `mvn test jacoco:report` and test counts
- Mermaid output renders and summary sections 1–7 follow the application-summary template

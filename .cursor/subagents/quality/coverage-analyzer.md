# Subagent: coverage-analyzer

## Purpose

Parse JaCoCo/Vitest output; prioritize uncovered branches by risk.

## Inputs

- `target/site/jacoco/jacoco.xml` (API)
- Optional UI lcov path

## Outputs

- Gap report with class/method recommendations
- confidence

## Command Contract Alignment

**Parent commands:** `/plan-sprint`, `/expand-test-coverage`, `/review-changes`, `/application-summary`

**Rules:** `.cursor/rules/07-testing-and-quality.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`, `.cursor/rules/13-business-analysis-invest.mdc` (when supporting `/plan-sprint`), `.cursor/rules/32-calculation-catalog.mdc` (amount coverage — catalog JSON primary on error)

**Templates:** `.cursor/templates/test-strategy-template.md`, `.cursor/templates/test-matrix-template.md`, `.cursor/templates/test-results-template.md`, `.cursor/templates/review-findings-template.md`

## Skill

- `.cursor/skills/jacoco-coverage/SKILL.md`

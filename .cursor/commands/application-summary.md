# /application-summary

## Objective

Regenerate application summary HTML/PDF with **fresh metrics**; validate prior command evidence is consistent.

**On amount-math error / conflict:** `artifacts/calculations/<module>-calculations.json` is the **primary source**; do not summarize formula “parity” from Java/SQL when it contradicts the catalog.

## When to Use

- Final step in manual flow after `/review-changes`
- Before demo, stakeholder review, or PR
- After `/deliver-sprint` closeout

## Orchestration

| Phase | Role | Agent / Subagent |
|-------|------|------------------|
| 1 Validate inputs | Primary | **documentation-agent** |
| 2 Metrics | Subagent | coverage-analyzer (if test-results stale) |
| 3 Generate | Subagent | application-summary-writer |

## Pre-flight Validation

Before regenerating summary, confirm:

| Check | Source |
|-------|--------|
| API tests ran | `artifacts/test-results-*.md` or run `api-test-runner` |
| UI tests ran | same file or `ui-test-runner` |
| JaCoCo current | latest `mvn jacoco:report` |
| Review complete | `artifacts/audit/*-review-changes.md` exists (recommended) |

If test artifacts missing or stale, **run `/expand-test-coverage` first** or re-run test runners.

## Rules

- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/32-calculation-catalog.mdc` (amount math — catalog JSON primary on error)

## Templates

- `.cursor/templates/application-summary-template.md`
- `.cursor/templates/audit-trail-template.md`

## Skills

- `.cursor/skills/application-summary-pdf/SKILL.md`
- `.cursor/skills/jacoco-coverage/SKILL.md`

## Definition of Done

- [ ] Test counts match latest test-results artifacts
- [ ] JaCoCo metrics match latest report
- [ ] HTML + PDF updated; Mermaid renders (`data-mermaid-ready="true"`)
- [ ] Summary sections 1–7 complete per template
- [ ] Audit trail written

## Outputs

- `config.yaml` → `artifacts.application_summary_html`
- `config.yaml` → `artifacts.application_summary_pdf`
- `artifacts/audit/<date>-application-summary.md`

## Next command

**On completion, tell the user:**

> ✅ `/application-summary` complete.  
> **Next command:** `/pr-assist` — optional, to open a PR with test evidence.

| Condition | Next command |
|-----------|--------------|
| Ready for PR | `/pr-assist` |
| More sprint work | `/implement-api` or `/plan-sprint` for next sprint |
| Done | None — delivery flow complete |

**Example:**

```
/pr-assist
Base branch: main
```

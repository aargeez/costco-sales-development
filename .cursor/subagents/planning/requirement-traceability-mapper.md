# Subagent: requirement-traceability-mapper

## Purpose

Build requirement inventory from BRD and compare to current code/tests to identify gaps for sprint planning.

## Inputs

| Field | Description |
|-------|-------------|
| brd_path | Approved or draft BRD |
| codebase_roots | `config.yaml` → `modules.api`, `modules.ui` |
| test_matrix_path | `docs/test-matrix.md` |

## Outputs

- `artifacts/sprints/<sprint-id>-traceability.md`
- Gap summary: Implemented | Partial | Missing | Blocked (DQ)
- **confidence**, **unresolved_assumptions**

## Command Contract Alignment

**Parent command:** `/plan-sprint`

**Rules:** `.cursor/rules/00-global-governance.mdc`, `.cursor/rules/11-intake-planning-governance.mdc`, `.cursor/rules/10-audit-trail.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`, `.cursor/rules/13-business-analysis-invest.mdc`, `.cursor/rules/32-calculation-catalog.mdc` (amount FRs — catalog JSON primary on error)

**Templates:** `.cursor/templates/requirement-traceability-template.md`, `.cursor/templates/test-matrix-template.md`

## Quality Gate

- Every in-scope FR-030–FR-044 validation has proposed test layer
- Gaps linked to files/packages where work likely lands
- Source locations and pending assumptions propagate into the sprint provenance artifact

## Audit

Record in parent audit under "Subagents delegated".

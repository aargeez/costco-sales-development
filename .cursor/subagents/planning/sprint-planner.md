# Subagent: sprint-planner

## Purpose

Produce a sprint backlog from traceability gaps and BRD priorities.

## Inputs

| Field | Description |
|-------|-------------|
| traceability_report | From requirement-traceability-mapper |
| sprint_goal | User-provided |
| sprint_duration | e.g. 2 weeks |
| team_capacity | Optional story-point or item count limit |

## Outputs

- `artifacts/sprints/<sprint-id>-plan.md` per template
- `artifacts/sprints/<sprint-id>-test-strategy.md`
- `artifacts/sprints/<sprint-id>-provenance.md`
- **confidence**, **unresolved_assumptions**

## Command Contract Alignment

**Parent command:** `/plan-sprint`

**Rules:** `.cursor/rules/00-global-governance.mdc`, `.cursor/rules/11-intake-planning-governance.mdc`, `.cursor/rules/10-audit-trail.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`, `.cursor/rules/13-business-analysis-invest.mdc`, `.cursor/rules/32-calculation-catalog.mdc` (amount SP items — catalog JSON primary on error)

**Templates:** `.cursor/templates/sprint-plan-template.md`, `.cursor/templates/test-strategy-template.md`, `.cursor/templates/behavior-provenance-template.md`, `.cursor/templates/test-matrix-template.md` (seed rows for sprint scope)

## Work Item Format

Each item MUST include:

| Field | Value |
|-------|-------|
| ID | SP-001, SP-002, … |
| Title | Short verb phrase |
| FR/BR refs | Comma-separated IDs |
| Command | `implement-api` \| `implement-ui` \| `expand-test-coverage` \| combined |
| DoD | Checklist |
| Depends on | SP-xxx or DQ resolution |
| Test evidence | unit / IT / E2E |

## Quality Gate

- No item without FR/BR trace
- Blocking DQ-xxx items either excluded or marked dependency with waiver in audit
- Sprint goal achievable within stated capacity (flag overflow)
- Planned behavior has source-backed provenance; assumptions remain pending approval
- Each `implement-ui` SP that includes entry/search/criteria: **UI session defaults** subsection per `.cursor/templates/sprint-plan-template.md` (cite each default field or state “no session defaults”)

## Audit

Record in parent audit under "Subagents delegated".

# Planner Agent

## Modes

| Mode | Command | Goal |
|------|---------|------|
| **sprint** (default) | `/plan-sprint` | BRD → sprint backlog, traceability, test strategy, implement-* mapping |
| **bounded-context** | `/plan-bounded-context` | Named bounded context readiness + Proposed plan (implement-* rows only; sequential contexts) |

For **governed story packs**, use **governed-story-planner-agent** via `/plan-governed-stories` (not this file's sprint/bounded-context modes).

Skills for all planning modes: `.cursor/skills/plan-as-input/SKILL.md`, `.cursor/skills/planning/SKILL.md`.

---

## Mode: sprint (`/plan-sprint`)

### Goal

Convert an **approved or draft BRD** into a sprint plan: prioritized backlog, work packages, traceability to FR/BR IDs, and execution command mapping.

## Delegate to Subagents

| Subagent | When |
|----------|------|
| requirement-traceability-mapper | BRD → FR/BR inventory + code/test gap analysis |
| sprint-planner | Slice backlog into sprint with capacity assumptions |

## Enforce Rules

- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/11-intake-planning-governance.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/09-agent-orchestration.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/13-business-analysis-invest.mdc`
- `.cursor/rules/32-calculation-catalog.mdc` (when sprint includes amount FRs)

## Templates

- `.cursor/templates/sprint-plan-template.md`
- `.cursor/templates/requirement-traceability-template.md`
- `.cursor/templates/test-strategy-template.md`
- `.cursor/templates/behavior-provenance-template.md`
- `.cursor/templates/calculation-catalog-template.md`
- `.cursor/templates/test-matrix-template.md` (seed rows for sprint scope)
- `.cursor/templates/audit-trail-template.md`

## Inputs

| Input | Required |
|-------|----------|
| BRD path | `config.yaml` → `modules.brd` or new draft |
| Sprint goal / duration | From user (e.g. 2 weeks, "validation chain + entry flow") |
| Current codebase state | Optional gap report from quality-agent |
| Calculation catalog | `artifacts/calculations/<module>-calculations.json` for amount SP items — **primary source** on amount errors/conflicts |

## Outputs

- `artifacts/sprints/<sprint-id>-plan.md`
- `artifacts/sprints/<sprint-id>-traceability.md`
- `artifacts/sprints/<sprint-id>-test-strategy.md`
- `artifacts/sprints/<sprint-id>-provenance.md`
- Updated seed for `docs/test-matrix.md` (sprint-scoped rows only)
- `artifacts/audit/<date>-plan-sprint-<sprint-id>.md`

## Human Gate (Mandatory)

Sprint plan status **Proposed — Pending Approval**. Do **not** invoke `/deliver-sprint` until user approves scope, priorities, and any BRD interpretations flagged as medium/high risk.

## Loop

Plan → Act → Validate → Refine (max 3)

## Exit Criteria

- Every sprint item maps to ≥1 FR/BR/NFR ID
- Amount items map to ≥1 `calculation_id` (or explicit extract dependency)
- Each item tagged with execution command: `implement-api` | `implement-ui` | `expand-test-coverage` | `api-contract`
- Dependencies and blockers explicit (open DQ-xxx, RBAC not ready, calc ASSUMPTION-*, etc.)
- Test strategy artifact maps every item to unit / IT / E2E coverage
- Provenance stub records source-backed planned behavior and pending assumptions

## Do Not (sprint mode)

- Plan work that contradicts approved BRD without escalation
- Plan formula work that contradicts the module calculation JSON without conflict resolution (catalog is primary on amount errors)
- Include out-of-scope BRD items without user waiver documented in audit

---

## Mode: bounded-context (`/plan-bounded-context`)

### Goal

Assess readiness of a **named bounded context** (wiki, prior Done, gates) and emit a Proposed sprint plan whose execute rows are **implement-* only**. Sequential: do not auto-start the next context if the prior is not Done unless a waiver is in `docs/harness/decisions.md`.

### Delegate to Subagents

| Subagent | When |
|----------|------|
| gap-identifier | Internal gap analysis (former `/gap-analysis`) |
| requirement-traceability-mapper | Context FR/BR inventory |
| sprint-planner | Slice context into sprint with capacity assumptions |
| open-questions-cataloger | Open DQ for critical gaps |

### Inputs

| Input | Required |
|-------|----------|
| `context_id` | Bounded context name |
| Wiki and/or BR checklist | For that context |
| Prior context Done evidence | Or waiver in `docs/harness/decisions.md` |

### Outputs

- `artifacts/sprints/<context_id>-gap-report.md`
- `artifacts/sprints/<context_id>-plan.md`
- `docs/bounded-context/**` (as applicable)
- `artifacts/audit/<date>-plan-bounded-context-<task>.md`

### Human Gate (Mandatory)

Status **Proposed — Pending Approval**. Do **not** emit generate-* execute steps. Do **not** start `/deliver-sprint` from this command.

### Do Not (bounded-context mode)

- Auto-approve the plan
- Start the next context without prior Done or waiver
- Emit legacy generate-* or phase-* commands as execute rows (removed from this library — use `/implement-api` / `/implement-ui` only)

# /plan-sprint

## Objective

Plan a sprint from an approved (or draft) BRD: backlog, traceability, and execution command mapping.

**On amount-math error / conflict:** `artifacts/calculations/<module>-calculations.json` is the **primary source**. Map amount SP items to `CALC-*`; do not plan formula work that contradicts the catalog.

Cross-artifact gap analysis is an **internal step** of this command (former `/gap-analysis`). Do not advertise a public gap command.

## Plan-as-input mode

When `governed_plan` or `sprint_governed_plan` is supplied, apply `.cursor/templates/plan-as-input-protocol.md` before Clarification Protocol.

## Command-Specific Hardening

Refuse unless BRD status is **Accepted** or an explicit waiver exists in `docs/harness/decisions.md`. A BRD that remains **Draft — Pending Domain Review** cannot start `/plan-sprint`. Output status is **Proposed — Pending Approval** — agents cannot self-approve.

## Orchestration

| Role | Agent / Subagent |
|------|------------------|
| **Orchestrator** | orchestrator-agent |
| **Primary** | planner-agent |
| **Subagents** | requirement-traceability-mapper → sprint-planner |
| **Support** | quality-agent (optional gap analysis via coverage-analyzer) |

## Rules

- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/11-intake-planning-governance.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/09-agent-orchestration.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/13-business-analysis-invest.mdc`
- `.cursor/rules/32-calculation-catalog.mdc` (amount FRs)

## Templates

- `.cursor/templates/sprint-plan-template.md`
- `.cursor/templates/requirement-traceability-template.md`
- `.cursor/templates/test-strategy-template.md` → `artifacts/sprints/<sprint-id>-test-strategy.md`
- `.cursor/templates/behavior-provenance-template.md` → `artifacts/sprints/<sprint-id>-provenance.md`
- `.cursor/templates/calculation-catalog-template.md`
- `.cursor/templates/test-matrix-template.md`
- `.cursor/templates/audit-trail-template.md`

## Ask Back If Missing

- BRD path (default: `config.yaml` → `modules.brd`)
- Sprint ID and goal (e.g. `sprint-01`, "validation chain + entry MVP")
- Sprint duration / capacity
- Confirm BRD approved or explicit waiver to plan from draft
- Module calculation catalog path when planning O/S / totals / pickups

## Definition of Done

- [ ] Traceability report: FR/BR → code/test status
- [ ] Amount FRs mapped to `CALC-*` (or blocked on `/extract-calculations`)
- [ ] Sprint plan with SP-xxx items, commands, dependencies
- [ ] Test strategy artifact at `artifacts/sprints/<sprint-id>-test-strategy.md`
- [ ] Provenance stub at `artifacts/sprints/<sprint-id>-provenance.md`
- [ ] Status: **Proposed — Pending Approval**
- [ ] Human gate documented — **stop before `/deliver-sprint`**

## Outputs

- `artifacts/sprints/<sprint-id>-traceability.md`
- `artifacts/sprints/<sprint-id>-plan.md`
- `artifacts/sprints/<sprint-id>-test-strategy.md`
- `artifacts/sprints/<sprint-id>-provenance.md`
- `artifacts/audit/<date>-plan-sprint-<sprint-id>.md`

## Next command

**On completion, tell the user:**

> ✅ `/plan-sprint` complete.  
> **Next command:** choose **Option A** (manual) or **Option B** (batch) — **after** sprint plan is **Approved**.

| Condition | Next command |
|-----------|--------------|
| Sprint plan needs approval (default) | **Wait** — review `artifacts/sprints/<sprint-id>-plan.md`, approve, then pick A or B |
| Sprint approved — manual (step-by-step) | `/implement-api` (or `/implement-ui` if API-only sprint) |
| Sprint approved — batch | `/deliver-sprint` |

**Example — Option A (manual, first step):**

```
/implement-api
Sprint: sprint-01
Scope: SP-001 — first backend item from sprint plan
```

**Example — Option B (batch):**

```
/deliver-sprint
Sprint: sprint-01
```

See [EXECUTION-OPTIONS.md](../EXECUTION-OPTIONS.md).

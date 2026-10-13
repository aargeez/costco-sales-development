# Sprint unified governed plan — Sprint {{SPRINT_NUMBER}}

## Metadata

| Field | Value |
|--------|--------|
| Sprint | {{SPRINT_NUMBER}} |
| Story pack source | {{STORY_PACK_PATH}} |
| Plan version | {{VERSION}} |
| Generated | {{ISO_TIMESTAMP}} |
| Generator | `/plan-governed-stories` |
| Validation summary | {{VALIDATION_SUMMARY}} |

## Executive summary

- **Sprint objectives**: …
- **Stories in scope**: …
- **Delivery approach**: story order, parallelization, shared bindings
- **Human gates**: PO / architect / compliance before cutover

## Story index (per-story execution manifests)

| Story ID | Title | Per-story plan | Readiness | Story steps complete |
|--------|-------|----------------|-----------|----------------------|
| {{ID}} | {{TITLE}} | `.cursor/plans/sprint{{N}}/{{ID}}-governed-implementation-plan.md` | Ready / Gated | 0/8 |

## Shared workspace bindings (sprint-wide)

| Key | Value | Notes |
|-----|-------|-------|
| sprint_number | {{N}} | |
| implementation_module_root | … | Shared unless story exception |
| implementation_frontend_root | … | |
| artifact_namespace | … | Capability segment |
| contract_version | sprint{{N}}-{{capability}}-v1 | |
| target_platform | PostgreSQL | |
| control_framework | `.cursor/rules/34-compliance-and-audit.mdc` + `.cursor/rules/02-security-and-compliance.mdc` | Default for `/compliance-check` |

## Sprint story orchestration order

| Order | Story ID | Rationale | Prerequisite stories |
|-------|----------|-----------|----------------------|
| 1 | … | DDL / contract foundation | — |

## Aggregated gap register (sprint-critical only)

| Gap ID | Story | Dimension | Status | Owner |
|--------|-------|-----------|--------|-------|
| G-SPRINT-01 | … | … | Open / Answered | … |

## Sprint command execution flow

Sprint-level commands run **after** per-story steps 1–8 are `complete` (or explicitly `skipped` with reason). **Status**: `pending` | `in_progress` | `complete` | `blocked` | `skipped`.

| Step | Command | Scope | Status | Runtime bindings / notes |
|------|---------|-------|--------|--------------------------|
| 9 | /compliance-check | sprint {{N}} | pending | release_or_scope_id: sprint{{N}}; evidence_inventory: `docs/sprint{{N}}/manifest.json` + all per-story §Execution logs + `docs/compliance/` |
| 10 | /sprint-handoff | sprint {{N}} | pending | sprint_id: {{N}}; story_artifact_paths: `docs/sprint{{N}}/`, `.cursor/plans/sprint{{N}}/`, manifest; daily_handoffs: scan `docs/handoffs/` |

**Invoker pattern (minimal):**

```text
/compliance-check @sprint_governed_plan: .cursor/plans/sprint3/sprint3-unified-governed-plan.md
/sprint-handoff @sprint_governed_plan: .cursor/plans/sprint3/sprint3-unified-governed-plan.md
```

Per-story commands use **per-story** plan:

```text
/implement-api @governed_plan: .cursor/plans/sprint3/452768-governed-implementation-plan.md
```

## Sprint execution log

| Step | Command | Completed (ISO) | Artifact paths | Validator | Notes |
|------|---------|-----------------|----------------|-----------|-------|
| — | — | — | — | — | — |

## Sprint-level Definition of Done

- [ ] Every in-scope story has a per-story governed plan with §Command execution flow steps 1–8 addressed.
- [ ] `docs/sprint{{N}}/manifest.json` lists all story artifacts with `kind` and paths.
- [ ] No **Open** critical gaps in aggregated register (or explicit assumptions approved).
- [ ] `/compliance-check` step `complete` with evidence mapped to controls.
- [ ] `/sprint-handoff` step `complete` with themes, roll-up, and next-sprint carryover.

## Traceability rollup

- Story pack: {{STORY_PACK_PATH}}
- Per-story plans: `.cursor/plans/sprint{{N}}/*-governed-implementation-plan.md`
- Sprint artifacts: `docs/sprint{{N}}/manifest.json`

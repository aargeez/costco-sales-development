# /generate-runbook

## Objective
Generate operational incident and support runbooks for critical workflows.

## Runtime Inputs

### Required
- `service_or_workflow`: system or workflow name for the runbook.
- `incident_scenarios`: failure modes to cover (outage, degradation, data drift, etc.).

### Optional
- `on_call_rotation`: team/channel routing for escalation section.
- `dependency_map`: upstream/downstream services affecting recovery.
- `slo_reference`: path to SLO doc for severity alignment.

## Use Agents
- sre-agent
- documentation-agent

## Delegate to Subagents
- incident-runbook-writer
- doc-structure-planner

## Enforce Rules
- .cursor/rules/16-observability-sre.mdc
- .cursor/rules/08-documentation-traceability.mdc

## Use Templates
- .cursor/templates/incident-runbook-template.md
- .cursor/templates/change-communication-template.md

## Required Output
- Incident response runbook
- Escalation and owner mapping
- Recovery verification checklist

## Command-Specific Hardening
- **Do** specify detection signals, triage steps, and verification after recovery.
- **Do** map severities to response times when SLO doc exists.
- **Don't** use placeholder contacts — use roles/channels or `TBD` with owner to fill.
- **Stop** when `incident_scenarios` are empty.

## Execution Policy
- Execution loop: Plan -> Act -> Evaluate -> Refine.
- Max iterations: 3 (escalate unresolved items after final iteration).
- Validation: `incident-runbook-writer` (`.cursor/subagents/ops/incident-runbook-writer.md`) checks trigger conditions, escalation paths, recovery steps, and verification criteria against the Required Output after each Act step.
- Refinement: agent patches artifact in-place based on validation findings before next iteration.

## Clarification Protocol

**Medium gate:**

1. What is `service_or_workflow`?
2. What `incident_scenarios` must the runbook cover?
3. What `on_call_rotation` or escalation channels apply?

## Artifact Persistence
- Save final output to: `docs/runbooks/<service-or-workflow>-runbook.md`
- Include metadata header: timestamp, iteration count, validation summary, command version (`/generate-runbook`).

## Definition of Done

- [ ] Incident runbook, escalation/owner mapping, and recovery verification checklist are actionable end-to-end.
- [ ] `incident-runbook-writer` reports no critical missing on-call routing, undefined severity thresholds, or unverifiable recovery steps.
- [ ] Incident-runbook and change-communication templates fully populated; no placeholder contact names or generic "notify team" without channel and role.
- [ ] Post-recovery verification steps are testable by on-call.

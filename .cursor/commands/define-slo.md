# /define-slo

## Objective
Define service SLI/SLO targets and operational alert expectations.

## Runtime Inputs

### Required
- `service_or_capability_key`: service name or bounded context for SLO doc.
- `critical_user_journeys`: flows that SLIs must protect (availability, latency, error rate).

### Optional
- `existing_observability`: dashboards, metrics, or log queries already in place.
- `error_budget_policy_hint`: team standard for burn alerts and freeze rules.
- `on_call_routing`: escalation paths for alert sections.

## Use Agents
- sre-agent

## Delegate to Subagents
- slo-definition-writer
- nfr-checklist-builder

## Enforce Rules
- .cursor/rules/16-observability-sre.mdc
- .cursor/rules/00-global-governance.mdc

## Use Templates
- .cursor/templates/slo-sli-template.md
- .cursor/templates/test-strategy-template.md

## Required Output
- SLI/SLO matrix
- Alert and escalation baseline
- Error budget policy

## Command-Specific Hardening
- **Do** define measurable SLIs with data sources and windows.
- **Do** align alerts to SLO burn rates, not vanity metrics.
- **Don't** set SLO targets without observability feasibility check.
- **Stop** when `critical_user_journeys` are undefined.

## Execution Policy
- Execution loop: Plan -> Act -> Evaluate -> Refine.
- Max iterations: 3 (escalate unresolved items after final iteration).
- Validation: **slo-definition-writer** checks SLI/SLO definitions, measurement windows, alert/escalation alignment, and error-budget policy coherence after each Act step.
- Refinement: agent patches artifact in-place based on validation findings before next iteration.

## Clarification Protocol

**Medium gate:**

1. What is `service_or_capability_key`?
2. What `critical_user_journeys` define SLI scope?
3. What observability exists today (`existing_observability`)?

## Artifact Persistence
- Save final output to: `docs/sre/slo/<service-or-capability-key>-slo-definition.md`
- Include metadata header: timestamp, iteration count, validation summary, command version (`/define-slo`).

## Definition of Done

- [ ] SLI/SLO matrix, alert/escalation baseline, and error budget policy are complete and internally consistent.
- [ ] **slo-definition-writer** reports no critical gaps in measurable SLIs, SLO targets, or burn/alert semantics.
- [ ] **slo-sli-template.md** (and **test-strategy-template.md** where referenced) are fully populated with no placeholder text remaining.
- [ ] Every SLO row cites a measurable SLI and observation method.

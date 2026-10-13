# Subagent: Incident Runbook Writer

## Purpose
Produces actionable incident response runbooks for the top-ranked failure modes of a service or capability, including mitigation, rollback, escalation paths, and post-incident verification.

## Required Inputs
- `service_or_capability`: string — bounded scope (e.g., API name, job, integration).
- `failure_modes`: list — ranked failure modes with symptoms, blast radius, and known dependencies (or link to SRE/architecture doc).
- `environment_context`: object — prod/stage, regions, deployment model (e.g., GCP, k8s, Cloud Run), and on-call/escalation contacts.
- `rollback_and_mitigation_constraints`: string (optional) — approved rollback mechanisms, change windows, regulatory or data constraints.

## Outputs
- `runbook_document`: structured markdown — per failure mode: detection, triage steps, mitigation, rollback procedure, escalation matrix (L1/L2/vendor), communication snippets, verification checks, and post-incident follow-ups.
- `failure_mode_coverage_matrix`: table — failure mode × runbook section completeness (Y/partial/N).
- `open_questions`: list — items needing SRE/architect/SME confirmation.
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., undocumented dependencies, unverified rollback steps, placeholder contacts.

## Failure Modes
- Incomplete or ambiguous failure mode list yields partial runbooks only for documented modes; undocumented modes listed as gaps.
- Missing environment or contact data produces runbooks with explicit placeholders and cannot mark verification/escalation as validated.
- Escalation: return partial result with explicit gaps to parent agent — include `failure_mode_coverage_matrix` and `open_questions` so the parent can reconcile or re-invoke with richer inputs.

## Quality Gate
- Parent accepts when every supplied failure mode has mitigation, rollback (or justified N/A with risk note), escalation, and verification sections; no silent gaps; assumptions and confidence justified.
- Parent rejects when steps are generic boilerplate without service-specific commands/dashboards, when rollback contradicts stated constraints, or when confidence is high despite unresolved critical assumptions.

# Subagent: NFR Checklist Builder

## Purpose
Builds a non-functional requirements checklist covering reliability, latency, security, and observability for a capability or release, mapping each NFR to measurable acceptance criteria and verification methods.

## Required Inputs
- `capability_scope`: string — services, user journeys, data stores, and integration points.
- `stakeholder_priorities`: object (optional) — ranked concerns (e.g., RTO/RPO, PII, regional deployment).
- `baseline_standards`: string (optional) — org SRE, security, and compliance baselines to inherit.

## Outputs
- `nfr_checklist`: table — category (reliability | latency | security | observability | scalability | compliance | operability), requirement statement, measurable AC, verification (test, monitor, review), owner role.
- `traceability`: list — NFR id → related SLOs, threat controls, or policy clauses if known.
- `gaps_and_tbd`: list — NFRs that cannot yet be measured or owned.
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., unknown traffic growth, unset RTO/RPO, provisional security tier.

## Failure Modes
- Undefined scope produces checklist only for named components with OUT_OF_SCOPE_EXPLICIT.
- Missing priorities defaults to balanced baseline with flagged DEFAULT_WEIGHTING.
- Escalation: return partial result with explicit gaps to parent agent — deliver complete rows for known areas and `gaps_and_tbd` for the rest.

## Quality Gate
- Parent accepts when each NFR has measurable AC and a verification method or explicit TBD with reason, categories cover reliability/latency/security/observability at minimum, and owners are plausible roles.
- Parent rejects when ACs are vague (“fast”, “secure”), duplicate rows without merge, or critical compliance needs are omitted without waiver.

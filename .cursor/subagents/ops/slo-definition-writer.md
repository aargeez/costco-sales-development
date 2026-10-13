# Subagent: SLO Definition Writer

## Purpose
Defines SLIs, SLO targets, and error budgets per service or capability, and maps each SLO to concrete monitoring queries, dashboards, and alerting policies suitable for GCP-native operations.

## Required Inputs
- `service_inventory`: list — service or capability names, owners, and user-facing vs internal classification.
- `critical_user_journeys`: list — journeys or APIs/jobs that matter for availability and latency (with optional traffic or business weighting).
- `baseline_or_requirements`: object (optional) — existing NFRs, contractual targets, or historical metrics windows.
- `observability_stack`: string — e.g., Cloud Monitoring, Prometheus, log-based metrics, trace backends in use.

## Outputs
- `slo_definitions`: table — per service: SLI name, measurement spec (numerator/denominator), SLO target (% or ms), measurement window, error budget policy.
- `monitoring_mapping`: list — each SLO linked to metric names, log filters, dashboard panels, and alert condition thresholds with burn-rate or multi-window guidance where applicable.
- `alerting_routing`: object — severity, notification channels, runbook links per alert.
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., missing traffic data, proxy SLIs, unowned services, guessed thresholds.

## Failure Modes
- Incomplete service or journey list produces SLOs only for known scope; remainder flagged as out-of-scope gaps.
- Ambiguous measurement surfaces (no metric access) forces document-level SLI definitions without validated query IDs.
- Escalation: return partial result with explicit gaps to parent agent — deliver `slo_definitions` for in-scope items plus `Unresolved assumptions` for missing data or tooling.

## Quality Gate
- Parent accepts when each in-scope service has at least availability- and latency-relevant SLIs (or documented waiver), error budget is computable, and every SLO maps to at least one monitorable signal and alert or explicit “observe only” rationale.
- Parent rejects when targets are copied without measurement method, when alerting cannot fire on the defined SLI, or when confidence is overstated relative to assumptions.

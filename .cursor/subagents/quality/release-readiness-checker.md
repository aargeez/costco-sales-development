# Subagent: Release Readiness Checker

## Purpose
Validates a release candidate against testing, security, performance, and operational readiness gates, producing a consolidated view of blockers with owner, severity, and target resolution dates.

## Required Inputs
- `release_id`: string — version, build ID, or change ticket reference.
- `scope`: string — services, migrations, or features in this release.
- `evidence_artifacts`: object — links or paths to test reports, security scans, perf results, change records, runbooks, feature flags, and rollback plans.
- `gate_policy`: string (optional) — org-specific checklist or template reference; defaults to project quality/security/ops baselines.

## Outputs
- `gate_assessment`: table — gate (testing | security | performance | ops | compliance | data_migration), status (pass | fail | waived | unknown), evidence pointer, notes.
- `blockers`: list — id, description, severity (critical | high | medium | low), owning role/team, due_date or “TBD”, dependency links.
- `waivers`: list — waived items with approver and rationale (empty if none).
- `summary_verdict`: enum — NO_GO | GO_WITH_CONDITIONS | GO.
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., missing evidence interpreted as unknown, stale scan age, ambiguous waiver authority.

## Failure Modes
- Missing or contradictory evidence forces `unknown` or `fail` per gate policy; never silently pass.
- Ambiguous release scope excludes unchecked components with explicit exclusion list.
- Escalation: return partial result with explicit gaps to parent agent — deliver `gate_assessment` with gaps flagged and `blockers` for all failed/unknown gates without invented owners; use “TBD” where ownership unknown.

## Quality Gate
- Parent accepts when every in-scope gate has a defensible status, blockers have severity and owner or explicit TBD with escalation path, and summary verdict matches gate outcomes.
- Parent rejects when critical failures are omitted, waivers lack approver/rationale, or verdict is GO despite documented critical blockers.

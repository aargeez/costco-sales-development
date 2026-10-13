# Subagent: Canary Rollout Planner

## Purpose
Defines phased canary rollout cohorts, guardrails, and rollback triggers for a production change, with success criteria and promotion checkpoints suitable for GCP deployments and replatformed services.

## Required Inputs
- `change_description`: string — what ships (services, config, migration step), blast radius, and feature-flag usage if any.
- `traffic_and_cohort_options`: object — available segmentation (region, tenant %, internal users, header-based), and minimum viable cohort sizes.
- `observability_hooks`: string — metrics, logs, traces, SLOs, and alerts that must hold during rollout.
- `rollback_mechanisms`: string — deploy revert, flag off, DB forward-only constraints, or runbook reference.

## Outputs
- `cohort_plan`: table — phase name, cohort definition, traffic % or absolute cap, duration or promotion trigger, owner approver role.
- `guardrails`: list — metric thresholds, error budget burn limits, synthetic checks, and manual gates between phases.
- `rollback_triggers`: list — automatic and manual triggers with actions and verification steps.
- `success_criteria`: list — per-phase exit criteria tied to observability signals and business checks.
- `promotion_checkpoints`: ordered list — what evidence is collected before next phase.
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., cannot segment traffic, single-tenant only, migration irreversibility.

## Failure Modes
- Missing rollback path produces plan with explicit NO_SAFE_ROLLBACK section and halts recommendation for automated promotion.
- Unknown traffic shaping forces cohorts described conceptually with NEEDS_PLATFORM_CONFIRMATION.
- Escalation: return partial result with explicit gaps to parent agent — deliver `cohort_plan` and `guardrails` with assumptions; parent resolves platform limits.

## Quality Gate
- Parent accepts when each phase has measurable exit criteria, rollback triggers align with available mechanisms, and riskiest phases have smaller cohorts or stricter gates.
- Parent rejects when plan promotes without SLO/alert coverage, rollback contradicts migration reality, or assumptions hide irreversible steps.

# Subagent: Data Cleaner

## Purpose
Define data-cleansing policies for invalid, duplicate, and orphan records and produce before/after quality scorecards to support migration and ongoing data quality gates.

## Required Inputs
- `source_profile`: statistics or sample — null rates, formats, cardinality, duplicate keys, orphan FKs, domain value distributions.
- `target_rules`: validation rules, target schema constraints, and business keys — what “clean” means for the slice.
- `cleansing_scope` (optional): table/list — entities or columns in scope; exclusions for read-only legal holds.

## Outputs
- `cleansing_policies`: document — per issue class (invalid, duplicate, orphan): detection rule, treatment (reject / quarantine / fix / default), owner, and idempotency notes.
- `scorecard_before`: metrics — row counts, error counts, duplicate clusters, orphan counts, dimension-level quality scores.
- `scorecard_after`: metrics — same measures post-policy application (projected or simulated if dry-run).
- `quarantine_and_audit`: list — fields to log for audit; retention of rejected rows.
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., authoritative duplicate survivor rule not decided, orphan parent missing in source extract timing.

## Failure Modes
- Missing profile or target rules blocks quantitative scorecards; policies are drafted as conditional with explicit data collection steps.
- Escalation: return partial result with explicit gaps to parent agent

## Quality Gate
- Parent accepts when policies reference concrete columns/keys, before/after metrics use the same definitions, and duplicate/orphan handling does not contradict target constraints; reject if policies are generic SQL advice without entity/key mapping or scorecards omit baselines.

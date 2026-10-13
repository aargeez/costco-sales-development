# Subagent: Rule Extractor

## Purpose
Converts parsed legacy program logic into explicit business rules expressed as trigger, condition, and outcome, tagging unknown or ambiguous behavior for SME review and traceability to source spans.

## Required Inputs
- `normalized_logic_map`: object — output or equivalent from `code-analyzer` (or equivalent structured parse).
- `domain_glossary`: object (optional) — approved terms, codes, and table field meanings.
- `prior_rules_catalog`: list (optional) — existing rules to merge or deduplicate against.

## Outputs
- `business_rules`: list — rule_id, name, trigger (event/batch/timer), condition (predicates), outcome (actions, status codes, messages), source_refs (program, section, lines).
- `unknowns_for_sme`: list — ambiguous branches, magic values, undocumented codes, conflicting outcomes across modules.
- `deduplication_notes`: list — merged or conflicting rules with rationale.
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., inferred intent from variable names, missing business glossary entries.

## Failure Modes
- Sparse logic map forces rules marked INFERRED_WITH_LOW_CONFIDENCE or deferred to `unknowns_for_sme`.
- Contradictions between modules produce separate rule candidates plus CONFLICT entry rather than silent merge.
- Escalation: return partial result with explicit gaps to parent agent — deliver high-confidence `business_rules` and comprehensive `unknowns_for_sme`; parent schedules SME review.

## Quality Gate
- Parent accepts when each rule has trigger/condition/outcome, source_refs present for implemented logic, and unknowns are exhaustive for ambiguous spans.
- Parent rejects when rules invent data elements not in the map, conflicts are hidden, or SME-unknown items are marked high confidence.

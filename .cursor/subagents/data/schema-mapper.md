# Subagent: Schema Mapper

## Purpose
Produce source-to-target data mapping with stable lineage IDs, documenting transformations, default values, and constraints for migration, APIs, and traceability.

## Required Inputs
- `source_schema`: DDL, data dictionary, or field list — names, types, keys, nullability, sample values.
- `target_schema`: DDL or canonical model — names, types, keys, constraints, enumerations.
- `existing_migration_inventory` (optional): baseline from repo migrations — used to mark target columns/tables as **already applied** vs **delta needed**.
- `business_keys`: list — natural keys or match rules for entity identity across systems.
- `legacy_semantics` (optional): notes — calculated fields, code-derived values, or AS400-specific representations.

## Outputs
- `mapping_table`: structured artifact — lineage_id, source_path, target_path, transformation (expression or rule ref), default_value, null_handling, constraint_notes.
- `baseline_delta_notes` (when baseline provided): list — per target object: present in repo migrations (yes/partial/no) -> recommended forward action (skip | ALTER ADD | new CREATE only).
- `transformation_catalog`: list — reusable transforms (e.g., date formats, code lookups) with inputs/outputs.
- `unmapped_report`: source-only and target-only fields with disposition (out of scope, derived later, gap).
- `validation_rules`: list — checks to run pre/post load (type, range, FK, uniqueness).
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., ambiguous source column meaning, target enum incomplete, soft-delete vs hard-delete behavior undecided.

## Failure Modes
- Partial schemas produce partial mappings; unmapped items and required SME questions are listed explicitly; no silent best-guess on business keys.
- Escalation: return partial result with explicit gaps to parent agent

## Quality Gate
- Parent accepts when every in-scope target column is sourced or marked intentional default/derived, lineage IDs are unique and stable strings, and transformations are executable descriptions (not vague “map as appropriate”); reject if FK/target constraints are ignored or defaults conflict with NOT NULL rules.

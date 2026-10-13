# Subagent: API Contract Designer

## Purpose
Draft OpenAPI and/or GraphQL contracts from **documentation packs** (source of truth, workflows, data model, DDL) with examples, error taxonomy, documentation traceability, and compatibility notes.

## Required Inputs
- `capability_scope`: screen code(s) or named slice
- `source_artifacts`: paths to documentation pack (or Sales Vault defaults from `/implement-api` Phase 1 / api-agent)
- `operation_candidates`: from `flow-simplifier` — resources, verbs, actors, sequencing notes
- `schema_bindings`: from `schema-mapper` — DTO field mappings, unmapped report
- `gap_report`: from `gap-identifier` — must have no **blocker** gaps before render
- `auth_model` or `auth_model_hint`
- `existing_contracts` (optional)
- `table_inventory` (optional): in-scope tables from data model when refining schemas
- `api_mode`: `table_level` | `composed` (maps to `resource_strategy` / `write_strategy`)
- `required_methods`: per-resource HTTP methods to cover
- `wrapper_contract` or `envelope_policy`
- `contract_version`
- `service_or_slice_name`
- `implementation_module_root` (optional)
- `nfr_checklist` (optional): from `nfr-checklist-builder` — security/observability constraints

## Documentation traceability (required per operation)
Each operation must cite at least one of:
- FR-* or BR-* id from `vault-source-of-truth.md`
- Workflow step (§7) or screen option (§8)
- Data-model table row or DDL entity from `schema_bindings`

Do **not** require user story IDs or Gherkin AC.

## Outputs
- `openapi_yaml` and/or `graphql_sdl`
- `schema_examples`
- `error_catalog`
- `error_code_matrix`
- `endpoint_resource_mapping`
- `endpoint_catalog`
- `documentation_traceability_matrix`: operation → FR-/BR-/workflow/screen/table source
- `coverage_matrix` for required resources/methods
- Confidence + unresolved assumptions (`ASSUMPTION-<id>` when sources are silent)

## Required REST semantics
- POST create operations that create addressable resources must explicitly define 201 success.
- Include `Location` header semantics/path template when resource identity is surfaced.
- Define list-read status semantics (default 200 unless contract overrides).
- Encode BR-/FR-driven validation and conflict rules in operation descriptions, request schemas, or documented error codes — each with source citation.

## Quality Gate
Reject drafts when:
- required methods/resources are missing,
- error matrix is incomplete for declared failures,
- envelope rules are ambiguous,
- contract relies on undocumented sample-code assumptions instead of explicit spec content,
- any operation lacks documentation traceability,
- `gap_report` contains unresolved **blocker** gaps for the slice,
- business behavior is invented without `ASSUMPTION-<id>` approval record.

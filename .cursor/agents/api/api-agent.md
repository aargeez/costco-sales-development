# API Agent (REST + GraphQL)

## Goal
Own versioned, consumer-safe API contracts (REST OpenAPI and GraphQL) with compatibility rules, examples, and consumer-impact documentation for each capability slice.

## Runtime Configuration
- `capability_scope`: screen code(s) or named slice (e.g. `VACE`, `vace-pickups`) — required for documentation-driven contract work on `/implement-api` Phase 1
- `source_artifacts`: documentation pack paths; defaults to Sales Vault pack when omitted (see `/implement-api` and api-agent verify/sync)
- `service_or_slice_name`: naming segment for contract artifacts
- `primary_interface`: REST | GraphQL | hybrid
- `resource_strategy`: table_level | domain_resource
- `write_strategy`: composed | non_composed
- `contract_version`: version label for the contract line
- `existing_contract_path` (optional): prior contract for compatibility mode
- `auth_model_hint` (optional): OAuth2, API key, mTLS, or project default
- `implementation_module_root` (optional): service root used for naming alignment
- `wrapper_contract` (optional)
- `envelope_policy` (optional): `none` | `project_default`
- `reference_locations` (optional): non-binding examples only

## Delegation (for `/implement-api` Phase 1 and alignment reviews)

See `/implement-api` for the documentation-driven contract verify/sync pipeline.

During `/implement-api` and `/implement-ui`, act as **contract authority** when API shape is in scope:
- Verify Java controllers and FE adapters match the published OpenAPI.
- Flag drift; route schema changes back through api-agent sync — do not fork contracts during implementation or UI wiring.
- On the same verify pass, for each operation the UI calls, record query parameter names and response JSON property names: OpenAPI name → client symbol → test assert. A silent rename is drift. Do not add a phase or a public command for this table.

## Core generation policy
- Contracts must be complete and valid without requiring an external sample repository.
- `implementation_module_root` + rules/templates are enough to derive conventions when references are absent.
- If references conflict with compatibility, security, or contract rules, contract/rules win.

## Capabilities
- Derive REST/GraphQL operations from documentation (FR-*, BR-*, workflows, screens, DDL) — not user stories.
- Author and evolve OpenAPI 3.x and/or GraphQL schemas.
- Classify additive vs breaking changes and document compatibility posture.
- Produce error code matrix, endpoint catalog, and endpoint-resource mapping with documentation traceability rows.
- For REST create operations, document 201 + `Location` semantics when identity exists.
- Define envelope behavior explicitly (`wrapper_contract` or `envelope_policy`).
- Hard-stop on `vault-source-of-truth.md` §12 Known Gaps that block contract semantics for the requested slice.

## Exit Criteria
- Versioned contract artifacts complete and lint-valid.
- Error model, error code matrix, and examples cover happy + negative paths in scope.
- Endpoint catalog and endpoint-resource mapping are complete for required methods/resources.
- Contract semantics are actionable for backend generation without relying on sample code.

## Artifact Persistence
- `docs/api/contracts/<service-or-capability>/<contract_version>/openapi.yaml` (or `.json`)
- `docs/api/contracts/<service-or-capability>/<contract_version>/schema.graphql` (if GraphQL)
- `docs/api/contracts/<service-or-capability>/<contract_version>/error-code-matrix.md`
- `docs/api/contracts/<service-or-capability>/<contract_version>/endpoint-resource-mapping.md`
- `docs/api/contracts/<service-or-capability>/<contract_version>/endpoint-catalog.md`

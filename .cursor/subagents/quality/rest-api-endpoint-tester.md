# Subagent: REST API Endpoint Tester

## Purpose
Execute and assess REST endpoint scenarios, including positive and negative paths, with contract-shape verification and reproducible command evidence. Primary consumer: `/validate-api-endpoints` for **Sales Vault** documentation-driven validation (journey order from `/implement-ui` wiring notes when supplied).

## Required Inputs
- `base_url`: string — service host and port, e.g. `http://localhost:8080` for `vault-be`.
- `rest_base_path`: string — REST base path (Sales Vault default `/api/v1`).
- `capability_scope`: screen code(s) or journey slice — filters scenario matrix.
- `contract_version`: version label matching contract pack.
- `endpoints` or `critical_endpoints`: list — REST resources to test; **derive when omitted** from `endpoint-catalog.md` + OpenAPI filtered to `capability_scope`.
  - **Wiring / journey mode:** derive from `api_sequence_specification` or `fe-be-sequence-wiring.md`; execute in sequence order when steps are dependent.
- `contract_paths` or `openapi_excerpt` (**required for Sales Vault**): authoritative shapes from OpenAPI pack (`openapi.yaml`, `envelope-policy.md`, `error-code-matrix.md`) produced or verified on `/implement-api`.
- `data_source_modes`: list (optional) — expected data-source selectors (Sales Vault: `vault` schema via `sales-vault-ddl/`).
- `scenario_policy`: object — required positive/negative scenarios; include `error_code_matrix` rows for negative paths and BR-/FR-* sequencing guards.

## Sales Vault envelope assertions
When `envelope-policy.md` declares `project_default`:
- Success `2xx`: body includes `correlationId` and `data`; optional `meta`.
- Error `4xx/5xx`: body includes `correlationId` and `errors[]` with `code` matching `SV-*` rows in `error-code-matrix.md`.
- Mutations: request body wrapped as `{ "data": <payload> }`; assert `X-Correlation-Id` header when contract requires it.
- Do **not** assert legacy `success`/`response` wrappers unless contract explicitly defines them.

## Outputs
- `scenario_results`: table — scenario_id, operationId, endpoint, request, expected (status + envelope + BR-/FR-*), actual, status.
- `contract_assertions`: list — checks for `correlationId`, `data`/`errors`, status code, `Location` on create.
- `failed_cases`: list — scenario_id, failure detail, likely cause, recommended fix, source gap (§12 / ASSUMPTION).
- Confidence: high | medium | low
- Unresolved assumptions: list

## Failure Modes
- If endpoint list is incomplete, produce partial execution with explicit `NEEDS_SCOPE_COMPLETION`.
- If backend is unreachable, return connectivity diagnostics and stop further endpoint assertions.
- If contract pack is missing, return `NEEDS_CONTRACT_PACK` — do not invent endpoints.
- Escalation: return partial result with explicit gaps to parent agent.

## Quality Gate
- Parent accepts when all critical REST scenarios for `capability_scope` are executed, mandatory negative paths from `error-code-matrix.md` are present, and envelope/contract checks are explicit.
- Parent rejects when status-only checks are provided without payload validation, traceability cites story IDs without documentation locators in Sales Vault mode, or failures lack remediation guidance.

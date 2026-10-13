# /validate-api-endpoints

## Objective
Validate and regression-test **REST** (and **GraphQL** when in scope) endpoints for a Sales Vault slice or service with reproducible commands, negative-path coverage, masked/redacted evidence, and auditable artifacts. Produce an endpoint/scenario matrix, executed command log, negative-path findings, remediation notes, and a clear verdict (**ready** / **conditionally ready** / **not ready**).

Traceability is **documentation-driven** — FR-*, BR-*, OpenAPI operations, `api_sequence_specification` steps, and `error-code-matrix.md` rows — **not** user stories or Gherkin AC.

**Upstream prerequisites:** `/implement-api` (and `/implement-ui` when validating FE parity) for the same `capability_scope` and `contract_version`; optional `.cursor/templates/fe-be-sequence-wiring-template.md` when validating journey order.

**Workspace hooks:** Submitting a prompt that contains **`/validate-api-endpoints`** runs [`.cursor/hooks/scaffold-from-command.mjs`](../hooks/scaffold-from-command.mjs) (`beforeSubmitPrompt`), which ensures **`docs/quality/api-validation/`** (and sprint mirror paths when `sprint_number` is in scope) exist before the agent executes. See [`.cursor/hooks/README.md`](../hooks/README.md).

## Runtime Inputs

### Required (Sales Vault — resolve defaults when omitted)
- `capability_scope`: screen code(s) or named slice — `VACE` | `VACI` | `VABI` | `VAOD` | `VACO`, or journey slice (e.g. `vace-pickup-bleed`, `vaco-cutoff`).
- `contract_version`: version label matching the contract pack (default `v1.0.0`).
- `implementation_module_root`: Spring Boot module root — default `vault-be/`.

### Optional
- `base_url`: running service origin — default `http://localhost:8081` (override via `BASE_URL`; `vault-be` uses port **8081**).
- `rest_base_path`: REST prefix after host — default `/api/v1` per Sales Vault OpenAPI `servers`.
- `contract_path`: authoritative contract pack — default `docs/api/contracts/sales-vault/<contract_version>/openapi.yaml`.
- `graphql_path`: default `/graphql` when `primary_interface` is `GraphQL` or `hybrid`; **omit** for Sales Vault v1.0.0 REST-only slices unless contract declares GraphQL.
- `artifact_namespace`: traceability segment for docs (default `sales-vault`).
- `source_artifacts`: documentation pack paths — defaults apply when omitted (see below).
- `api_sequence_specification`: ordered steps for journey validation (from `fe-be-sequence-wiring.md` + §7 workflow order, or implement-ui wiring notes).
- `critical_endpoints` (optional): explicit list of method + path + scenario IDs; if omitted, derive from `endpoint-catalog.md`, OpenAPI, and `error-code-matrix.md` filtered to `capability_scope`.
- `primary_interface` (optional): `REST` (default) | `GraphQL` | `hybrid`.

### Story-driven replatforming only (optional — not Sales Vault default)
- `governed_plan`: `.cursor/plans/sprint<N>/<story-id>-governed-implementation-plan.md`.
- `story_scope`: story IDs or AC artifacts — use **only** when story packs are the declared input.
- `plan_step`: row in §Command execution flow; default first `pending` row for `/validate-api-endpoints`.

### Default `source_artifacts` (Sales Vault)

Resolve documentation pack from **`config.yaml` → `vault.source_of_truth`** (fallback **`modules.brd`**). Example default filename in `config.examples/sales-vault.config.yaml`: `vault-source-of-truth.md`.

| Priority | Artifact | Use for |
|----------|----------|---------|
| 1 | Resolved SoT path (above) | BR-*, FR-*, §7 workflows, §8 screens, §12 Known Gaps |
| 2 | `docs/api/contracts/.../openapi.yaml` | Operations, schemas, envelope policy |
| 3 | `docs/api/contracts/.../endpoint-catalog.md` | Scenario matrix seed — method, path, operationId, screen |
| 4 | `docs/api/contracts/.../error-code-matrix.md` | Required negative paths and expected `SV-*` codes |
| 5 | `docs/api/contracts/.../fe-be-sequence-wiring.md` | Journey order and step dependencies |
| 6 | `config.yaml` → `modules.ui` traceability (`vault.screenconversion.traceability` or `src/lib/traceability/screenRequirements.ts`) | Route and session context for FE-readiness notes |
| 7 | `sales-vault-ddl/` + `field-traceability.md` | Persistence-backed route assumptions |

**Out of scope:** VACX, VAEX, DDE write paths, STE clerk flows — per scope rule.

## Documentation-as-input mode

When `contract_path` (or default contract pack) is supplied, resolve validation inputs before Clarification Protocol:

| Validation input | Source |
|------------------|--------|
| `critical_endpoints` | `endpoint-catalog.md` + OpenAPI paths filtered to `capability_scope` |
| Scenario order (journeys) | `api_sequence_specification` or `fe-be-sequence-wiring.md` §Sequence |
| Negative paths | `error-code-matrix.md` rows + BR-/FR-* sequencing guards (BR-005, BR-007, BR-013, DV-007) |
| Envelope assertions | `envelope-policy.md` — success `{ correlationId, data, meta? }`; errors `{ correlationId, errors[] }` |
| Auth expectations | OpenAPI security + resolved SoT §5 (BR-006 role-scoped reads) |
| `rest_base_path`, `base_url` | OpenAPI `servers` + `implementation_module_root` local defaults |

**Hard-stop** when contract pack is missing or stale vs `capability_scope`, backend is not running and smoke cannot proceed, or §12 Known Gaps block validation semantics for a declared scenario.

**Do not** invent endpoints or error codes not present in the contract pack; escalate gaps via `ASSUMPTION-*` or §12 references.

## Plan-as-input mode (story-driven only)

When `governed_plan` is supplied (general replatforming), resolve inputs per `.cursor/templates/plan-as-input-protocol.md`:

| Input | Plan source |
|-------|-------------|
| `implementation_module_root` | §Workspace bindings |
| `contract_version`, `artifact_namespace` | §Workspace bindings |
| `story_scope` | §Story pack story ID |
| `critical_endpoints` | §Story pack API mapping + §Traceability contracts |

Hard-stop if backend/API contract steps are not `complete` unless waived in plan. Update plan execution sections and manifest on completion.

## Generation Baseline (must always apply)
- Follow [`.cursor/rules/20-api-endpoint-validation.mdc`](../rules/20-api-endpoint-validation.mdc): positive + negative paths, contract-shape checks, no secrets in persisted evidence.
- Align URLs and envelope shapes with **implemented** DTOs/OpenAPI — Sales Vault REST uses **`data`/`errors`** (`ApiResponse` / `envelope-policy.md`), not legacy `success`/`response`.
- Assert `correlationId` on success and error envelopes when `project_default` envelope is in scope.
- Mask tokens, cookies, and PII in snippets; describe auth headers generically.

## Prerequisites (typical upstream)
- `vault-be` running locally or on a named test host; TLS/corporate CA documented if curl fails PKIX.
- Database/migrations consistent with IT profile (`sales-vault-ddl/scripts/apply.sh`) when validating persistence-backed routes.
- Test session context documented: warehouse id, open sales date, register, pickup # as required by scenario matrix.

## Use Agents
- [api-endpoint-validation-agent](../agents/testing/api-endpoint-validation-agent.md)
- [test-results-logger-agent](../agents/testing/test-results-logger-agent.md)

## Delegate to Subagents
- [rest-api-endpoint-tester](../subagents/quality/rest-api-endpoint-tester.md)
- [graphql-api-endpoint-tester](../subagents/quality/graphql-api-endpoint-tester.md) — only when GraphQL is in scope
- [test-results-recorder](../subagents/quality/test-results-recorder.md)

## Related Commands
- `/implement-ui` — supplies wiring notes for journey-order validation when applicable.
- `/expand-test-coverage` — consumes validation findings when extending API test scripts.
- `/implement-api` — source of truth for routes and error semantics (OpenAPI + backend slice).

## Enforce Rules
- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/02-security-and-compliance.mdc`
- `.cursor/rules/07-testing-and-quality.mdc`
- `.cursor/rules/08-documentation-traceability.mdc`
- `.cursor/rules/20-api-endpoint-validation.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/overlays/sales-vault/scope.mdc` (when active product is Sales Vault)

## Use Skills
- `.cursor/skills/api/SKILL.md` — objective API review for correctness, security, and operational readiness (when present).

## Use Templates
- `.cursor/templates/api-validation-template.md`
- `.cursor/templates/api-test-evidence-template.md`
- `.cursor/templates/test-results-log-template.md`

## Required Output
- **Endpoint matrix** (REST + GraphQL when scoped) with **PASS/FAIL** (or blocked) per scenario, linked to FR-/BR-*, OpenAPI `operationId`, and `error-code-matrix` rows — not story IDs.
- **Executed commands** with timestamps, environment (`BASE_URL`, OS, curl version), and HTTP status / exit metadata.
- **Negative-path findings** (invalid input, wrong method, malformed GraphQL when scoped, oversize body, auth failures, sequencing guards) with remediation.
- **Final verdict** and **Traceability** subsection (mandatory — cite documentation and contract locators).
- **Blocking findings list**: any required-negative scenario that returned 5xx, each with owner and re-test command.

## Command-Specific Hardening

### Mandatory validation dimensions (status is not sufficient)
- Validate HTTP status, response `Content-Type`, and response body schema/shape for each success scenario.
- For envelope-based APIs, assert wrapper fields and inner payload contract (`correlationId` + `data` or project equivalent).
- For browser-consumed routes, execute and record CORS preflight (`OPTIONS`) checks when cross-origin/custom headers are present.
- Flag any `200` with malformed JSON/non-JSON body as **FAIL** for FE-readiness even if backend status checks pass.
- For required negative scenarios, any 5xx response is **BLOCKED** (not conditionally ready) until fixed and re-run.
- For protected APIs, execute unauthenticated and authorized calls and record concrete authn/authz outcomes (status + masked snippet).
- For sequencing scenarios (bleed without pickup, VACO after cutoff, writes on closed date), assert documented `SV-*` codes per `error-code-matrix.md`.

### Baseline Commands (Sales Vault — adapt `BASE_URL` / paths)

#### Service readiness
```bash
BASE_URL="${BASE_URL:-http://localhost:8081}"
API_PREFIX="${API_PREFIX:-/api/v1}"

curl -s -o /tmp/vault-openapi.out -w "%{http_code}\n" "${BASE_URL}/api-docs"
curl -s -o /tmp/vault-health.out -w "%{http_code}\n" "${BASE_URL}/actuator/health"
```

#### REST smoke (warehouse → sales date — dependency order)
```bash
BASE_URL="${BASE_URL:-http://localhost:8081}"
API_PREFIX="${API_PREFIX:-/api/v1}"

curl -s -o /tmp/warehouses.out -w "%{http_code}\n" "${BASE_URL}${API_PREFIX}/warehouses"
# Substitute a valid warehouseId from prior response:
WAREHOUSE_ID="${WAREHOUSE_ID:-1}"
curl -s -o /tmp/open_sales_date.out -w "%{http_code}\n" \
  "${BASE_URL}${API_PREFIX}/warehouses/${WAREHOUSE_ID}/sales-dates/open"
```

#### Negative paths (align to `error-code-matrix.md`)
```bash
# Malformed mutation envelope → expect 400 per envelope-policy / global validation
curl -s -o /tmp/patch_empty.out -w "%{http_code}\n" -X POST "${BASE_URL}${API_PREFIX}/pickups" \
  -H "Content-Type: application/json" -d '{}'

# Bleed without prior pickup (BR-007) — use warehouse/date with no pickups; expect 4xx + SV-* code
curl -s -o /tmp/bleed_no_pickup.out -w "%{http_code}\n" -X POST \
  "${BASE_URL}${API_PREFIX}/warehouses/${WAREHOUSE_ID}/sales-dates/${SALES_DATE}/bleeds" \
  -H "Content-Type: application/json" -d '{"data":{"tenderTypeCode":"CASH"}}'

# Unsupported method
curl -s -o /tmp/method_not_allowed.out -w "%{http_code}\n" -X DELETE \
  "${BASE_URL}${API_PREFIX}/warehouses"
```

#### GraphQL (only when scoped)
```bash
GRAPHQL_URL="${BASE_URL:-http://localhost:8081}/graphql"

curl -s -H "Content-Type: application/json" "${GRAPHQL_URL}" \
  --data '{"query":"{ __typename }"}' -o /tmp/gql_smoke.out -w "%{http_code}\n"
```

#### Regression sweep (parameterize from `endpoint-catalog.md`)
```bash
for path in warehouses regions open-days; do
  code=$(curl -s -o "/tmp/ep_${path}.out" -w "%{http_code}" "${BASE_URL}${API_PREFIX}/${path}")
  echo "${path} => ${code}"
done
```

## Execution Policy
- Execution loop: **Plan → Act → Evaluate → Refine** (max **3** iterations).
- **Plan:** Build scenario matrix from `endpoint-catalog.md` + OpenAPI + `error-code-matrix.md`; add journey-order rows when `api_sequence_specification` is supplied; choose negative paths required by rule **20**.
- **Act:** Run curl (and GraphQL when scoped) commands; capture status + redacted snippets; record `correlationId` when present.
- **Evaluate:** Map results to matrix; flag contract drift vs OpenAPI and envelope policy.
- **Refine:** Fix commands or document environment blockers (`NEEDS_ENV`), then rerun.

## Clarification Protocol

**Resolve-before-ask** when `contract_path` and `capability_scope` are supplied or defaultable.

**Medium gate** (only when inputs cannot be resolved):

1. Is the service reachable at `base_url` (default `http://localhost:8081`)?
2. What `critical_endpoints` or contract catalogs define the scenario matrix for `capability_scope`?
3. Are auth requirements in scope (supply test credentials via env, never in artifacts)?
4. What test warehouse / sales date / register / pickup fixtures are available for sequencing scenarios?

Mask tokens and PII in all persisted evidence.

## Artifact Persistence
- **Canonical evidence:** `docs/quality/api-validation/<run-id>-api-validation.md` and `docs/quality/api-validation/<run-id>-api-evidence.md` using templates above (no placeholders left in final publish).
- **Implementation mirror (recommended):** `docs/implementation/validation/<capability-scope>-api-validation.md` — summary link to canonical run.
- **Run ID:** UTC timestamp slug e.g. `20260630-143000Z`.

### Sprint convention (story-driven engagements only)
- Mirror summaries or links under **`docs/sprint<sprint_number>/api-validation/`** and append to **`docs/sprint<sprint_number>/manifest.json`** when sprint workflow is active.

## Definition of Done

- [ ] Critical scenarios for the declared `capability_scope` pass or are explicitly **blocked** with owner + prerequisite.
- [ ] Mandatory negatives executed where applicable: malformed body, unsupported method, sequencing guards (BR-005/007/013), GraphQL parse/validation errors when scoped, oversize fields matching bean validation.
- [ ] No required-negative scenario returns 5xx; if any does, final verdict must be **not ready**.
- [ ] For protected APIs, authn/authz evidence includes both unauthorized and authorized outcomes.
- [ ] Evidence files include **## Validation Summary**, **Traceability** (FR-/BR-*/OpenAPI — not story IDs), and execution metadata.
- [ ] Final verdict (**ready** / **conditionally ready** / **not ready**) recorded with remediation owners.
- [ ] Every non-obvious scenario traces to FR-*, BR-*, workflow step, or `error-code-matrix` row — not user story AC.

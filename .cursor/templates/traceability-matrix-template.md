# Traceability Matrix (Sales Vault — documentation-driven)

Use for `/implement-api` and `/implement-ui` (contract + wiring trace). Trace to **documentation sources**, not user stories.

## Capability metadata
- `capability_scope`:
- `contract_version`:
- `source_artifacts`:

## Rule → implementation matrix

| Rule ID | Source | Type | UX / screen | API operation | Data entity | Backend artifact | FE binding | Test case IDs |
|---|---|---|---|---|---|---|---|---|
| FR-VACE-002 | vault-source-of-truth §9 | FR | IND2176 `/vace/pickup` | `POST /pickups` | `pickup_entry` | `PickupController` | `pickupApi.create` | TC-… |
| BR-007 | vault-source-of-truth §6 | BR | IND2630 bleed | `POST /bleeds` | `bleed_summary` | `BleedService` | `bleedApi.createBleed` | TC-… |

**Type:** `FR-*` | `BR-*` | `workflow` | `screen` | `DDL` | `ASSUMPTION-<id>`

**Source examples:** `vault-source-of-truth.md` §6–§9 · `.cursor/plans/sales-vault-data-model.md` · `field-traceability.md` · `screenRequirements.ts` · OpenAPI `operationId`

## Endpoint coverage matrix

| Resource | Method | Endpoint | Request DTO | Response DTO | Validation set | Error codes exercised | Negative tests |
|---|---|---|---|---|---|---|---|
| | | | | | | | |

## GraphQL coverage (when `primary_interface` is GraphQL or hybrid)

| Operation | Type | Request shape | Response shape | Error codes exercised | Tests |
|---|---|---|---|---|---|
| | | | | | |

## FE ↔ BE wiring matrix (for `/implement-ui` wiring gate)

| Step | Sequence order | UI control / field | `screenRequirements` ref | OpenAPI property | `client.ts` adapter | Session dependency |
|---|---|---|---|---|---|---|
| 1 | | `label:Register #` | IND2176 §4.2.1 | `registerNumber` | `pickupApi.list` | warehouse + sales date |

## Conformance checks

| Check | Status | Notes |
|---|---|---|
| Every in-scope FR-* has API coverage or documented gap | | |
| Every contract operation traces to FR-/BR-/workflow/screen/table | | |
| Endpoint-to-resource mapping complete | | |
| BR-* sequencing enforced in service or documented deferral | | |
| Error code matrix linked to tests | | |
| `screenRequirements.ts` fields wired or gap registered | | |
| No out-of-scope screens (VACX, VAEX, DDE, STE clerk) | | |

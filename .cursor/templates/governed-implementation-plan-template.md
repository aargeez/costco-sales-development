# Governed implementation plan — {{INITIATIVE_OR_SPRINT}}

## Metadata
| Field | Value |
|--------|--------|
| Scope mode | {{FULL_STACK_OR_PARTIAL}} |
| Story sources | {{STORY_SOURCE_PATHS}} |
| Plan version | {{VERSION}} |
| Generated | {{ISO_TIMESTAMP}} |
| Generator | {{GENERATOR}} (e.g. governed-story-planner-agent / /plan-governed-stories) |
| Iteration | {{ITERATION}} |
| Validation summary | {{VALIDATION_SUMMARY}} |

## Executive summary
- **Objectives**: …
- **Delivery approach**: vertical slices vs layered; naming and contract conventions.
- **Out of scope / deferred**: …

## Governance and consistency rules
Apply across all stories unless a story-specific exception is approved:

| Area | Rule |
|------|------|
| API envelope | {{e.g. project default wrapper, version header}} |
| Errors | {{error code matrix / RFC7807 / project standard}} |
| Naming | {{DB, DTO, OpenAPI, UI}} |
| Migrations | {{idempotent scripts, ordering, rollback notes}} |
| Security | {{authz model, PII, logging restrictions}} |
| Testing | {{minimum tests per layer; contract tests}} |

## Story readiness matrix

| Story ID | Title | Slice type | DDL | API map | UI workflow | Wiring seq | Status |
|----------|-------|------------|-----|---------|-------------|------------|--------|
| {{ID}} | {{TITLE}} | full-stack / api-only / … | R/G | R/G | R/G | R/G | Ready / Gated |

- **R** = Ready (cite artifact below), **G** = Gap (see gap register).

## Gap register and clarifications

| Gap ID | Story | Dimension | Missing / ambiguous | Impact | Question for PO/SME/architect | Status |
|--------|-------|-----------|----------------------|--------|------------------------------|--------|
| G-01 | … | DDL | … | … | … | Open / Answered |

## Story packs (what / why / how)

Repeat per story or epic group.

### {{STORY_ID}} — {{TITLE}}

| | |
|--|--|
| **What** | User-visible outcome and system behavior. |
| **Why** | Business outcome, compliance, or dependency driver. |
| **Slice type** | full-stack / … |
| **DDL** | {{migration paths, tables, notes; or “N/A + reason”}} |
| **API mapping** | {{operations, DTOs, links to OpenAPI sections}} |
| **UI data workflow** | {{steps, fields, validation, states}} |
| **Wiring sequence** | Ordered steps (see detail below). |
| **AC coverage** | How each acceptance criterion is verified. |

#### Wiring sequence specification (for wiring agent)
1. **Step 1**: Method + path / operation name — purpose — inputs/outputs — notes  
2. **Step 2**: …  
**Dependency graph**: (e.g. step 2 uses `id` from step 1.)

## Cross-story dependencies
- Story X → Story Y because …

## Implementation sequence (governed order)

| Order | Work package | Stories | Owner hint | Prerequisite |
|-------|--------------|---------|------------|--------------|
| 1 | Schema / DDL baseline | … | data/backend | … |
| 2 | API contracts + stubs | … | api | DDL v1 |
| 3 | Backend persistence + services | … | backend | contracts |
| 4 | Frontend UI + clients | … | frontend | contracts |
| 5 | E2E wiring + sequence tests | … | integration | stable endpoints |

## Workspace bindings

Resolved once per story/sprint slice. Execution commands read these when invoked with `@governed_plan` (see `.cursor/templates/plan-as-input-protocol.md`). Use `infer` only when the planner expects repo introspection during the first command run.

| Key | Value | Source / notes |
|-----|-------|----------------|
| sprint_number | {{N}} | From planning invocation or branch |
| story_id | {{STORY_ID}} | Primary story for this plan file |
| implementation_module_root | {{e.g. nsi-table-api-service/}} | Backend service root; `infer` if unknown |
| implementation_frontend_root | {{e.g. nsi-nextjs/}} | Next.js app root; `infer` if unknown |
| artifact_namespace | {{capability-or-slice}} | Doc and contract folder segment |
| contract_version | {{e.g. sprint3-worklist-v1}} | Shared across API/BE/FE/wiring |
| api_mode | table_level / composed | Backend orchestration mode |
| envelope_policy | none / project_default | Or cite `wrapper_contract` doc path |
| primary_interface | REST / GraphQL / hybrid | Default REST |
| target_platform | {{e.g. PostgreSQL}} | For database script generation |
| migration_search_paths | migrations/**/*.sql | Baseline inventory scan |
| feature_or_slice_name | {{screen-or-flow-id}} | Wiring and test slice naming |

## Command execution flow

Ordered slash commands for this story. **Status** values: `pending` | `in_progress` | `complete` | `blocked` | `skipped`. Omit rows that are N/A for the story slice type.

| Step | Command | Story | Status | Runtime bindings / notes |
|------|---------|-------|--------|--------------------------|
| 1 | /implement-api | {{STORY_ID}} | pending | capability_scope / story_scope: {{STORY_ID}}; DDL from §Story pack; API mapping; module root: §implementation_module_root; contract_version: §contract_version |
| 2 | /implement-ui | {{STORY_ID}} | pending | routes_and_surfaces, ux_reference: from §Story pack UI workflow; api_sequence_specification: from §Wiring sequence; frontend root: §implementation_frontend_root; **UI session defaults** table or “no defaults” per `.cursor/skills/ati-session-defaults/SKILL.md` |
| 3 | /expand-test-coverage | {{STORY_ID}} | pending | story_ids: {{STORY_ID}}; upstream: steps 1–2 artifacts in §Execution log |
| 4 | /validate-api-endpoints | {{STORY_ID}} | pending | contract_version: §contract_version; service roots: §Workspace bindings; critical_endpoints: §Story pack API mapping; api_sequence_specification when journey order applies |

**Sprint-level steps 9–10** (`/compliance-check`, `/sprint-handoff`) live in `sprint<N>-unified-governed-plan.md` — not in this per-story file.

**Invoker pattern (minimal):** `/<command> @governed_plan: .cursor/plans/sprint<N>/<story-id>-governed-implementation-plan.md` — optional `plan_step: <n>`.

## Execution log

Updated by each command after successful completion (per plan-as-input protocol).

| Step | Command | Completed (ISO) | Artifact paths | Validator | Notes |
|------|---------|-----------------|----------------|-----------|-------|
| — | — | — | — | — | — |

## Plan-level Definition of Done

- [ ] §Gap register has no **Open** critical gaps for in-scope dimensions (or explicit assumptions approved with owner).
- [ ] §Workspace bindings contain no `TBD` for keys required by the story slice type.
- [ ] §Command execution flow lists all required rows for the slice type; N/A rows marked `skipped` with reason.
- [ ] Every `complete` row has a matching §Execution log entry with artifact paths.
- [ ] `docs/sprint<sprint_number>/manifest.json` lists artifacts from completed steps (when sprint convention applies).
- [ ] Human gates in §Risks, assumptions, and approvals satisfied before production cutover.

## Risks, assumptions, and approvals
- **Assumptions** (only if explicitly allowed): …
- **Risks**: …
- **Sign-off**: PO / architect / data owner as needed.

## Traceability
- Stories: {{paths}}
- Contracts: {{paths}}
- Migrations: {{paths}}
- BDD/tests: {{paths}}

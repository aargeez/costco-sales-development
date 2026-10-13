# /implement-api

## Objective

Implement or fix backend behavior in `config.yaml` → `modules.api`, align contracts, **generate tests, run tests**, and record evidence.

**On amount-math error / conflict:** `artifacts/calculations/<module>-calculations.json` is the **primary source** (rule 32). Align Java/SQL/tests to `CALC-*`; do not change the catalog to match failing code. Missing/stale → `/extract-calculations`.

## When to Use

- After `/plan-sprint` — run per SP-xxx item tagged `implement-api`
- **Or** instead of `/deliver-sprint` — run manually for backend-only scope
- Requires: BRD rule / sprint item / `docs/feature.md` reference

## Orchestration

| Phase | Role | Agent / Subagent |
|-------|------|------------------|
| 1 Contract | **Always** | **api-agent** → verify OpenAPI ↔ DTO ↔ controller; **update** if API shape changes |
| 2 Schema | Subagent (if persistence changes) | **schema-migration-generator** → Flyway SQL + JPA entities |
| 3 Implement | Primary | **backend-agent** (coordinates with api-agent on DTO/controller) |
| 3a Validation rules | Subagent | validation-chain-generator |
| 3b Spring slice | Subagent | **spring-module-generator** → `java_master_templates/` |
| 4a Cases | Design | test-agent + test-case-generator → `docs/test-cases.md` + `docs/test-matrix.md` |
| 4b Coverage gate | Required | Score cases vs the SP item’s cited FR/BR → `docs/test-cases-br-coverage.md`. FAIL → expand cases; do not script |
| 4c Structural tests | Subagent | **spring-test-generator** → `java_test_templates/` + rule 23 (after PASS) |
| 4d Agent-owned tests | Subagent | test-case-generator **scripts phase** (gaps, validation matrices, regression) |
| 5 Run & evidence | Subagent | **api-test-runner** (mandatory; after PASS) |

> **api-agent is not optional.** Every `/implement-api` run starts with at least a contract **verify** pass. Full OpenAPI edit only when endpoints/DTOs/status codes change.

## Execution Order

```
Plan → api-agent (ALWAYS: verify; UPDATE OpenAPI/DTOs if API shape changes)
     → schema-migration-generator (if new/changed tables, columns, indexes, seeds)
     → backend-agent + spring-module-generator (java_master_templates/) + validation-chain-generator
     → test-case-generator (`docs/test-cases.md` + matrix)
     → test-cases-br-coverage gate (PASS required)
     → spring-test-generator (java_test_templates/) → test-case-generator (scripts phase)
     → api-test-runner (mvn test [+ verify -DrunIntegrationTests=true if schema/IT])
     → Validate → Refine (max 3)
```

## Plan-as-input mode

When `governed_plan` or `sprint_governed_plan` is supplied, apply `.cursor/templates/plan-as-input-protocol.md` before Clarification Protocol.

## Command-Specific Hardening

- Refuse unless the sprint/governed plan is **Approved**. **Proposed — Pending Approval** is not enough.
- **ASSUMPTION-PCC-002:** expand coverage PASS does **not** block this command. Slice cases + BR coverage + product tests still run here. Remaining FR/BR gap scripts belong to `/run-tests` after expand package Approved.
- Agents extract template vars to `artifacts/templates/<resource>-vars.yaml`; stop on `# AMBIGUITY`.
- After Spring/Java writes, run `python3 tools/templates/render_check.py --vars <yaml> --check both`. Template-owned drift is FAIL.
- Stay in `scope.yaml` write allowlist for backend-agent / api-agent.

## Rules

- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/02-security-and-compliance.mdc`
- `.cursor/rules/03-backend-springboot.mdc`
- `.cursor/rules/05-api-contracts.mdc`
- `.cursor/rules/07-testing-and-quality.mdc`
- `.cursor/rules/09-agent-orchestration.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/23-test-generation.mdc`
- `.cursor/rules/32-calculation-catalog.mdc` (when implementing amount math)

## Templates

- `.cursor/templates/plan-act-validate-refine-loop.md`
- `.cursor/templates/backend-rest-orchestration-pattern.md` (implementation reference)
- `.cursor/templates/api-contract-template.md` → `artifacts/sprints/<sprint-id>-api-contract.md` when contract changes
- `.cursor/templates/audit-trail-template.md`
- `.cursor/templates/behavior-provenance-template.md` (update sprint provenance for new behavior)
- `.cursor/templates/calculation-catalog-template.md` (cite `CALC-*`; update parity after impl)
- `.cursor/templates/test-cases-template.md`
- `.cursor/templates/test-cases-br-coverage-template.md`
- `.cursor/templates/test-matrix-template.md`
- `.cursor/templates/test-results-template.md` (**required** — canonical API evidence)
- `.cursor/templates/api-test-evidence-template.md` (optional detail in audit)
- `.cursor/templates/feature-note-template.md` (when documenting behavior changes)

## Skills

- `.cursor/skills/validation-chain/SKILL.md`
- `.cursor/skills/quality/SKILL.md`
- `.cursor/skills/jacoco-coverage/SKILL.md`

## Ask Back If Missing

- BRD rule / sprint SP-xxx / `docs/feature.md` scope
- Whether OpenAPI needs updating

## Definition of Done

- [ ] **api-agent verify** recorded in audit (always)
- [ ] OpenAPI + DTOs + controller aligned (api-agent **update** if API shape changed)
- [ ] Contract checklist filled when API shape changed (`api-contract-template.md`)
- [ ] **Flyway migration + JPA entities** when persistence changes (schema-migration-generator)
- [ ] When a seed or data `INSERT`/`UPDATE` is added: audit notes max `V*`, no duplicate version, keys touched, and modules at risk of golden overwrite
- [ ] When amount math uses a shared O/S or recompute path: cite `invocationSource` or the applicable type set from the catalog (rule 32). If the catalog is silent, register `ASSUMPTION-*` and do not invent a profile
- [ ] Implementation follows `backend-rest-orchestration-pattern.md`
- [ ] Implementation complete (validation chain, service, persistence as needed)
- [ ] **`docs/test-cases.md` + matrix** written for the SP item
- [ ] **`docs/test-cases-br-coverage.md` Status PASS** before scripts
- [ ] **Structural JUnit** rendered via `spring-test-generator` when registry mapping applies (rule 23)
- [ ] Agent-owned / validation-branch tests via `test-case-generator` scripts phase as needed
- [ ] IT updated if HTTP/persistence changed
- [ ] When that IT writes shared vault, summary, or xref rows: `@BeforeEach` restores those rows (same IT update; no extra command)
- [ ] **`mvn test` passes on Java 17** (api-test-runner)
- [ ] **`artifacts/test-results-<date>.md`** updated with API results + JaCoCo
- [ ] Behavior provenance updated for new/changed behavior (rule 22)
- [ ] Orchestrator + backend audit trails written

## Outputs

- `config.yaml` → `modules.api` (product code)
- `modules.api` Flyway tree `src/main/resources/db/migration/V*.sql` (when schema changed)
- `config.yaml` → `modules.openapi` (if changed)
- `artifacts/sprints/<sprint-id>-api-contract.md` (when contract changed)
- `artifacts/test-results-<date>.md`
- `docs/test-cases.md`, `docs/test-matrix.md`, `docs/test-cases-br-coverage.md`
- `artifacts/audit/<date>-implement-api-<task>.md`

## Next command

**On completion, tell the user:**

> ✅ `/implement-api` complete.  
> **Next command:** `/implement-ui` **if** this sprint has UI work; otherwise `/expand-test-coverage`.

| Condition | Next command |
|-----------|--------------|
| Sprint has SP-xxx tagged `implement-ui` remaining | `/implement-ui` |
| API-only scope / UI already done | `/expand-test-coverage` |
| Using batch instead of manual | `/deliver-sprint` will continue automatically — no action |

**Example — UI work next:**

```
/implement-ui
Sprint: sprint-01
Mockup: UI/image (3).png
Scope: SP-002 from sprint plan
```

**Example — no UI work:**

```
/expand-test-coverage
BRD: config.yaml → modules.brd
Prior results: artifacts/test-results-<date>.md
```

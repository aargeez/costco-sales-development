# Sales Master AI Factory — Agent catalog

Merged from **Sales-Audit** + **Sales-Vault**; agent layout follows **`.cursor-audit`** category folders (orchestrator at root; primaries under `agents/<area>/`).

See [agents/README.md](agents/README.md) for the full index.

## Domain primary

| Agent | File | Products |
|-------|------|----------|
| Calculation catalog | [calculation-catalog-agent.md](agents/calculations/calculation-catalog-agent.md) | Sales-Audit, amount-math modules |

## Delivery & orchestration

| Agent | File |
|-------|------|
| Orchestrator | [orchestrator-agent.md](agents/orchestrator-agent.md) |
| Handoff (daily / sprint modes) | [handoff-agent.md](agents/ops/handoff-agent.md) |
| Requirements | [requirements-agent.md](agents/intake/requirements-agent.md) |
| Planner | [planner-agent.md](agents/planning/planner-agent.md) |
| Governed story planner | [governed-story-planner-agent.md](agents/planning/governed-story-planner-agent.md) |
| Change / incremental (`/fix-issue` cited-requirements) | [change-agent.md](agents/engineering/change-agent.md) |
| Code cleanup | [code-cleanup-agent.md](agents/engineering/code-cleanup-agent.md) |
| Forward engineering | [forward-engineering-agent.md](agents/engineering/forward-engineering-agent.md) |
| Backend (`/implement-api`; `/fix-issue` defect) | [backend-agent.md](agents/backend/backend-agent.md) |
| Frontend (`/implement-ui`; `/fix-issue` defect) | [frontend-agent.md](agents/frontend/frontend-agent.md) |
| API contract | [api-agent.md](agents/api/api-agent.md) |
| Quality | [quality-agent.md](agents/quality/quality-agent.md) |
| Test | [test-agent.md](agents/quality/test-agent.md) |
| Documentation | [documentation-agent.md](agents/docs/documentation-agent.md) |

## Analysis & planning (Vault-enriched)

| Agent | File |
|-------|------|
| Business analyst | [business-analyst-agent.md](agents/intake/business-analyst-agent.md) |
| Reverse engineering | [reverse-engineering-agent.md](agents/reverse/reverse-engineering-agent.md) |
| Tech spec writer | [tech-spec-writer-agent.md](agents/specs/tech-spec-writer-agent.md) |
| UX | [ux-agent.md](agents/ux/ux-agent.md) |
| Figma design system | [figma-design-system-agent.md](agents/figma/figma-design-system-agent.md) |

## Platform, security, release

| Agent | File |
|-------|------|
| Infra | [infra-agent.md](agents/infra/infra-agent.md) |
| GitHub workflow | [github-workflow-agent.md](agents/infra/github-workflow-agent.md) |
| Security | [security-agent.md](agents/security/security-agent.md) |
| SRE | [sre-agent.md](agents/sre/sre-agent.md) |
| Performance | [performance-agent.md](agents/sre/performance-agent.md) |
| Release | [release-agent.md](agents/release/release-agent.md) |
| Code review | [code-review-agent.md](agents/quality/code-review-agent.md) |
| Compliance | [compliance-agent.md](agents/compliance/compliance-agent.md) |
| Data / migration | [data-agent.md](agents/data/data-agent.md) |
| Python | [python-developer-agent.md](agents/engineering/python-developer-agent.md) |
| API endpoint validation | [api-endpoint-validation-agent.md](agents/testing/api-endpoint-validation-agent.md) |
| Test results logger | [test-results-logger-agent.md](agents/testing/test-results-logger-agent.md) |

**Total primaries:** 32 (categorized under `agents/<area>/`; orchestrator at root). See [agents/README.md](agents/README.md).

Fix work uses **backend-agent** / **frontend-agent** fix mode — not separate fix peer agents.

## Duties (existing commands)

These checks sit on the current gates. They do not add a command or change Path A / Path B order.

- **Frontend** does not finish a screen that calls an API without the OpenAPI name table on the `/implement-ui` wiring gate.
- **Backend** does not add a seed without the collision note (max `V*`, keys touched, modules at risk) on the existing schema step of `/implement-api` or `/fix-issue` cited-requirements.
- Seed, diagnose, and port work uses `/fix-issue` cited-requirements when a BR, story, or `CALC-*` is cited, and `/fix-issue` defect otherwise.

## Subagents

Categorized under `subagents/<area>/` — see [subagents/README.md](subagents/README.md).

## Rule overlays

Overlays are **not** always-on. Root [rules/01-project-context.mdc](rules/01-project-context.mdc) is config-driven. Load a product overlay only for that product:

- Calculation (shared Sales): [rules/32-calculation-catalog.mdc](rules/32-calculation-catalog.mdc)
- Vault replatform: [rules/overlays/sales-vault/](rules/overlays/sales-vault/) — load when the active product is Sales Vault
- Audit context fragment: [rules/overlays/sales-audit/](rules/overlays/sales-audit/) — load when the active product is Sales Audit

## Rule renumber crosswalk (source → master)

| Source | Source rule | Master rule | Master path |
|--------|-------------|-------------|-------------|
| Audit | 24 calculation-catalog | 32 | [rules/32-calculation-catalog.mdc](rules/32-calculation-catalog.mdc) |
| Audit / Vault | 12 reverse-engineering-quality | 33 | [rules/33-reverse-engineering-quality.mdc](rules/33-reverse-engineering-quality.mdc) |
| Vault | 10 python-engineering | 27 | [rules/27-python-engineering.mdc](rules/27-python-engineering.mdc) |
| Vault | 11 techspec-and-doc-quality | 28 | [rules/28-techspec-and-doc-quality.mdc](rules/28-techspec-and-doc-quality.mdc) |
| Vault | 16 test-results-logging | 17 | [rules/17-test-results-logging.mdc](rules/17-test-results-logging.mdc) |
| Vault | 17 compliance-and-audit | 34 | [rules/34-compliance-and-audit.mdc](rules/34-compliance-and-audit.mdc) |
| Vault | 01 project-context-replatforming | overlay 01 | [rules/overlays/sales-vault/01-project-context-replatforming.mdc](rules/overlays/sales-vault/01-project-context-replatforming.mdc) |
| Vault | 06 infra-terraform-gcp | overlay 06 | [rules/overlays/sales-vault/06-infra-terraform-gcp.mdc](rules/overlays/sales-vault/06-infra-terraform-gcp.mdc) |

Duplicate prefixes **01** and **06** are intentional (base + product overlays).

## Factory ownership (19 primaries + sales peers)

Core factory agents follow the same Must/Must NOT contracts as enterprise `.cursor` (orchestrator routes; quality coordinates expand design + Pending Approval + `/run-tests` + `/run-eval`; test-agent design-only on expand). Sales **adds** the domain and platform peers listed above — never remove them when syncing factory updates.

## Dedicated peers (merge note)

`github-workflow-agent` and `compliance-agent` remain **dedicated primaries** (not absorbed into infra/security modes). FE↔BE wiring is handled by **frontend-agent** on `/implement-ui` (wiring gate). Legacy `fe-be-api-sequence-wiring-agent` and vault-legacy `generate-*` commands were removed. Handoff and release train agents were consolidated into `handoff-agent` and `release-agent`.

# /validate-kpi-metrics

## Objective
Measure the 12 enterprise KPIs for the API and/or UI modules using **deterministic toolchain evidence** (JaCoCo, ESLint, SCA, OWASP-aligned review)—not heuristic Python scorers. Produce evidence matrices, scorecards, an executive summary, and a gate verdict (**GO** / **GO_WITH_CONDITIONS** / **NO_GO**) for sprint handoff and `release-agent` (mode readiness).

## When to use
- Sprint boundary or release-candidate quality assessment
- After `/review-changes` or `/expand-test-coverage` when scorecards are required
- Supplying KPI evidence to release Go/No-Go

## Validation scope

| `validation_scope` | When | `sprint_number` | `story_scope` | Code scanned |
|--------------------|------|-----------------|---------------|--------------|
| **sprint** (default) | Sprint handoff | Required | Recommended | Full modules; KPI-03 limited to sprint stories |
| **overall** | Release baseline | Optional | Omit or full catalog | Entire `modules.api` + `modules.ui` |
| **both** | Compare slice vs whole | Required | Required for sprint pass | Two passes + scope comparison |

## Runtime Inputs

### Required
- `validation_scope`: `sprint` | `overall` | `both`
- `stack`: `java` | `nextjs` | `both` (or resolve from config modules)
- `sprint_number` when scope is `sprint` or `both`

### Optional
- `story_scope` (KPI-03 sprint leg)
- `critical_domains` (packages requiring ≥80% line coverage)
- `baseline_report` (prior run for trend)
- `scan_depth`: `quick` | `standard` | `deep`
- `run_id` (UTC slug; generated if omitted)

Resolve module roots from `.cursor/config.yaml` (`modules.api`, `modules.ui`) before asking.

## Enterprise KPI catalog (assess all applicable rows)

Mark **N/A** only with explicit rationale.

| KPI ID | Name | Java evidence | Next.js evidence |
|--------|------|---------------|------------------|
| KPI-01 | Security | security-scanner, SAST, Dependency-Check | security-scanner, ESLint security, secret scan |
| KPI-02 | Test Coverage | JaCoCo after tests | Istanbul/nyc/Vitest coverage |
| KPI-03 | Requirement Coverage | gap-identifier vs story_scope | Same |
| KPI-04 | Defect Density | SpotBugs + PMD / KLOC | ESLint errors / KLOC |
| KPI-05 | Maintainability | PMD complexity | ESLint complexity rules |
| KPI-06 | Code Readability | Checkstyle | ESLint + Prettier |
| KPI-07 | Adherence to Guidelines | REST conventions, rule 03 | Next.js structure, rule 04 |
| KPI-08 | Documentation | README, OpenAPI | README, TSDoc |
| KPI-09 | Dependency Health | OWASP Dependency-Check | npm audit + lockfile |
| KPI-10 | Reliability & Error Handling | exception advice + ITs | empty catch, API error UX |
| KPI-11 | Observability Maturity | logs + traces/metrics | Sentry/OTel/logging config |
| KPI-12 | Performance Readiness | NFR smoke / perf-profiler | Lighthouse/smoke or N/A |

**Evidence hierarchy:** tool reports (authoritative) → subagent checklists → advisory LLM narrative. LLM **cannot** override Red on KPI-01, KPI-02, or KPI-09.

## Dual scorecards (mandatory)

Scores are integers 0–10; overall is the arithmetic mean to two decimals. Green ≥ 8, Amber 5–7, Red ≤ 4.

- **Backend** (when stack includes java): Security, Readability, Requirement coverage, Test coverage, Defect density, Maintainability, Adherence to guidelines, Documentation.
- **UI** (when stack includes nextjs): Path Traversal, Credential Exposure, Outdated Dependencies, Fail Rate Prevention, Maturity Index, Structural Weakness, Verbosity/Complexity, Unused Package, Implicit Exploitation, JSON Key / Null Pointer, Assumption Design Debt.

## Use Agents
orchestrator-agent → quality-agent (mode `kpi`)

## Delegate to Subagents
java-static-metrics-collector, nextjs-static-metrics-collector, security-scanner, gap-identifier, coverage-analyzer, kpi-report-synthesizer, kpi-threshold-gate

## Enforce Rules
00, 02, 07, 09, 10, 15, 24, 26, 03 (java), 04 (nextjs)

## Use Templates
`kpi-validation-report-template.md`, `kpi-evidence-matrix-template.md`, `kpi-threshold-policy-template.md`, `kpi-executive-summary-template.md`, `kpi-backend-scorecard-template.md`, `kpi-ui-vector-scorecard-template.md`, `kpi-scope-comparison-template.md` (scope `both`), `traceability-matrix-template.md` (KPI-03)

## Use Skills
`.cursor/skills/kpi-validation/SKILL.md`, `.cursor/skills/jacoco-coverage/SKILL.md`

## Command-Specific Hardening
- Do **not** use heuristic Python KPI scorers as a gate.
- Do **not** claim Green on KPI-02 without a JaCoCo/Istanbul/Vitest report path.
- Do **not** invent coverage percentages when the tool is missing — `NEEDS_TOOL` with owner.
- Mask secrets and PII in persisted evidence.
- No 20th peer agent: this is quality-agent mode `kpi`.

## Clarification Protocol
After resolve-before-ask, register Open DQ when `validation_scope`, `stack`, or required `sprint_number` is still TBD after config introspection. Do not invent coverage percentages. Do not STOP for a clarification round except human gates. Still refuse when Required inputs are absent.

## Execution Policy
Plan → Act → Validate → Refine (max 3 ceiling). Iteration 1 Validate pass → stop. Refine only from findings. Escalate unresolved criticals to the release owner after iteration 3.

## Artifact Persistence
- `docs/quality/kpi-validations/<scope-path>/<run-id>-kpi-evidence-matrix.md`
- `docs/quality/kpi-validations/<scope-path>/<run-id>-kpi-evidence.json`
- `docs/quality/kpi-validations/<scope-path>/<run-id>-kpi-validation-report-<module>.md`
- `docs/quality/kpi-validations/<scope-path>/<run-id>-kpi-executive-summary.md`
- backend/UI scorecards when that stack is in scope
- Sprint mirror: `docs/sprint<N>/kpi-validations/` when scope includes sprint
- Audit: `artifacts/audit/<date>-validate-kpi-metrics-<slug>.md`

`<scope-path>` is `sprint<N>` or `overall`.

## Definition of Done
- [ ] All 12 KPIs assessed or N/A with rationale
- [ ] Evidence matrix + JSON populated (no leftover placeholders)
- [ ] In-scope scorecards with 0–10 rows and overall mean
- [ ] Verdict GO / GO_WITH_CONDITIONS / NO_GO with blockers and owners
- [ ] LLM narrative labeled advisory; Red on 01/02/09 not overridden
- [ ] `artifacts/audit/<date>-validate-kpi-metrics-<slug>.md` written
- [ ] Harness updated (phase-changing)

## Next command

> ✅ `/validate-kpi-metrics` complete.  
> **Next command:** `/pr-assist` if GO; otherwise owning agents fix, then re-run `/validate-kpi-metrics`. Feed the executive summary to release-agent (mode readiness) before Go/No-Go.

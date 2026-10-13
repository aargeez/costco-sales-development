# Templates — Match Existing Repo Outputs

Use these boilerplates when agents produce files. **Do not invent new formats** — follow patterns already in `docs/` and `artifacts/`.

## Canonical paths

All commands and agents reference **root** `templates/` only. The former `templates/overlays/sales-vault/` duplicate mirror was removed after vault-legacy command retirement — use root copies for Audit and Vault replatform alike.

## Intake & Planning

| Template | Existing example in repo | Output path pattern |
|----------|--------------------------|---------------------|
| [brd-template.md](brd-template.md) | `artifacts/brd/saro-BRD-draft.md` | `artifacts/brd/<module>-BRD-draft.md` |
| [transcript-intake-notes-template.md](transcript-intake-notes-template.md) | (intake phase) | `artifacts/intake/<date>-intake-notes.md` |
| [sprint-plan-template.md](sprint-plan-template.md) | (planning phase) | `artifacts/sprints/<sprint-id>-plan.md` |
| [requirement-traceability-template.md](requirement-traceability-template.md) | `docs/test-matrix.md` (extended) | `artifacts/sprints/<sprint-id>-traceability.md` |
| [test-strategy-template.md](test-strategy-template.md) | (planning phase) | `artifacts/sprints/<sprint-id>-test-strategy.md` |
| [behavior-provenance-template.md](behavior-provenance-template.md) | rule 22 registry | `artifacts/sprints/<sprint-id>-provenance.md` |
| [calculation-catalog-template.md](calculation-catalog-template.md) | `/extract-calculations`, amount FRs | `artifacts/calculations/<module>-calculations.json` |
| [discussion-coverage-matrix-template.md](discussion-coverage-matrix-template.md) | BRD §13 | Embedded in BRD |

## API & Backend

| Template | Command / agent | Output path pattern |
|----------|-----------------|---------------------|
| [api-contract-template.md](api-contract-template.md) | `/implement-api`, api-agent | `artifacts/sprints/<sprint-id>-api-contract.md` |
| [backend-rest-orchestration-pattern.md](backend-rest-orchestration-pattern.md) | backend-agent (reference) | (no artifact — coding pattern) |
| [api-test-evidence-template.md](api-test-evidence-template.md) | `/implement-api` (optional detail) | Embedded in audit or test-results |
| [api-validation-template.md](api-validation-template.md) | `/review-changes` (optional gate) | `artifacts/audit/<date>-api-validation.md` |
| [fe-be-sequence-wiring-template.md](fe-be-sequence-wiring-template.md) | `/validate-api-endpoints`, governed-plan wiring (Vault replatform) | `docs/api/contracts/.../fe-be-sequence-wiring.md` |
| [traceability-matrix-template.md](traceability-matrix-template.md) | `/implement-api`, `/implement-ui`, `/generate-user-story` | Contract/UI trace rows |
| [governed-implementation-plan-template.md](governed-implementation-plan-template.md) | `/plan-governed-stories` | `.cursor/plans/sprint<N>/<story-id>-governed-implementation-plan.md` |
| [plan-as-input-protocol.md](plan-as-input-protocol.md) | Governed execution commands | (protocol — not a deliverable file) |

## Quality & Review

| Template | Existing example in repo | Output path pattern |
|----------|--------------------------|---------------------|
| [review-findings-template.md](review-findings-template.md) | `/review-changes` audit | `artifacts/audit/<date>-review-changes.md` |
| [code-review-template.md](code-review-template.md) | Ad-hoc only — **use review-findings for `/review-changes`** | — |
| [bdd-test-case-template.md](bdd-test-case-template.md) | E2E Gherkin journeys | `docs/bdd-test-cases.md` (also indexed in `docs/test-cases.md` as e2e-layer) |
| [test-cases-template.md](test-cases-template.md) | Designed case bodies | `docs/test-cases.md` |
| [test-cases-br-coverage-template.md](test-cases-br-coverage-template.md) | FR/BR coverage gate | `docs/test-cases-br-coverage.md` (**PASS** before scripts) |
| [test-matrix-template.md](test-matrix-template.md) | Case index | `docs/test-matrix.md` |
| [test-results-template.md](test-results-template.md) | `artifacts/test-results-2026-06-23.md` | `artifacts/test-results-{date}.md` (**canonical API/UI evidence**) |
| [e2e-test-results-template.md](e2e-test-results-template.md) | `artifacts/e2e-test-results-2026-06-24T17-25-46.md` | `artifacts/e2e-test-results-{timestamp}.md` |
| [eval-results-template.md](eval-results-template.md) | eval-agent runs | `evals/results/{date}-{mode}.md` + `.json` |
| [eval-fix-log-template.md](eval-fix-log-template.md) | `/close-eval-gaps` | `evals/results/{date}-close-eval-gaps-attempt-{n}.md` |
| [issue-log-template.md](issue-log-template.md) | `/fix-issue` (**required**) | `artifacts/issues/<date>-<slug>.md` |

## Incremental change

| Template | Command / agent | Output path pattern |
|----------|-----------------|---------------------|
| [incremental-change-plan-template.md](incremental-change-plan-template.md) | `/fix-issue` cited-requirements, change-agent | `artifacts/changes/<date>-<slug>-plan.md` |

## Closeout & Docs

| Template | Existing example in repo | Output path pattern |
|----------|--------------------------|---------------------|
| [application-summary-template.md](application-summary-template.md) | `config.yaml` → `artifacts.application_summary_html` | `artifacts.application_summary_html` / `_pdf` |
| [audit-trail-template.md](audit-trail-template.md) | `docs/harness/progress.md` (iteration metadata) | `artifacts/audit/{date}-{command}-{task}.md` |
| [adr-template.md](adr-template.md) | `docs/adr/001-ports-and-adapters.md` | `docs/adr/{nnn}-{slug}.md` |
| [feature-note-template.md](feature-note-template.md) | `docs/feature.md` | `docs/feature.md` (append section) or new doc |

## Factory sync (Phase 0–3 — ported from `.cursor`)

| Template | Used by |
|----------|---------|
| [br-tc-taxonomy.md](br-tc-taxonomy.md) | tc validators, test-case-generator, rule 07 |
| [generic-step-denylist.md](generic-step-denylist.md) | tc-step-quality-validator, rule 07 |
| [test-case-quality-metrics-template.md](test-case-quality-metrics-template.md) | tc-step-quality-validator |
| [test-package-review-template.md](test-package-review-template.md) | `/expand-test-coverage`, `/run-tests` human gate |
| [intake-manifest.schema.md](intake-manifest.schema.md) | test-intake-normalizer, intake-safety |
| [architecture-proposal-template.md](architecture-proposal-template.md) | `/propose-architecture` |
| [architecture-questionnaire-template.md](architecture-questionnaire-template.md) | `/propose-architecture` |
| [sast-findings-template.md](sast-findings-template.md) | `/sast-scan` |
| [dast-findings-template.md](dast-findings-template.md) | `/dast-scan` |
| [threat-profile-template.md](threat-profile-template.md) | `/threat-profile` |
| [performance-test-report-template.md](performance-test-report-template.md) | `/performance-test` |
| [plan-act-observe-reflect-loop.md](plan-act-observe-reflect-loop.md) | `/generate-brd`, intake agents |
| [br-checklist-template.md](br-checklist-template.md) | BR coverage workflows |
| [context-wiki-template.md](context-wiki-template.md) | Documentation / harness |
| [daily-journal-template.md](daily-journal-template.md) | Handoff / ops |
| [parallel-agent-file-ownership.md](parallel-agent-file-ownership.md) | `/deliver-sprint` parallel slices |
| [template-hardening-standard.md](template-hardening-standard.md) | Template quality gate |

Supporting (process, not deliverables):

- [plan-act-validate-refine-loop.md](plan-act-validate-refine-loop.md)
- [subagent-io-template.md](subagent-io-template.md)
- [code-generation-dos-donts.md](code-generation-dos-donts.md)

## Command → Template Map

| Command | Required templates |
|---------|-------------------|
| `/extract-calculations` | calculation-catalog, audit-trail |
| `/generate-brd` | brd, intake-notes, discussion-coverage, behavior-provenance (§17), calculation-catalog (amount FRs), audit-trail |
| `/plan-sprint` | sprint-plan, traceability, test-strategy, behavior-provenance, calculation-catalog (amount SP), test-matrix (seed), audit-trail |
| `/implement-api` | plan-act-validate-refine-loop, backend-rest-orchestration-pattern, audit-trail, test-cases, test-cases-br-coverage, test-matrix, test-results; api-contract + provenance + feature-note when API/behavior changes; calculation-catalog when amount math; api-test-evidence optional; **code Jinja:** `java_master_templates/` + `java_test_templates/` (rule 23) |
| `/implement-ui` | plan-act-validate-refine-loop, audit-trail, test-cases, test-cases-br-coverage, test-matrix, test-results; **bdd-test-case → `docs/bdd-test-cases.md`** + e2e-test-results (when journeys change/E2E ran); calculation-catalog when binding amount fields; **ati-navigation-checklist** + **ui-spec-template** (navigation) when back-stack or handoff |
| `/expand-test-coverage` | test-strategy (update if gaps), test-cases, test-cases-br-coverage, test-matrix, **bdd-test-case → `docs/bdd-test-cases.md`**, test-results, e2e-test-results, eval-results, audit-trail; **structural API:** `java_test_templates/` via spring-test-generator |
| `/close-eval-gaps` | eval-fix-log, eval-results (re-run), audit-trail |
| `/review-changes` | review-findings (**not** code-review), eval-results, api-validation (optional), audit-trail |
| `/application-summary` | application-summary, audit-trail |
| `/pr-assist` | test-results, e2e-test-results (when E2E ran), review-findings, audit-trail |
| `/fix-issue` defect | issue-log (**required**), plan-act-validate-refine-loop, audit-trail, test-results; calculation-catalog when the defect is amount math |
| `/fix-issue` cited-requirements | incremental-change-plan (**required**), plan-act-validate-refine-loop, audit-trail, behavior-provenance, code-generation-dos-donts; **calculation-catalog section mandatory when amount math**; feature-note when behavior changes; test-results when tests ran; api-contract when API shape changes |
| `/deliver-sprint` | Inherits the required templates from each invoked delivery command |

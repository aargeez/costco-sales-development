# SDLC Pipeline — Intake → Plan → Deliver

## Phase 0 — ATI menu start (calculations)

When starting from an **ATI menu option** (or any amount-heavy module):

```
ATI option  →  /extract-calculations  →  artifacts/calculations/<module>-calculations.json
            →  (then Phase 1)
```

Skip extract only if the module **math catalog** already exists and is current for the IBMi revision in scope. Schema: `artifacts/calculations/calculation-catalog.schema.json` (math-only). Full pre-trim extracts: `artifacts/archive_asset/extract-calculations/`. Rule: `.cursor/rules/32-calculation-catalog.mdc`.

Some modules have **no active catalog** (all records non-math, e.g. SAOD, MDT, DTM). Use archived full copy + rule 22 provenance for those behaviors.

**On amount-math error / conflict (every later phase):** `artifacts/calculations/<module>-calculations.json` is the **primary source**. Align BRD amount FRs, code, and tests to `CALC-*`. Refresh from IBMi only via `/extract-calculations`.

## Phase 1 — Intake

```
Transcript + docs (+ calc catalog)  →  /generate-brd  →  🛑 PO review  →  BRD Accepted
```

## Phase 2 — Plan

```
BRD  →  /plan-sprint  →  🛑 Sprint approval
```

## Phase 3 — Execute (choose one)

**Full guide:** [EXECUTION-OPTIONS.md](EXECUTION-OPTIONS.md)

| Option | What you run | Same result? |
|--------|--------------|--------------|
| **A — Manual** | **Seven steps individually** — run each slash command in order (see below) | Yes |
| **B — Batch** | `/deliver-sprint` only | Yes — orchestrator runs the **same seven steps** |

**Seven delivery steps** (after sprint approval; detail: [CURSOR-FLOW.md](CURSOR-FLOW.md)):

```
1. /implement-api
2. /implement-ui
3. /expand-test-coverage  →  🛑 Human: test-package Approved
4. /run-tests
5. /run-eval
6. /review-changes
7. /application-summary
```

Contracts: [run-tests.md](commands/run-tests.md) · [run-eval.md](commands/run-eval.md)

### Option A — Manual (seven steps, one command at a time)

Run in order after sprint approval. **Skip** `/implement-api` or `/implement-ui` only when the **Approved** plan marks that layer N/A (record skip + reason in the command audit).

```
1. /implement-api
      api-agent (always verify; full sync if API shape changes)
      → schema-migration-generator (Flyway + JPA if persistence changes)
      → spring-module-generator (java_master_templates/) + validation-chain-generator
      → test-case-generator (`docs/test-cases.md` + matrix) → BR coverage PASS
      → spring-test-generator (java_test_templates/, rule 23) → test-case-generator scripts phase
      → api-test-runner (mvn test + JaCoCo)
      → artifacts/test-results-<date>.md

2. /implement-ui
      mockup-to-component (PNG / Figma / feature.md)
      → next-component-generator (layout: `.cursor/skills/audit-ui-layout/SKILL.md`; nav: `.cursor/skills/ati-navigation-state/SKILL.md`)
      → test-case-generator + e2e-scenario-writer → docs/test-cases.md + matrix + docs/bdd-test-cases.md
      → test-cases-br-coverage gate (PASS required)
      → Vitest / Playwright slice scripts
      → ui-test-runner (npm test + e2e)
      → artifacts/test-results-<date>.md, e2e-test-results-*.md

3. /expand-test-coverage
      reads prior test-results + BRD gaps (design only — no full-suite run, no eval here)
      → test-intake-normalizer (untrusted intake only)
      → test-case-generator → docs/test-cases.md + docs/test-matrix.md
      → tc-completeness-validator + tc-step-quality-validator (case bodies)
      → e2e-scenario-writer → docs/bdd-test-cases.md (when e2e-layer)
      → br-coverage-validator → docs/test-cases-br-coverage.md (PASS required)
      → docs/quality/test-package-review.md **Pending Approval**
      → 🛑 STOP — do not run /run-tests until human sets Approval = Approved

4. /run-tests  (after test-package Approved — see commands/run-tests.md)
      orchestrator gate: PASS + Approved
      → quality-agent → test-agent
      → spring-test-generator / playwright-codegen (gap scripts by execution_layer)
      → api-test-runner then ui-test-runner (JUnit/IT → Vitest → Playwright)
      → coverage-analyzer, e2e-defect-analyzer (failures), playwright-self-healer (suggestions only)
      → artifacts/test-results-<date>.md

5. /run-eval  (see commands/run-eval.md)
      quality-agent → eval-agent (incremental sprint scope, or full-sweep when commanded)
      → evals/product/results/* completeness ledger + fix-queue
      → if auto_fix open: /close-eval-gaps (max 3) then re-run affected graders
      → updates evals/STATUS.md when applicable

6. /review-changes
      quality-agent coordinates → code-review-agent + security-agent
      (does **not** invoke eval-agent — requires /run-eval completeness ledger)
      → coverage-analyzer, br-coverage-validator
      → verdict failures → `/fix-issue` (human proceed gate) when appropriate
      → artifacts/audit/<date>-review-changes.md

7. /application-summary
      validates evidence → HTML + PDF summary
```

### Option B — Batch

```
/deliver-sprint
Sprint: <sprint-id>
```

Runs the **same seven steps** as Option A: `implement-api` + `implement-ui` per sprint items, then `expand-test-coverage` once, **STOP** at test-package Pending Approval. After human **Approved**, resume steps 4–7 (`/run-tests` → `/run-eval` → `/review-changes` → `/application-summary`), then docs closeout (root `README.md` when surface changed + harness progress + retrospective).

---

## Reactive fixes (outside sprint delivery)

For ad-hoc bugs, gaps, or enhancements **not** on an approved sprint plan:

```
/fix-issue  →  [classify in command]  →  fix plan  →  🛑 Human: proceed
       →  backend-agent (fix mode) / frontend-agent (fix mode)  →  unit + regression tests
       →  artifacts/issues/<date>-<slug>.md
```

| Step | Command | Notes |
|------|---------|-------|
| Fix | `/fix-issue` defect | Classification embedded; no triage agent |
| Review (if API/schema/security) | `/review-changes` | Optional closeout; sprint-grade review requires `/run-eval` completeness ledger (see `fix-issue.md`) |
| PR | `/pr-assist` | When ready to ship |

Rule: `.cursor/rules/12-production-fix-governance.mdc` (defect mode). Cited diffs use `/fix-issue` cited-requirements, not this proceed gate.

If scope grows to sprint size → `/plan-sprint` instead of repeated `/fix-issue`.

---

## Command reference

| # | Command | Agents / subagents | Must run tests? |
|---|---------|-------------------|-----------------|
| — | `/generate-brd` | requirements-agent | — |
| — | `/plan-sprint` | planner-agent | — |
| 1 | `/implement-api` | backend, api-contract, **schema-migration-generator**, spring-module-generator, **test-agent**, test-case-generator, **spring-test-generator**, **api-test-runner** | **Yes** (slice; after BR coverage PASS) |
| 2 | `/implement-ui` | frontend, mockup-to-component, next-component-generator, **test-agent**, test-case-generator, **ui-test-runner** | **Yes** (slice; after BR coverage PASS) |
| 3 | `/expand-test-coverage` | quality-agent, **test-agent**, br-coverage-validator, automation-companion-author, tc-* validators | **No** — design + gate + Pending Approval only |
| 4 | `/run-tests` | quality-agent → **test-agent**, backend/frontend agents, spring-test-generator, playwright-codegen, api/ui test runners | **Yes** (after package **Approved**) |
| 5 | `/run-eval` | quality-agent → **eval-agent** | Eval ledger required for review |
| 5b | `/close-eval-gaps` | orchestrator, backend/frontend agents, **eval-agent** (when fix-queue open) | **Yes** (affected graders) |
| 6 | `/review-changes` | quality-agent → code-review-agent + security-agent; coverage-analyzer, br-coverage-validator | — (requires `/run-eval` ledger) |
| 7 | `/application-summary` | documentation, application-summary-writer | Validates prior |
| — | `/deliver-sprint` | orchestrator → seven steps above | **Yes** (after step 3 approval for `/run-tests`) |
| — | `/fix-issue` | **defect:** orchestrator, **backend-agent** / **frontend-agent** (fix mode), api/ui test runners. **cited-requirements:** change-agent (+ api-contract / generators / runners as scoped) | **Yes** (affected surface) |

### Cited incremental changes (outside full sprint batch)

```
Cited requirements  →  /fix-issue (cited-requirements)  →  change plan + minimal diffs + provenance
                    →  /expand-test-coverage (if gaps) or /review-changes
```

Use when the ask is incremental and source-backed. Do **not** invent behavior; prefer `/implement-api` / `/implement-ui` for full vertical slices.

---

## Artifact chain

```
implement-api  ──► test-results-<date>.md (API slice)
implement-ui   ──► test-results + e2e-test-results-* (when E2E ran)
expand-*       ──► test-cases.md + test-matrix.md + test-cases-br-coverage.md + test-package-review (Pending Approval)
run-tests      ──► test-results + gap scripts executed (+ defect/heal issue logs when failures)
run-eval       ──► evals/product/results/* ledger + fix-queue + evals/STATUS.md
close-eval-*   ──► eval-fix-log + updated incremental results
review-changes ──► audit/review-changes.md (reads run-eval ledger)
application-summary ──► summary.html + .pdf
deliver-sprint closeout ──► README.md (if needed) + harness progress
fix-issue defect              ──► artifacts/issues/<date>-<slug>.md (+ test-results when tests ran)
fix-issue cited-requirements  ──► artifacts/changes/<date>-<slug>-plan.md (+ code/provenance)
```

All steps write `artifacts/audit/<date>-<command>-*.md`.

**Agent rule:** Every command completion message includes **Next command:** per the command file § Next command section.

### Next command cheat sheet

Full table: [EXECUTION-OPTIONS.md](EXECUTION-OPTIONS.md#next-command-cheat-sheet) · [CURSOR-FLOW.md](CURSOR-FLOW.md)

| Completed | Next command |
|-----------|--------------|
| `/generate-brd` | `/plan-sprint` *(after BRD review)* |
| `/plan-sprint` | `/implement-api` or `/deliver-sprint` *(after sprint approval)* |
| `/implement-api` | `/implement-ui` or `/expand-test-coverage` |
| `/implement-ui` | `/expand-test-coverage` |
| `/expand-test-coverage` | **Wait** — human sets test-package **Approved**, then `/run-tests` |
| `/run-tests` | `/run-eval` |
| `/run-eval` | `/review-changes` or `/close-eval-gaps` |
| `/close-eval-gaps` | `/review-changes` (when green) |
| `/review-changes` | `/application-summary` *(or `/validate-kpi-metrics` per review-changes.md)* |
| `/application-summary` | `/pr-assist` *(optional)* |
| `/deliver-sprint` (paused) | Human approval, then `/run-tests` or resume batch |

---

## Human gates

| After | Gate |
|-------|------|
| `/generate-brd` | Domain BRD review |
| `/plan-sprint` | Sprint approval |
| Before execution | BRD Accepted + sprint Approved |
| `/expand-test-coverage` | Human sets `docs/quality/test-package-review.md` **Approved** before `/run-tests` |

Governance: `.cursor/rules/11-intake-planning-governance.mdc`

Seed changes, diagnose traces, and cross-module ports stay on `/fix-issue` cited-requirements when a BR, story, or `CALC-*` is cited, and on `/fix-issue` defect otherwise. They do not insert a step into the execute order above.

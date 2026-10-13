# /fix-issue

## Objective

One command for work **outside** the seven-step delivery path (`/implement-api` … `/application-summary`). The orchestrator picks a mode before any production edit. Cited incremental diffs use **cited-requirements** mode. Ad-hoc bugs use **defect** mode.

Module name, BRD path, calculation catalog, and code roots come from `.cursor/config.yaml`. The same command applies to any sales product. Do not hardcode a product, ATI, or warehouse.

| Mode | Use when | Primary | Gate before code |
|------|----------|---------|------------------|
| **defect** | Ad-hoc bug, gap, or enhancement (expected vs actual). Not an approved sprint SP-xxx. | **backend-agent** / **frontend-agent** (fix mode) | Fix plan, then explicit human **proceed** |
| **cited-requirements** | A cited source (BRD FR/BR, SP-xxx, `docs/feature.md`, pasted requirement, or `CALC-*`) needs a **minimal** diff. Includes cited seed, diagnose-to-diff, and cross-ATI port. | **change-agent** | Change plan written first. Human confirm **only** when scope or the catalog is ambiguous. |

Prefer `/implement-api` or `/implement-ui` when the sprint item is a full vertical slice with new endpoints or screens.

Classification and mode selection happen **in this command**. There is no separate triage agent.

When the change touches **amount math** (O/S, totals, pickups, FX, adjustments, vault fund, inquiry display amounts), implement from `artifacts/calculations/<module>-calculations.json` — not from free-form BRE paraphrase. On error or conflict, that JSON is the **primary source**. Align code and tests to `CALC-*`. Do not rewrite the catalog toward failing Java/SQL/UI. Missing or stale catalog → `/extract-calculations` before code.

## Mode selection

Evaluate in order. First strong match wins.

| Signal | Mode |
|--------|------|
| Cited BRD FR/BR, approved story / SP-xxx, `docs/feature.md` section, pasted requirement, or `CALC-*`, and the ask is an incremental change | **cited-requirements** |
| Seed or Flyway data change, diagnose that ends in a cited diff, or port of a pattern from another ATI, **with** a BR, story, or `CALC-*` cite | **cited-requirements** |
| Bug, gap, or enhancement with steps / expected / actual, and no requirement cite driving the change | **defect** |
| Same seed, diagnose, or port work **without** a cite | **defect** |
| Cannot tell bug from enhancement, or expected behavior contradicts the BRD | **defect** → classification `needs-clarification` (STOP) |

Full-sweep eval verdict failures stay on **defect** mode (`/run-eval` full-sweep). They are not sprint SP items.

---

# Mode: defect

Triage, analyze, and fix a reported bug, gap, or enhancement. Wait for human approval on the fix plan before writing production code. Run unit + regression tests and produce a complete issue log.

## When to Use

- Ad-hoc bug, gap, or enhancement found in dev, QA, or review
- Issue is **not** already tracked as an approved sprint SP-xxx item
- User wants a production-grade fix with test evidence

## Example

```
/fix-issue
Mode: defect
Issue: Discrepancy list shows zero variance when the pickup amount differs from expected
Steps: Open the module screen for the cited location and business date → view the discrepancy grid
Expected: variance column reflects pickup vs expected delta per the cited BRD FR
Actual: variance cells show 0.00
Severity: High
```

## Preconditions (Hard Stop)

- [ ] User provided issue description (what, steps, expected vs actual — or agent asks and **STOPS**)
- [ ] If severity is Critical and production-impacting → confirm environment scope before implement

## Phase 0 — Classification (embedded triage; no triage agent)

The **orchestrator** performs classification using the rules below, records rationale in the orchestrator audit, then delegates.
Do **not** write production code in this phase. Do **not** have the orchestrator write `artifacts/issues/**` (`scope.yaml` audit-only). The primary agent writes the issue log, copying the orchestrator's classification.

### Required inputs (ask back if missing)

| Field | Required | Notes |
|-------|----------|-------|
| Issue summary | Yes | One-line what is wrong |
| Steps to reproduce | Yes for bugs | Skip only for pure doc/config asks |
| Expected behavior | Yes | |
| Actual behavior | Yes for bugs | |
| Severity | Preferred | Critical / High / Medium / Low |

If any required field is missing → **refuse Phase 0** (Required input absent). Register Open DQ. Do not run a numbered clarification round. Do not invent classification.

### Classification rules

Evaluate signals in order; first strong match wins. When multiple apply, use **full-stack** order (backend first, then frontend).

| Classification | Route to | Signals (any strong match) |
|----------------|----------|----------------------------|
| **backend** | **backend-agent** (fix mode) | Stack trace in `config.yaml` → `modules.api`; HTTP 4xx/5xx from API; wrong/missing DB data; Flyway/migration; service/repository/validator; OpenAPI/DTO mismatch; auth/filter on API |
| **frontend** | **frontend-agent** (fix mode) | UI layout/state/routing only; client-side validation; Next.js page/component; API call wrong from UI but API correct in Postman/curl; BFF/proxy config in UI |
| **full-stack** | **backend-agent** (fix mode) then **frontend-agent** (fix mode) | Contract mismatch (UI sends payload API rejects); end-to-end flow broken across both; shared field semantics unclear |
| **config-env** | **backend-agent** (fix mode) or human | Local env (JDK, Postgres, Zscaler, `JAVA_HOME`); not application logic — document fix in issue log; minimal or no code |
| **needs-clarification** | **STOP — ask human** | Cannot reproduce; expected behavior contradicts BRD; ambiguous whether bug or enhancement; missing acceptance criteria |

### Context to load before routing

| Source | Purpose |
|--------|---------|
| `.cursor/config.yaml` → `modules.brd` | Business intent (module BRDs under `artifacts/brd/`) |
| `.cursor/config.yaml` → `modules.openapi` | API contract (sprint OpenAPI under `contracts/`) |
| `docs/feature.md` | Feature notes |
| Related code (grep / code search) | Root-cause hints |
| `artifacts/audit/*` | Recent related changes |
| `artifacts/calculations/<module>-calculations.json` | When the defect is amount math — catalog wins |

### Classification output (write before delegating)

Record in issue log draft (`artifacts/issues/<date>-<slug>.md`):

- **Classification:** backend | frontend | full-stack | config-env
- **Rationale:** 1–3 sentences citing signals
- **Primary agent:** backend-agent (fix mode) and/or frontend-agent (fix mode)

## Phase 1 — Analyze (fix agent; no code yet)

| Classification | Agent |
|----------------|-------|
| backend | **backend-agent** (fix mode) |
| frontend | **frontend-agent** (fix mode) |
| full-stack | **backend-agent** (fix mode) → then **frontend-agent** (fix mode) (sequential) |
| config-env | **backend-agent** (fix mode) (doc/env) or escalate to human |

Fix agent must:

1. Reproduce or trace the issue in code/tests/logs
2. Document **root cause** (not symptom only)
3. Produce **impact analysis** (files, endpoints, schema, UI routes, tests at risk)
4. Produce **fix plan** — production-grade, reuse existing patterns, no workarounds
5. Flag BRD/OpenAPI interpretation changes → **STOP for PO confirmation** (do not implement)
6. Flag breaking API/schema changes → **STOP for architect confirmation**
7. If amount math is in the defect: load the calculation catalog and cite `CALC-*` in the fix plan before any formula edit (same amount-math gate as cited-requirements mode)

Update issue log with analysis sections. Then **STOP**.

### Human gate — fix plan approval

Present fix plan to human. Wait for explicit **"proceed"** (or equivalent approval).

| Human response | Action |
|----------------|--------|
| proceed / approved / yes | Continue to Phase 2 |
| revise / change plan | Update plan; **STOP** again for approval |
| cancel / no | Close issue log as cancelled; write audit only |

**Do not implement until approved.**

## Phase 2 — Implement (after approval only)

| Area | Agent | Subagents (reuse existing) |
|------|-------|------------------------------|
| Backend | **backend-agent** (fix mode) | api-agent (verify/update); schema-migration-generator (if DB); spring-module-generator; validation-chain-generator; test-case-generator; **api-test-runner** |
| Frontend | **frontend-agent** (fix mode) | mockup-to-component (if layout); next-component-generator; test-case-generator; e2e-scenario-writer (if journey changed); **ui-test-runner** |

Follow `.cursor/rules/12-production-fix-governance.mdc` — minimal diff, no reinvention, no applied Flyway edits. Stay in `scope.yaml` write allowlists.

Amount math in a defect still follows the amount-math gate below. UI displays snapshot/API amounts; do not recompute catalog math in the browser.

## Phase 3 — Validate & close

| Check | Command / agent |
|-------|-----------------|
| New/changed unit tests | test-case-generator output |
| API regression | **api-test-runner** → `mvn test` (+ JaCoCo) |
| UI regression | **ui-test-runner** → `npm test` |
| E2E (if user-facing flow changed) | ui-test-runner / Playwright skill |
| Issue log complete | `artifacts/issues/<date>-<slug>.md` |
| Sibling scan | Same issue log: other ATIs searched for the same anti-pattern; for **nav / bypass / returnQs** follow `.cursor/skills/ati-navigation-state/SKILL.md` § Sibling scan; for **layout / criteria column drift** follow `.cursor/skills/audit-ui-layout/SKILL.md` § Sibling scan; for **shared molecule / pagination drift** follow `.cursor/skills/shared-ui-molecules/SKILL.md` § Molecule validate (including sibling scan); for **uncited session defaults / defaults on load** follow `.cursor/skills/ati-session-defaults/SKILL.md` § Session defaults validate; attach finding tables; no edits to out-of-scope modules |
| Audit | `artifacts/audit/<date>-fix-issue-<slug>.md` |

If API contract, schema, or security surface changed → recommend `/review-changes`.

### Defect — Rules

- `.cursor/rules/12-production-fix-governance.mdc` (**mandatory**)
- `.cursor/rules/09-agent-orchestration.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/07-testing-and-quality.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/32-calculation-catalog.mdc` (when amount math is in the defect)
- `.cursor/rules/03-backend-springboot.mdc` (backend)
- `.cursor/rules/04-frontend-nextjs.mdc` (frontend)
- `.cursor/rules/05-api-contracts.mdc` (if API touched)

### Defect — Templates

- `.cursor/templates/issue-log-template.md` (**required**)
- `.cursor/templates/plan-act-validate-refine-loop.md`
- `.cursor/templates/audit-trail-template.md`
- `.cursor/templates/test-results-template.md`
- `.cursor/templates/incremental-change-plan-template.md` (only when the defect includes amount math and a catalog cite)

### Defect — Skills

- `.cursor/skills/audit-ui-layout/SKILL.md` (entry/search layout)
- `.cursor/skills/shared-ui-molecules/SKILL.md` (list tables / pagination molecules)
- `.cursor/skills/ati-session-defaults/SKILL.md` (Plan/BRD-cited session defaults)
- `.cursor/skills/ati-navigation-state/SKILL.md` (UI Back / returnQs / hydration)
- `.cursor/skills/jacoco-coverage/SKILL.md` (backend)
- `.cursor/skills/playwright-e2e/SKILL.md` (UI E2E if needed)
- `.cursor/skills/validation-chain/SKILL.md` (backend validation fixes)
- `.cursor/skills/calculation-catalog/SKILL.md` (when amount formulas change)

### Defect — Definition of Done

- [ ] Classification recorded with rationale (Phase 0)
- [ ] Root cause + impact + fix plan documented
- [ ] Human approved fix plan before implementation
- [ ] Production-grade fix (no workarounds per rule 12)
- [ ] If amount math: catalog loaded; fix plan cites `calculation_id`; formula comes from the catalog
- [ ] Unit tests for new/changed behavior
- [ ] Module regression pass (API and/or UI)
- [ ] `artifacts/issues/<date>-<slug>.md` complete (issue, fix, test outcomes, sibling scan)
- [ ] `artifacts/audit/<date>-fix-issue-<slug>.md` written
- [ ] `artifacts/test-results-<date>.md` updated if tests ran

### Defect — Outputs

- Code fixes under `config.yaml` → `modules.api` and/or `modules.ui`
- `artifacts/issues/<date>-<slug>.md`
- `artifacts/audit/<date>-fix-issue-<slug>.md`
- `artifacts/test-results-<date>.md` (when tests run)
- `artifacts/e2e-test-results-*.md` (if E2E ran)
- Flyway `V*.sql` / OpenAPI updates only when plan approved and needed
- Updated `artifacts/calculations/<module>-calculations.json` when amount math changed

---

# Mode: cited-requirements

Apply **only** the stated requirements as **incremental** code changes. Preserve existing structure, invent nothing, and document every behavior change with provenance.

## When to Use

- Small / medium requirement deltas against an existing codebase (bug fix that cites an FR, FR slice, clarification, config-driven tweak)
- After `/plan-sprint` when a single SP-xxx is surgical (not a greenfield module)
- Ad-hoc change requests that cite BRD / `docs/feature.md` / approved story
- Closing `parity.gaps` from a module calculation catalog (`CALC-*`)
- Cited seed or Flyway data change (seed collision note on the existing schema step: max `V*`, keys touched, modules at risk)
- Diagnose whose outcome is a cited diff
- Port of a pattern from another ATI when a BR, story, or `CALC-*` is cited

## Example (non-math)

```
/fix-issue
Mode: cited-requirements
Source: artifacts/brd/<module>-BRD-draft.md FR-012
Surface: API
Slug: fr-012-status
```

## Example (amount math — catalog required)

```
/fix-issue
Mode: cited-requirements
Source: artifacts/brd/<module>-BRD-draft.md FR-040
Module: <module>
Calculations: artifacts/calculations/<module>-calculations.json
calculation_id: CALC-<MODULE>-<RESULT>
Surface: API
Slug: <module>-amount-parity
```

## Orchestration

| Phase | Role | Agent / Subagent |
|-------|------|------------------|
| 0 Calc catalog | Conditional → **mandatory if amount math** | Load `artifacts/calculations/<module>-calculations.json`; if missing/stale → **calculation-catalog-agent** (`/extract-calculations`) before scope lock |
| 1 Scope lock | Primary | **change-agent** → write change plan (in / out of scope); cite `CALC-*` when math in scope |
| 2 Contract | Conditional | **api-agent** — verify always if API touched; update only if shape changes |
| 3 Implement | Primary | **change-agent** → edit existing modules using catalog `canonical_formula` + `steps`/`branches` + `modern_impl`; delegate generators only when structure already uses them |
| 3a Schema | Subagent (if persistence) | schema-migration-generator (align with catalog `inputs` / modern columns) |
| 3b Validation | Subagent (if rules) | validation-chain-generator |
| 3c UI | Subagent (if UI) | next-component-generator / mockup-to-component **only** for files in scope — **display snapshot/API amounts; do not recompute catalog math in the browser** |
| 4 Tests | Subagents | spring-test-generator / test-case-generator / e2e as needed for **touched** paths only; prefer catalog `parity.golden_refs` |
| 5 Run & evidence | Subagents | api-test-runner and/or ui-test-runner for affected surface |
| 6 Document | Primary + docs | provenance + feature note + audit; **update catalog `modern_impl` / `parity`** when math changed |

## Execution Order

```
Plan → Parse requirements
     → IF amount math in scope:
          Load artifacts/calculations/<module>-calculations.json
          Resolve calculation_id(s); refuse inventing formulas
          IF catalog missing → /extract-calculations (or block) BEFORE code
     → Scope lock (change plan; human confirm if ambiguous)
     → Map to existing files/patterns via catalog.modern_impl + neighbors
     → api-agent verify (if API in scope)
     → Minimal diffs only (backend / frontend / schema as listed)
          Translate catalog pseudo formula + steps into existing SQL/Java patterns
     → Tests for changed behavior only (honor golden_refs when present)
     → Update catalog parity/modern_impl when math changed
     → Document (feature note, provenance, change plan status)
     → Validate → Refine (max 3)
```

## Amount-math gate (non-negotiable)

Treat as **amount math** if the requirement mentions over/short, deposit/register totals, pickup amounts, FX/banked conversion, vault fund, count/misc adjustments, rebate adj, or any currency formula. Defect mode uses this same gate when the defect is amount math.

| Rule | Action |
|------|--------|
| Catalog path | `config.yaml` → `artifacts.calculations_module` → `artifacts/calculations/<module>-calculations.json` |
| Required cite | Every math REQ maps to ≥1 `calculation_id` in the change plan |
| Implement from | `canonical_formula` + ordered `steps` / **all** `branches` (including miss/zero/skip) + `inputs` (legacy ↔ modern). Do not `COALESCE(missing, 0)` on a lookup unless a branch `effect_kind` is `zero` for that operand. |
| Placement | Honor `placement.layer` (data_layer / api_layer / ui_layer). If `unclear` or `pending_clarification` → stop and ask. IBMi is formula source only |
| Code target | Prefer paths in `modern_impl[]` aligned to `placement`; if `parity_status=missing`, extend existing recompute/service pattern — do not invent a parallel formula owner. `temporary_live_merge` only until DB snapshot SoR |
| Conflicts | Catalog JSON wins over BRD/BRE/modern code. Catalog `conflict_log` / pending `assumption_registry` → `BLOCKED_CONFLICT` or ask; do not ship Done |
| UI | Bind API/DB fields only; never re-derive `CALC-*` in TypeScript; display-only IBMi rules via `display_via` / `display_rules` / query filters |
| Shared engine | If `modern_impl` is shared across ATIs, cite `invocationSource` or the applicable type set from the catalog and filter on it. Silent catalog → `ASSUMPTION-*` and not Done. Do not invent a profile. |
| After code | Update that calculation’s `modern_impl` and `parity` (and clear `placement.interim` when snapshot becomes SoR) in the JSON |

## Hard constraints (non-negotiable)

1. **Requirements-only** — Do not change files, APIs, copy, or behavior outside the approved scope list.
2. **No hallucination** — Every behavior statement needs a source (rule 22). If silent → `ASSUMPTION-*` + ask; do not ship pending assumptions as Done.
3. **No invented formulas** — Amount math comes from the calculation catalog (rule 32) or IBMi via `/extract-calculations`. BRE/BRD are secondary crosswalk only.
4. **Structure fidelity** — Match packages, layers, naming, templates, and patterns already in the touched area. Prefer edit over rewrite; no drive-by refactors, renames, or “while we’re here” cleanups.
5. **Incremental** — Smallest diff that satisfies the requirement. Do not introduce new frameworks, folders, or abstractions unless the requirement explicitly demands them **and** existing structure cannot host the change.
6. **Document** — Change plan, audit trail, provenance updates, and feature note (or explicit no-op) are required outputs.

## Cited-requirements — Rules

- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/01-project-context.mdc`
- `.cursor/rules/02-security-and-compliance.mdc`
- `.cursor/rules/09-agent-orchestration.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/03-backend-springboot.mdc` (when API in scope)
- `.cursor/rules/04-frontend-nextjs.mdc` (when UI in scope)
- `.cursor/rules/05-api-contracts.mdc` (when API in scope)
- `.cursor/rules/07-testing-and-quality.mdc`
- `.cursor/rules/23-test-generation.mdc` (when structural Java tests apply)
- `.cursor/rules/32-calculation-catalog.mdc` (**required** when amount math in scope)

Rule 12 (production-fix proceed gate) applies to **defect** mode. Cited-requirements mode confirms with a human only when the change plan is ambiguous, the catalog placement is unclear, or an assumption blocks Done.

## Cited-requirements — Templates

- `.cursor/templates/incremental-change-plan-template.md` → `artifacts/changes/<date>-<slug>-plan.md` (**required**; includes Calculation catalog section)
- `.cursor/templates/plan-act-validate-refine-loop.md`
- `.cursor/templates/audit-trail-template.md`
- `.cursor/templates/behavior-provenance-template.md`
- `.cursor/templates/calculation-catalog-template.md`
- `.cursor/templates/feature-note-template.md` (when behavior changes)
- `.cursor/templates/code-generation-dos-donts.md`
- `.cursor/templates/test-results-template.md` (when tests run)
- `.cursor/templates/api-contract-template.md` (when API shape changes)
- `.cursor/templates/audit-ui-layout-checklist.md` (when entry/search/criteria UI changes)
- `.cursor/templates/ati-navigation-checklist.md` (when the change includes back-stack or cross-screen handoff)

## Cited-requirements — Skills

- `.cursor/skills/audit-ui-layout/SKILL.md` (entry/search layout)
- `.cursor/skills/shared-ui-molecules/SKILL.md` (list tables / pagination molecules)
- `.cursor/skills/ati-session-defaults/SKILL.md` (Plan/BRD-cited session defaults)
- `.cursor/skills/ati-navigation-state/SKILL.md` (UI Back / returnQs / hydration)
- `.cursor/skills/validation-chain/SKILL.md` (if validation rules change)
- `.cursor/skills/jacoco-coverage/SKILL.md` (if API tests run)
- `.cursor/skills/playwright-e2e/SKILL.md` (if UI journeys change)
- `.cursor/skills/calculation-catalog/SKILL.md` (**required** if amount formulas change)

## Ask Back If Missing

- Explicit requirement source (BRD FR/BR, SP-xxx, `docs/feature.md` section, pasted requirement, or `CALC-*`)
- Target surface (API / UI / both / config / schema)
- Whether out-of-scope adjacent defects may be touched (default: **no**)
- **Module** and path to `artifacts/calculations/<module>-calculations.json` when amount math is in scope
- Which `calculation_id`(s) to implement if the FR maps to more than one

## Cited-requirements — Definition of Done

- [ ] Change plan written with **In scope** / **Out of scope** / **Open questions** before coding
- [ ] If amount math: catalog loaded; every math REQ cites `calculation_id`; plan § Calculation catalog filled
- [ ] If that math uses a shared O/S or recompute path: invocation profile or type set cited, or `ASSUMPTION-*` registered (not invented)
- [ ] If the change includes back-stack or cross-screen handoff: destination screen cited; `ati-navigation-checklist.md` filled from that cite (do not invent a route)
- [ ] Code implements catalog formula/steps (translated into existing SQL/Java), not ad-hoc BRE paraphrase
- [ ] Every in-scope item traced to a source locator (rule 22) — catalog and/or BRD/IBMi
- [ ] Diff limited to files justified by the change plan (no unrelated edits)
- [ ] Existing code structure/patterns preserved in touched areas
- [ ] No uncited behavior, UI copy, error semantics, auth decisions, or invented formulas
- [ ] Pending `ASSUMPTION-*` (incl. catalog `assumption_registry`) either approved or change marked blocked (not Done)
- [ ] Catalog `conflict_log` items affecting scope resolved or blocked
- [ ] api-agent verify recorded if API touched
- [ ] Tests updated/added for changed behavior; relevant runners pass; golden_refs covered when listed
- [ ] Slice coverage PASS before this slice’s scripts (rule 07)
- [ ] `artifacts/test-results-<date>.md` updated when API/UI tests ran
- [ ] Feature note appended (or documented no-op) for behavior changes
- [ ] If math changed: `artifacts/calculations/<module>-calculations.json` `modern_impl` / `parity` updated
- [ ] Provenance artifact updated (include `CALC-*`)
- [ ] Audit trail written

## Cited-requirements — Outputs

- `artifacts/changes/<date>-<slug>-plan.md`
- Touched code under `config.yaml` → `modules.api` and/or `modules.ui` (as scoped)
- Updated `artifacts/calculations/<module>-calculations.json` when amount math changed
- `artifacts/sprints/<sprint-id>-provenance.md` or `artifacts/changes/<date>-<slug>-provenance.md`
- `docs/feature.md` section (or dated feature note) when behavior changes
- `artifacts/test-results-<date>.md` (when tests ran)
- `artifacts/audit/<date>-fix-issue-<task>.md`

---

## Next command

**On completion, tell the user:**

> ✅ `/fix-issue` complete.  
> **Next command:** see the mode table below.

| Mode | Condition | Next command |
|------|-----------|--------------|
| defect | API, schema, auth, or security surface changed | `/review-changes` |
| defect | Ready to open PR | `/pr-assist` |
| defect | Another unrelated issue | `/fix-issue` with a new description |
| defect | Fix reveals sprint-sized scope | `/plan-sprint` |
| cited-requirements | Catalog missing for amount work | `/extract-calculations` then re-run `/fix-issue` (same mode) |
| cited-requirements | Tests thin / gaps vs BRD or catalog golden_refs | `/expand-test-coverage` |
| cited-requirements | Change ready for review | `/review-changes` (requires `/run-eval` completeness ledger when no prior eval for this slice) |
| cited-requirements | Larger sprint work remains | Continue `/implement-api` or `/implement-ui` for remaining SP-xxx |
| either | Blocked on assumption / conflict | Wait for human decision — do not proceed |

**Example — defect, review first:**

```
/review-changes
Scope: fix-issue ISS-20260803-search-location-filter
Prior results: artifacts/test-results-<date>.md
```

**Example — defect, open PR:**

```
/pr-assist
```

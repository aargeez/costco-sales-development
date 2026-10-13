# Frontend Agent

## Goal

Implement and maintain the UI module at `config.yaml` → `modules.ui`: screens, API client, unit and E2E tests.

## Must / Must NOT

- **Must:** Wiring proven against OpenAPI; typed client generated from OpenAPI; caller-defined sequence; traceability from UI action → endpoint → story/BR. When entry/search/criteria UI changes, load `.cursor/skills/audit-ui-layout/SKILL.md` and fill `audit-ui-layout-checklist.md`, and load `.cursor/skills/ati-session-defaults/SKILL.md` with `audit-ui-session-defaults-checklist.md` (provenance table + gate). When list/results tables or pagination change, load `.cursor/skills/shared-ui-molecules/SKILL.md`, run § Molecule validate (checklist + sibling scan), and record findings in audit. When the screen has back or handoff, load `.cursor/skills/ati-navigation-state/SKILL.md`, fill `ati-navigation-checklist.md` from cited sources, reuse existing `*ReturnTo` / `withSelected` helpers, and add Vitest that proves UI restore when Back is in scope.
- **Must NOT:** Write API business logic; skip wiring because components look done; public `/wire-*` command; invent domain fields; put secrets in the client; invent microcopy; in mode `fix`, implement before human **proceed** or paper over an API bug in the UI.

## Input checklist

- Plan / mockup / Figma sketch / UX spec, **or** `/fix-issue` issue log + user report
- OpenAPI at `modules.openapi`
- Human `proceed` before implement when mode is `fix`

## Clarification protocol

If neither mockup, sketch, UX spec, nor story UI table is available, register an Open DQ. `/implement-ui` refuses unless the plan is **Approved**. Mode `fix`: STOP for human **proceed**.

## Wiring gate (required check gate)

Ordered UI→API sequence is a **check gate of this agent**, not a peer agent. Evidence: typed client + `docs/sprint<N>/wiring/`.

## Enforce Rules

- `.cursor/rules/04-frontend-nextjs.mdc`
- `.cursor/rules/05-api-contracts.mdc`
- `.cursor/rules/07-testing-and-quality.mdc`
- `.cursor/rules/09-agent-orchestration.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/32-calculation-catalog.mdc` (amount columns — display catalog/API values only; do not recompute formulas)

## Templates

- `.cursor/templates/plan-act-validate-refine-loop.md`
- `.cursor/templates/audit-trail-template.md`
- `.cursor/templates/test-cases-template.md`
- `.cursor/templates/test-cases-br-coverage-template.md`
- `.cursor/templates/test-matrix-template.md`
- `.cursor/templates/test-results-template.md`
- `.cursor/templates/e2e-test-results-template.md` (when E2E runs)
- `.cursor/templates/bdd-test-case-template.md` → `docs/bdd-test-cases.md` (when journeys change)
- `.cursor/templates/calculation-catalog-template.md` (when binding amount fields)
- `.cursor/templates/audit-ui-layout-checklist.md` (when entry/search/criteria UI changes)
- `.cursor/templates/audit-ui-session-defaults-checklist.md` (when entry/search session defaults in scope)
- `.cursor/templates/audit-ui-list-table-checklist.md` (when list/results tables or pagination change)
- `.cursor/templates/ati-navigation-checklist.md` (when the screen has back-stack or cross-screen handoff)
- `.cursor/templates/ui-spec-template.md` (navigation fields when a UI spec is written)

## Delegate to Subagents

| Subagent | When |
|----------|------|
| mockup-to-component | Layout from mockup/Figma/feature doc |
| next-component-generator | Component implementation |
| test-case-generator | Designed cases + matrix first; Vitest after BR coverage PASS |
| e2e-scenario-writer | Design `docs/bdd-test-cases.md` when journeys change; Playwright after PASS |
| ui-test-runner | **Always** before exit |

## Skills

- `.cursor/skills/audit-ui-layout/SKILL.md` (entry/search layout)
- `.cursor/skills/ati-session-defaults/SKILL.md` (cited session defaults only)
- `.cursor/skills/shared-ui-molecules/SKILL.md` (list tables / pagination molecules)
- `.cursor/skills/ati-navigation-state/SKILL.md` (Back / handoff / URL restore)
- `.cursor/skills/quality/SKILL.md`
- `.cursor/skills/playwright-e2e/SKILL.md`
- `.cursor/skills/calculation-catalog/SKILL.md` (do not recompute catalog formulas in UI)

## Reference

- OpenAPI: `config.yaml` → `modules.openapi`
- BRD: `config.yaml` → `modules.brd`
- Calculation catalog: `artifacts/calculations/<module>-calculations.json` (**primary source** on amount errors — display API/DB values that match `CALC-*`; do not recompute)
- E2E: `config.yaml` → `modules.ui` (specs under that module’s e2e tree)

## Loop

Plan → Act → Validate → Refine (max 3)

## Exit Criteria

- `npm test` passes (ui-test-runner)
- E2E updated if journey changed
- When entry/search/criteria in scope: `audit-ui-layout-checklist.md` complete per layout skill; `audit-ui-session-defaults-checklist.md` + provenance table per ati-session-defaults skill
- When list/results tables in scope: `audit-ui-list-table-checklist.md` complete + shared-molecule gate PASS per shared-ui-molecules skill
- When Back/handoff in scope: checklist complete + Vitest UI restore per `ati-navigation-state` skill
- UI behavior provenance recorded for new copy/flows
- Audit trail written

---

## Fix mode (`/fix-issue` frontend leg)

Invoked by orchestrator after Phase 0 classification (`frontend`, `full-stack` second leg). **Does not replace** `/implement-ui` — use this mode only for ad-hoc fixes outside an approved sprint item.

### Must NOT (fix mode)

- Implement before human approves the fix plan (Phase 1 → gate → Phase 2)
- Change BRD meaning without user confirmation
- Duplicate components or hooks that already exist
- Skip `ui-test-runner` regression
- Patch around API bugs in the UI when backend should be fixed (escalate to **backend-agent** fix mode)

### Phase 1 — Analyze (no production code)

```
Plan → read issue log + user report
     → trace: page → component → hook/client → API proxy
     → if the screen has back or handoff: trace full returnQs, *ReturnTo.ts, and withSelected before blaming the API; confirm bypass runs only when cited invocationSource is present and non-empty
     → verify API behavior (curl/known contract) if UI/API boundary unclear
     → root cause + impact table + fix plan
     → update issue log
     → STOP — present plan; wait for human proceed
```

**Impact analysis must cover:** routes, components, state, API client calls, proxy config, existing Jest/Playwright tests.

### Phase 2 — Implement (after proceed only)

```
Act → extend existing components/hooks (prefer over new parallel UI)
    → reuse *ReturnTo / withSelected; keep full cited returnQs (no stripping inside helpers)
    → mockup-to-component (only if layout/visual reference needed)
    → next-component-generator (if new component slice required)
    → test-case-generator (cases + matrix for changed behavior)
    → e2e-scenario-writer (if buyer journey changed → docs/bdd-test-cases.md then specs after PASS)
    → ui-test-runner (npm test; e2e if journey changed)
    → Validate → Refine (max 3)
```

### Fix mode — additional rules

- `.cursor/rules/12-production-fix-governance.mdc` (**mandatory**)
- `.cursor/rules/04-frontend-nextjs.mdc` navigation: full cited `returnQs`, bypass only with non-empty cited `invocationSource`
- When the fix touches Back or cross-screen handoff: copy filled `ati-navigation-checklist.md` (including **returnQs shape**) into `artifacts/audit/<date>-fix-issue-<slug>-frontend.md`

### Fix mode — outputs

- `artifacts/issues/<date>-<slug>.md` (analysis + implementation + tests)
- `artifacts/audit/<date>-fix-issue-<slug>-frontend.md` or consolidated parent audit
- `artifacts/e2e-test-results-*.md` (if E2E ran)

### Escalation

If analysis shows the root cause is API-side → stop UI implement; route back to **backend-agent** (fix mode) with updated classification **full-stack** or **backend**.

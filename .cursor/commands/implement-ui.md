# /implement-ui

## Objective

Implement or fix UI in `config.yaml` → `modules.ui` from mockups/feature docs, **generate tests, run tests**, and record evidence.

**On amount-math error / conflict:** `artifacts/calculations/<module>-calculations.json` is the **primary source**. Bind API/DB values that match `CALC-*`; never recompute in the browser to “match” a failing screen. Missing/stale → `/extract-calculations`.

## When to Use

- After `/implement-api` (or in parallel if no API dependency)
- After `/plan-sprint` — per SP-xxx tagged `implement-ui`
- **Or** instead of `/deliver-sprint` — manual frontend-only scope

## Orchestration

| Phase | Role | Agent / Subagent |
|-------|------|------------------|
| 1 Design intake | Subagent | **mockup-to-component** (PNG, Figma URL, or `docs/feature.md`) |
| 2 Implement | Primary | **frontend-agent** → next-component-generator |
| 3a Cases | Design | test-agent + test-case-generator + e2e-scenario-writer (if journey changed) → `docs/test-cases.md` + matrix + `docs/bdd-test-cases.md` |
| 3b Coverage gate | Required | Score cases vs cited UI/AC/FR/BR → `docs/test-cases-br-coverage.md`. FAIL → expand cases; do not run |
| 4 Scripts | After PASS | test-case-generator (Vitest) + e2e-scenario-writer (if journey changed) |
| 5 Run & evidence | Subagent | **ui-test-runner** (mandatory; after PASS) |

## Execution Order

```
Plan → mockup-to-component (resolve layout from mockup/Figma/feature doc)
     → frontend-agent + next-component-generator
     → test-case-generator → `docs/test-cases.md` + matrix
     → e2e-scenario-writer (if flow changed) → `docs/bdd-test-cases.md`
     → test-cases-br-coverage gate (PASS required)
     → Vitest / Playwright scripts for covered cases only
     → Layout validate (`.cursor/skills/audit-ui-layout/SKILL.md`: checklist + 360×640 + skill sibling scan)
     → Shared molecule validate (`.cursor/skills/shared-ui-molecules/SKILL.md` § Molecule validate: checklist + sibling scan when list/results tables in scope)
     → Session defaults validate (`.cursor/skills/ati-session-defaults/SKILL.md`: provenance table + checklist + sibling scan when entry/search/criteria in scope)
     → Navigation validate (`.cursor/skills/ati-navigation-state/SKILL.md`: Back URL → entry hydration → Vitest UI restore + skill sibling scan)
     → ui-test-runner (npm test; npm run test:e2e if journey changed)
     → Validate → Refine (max 3)
```

## Plan-as-input mode

When `governed_plan` or `sprint_governed_plan` is supplied, apply `.cursor/templates/plan-as-input-protocol.md` before Clarification Protocol.

## Command-Specific Hardening

- Refuse unless the sprint/governed plan is **Approved**. **Proposed — Pending Approval** is not enough.
- **ASSUMPTION-PCC-002:** expand coverage PASS does **not** block this command. Slice cases + BR coverage + UI tests still run here. Remaining FR/BR gap scripts belong to `/run-tests` after expand package Approved.
- Wiring is a required check gate (frontend-agent). Do not write API business logic.
- Stay in frontend-agent `scope.yaml` allowlist.

## Mockup / Figma Inputs

| Input | Path / action |
|-------|----------------|
| Repo PNG | `UI/image (*.png)` or `artifacts/intake/supporting/` |
| Figma | User provides figma.com URL → mockup-to-component uses Figma MCP |
| Text only | `docs/feature.md` or BRD FR-xxx |

## Rules

- `.cursor/rules/04-frontend-nextjs.mdc`
- `.cursor/rules/05-api-contracts.mdc`
- `.cursor/rules/07-testing-and-quality.mdc`
- `.cursor/rules/09-agent-orchestration.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/32-calculation-catalog.mdc` (amount columns — display API values only)

## Templates

- `.cursor/templates/plan-act-validate-refine-loop.md`
- `.cursor/templates/audit-trail-template.md`
- `.cursor/templates/test-cases-template.md`
- `.cursor/templates/test-cases-br-coverage-template.md`
- `.cursor/templates/test-matrix-template.md`
- `.cursor/templates/test-results-template.md` (UI section)
- `.cursor/templates/e2e-test-results-template.md` (when E2E ran)
- `.cursor/templates/bdd-test-case-template.md` → `docs/bdd-test-cases.md` (when journeys change)
- `.cursor/templates/calculation-catalog-template.md`
- `.cursor/templates/audit-ui-layout-checklist.md` (when entry, search/criteria, or session filters change)
- `.cursor/templates/audit-ui-list-table-checklist.md` (when list/results tables or pagination change)
- `.cursor/templates/audit-ui-session-defaults-checklist.md` (when entry, search/criteria, or session fields change)
- `.cursor/templates/ati-navigation-checklist.md` (when the screen has back-stack or cross-screen handoff)
- `.cursor/templates/ui-spec-template.md` (navigation section when a UI spec is written)

## Skills

- `.cursor/skills/audit-ui-layout/SKILL.md` (entry/search column layout, RF 360×640)
- `.cursor/skills/shared-ui-molecules/SKILL.md` (list tables, pagination molecules, manifest check)
- `.cursor/skills/ati-session-defaults/SKILL.md` (Plan/BRD-cited session defaults only)
- `.cursor/skills/ati-navigation-state/SKILL.md` (Back / handoff / URL restore)
- `.cursor/skills/quality/SKILL.md`
- `.cursor/skills/playwright-e2e/SKILL.md`
- `.cursor/skills/calculation-catalog/SKILL.md`

## Ask Back If Missing

- UI mockup path, Figma URL, or `docs/feature.md` reference

## Definition of Done

- [ ] **`docs/test-cases.md` + matrix** include the UI slice
- [ ] **`docs/bdd-test-cases.md`** written when journey / e2e-layer cases are in scope
- [ ] **`docs/test-cases-br-coverage.md` Status PASS** before runners
- [ ] RF viewport (360×640) + i18n (en-US / fr-CA)
- [ ] Components match mockup or feature doc; reuses existing RF patterns
- [ ] When the slice calls an API: audit table of OpenAPI query/JSON names → client symbols → test asserts (same wiring gate; no extra command)
- [ ] When the screen has back-stack or cross-screen handoff: `ati-navigation-checklist.md` filled from cited sources (no invented route), including **returnQs shape** with each kept key listed
- [ ] When entry/search/criteria UI changes: `audit-ui-layout-checklist.md` filled from cite (peer screen or mockup)
- [ ] When list/results tables or pagination change: `audit-ui-list-table-checklist.md` filled; shared-molecule gate PASS (in-slice + sibling scan table) per skill
- [ ] When entry/search/criteria or session fields change: `audit-ui-session-defaults-checklist.md` filled; provenance table + session-defaults gate PASS per skill
- [ ] When checklist documents Back: Vitest proves **UI restore** from return URL (not only `router.push` target)
- [ ] New grids use the module’s selectable-row pattern; no Option column
- [ ] **`npm test` passes** (ui-test-runner)
- [ ] **E2E updated and passing** if journey changed
- [ ] When that E2E includes Back or a handoff: locators follow the Playwright skill, and only if `ati-navigation-checklist.md` says the journey has Back or a handoff
- [ ] `artifacts/e2e-test-results-<timestamp>.md` when E2E ran
- [ ] Audit trails written

## Outputs

- `config.yaml` → `modules.ui`
- `docs/test-cases.md`, `docs/test-matrix.md`, `docs/test-cases-br-coverage.md`
- `docs/bdd-test-cases.md` (when journeys / e2e-layer in scope)
- `artifacts/test-results-<date>.md` (UI section)
- `artifacts/e2e-test-results-<timestamp>.md` (optional)
- `artifacts/audit/<date>-implement-ui-<task>.md`

## Next command

**On completion, tell the user:**

> ✅ `/implement-ui` complete.  
> **Next command:** `/expand-test-coverage`

| Condition | Next command |
|-----------|--------------|
| Manual flow (default) | `/expand-test-coverage` |
| More SP-xxx `implement-ui` items in sprint | `/implement-ui` again for next SP item, then `/expand-test-coverage` |
| Using batch | `/deliver-sprint` continues — no action |

**Example:**

```
/expand-test-coverage
BRD: artifacts/brd/saro-BRD-draft.md
Prior results: artifacts/test-results-<date>.md
Sprint: sprint-01
```

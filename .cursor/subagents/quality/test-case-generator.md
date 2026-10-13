# test-case-generator

## Purpose
AC/OpenAPI/FR/BR → designed test cases in markdown **and** a matrix index. Does not write executable scripts.

## Parent
test-agent

## Must / Must NOT
- Must: write `docs/test-cases.md` (case bodies with full steps + expected) and update `docs/test-matrix.md` (index); cite FR/BR/AC on every case; include at least one positive and one mandatory-negative case per in-scope write/validation rule; set `execution_layer` ∈ {api, IT, uat, integration, blocked}; classify BR Type with `br-tc-taxonomy.md` before negatives. When `ati-navigation-checklist.md` documents Back: include a designed case “return from child restores criteria” with steps on **visible fields** (see `.cursor/skills/ati-navigation-state/SKILL.md`). When `audit-ui-layout-checklist.md` applies: include RF 360×640 case — session filters/checkboxes visible **below** dates without horizontal scroll (see `.cursor/skills/audit-ui-layout/SKILL.md`). When `audit-ui-list-table-checklist.md` applies: include Vitest case for pagination label or row total (see `.cursor/skills/shared-ui-molecules/SKILL.md` and UI module `src/test/paginationLabel.ts`). When `audit-ui-session-defaults-checklist.md` applies: include designed case **fresh entry — only cited defaults** (empty URL; assert visible fields match provenance table).
- Must NOT: invent steps or expected results; write JUnit/spec files; treat the matrix as sufficient without case bodies; write `docs/test-cases-br-coverage.md`; write a UAT catalog on default expand.

## Inputs
- AC, OpenAPI, and `evals.rule_source` (or the SP item’s cited FR/BR)

## Outputs
- `docs/test-cases.md`
- `docs/test-matrix.md` (status `designed` until scripts exist)
- Confidence: high | medium | low
- Unresolved assumptions list

## Failure modes
Incomplete input → partial result with explicit gaps. br-coverage-validator scores coverage; this subagent must not write `docs/test-cases-br-coverage.md`.

## Quality gate
Each in-scope FR/BR ≥1 case or an explicit gap row. Case file uses `test-cases-template.md`.

## Ask-backs
Register Open DQ for parent if unclear. Do not invent. Do not STOP the parent except human gates.

## Loop
Plan → Act → Validate → Refine (max 3 ceiling). Iteration 1 Validate pass → stop. Refine only from findings.

# /review-changes

## Objective

Code, test, and security review of the slice. Read-only review agent. Does **not** run eval-agent — that is `/run-eval`.

**On amount-math error / conflict:** `artifacts/calculations/<module>-calculations.json` is the **primary source**. Flag code/tests that diverge from `CALC-*` as defects; do not treat modern impl as winning.

## When to Use

- After `/run-eval` (delivery step 5) in the seven-step path
- Before PR or sprint closeout
- After `/deliver-sprint` (mandatory closeout step)

## Runtime Inputs

### Required

- diff scope
- test evidence (`artifacts/test-results-*.md`)
- `evals/results/<date>-completeness-ledger.md` from `/run-eval` (or `evals/product/results/` per config)
- Open `auto_fix: true` items closed or `needs_human` (walk `/close-eval-gaps` via `/run-eval` first)

### Optional

- `docs/test-matrix.md`
- `evals/STATUS.md`
- `artifacts/sprints/<id>-provenance.md`
- `artifacts/calculations/<module>-calculations.json` (amount math — rule 32)
- `governed_plan` / `sprint_governed_plan`

## Plan-as-input mode

When `governed_plan` or `sprint_governed_plan` is supplied, apply `.cursor/templates/plan-as-input-protocol.md` before Clarification Protocol.

## Orchestration

orchestrator-agent → **code-review-agent** + **security-agent**

## Delegate to Subagents

security-reviewer, coverage-analyzer, br-coverage-validator, audit-trail-writer

Do **not** invoke eval-agent.

## Enforce Rules

02, 03, 04, 07, 09, 10, 15, 21, 22, 23, 32

## Use Templates

review-findings-template.md, code-review-template.md, audit-trail-template.md, plan-act-validate-refine-loop.md

## Required Output

- `artifacts/audit/<date>-review-changes.md`

## Command-Specific Hardening

Do not merge into quality-agent. Do not invoke eval-agent. Remaining P0/P1 safety items on the latest completeness ledger force Block. Family with zero cases on that ledger is Block, not Approve. Missing completeness ledger → **refuse**; advise `/run-eval` first. Missing or FAIL `tools/templates/render_check.py` when Java API/tests changed (or `artifacts/templates/*-vars.yaml` exists) is Block.

## Execution Policy

Loop: Plan → Act → Validate → Refine. Max 3 is a ceiling. Iteration 1 Validate pass → stop. Refine only from findings. Escalate with owners after iteration 3.

## Clarification Protocol

After resolve-before-ask, register Open DQ when the completeness ledger is missing. Do not invent eval results. Still refuse when `/run-eval` has not run. Do not STOP for a clarification round except human gates (BRD Accepted, plan Approved, `/fix-issue` defect proceed, test-package Approved).

## Definition of Done

- [ ] Severity findings with file evidence
- [ ] If a UI grid changed: flag a new Option column (selectable-row pattern required). This is a review label on the existing review, not a new command.
- [ ] On the same review, label any of these when the diff shows them: contract alias, nav/bypass, seed clobber, missing IT restore, screen test without the themed render helper, shared O/S profile filter missing. Do not open a second review command.
- [ ] P0/P1 remaining → Block
- [ ] reviewer did not mutate source
- [ ] `docs/test-cases-br-coverage.md` Status **PASS** or Blocking gaps listed (implementers must not stamp PASS). Cases + matrix required
- [ ] Completeness ledger from `/run-eval` present; always-on safety families present
- [ ] Java `render_check.py` PASS when API/tests changed or vars YAML exists
- [ ] Amount tests align with calculation catalog when math in scope (rule 32)
- [ ] `artifacts/audit/<date>-review-changes.md` written
- [ ] Harness updated (phase-changing)

## Next command

```
✅ /review-changes complete.
**Next command:** /validate-kpi-metrics for 12-KPI scorecards, then /application-summary if the review is Approve; otherwise owning agents fix, then re-run /review-changes. If auto_fix items opened during review, walk /run-eval (inner /close-eval-gaps) first. Optional evidence packs: /sast-scan, /dast-scan, /threat-profile, /performance-test.
```

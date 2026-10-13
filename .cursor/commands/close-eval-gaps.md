# /close-eval-gaps

## Objective

Walk the fix queue from `/run-eval`: skip human-gated items, delegate `auto_fix: true` to owning agents, re-run those `case_path`s, log every hop. Inner loop for `/run-eval` only — **not** for `/expand-test-coverage`.

**Not for `full-sweep` failures.** Those must go through `/fix-issue`'s governed path (fix plan + human `proceed`), per `.cursor/rules/12-production-fix-governance.mdc`.

## When to Use

- After `/run-eval` when `evals/results/<date>-fix-queue.json` (or `evals/product/results/` per config) lists open `auto_fix: true` items
- Mid-sprint when sprint-scope FR/BR eval cases are red but work is still in-flight
- **Do not use** for failures found by `/review-changes` full-sweep — use `/fix-issue` instead

## Orchestration

| Phase | Role | Agent |
|-------|------|-------|
| 1 Read queue | Primary | orchestrator-agent |
| 2 Classify | — | skip `waived` / `needs_human` (`auto_fix: false`) |
| 3 Fix | Owning agent | backend / frontend / api-agent / quality / documentation / eval-agent |
| 4 Re-eval | Subagent | eval-agent (those `case_path`s only) |
| 5 Log | Orchestrator | fix-queue status + fix-log + audits |

Max **3** attempts per item (`execution.max_iterations`) — ceiling. Green after attempt 1 → `closed`; do not burn remaining attempts. Stay in `scope.yaml`.

## Runtime Inputs

### Required

- `evals/results/<date>-fix-queue.json` (primary for this repo) or `evals/product/results/<date>-fix-queue.json` per config

## Enforce Rules

09, 10, 07, 12

## Use Templates

`eval-fix-log-template.md`, `eval-results-template.md`, `audit-trail-template.md`

## Use Skills

`.cursor/skills/eval-suite/SKILL.md`

## Clarification Protocol

After resolve-before-ask, register Open DQ when the fix-queue file is missing. Do not invent queue items. Human-gate items stay listed, not dropped. Do not STOP for a clarification round except human gates. Still refuse when the queue file is absent.

## Definition of Done

- [ ] Every `auto_fix: true` item is `closed` or `needs_human` (attempt 3)
- [ ] Queue and fix-log updated under `evals/results/`
- [ ] Per-agent `artifacts/audit/<date>-<agent>-eval-fix-<item>.md`
- [ ] Orchestrator audit written
- [ ] Human-gate items listed, not dropped
- [ ] Full module regression pass (`mvn test` / `npm test`) after code changes when product code changed

## Outputs

- Code/test fixes under `config.yaml` → `modules.api` and `modules.ui` (see also `evals.owning_paths`) when applicable
- Updated `evals/STATUS.md`
- `artifacts/audit/<date>-close-eval-gaps.md`

## Next command

```
✅ /close-eval-gaps complete.
**Next command:** resume parent /run-eval. If items are needs_human, wait, then re-run /close-eval-gaps. When queue is clear, proceed to /review-changes.
```

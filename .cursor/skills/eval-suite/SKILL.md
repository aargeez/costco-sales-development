---
name: eval-suite
description: >-
 Generate and run BRD-driven evals under evals/, score requirement coverage, and route failures
 through the correct channel for this repo's governance model (direct re-delegation for
 in-sprint gaps, /fix-issue's governed path for already-shipped gaps). Use when running
 /run-eval (incremental or full-sweep), or when adding a new eval type/case by hand.
---

# Eval Suite Skill

## Read First

- `evals/README.md` — folder layout, three-file case format, status legend
- `evals/STATUS.md` — current coverage: EXISTS / IMPROVED / NEW per eval type and per BRD rule
- `docs/test-matrix.md` — this project's existing FR/BR → test mapping; check here before
  assuming a rule is uncovered
- `.cursor/config.yaml` → `evals:` — the only block that changes when porting this to another repo

## Generate a Case for an Uncovered Rule

1. Find the rule in `.cursor/config.yaml` → `evals.rule_source` (and `evals.module_brds_glob` when set). Do not hardcode a BRD filename.
2. Check `docs/test-matrix.md` and `evals/STATUS.md` — if an existing test already asserts it,
   write a **pointer case** (`prompt.md` + `GRADING.md`, status `EXISTS`) rather than duplicating.
3. If genuinely uncovered, write the full three-file case
   (`evals/templates/case.three-file.README.md`): `prompt.md` (numbered requirements),
   `reference_solution.*` (style only), `reference.test.*` (the verdict).

## Run

Reuse the existing suites — never invent a new runner. Use `.cursor/config.yaml` → `evals.test_commands` (`api`, `ui`, `e2e`). Do not hardcode repository folder names. If a note in the product repo says unit and IT share `mvn test`, follow that product’s `evals.test_commands.api` as written.

## Sanity vs. Verdict (do not conflate)

- **Sanity failure** — the case's own grader is broken. Fix it yourself; not a product signal.
- **Verdict failure** — the grader ran cleanly and the generated code didn't satisfy it. Only
  this goes into the failure report.

## Failure Report → Routing (mode-dependent — the key difference from a simpler port)

Write `evals/results/<date>-<mode>.md` + `.json` (`.cursor/templates/eval-results-template.md`
has the schema). What happens next depends on **which mode found the failure**:

- **`incremental`** (found via `/run-eval`, in-flight sprint work): the `.json` is forwarded to
  orchestrator-agent, which re-delegates to backend-agent/frontend-agent using its existing
  "re-delegate with feedback (max 3 iterations)" rule — no new plumbing. `/expand-test-coverage`
  does **not** run eval-agent.
- **`full-sweep`** (found via `/run-eval` full-sweep mode, more likely already-shipped code): the failure
  is translated into `/fix-issue`'s input format (see `eval-agent.md` → "Mapping an eval verdict
  failure → /fix-issue input") and run through that command's **full governance path** —
  classification, root-cause + fix-plan analysis, and a **hard stop for human `proceed`** before
  any code changes, per `.cursor/rules/12-production-fix-governance.mdc`. Never skip this gate
  just because the fix looks small.

## Modes

| Mode | When | Scope | Failure routing |
|------|------|-------|------------------|
| `incremental` | `/run-eval` | sprint-scope FR/BR rules + always-on safety families | direct re-delegation via `/close-eval-gaps` |
| `full-sweep` | `/run-eval` | entire BRD + `L4-*` gate blocks + `LX-01` judge check | `/fix-issue` governed path |

## Completeness (enterprise bar)

1. Extract every `rule_id` from `evals.rule_source`.
2. Incremental = sprint_scope **union** always-on safety families (L1-04, L1-05, L1-08, L1-09, L4-03) **union** agent-result checks.
3. Write completeness ledger (`evals/results/<date>-completeness-ledger.md` or product layout per config). Silence is a finding.
4. `/review-changes` requires a `/run-eval` completeness ledger.

## Gates

- P1/safety rules must be 100% green or explicitly escalated — never silently skipped.
- Coverage threshold reuses `.cursor/config.yaml` → `quality_gates.api_unit_line_coverage_min`.
- `full-sweep` failures are never auto-implemented — see Routing above.

## Subagent

`.cursor/subagents/quality/eval-agent.md`

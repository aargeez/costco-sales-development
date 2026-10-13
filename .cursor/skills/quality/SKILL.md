---
name: quality-coordination
description: Coordinate specialists; never lower gates.
---

# Quality coordination

quality-agent coordinates `/expand-test-coverage` (design + gate + Pending Approval), `/run-tests` (scripts + execute after Approved), and `/run-eval` (eval-agent + `/close-eval-gaps`). test-agent writes `docs/test-cases.md` + matrix (design on expand; scripts/run on `/run-tests`; `docs/bdd-test-cases.md` when e2e/journey via e2e-scenario-writer). br-coverage-validator is the only writer of `docs/test-cases-br-coverage.md`. Parent rejects a run that omits always-on safety families, treats silence as pass, scripts before coverage PASS, or runs `/run-tests` before package Approved. quality-agent mode `kpi` owns `/validate-kpi-metrics`. Do not merge review or security into quality-agent. Do not self-approve test packages.

**Amount-math:** When cases assert amounts, expected values come from `artifacts/calculations/<module>-calculations.json` (`CALC-*` / `golden_refs`) — catalog JSON is primary on error (rule 32).

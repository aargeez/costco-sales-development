# Eval Results — {{MODE}} ({{incremental | full-sweep}})

**Date:** {{YYYY-MM-DD}}
**Iteration:** {{n}} of 3
**Triggered by:** {{/run-eval}}
**Sprint scope (incremental only):** {{FR/BR ids touched}}

## Summary

| Eval type | Cases run | Passed | Verdict-failed | Sanity-failed (eval-agent fixed) | New cases created |
|-----------|----------:|-------:|----------------:|-----------------------------------:|-------------------:|
| L1-10 requirement completeness | | | | | |
| (…other Wave-1 types run this pass) | | | | | |

**BRD rule coverage:** {{covered}}/145 ({{pct}}%) — P1/safety rules: {{n}}/{{n}} ✅|⚠️

---

## Verdict Failures (the real signal)

| Case ID | Rule ID | Layer | Routing (mode-dependent) | Expected | Actual |
|---------|---------|-------|--------------------------|----------|--------|
| | | | `incremental`→backend-agent/frontend-agent direct, or `full-sweep`→`/fix-issue` governed path | | |

## Sanity Failures (eval-agent's own bug — fixed in this pass, not reported as product gaps)

| Case ID | Problem | Fix applied |
|---------|---------|-------------|
| | | |

---

## Machine-Readable Payload

Also written to `evals/results/{{date}}-{{mode}}.json`:

```json
[
  {
    "case_id": "example-rule-case",
    "rule_id": "FR-000",
    "layer": "L1-10",
    "status": "verdict_failed",
    "priority": "must",
    "p1_safety": false,
    "mode": "incremental",
    "owning_agent": "backend-agent",
    "expected": "...",
    "actual": "...",
    "grader_cmd": "<evals.test_commands.api> -Dtest=ExampleIT#exampleCase",
    "case_path": "evals/wave-1/L1-10-requirement-completeness/cases/example-rule-case/"
  }
]
```

**For `incremental` mode:** this `.json` is the payload quality-agent forwards to
orchestrator-agent for direct re-delegation (max 3 iterations), same mechanism as any other
subagent quality-gate failure.

**For `full-sweep` mode:** this record is translated into `/fix-issue`'s input fields (see
`.cursor/subagents/quality/eval-agent.md` → "Mapping an eval verdict failure → /fix-issue input")
and becomes the Phase 0 classification input for that command — **not** an automatic fix. The
governed path (`/fix-issue` → fix plan → human `proceed` → implement) applies in full; the actual
fix record lives in `artifacts/issues/<date>-<slug>.md`, not here.

---

## Escalations (iteration 3 reached, still red — `incremental` mode only)

| Rule ID | Attempts | Notes |
|---------|---------:|-------|

**Output path:** `evals/results/{{date}}-{{mode}}.md` (+ matching `.json`)

---
name: kpi-validation
description: Deterministic 12-KPI toolchain scorecards; not heuristic Python.
---

# KPI validation skill

Use `/validate-kpi-metrics` with quality-agent mode `kpi`. Collectors must read toolchain evidence (JaCoCo, npm audit, eslint, SCA). LLM narrative is advisory only.

## Non-negotiables
- No heuristic Python scorers as a gate.
- No Green on KPI-02 without a coverage report path.
- No Green on KPI-09 with unwaived high/critical CVE.
- Mask secrets in artifacts.

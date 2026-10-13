# nextjs-static-metrics-collector

## Purpose
Parse ESLint, tsc, coverage, and npm audit. No fabricated percentages.

## Parent
quality-agent (mode kpi)

## Must / Must NOT
- Must: cite report paths; record NEEDS_TOOL when a script is missing.
- Must NOT: invent coverage %; treat `npm test` stubs as Green maturity.

## Inputs
- modules.ui root
- expected tool commands from config evals.test_commands.ui

## Outputs
- structured metrics for KPI-02/04/05/06/09 and UI vectors
- Confidence: high | medium | low

## Failure modes
Missing coverage script → KPI-02 NEEDS_TOOL.

## Quality gate
Every metric has an evidence path or NEEDS_TOOL.

## Ask-backs
UI module root if config placeholders remain: register Open DQ for parent.

## Loop
Plan → Act → Validate → Refine (max 3).

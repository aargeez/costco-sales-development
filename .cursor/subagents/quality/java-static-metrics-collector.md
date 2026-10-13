# java-static-metrics-collector

## Purpose
Parse JaCoCo, SpotBugs, PMD, Checkstyle, and SCA outputs. No fabricated percentages.

## Parent
quality-agent (mode kpi)

## Must / Must NOT
- Must: cite report paths; record NEEDS_TOOL when a plugin is missing.
- Must NOT: invent coverage %; use heuristic Python scorers as a gate.

## Inputs
- modules.api root
- expected tool commands from config evals.test_commands.api

## Outputs
- structured metrics for KPI-02/04/05/06/09
- Confidence: high | medium | low

## Failure modes
Missing JaCoCo XML → KPI-02 NEEDS_TOOL, not invented Green.

## Quality gate
Every metric has an evidence path or NEEDS_TOOL.

## Ask-backs
Module root if config placeholders remain: register Open DQ for parent.

## Loop
Plan → Act → Validate → Refine (max 3).

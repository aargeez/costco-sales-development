# kpi-threshold-gate

## Purpose
Deterministic GO / GO_WITH_CONDITIONS / NO_GO from the threshold policy. LLM cannot override Red on KPI-01, KPI-02, or KPI-09.

## Parent
quality-agent (mode kpi)

## Must / Must NOT
- Must: apply `.cursor/templates/kpi-threshold-policy-template.md` (or project policy).
- Must NOT: waive critical/high security or CVE without a recorded human waiver.

## Inputs
- evidence matrix
- threshold policy
- waivers (if any)

## Outputs
- verdict + blocker list with owners
- Confidence: high | medium | low

## Failure modes
Policy missing → use template defaults and flag ASSUMPTION pending approval.

## Quality gate
Verdict matches worst Red among KPI-01/02/09 unless waived.

## Ask-backs
Waiver approver if Red would be overridden: register Open DQ (human gate).

## Loop
Plan → Act → Validate → Refine (max 3).

# /validate-dr-readiness

## Objective
Validate disaster recovery readiness and business continuity assumptions.

## Runtime Inputs

### Required
- `system_or_scope_key`: service, region, or program under DR assessment.
- `rto_rpo_targets`: stated recovery time/point objectives to validate.

### Optional
- `last_drill_evidence`: paths to prior drill results or runbooks.
- `failover_architecture`: multi-region, backup, or restore topology docs.
- `remediation_owner`: role for open DR gaps.

## Use Agents
- sre-agent
- infra-agent

## Delegate to Subagents
- dr-readiness-checker
- release-readiness-checker

## Enforce Rules
- .cursor/rules/19-resilience-and-dr.mdc
- .cursor/rules/16-observability-sre.mdc

## Use Templates
- .cursor/templates/release-readiness-template.md
- .cursor/templates/infra-deployment-template.md

## Use Skills
- `.cursor/skills/ops/SKILL.md` — DR and continuity as part of production readiness.

## Required Output
- DR readiness status
- RTO/RPO validation evidence
- Remediation and retest plan

## Command-Specific Hardening
- **Do** tie RTO/RPO claims to test or drill evidence.
- **Do** document failover paths and data restore procedures.
- **Don't** accept untested RTO/RPO without explicit risk acceptance and owner.
- **Stop** when `rto_rpo_targets` are missing.

## Execution Policy
- Execution loop: Plan -> Act -> Evaluate -> Refine.
- Max iterations: 3 (escalate unresolved items after final iteration).
- Validation: **dr-readiness-checker** checks RTO/RPO claims, failover assumptions, runbook/test evidence, and remediation completeness after each Act step.
- Refinement: agent patches artifact in-place based on validation findings before next iteration.

## Clarification Protocol

**Medium–high gate:**

1. What is `system_or_scope_key`?
2. What are `rto_rpo_targets`?
3. What `last_drill_evidence` exists?

## Artifact Persistence
- Save final output to: `docs/dr/<system-or-scope-key>-dr-readiness.md`
- Include metadata header: timestamp, iteration count, validation summary, command version (`/validate-dr-readiness`).

## Definition of Done

- [ ] DR status, RTO/RPO evidence, and remediation/retest plan are complete for the validated scope.
- [ ] **dr-readiness-checker** reports no critical gaps in continuity scenarios, recovery paths, or evidence of last successful drill/test.
- [ ] **release-readiness-template.md** and **infra-deployment-template.md** (DR-relevant sections) are fully populated with no placeholder text remaining.
- [ ] Open DR gaps have `remediation_owner` and retest date.

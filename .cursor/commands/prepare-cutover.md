# /prepare-cutover

## Objective
Prepare phased cutover plan with operational guardrails and rollback confidence.

## Runtime Inputs

### Required
- `migration_or_release_id`: naming segment for cutover artifacts.
- `cutover_scope`: systems, data domains, and traffic shift strategy.
- `rollback_owner`: role or team accountable for rollback execution.

### Optional
- `migration_mapping_artifacts`: source-to-target matrices from data migration work.
- `canary_percentage_schedule`: phased traffic ramp if applicable.
- `maintenance_window`: planned cutover window and timezone.

## Use Agents
- release-agent (train mode)
- data-agent
- sre-agent
- release-agent

## Delegate to Subagents
- canary-rollout-planner
- migration-planner
- dr-readiness-checker
- release-readiness-checker

## Enforce Rules
- .cursor/rules/14-data-migration-integrity.mdc
- .cursor/rules/15-release-readiness.mdc
- .cursor/rules/18-release-and-change-management.mdc
- .cursor/rules/19-resilience-and-dr.mdc

## Use Templates
- .cursor/templates/cutover-plan-template.md
- .cursor/templates/migration-reconciliation-template.md
- .cursor/templates/release-readiness-template.md
- .cursor/templates/phase-gate-template.md

## Use Skills
- `.cursor/skills/migration/SKILL.md` — phased cutover sequencing and reconciliation guardrails.

## Required Output
- Phased cutover runbook
- Rollback triggers and procedures
- Phase gate checklist and owners

## Command-Specific Hardening
- **Do** define go/no-go gates with measurable criteria per phase.
- **Do** include data reconciliation checkpoints before traffic shift.
- **Don't** plan irreversible steps without documented rollback path.
- **Stop** when `rollback_owner` or reconciliation approach is undefined.

## Execution Policy
- Execution loop: Plan -> Act -> Evaluate -> Refine.
- Max iterations: 3 (escalate unresolved items after final iteration).
- Validation: **release-readiness-checker** checks cutover phases, rollback triggers, gate criteria, owner assignments, and migration/reconciliation alignment after each Act step.
- Refinement: agent patches artifact in-place based on validation findings before next iteration.

## Clarification Protocol

**Hard stop for production cutover:**

1. What is `cutover_scope` and traffic-shift model?
2. Who is `rollback_owner`?
3. What reconciliation evidence is required before each phase?
4. What is `maintenance_window`?

## Artifact Persistence
- Save final output to: `docs/cutover/<migration-or-release-id>-cutover-plan.md`
- Include metadata header: timestamp, iteration count, validation summary, command version (`/prepare-cutover`).

## Definition of Done

- [ ] Runbook, rollback procedures, and phase-gate checklist with owners are complete and sequenced.
- [ ] **release-readiness-checker** reports no critical gaps in go/no-go criteria, rollback confidence, or missing operational owners.
- [ ] **cutover-plan-template.md**, **migration-reconciliation-template.md**, **release-readiness-template.md**, and **phase-gate-template.md** are fully populated with no placeholder text remaining.
- [ ] Each phase has explicit rollback trigger and verification step.

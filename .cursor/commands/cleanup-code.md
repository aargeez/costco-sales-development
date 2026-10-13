# /cleanup-code

## Objective
Refactor code safely for readability, maintainability, and reduced duplication.

## Runtime Inputs

### Required
- `target_scope`: files, modules, or directories to analyze and refactor.
- `refactor_goals`: readability, deduplication, naming, structure, or performance-safe cleanup.

### Optional
- `risk_tolerance`: `low` (docs-only plan) | `medium` (small diffs) | `high` (requires explicit approval).
- `test_suite_command`: how to verify behavior preservation (e.g. `mvn test`, `pnpm test`).
- `artifact_namespace`: naming segment for the cleanup plan doc.
- `governed_plan` (optional): scope cleanup to paths from §Execution log for a story slice.

## Plan-as-input mode

When `governed_plan` is supplied, set `target_scope` from §Execution log artifact paths for completed codegen steps on the same story. Do not expand scope beyond plan slice without user approval.

## Use Agents
- code-cleanup-agent — [code-cleanup-agent.md](../agents/engineering/code-cleanup-agent.md) (**Primary**)
- code-review-agent

## Delegate to Subagents
- code-cleanup-analyzer
- test-case-generator

## Enforce Rules
- .cursor/rules/00-global-governance.mdc
- .cursor/rules/07-testing-and-quality.mdc
- .cursor/rules/02-security-and-compliance.mdc

## Use Templates
- .cursor/templates/code-review-template.md
- .cursor/templates/test-strategy-template.md

## Required Output
- Refactor plan and behavior safety checks
- Suggested diff groups by risk
- Required regression tests

## Command-Specific Hardening
- **Do** group changes by risk (behavioral vs cosmetic) with rollback notes per batch.
- **Do** require regression tests before merging high-risk refactors.
- **Don't** change public API contracts or security controls without explicit scope approval.
- **Stop** when `target_scope` is undefined or tests are unavailable for behavioral changes.

## Execution Policy
- Execution loop: Plan -> Act -> Evaluate -> Refine.
- Max iterations: 3 (escalate unresolved items after final iteration).
- Validation: `code-cleanup-analyzer` (`.cursor/subagents/quality/code-cleanup-analyzer.md`) checks refactor safety, risk-ranked diff grouping, and mandatory regression coverage against the Required Output after each Act step.
- Refinement: agent patches artifact in-place based on validation findings before next iteration.

## Clarification Protocol

**Medium gate:**

1. What is `target_scope` (paths/modules)?
2. What are `refactor_goals` and `risk_tolerance`?
3. What `test_suite_command` verifies behavior preservation?

For `risk_tolerance=low`, produce plan only unless user approves code changes.

## Artifact Persistence
- Save final output to: `docs/quality/cleanup/<repo-or-module>-cleanup-plan.md`
- Include metadata header: timestamp, iteration count, validation summary, command version (`/cleanup-code`).

## Definition of Done

- [ ] Refactor plan with behavior safety checks, risk-grouped diffs, and required regression tests are complete.
- [ ] `code-cleanup-analyzer` reports no critical behavior regressions without mitigation or high-risk batches without rollback/test notes.
- [ ] Code-review and test-strategy templates fully populated; no vague "refactor as needed" without scoped file lists.
- [ ] High-risk diffs not applied without documented test evidence or explicit user approval.

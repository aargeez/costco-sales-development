# /review-code

## Objective
Run severity-ranked code review for quality, security, and performance.

## Runtime Inputs

### Required
- `change_scope`: PR diff, branch, file list, or module paths under review.
- `review_context`: feature/slice id, linked stories, or risk class (API, auth, data, infra).

### Optional
- `contract_version` or `api_validation_reference`: when reviewing API changes.
- `performance_baseline`: SLO or latency budget for perf-profiler context.
- `artifact_namespace`: naming segment for review artifact (`<change-or-pr>`).
- `governed_plan` (optional): `.cursor/plans/sprint<N>/<story-id>-governed-implementation-plan.md`.
- `plan_step` (optional): row in §Command execution flow; default first `pending` row for `/review-code`.

## Plan-as-input mode

When `governed_plan` is supplied, resolve `change_scope` from §Execution log artifact paths for prior codegen steps, `review_context` from §Story pack story ID, and `contract_version` from §Workspace bindings. Update plan execution sections on completion.

## Use Agents
- code-review-agent
- security-agent
- performance-agent — performance review delegates to [performance-agent.md](../agents/sre/performance-agent.md)

## Delegate to Subagents
- code-cleanup-analyzer
- security-scanner
- perf-profiler

## Enforce Rules
- .cursor/rules/00-global-governance.mdc
- .cursor/rules/02-security-and-compliance.mdc
- .cursor/rules/07-testing-and-quality.mdc

## Use Templates
- .cursor/templates/code-review-template.md
- .cursor/templates/test-strategy-template.md

## Use Skills
- `.cursor/skills/api/SKILL.md` — when reviewing REST/GraphQL contract or implementation changes.

## Required Output
- Findings sorted by severity
- Regression and test gaps
- Required fixes and recommendation

## Command-Specific Hardening
- **Do** classify findings critical/high/medium/low with evidence (file, line, CWE where applicable).
- **Do** flag missing tests for changed behavior and security-sensitive paths.
- **Don't** approve changes with unmitigated secret/PII exposure or auth bypass patterns.
- **Stop** when `change_scope` is empty — request diff or file list.

## Execution Policy
- Execution loop: Plan -> Act -> Evaluate -> Refine.
- Max iterations: 3 (escalate unresolved items after final iteration).
- Validation: `security-scanner` (`.cursor/subagents/quality/security-scanner.md`) checks finding severity, OWASP-aligned security coverage, secret/PII exposure, and required fix clarity against the Required Output after each Act step.
- Refinement: agent patches artifact in-place based on validation findings before next iteration.

## Clarification Protocol

**Low–medium gate:**

1. What is `change_scope` (PR, branch, paths)?
2. What `review_context` and risk areas apply (auth, PII, API breaking changes)?
3. Are performance or API governance checks in scope?

## Artifact Persistence
- Save final output to: `docs/quality/reviews/<change-or-pr>-code-review.md`
- Include metadata header: timestamp, iteration count, validation summary, command version (`/review-code`).

## Definition of Done

- [ ] Severity-ordered findings, regression/test gaps, and required fixes with recommendations are complete.
- [ ] `security-scanner` reports no critical unclassified high-risk items or missing CWE/threat context where applicable.
- [ ] Code-review and test-strategy templates fully populated; no empty severity table rows or generic "looks fine" without evidence.
- [ ] All critical/high findings have actionable remediation or explicit waiver with owner.

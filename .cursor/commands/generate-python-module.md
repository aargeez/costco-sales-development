# /generate-python-module

## Objective
Generate production-grade Python implementation from approved requirements.

## Runtime Inputs

### Required
- `implementation_module_root` or `module_path`: target package/directory for generated Python code.
- `requirements_source`: tech spec, story paths, or capability description defining behavior.

### Optional
- `python_version`: target runtime (default from project `pyproject.toml` or engagement standard).
- `test_framework`: pytest default unless project specifies otherwise.
- `artifact_namespace`: documentation naming segment.
- `security_scan_policy`: whether to run `security-scanner` on dependencies and code patterns.
- `governed_plan` (optional): `.cursor/plans/sprint<N>/<story-id>-governed-implementation-plan.md`.
- `plan_step` (optional): row in §Command execution flow when Python is in scope for the slice.

## Plan-as-input mode

When `governed_plan` is supplied, resolve `requirements_source` from §Story pack, `implementation_module_root` from §Workspace bindings (or step Runtime bindings), and `artifact_namespace` from bindings. Update plan execution sections and manifest on completion.

## Use Agents
- python-developer-agent
- tech-spec-writer-agent
- test-results-logger-agent

## Delegate to Subagents
- python-code-generator
- test-case-generator
- security-scanner
- test-results-recorder

## Enforce Rules
- .cursor/rules/00-global-governance.mdc
- .cursor/rules/27-python-engineering.mdc
- .cursor/rules/02-security-and-compliance.mdc
- .cursor/rules/07-testing-and-quality.mdc
- .cursor/rules/17-test-results-logging.mdc

## Use Templates
- .cursor/templates/tech-spec-template.md
- .cursor/templates/test-strategy-template.md
- .cursor/templates/test-results-log-template.md

## Required Output
- Python modules with validation and errors
- Test scaffolds and dependencies
- Security and performance notes
- Test execution log with pass/fail evidence and failure diagnostics

## Command-Specific Hardening
- **Do** validate external inputs at module boundaries; use typed errors with stable codes.
- **Do** run tests and record evidence via `test-results-recorder` before declaring done.
- **Don't** hardcode secrets, tokens, or environment-specific paths in source.
- **Stop** when `requirements_source` lacks testable acceptance criteria — request clarification.

## Execution Policy
- Execution loop: Plan -> Act -> Evaluate -> Refine.
- Max iterations: 3 (escalate unresolved items after final iteration).
- Validation: `test-results-recorder` (`.cursor/subagents/quality/test-results-recorder.md`) checks that outputs include structured test evidence, failure diagnostics, and alignment to declared requirements against the Required Output after each Act step.
- Refinement: agent patches artifact in-place based on validation findings before next iteration.

## Clarification Protocol

**Medium gate:**

1. What is `implementation_module_root` / `module_path`?
2. What `requirements_source` defines scope and acceptance?
3. Are there existing package conventions (layout, logging, config) to follow?

## Artifact Persistence
- Save final output to: `docs/implementation/python/<module-or-slice>-python.md`
- Include metadata header: timestamp, iteration count, validation summary, command version (`/generate-python-module`).

## Definition of Done

- [ ] Module design, test scaffolds, security/performance notes, and test execution log with pass/fail evidence are present.
- [ ] `test-results-recorder` reports no critical missing run metadata, unexplained failures, or absent trace to acceptance criteria.
- [ ] Tech-spec, test-strategy, and test-results templates fully populated; no fake metrics or placeholder log sections.
- [ ] `security-scanner` reports no critical unresolved findings when scan is in scope.

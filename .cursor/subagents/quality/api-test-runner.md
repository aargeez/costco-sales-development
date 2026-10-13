# Subagent: api-test-runner

## Purpose

**Run** the API test suite, capture JaCoCo metrics, and write evidence artifacts. Do not invent or generate test scripts. Refuse to run if this command’s in-scope FR/BR still lack `docs/test-cases-br-coverage.md` Status **PASS**.

## Inputs

| Field | Description |
|-------|-------------|
| scope | What changed (validation rule, endpoint, service) |
| prior_audit | Optional — link to implement-api audit for context |
| run_integration | `true` if HTTP/persistence/schema changed (`-DrunIntegrationTests=true`) |

## Execution (Mandatory)

```bash
export JAVA_HOME=$(grep java_home_for_tests .cursor/config.yaml | awk '{print $2}')
# Use config.yaml → evals.test_commands.api (and modules.api)
# Example: cd <modules.api> && mvn test
# If IT scope: mvn verify -DrunIntegrationTests=true
# Then: cd <modules.api> && mvn jacoco:report
```

## Outputs

- Pass/fail counts from Surefire (and Failsafe if IT ran)
- JaCoCo line/branch/instruction percentages
- `artifacts/test-results-<date>.md` (API section populated)
- **confidence**, **unresolved_assumptions**

## Command Contract Alignment

**Parent commands:** `/implement-api`, `/expand-test-coverage`

**Rules:** `.cursor/rules/07-testing-and-quality.mdc`, `.cursor/rules/10-audit-trail.mdc`

**Templates:** `.cursor/templates/test-results-template.md`, `.cursor/templates/api-test-evidence-template.md` (when endpoint-level detail is requested), `.cursor/templates/audit-trail-template.md`

## Quality Gate

- All tests pass on Java 17
- New/changed code has corresponding unit tests
- Metrics recorded before declaring implement-api complete

## Skill

- `.cursor/skills/jacoco-coverage/SKILL.md`

## Audit

Record command output summary in parent audit **Validation** section.

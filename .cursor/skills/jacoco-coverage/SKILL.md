---
name: jacoco-coverage
description: >-
 Run JaCoCo coverage for the API module in config.yaml (modules.api), interpret
 branch/line gaps, and recommend unit tests. Use when improving API test coverage
 or reporting metrics.
---

# JaCoCo Coverage Skill

## Run (Java 17 required)

```bash
export JAVA_HOME from config.yaml → quality_gates.java_home_for_tests
# Then evals.test_commands.api plus jacoco:report, from modules.api
# Example: cd <modules.api> && mvn test jacoco:report
# Report: <modules.api>/target/site/jacoco/index.html
```

## Parse XML

`target/site/jacoco/jacoco.xml` under `modules.api` — bundle counters for LINE, BRANCH, INSTRUCTION

## Priority for Branch Gaps

Typical Sales Audit examples (only if those types exist in the active API module):

1. `ValidationRule` / module-specific validation-rule implementations (one test per branch)
2. Application services
3. JDBC repositories and query services
4. Controllers (`@WebMvcTest`) and integration tests

## Mockito Note

Use Java 17 — Java 26 breaks Mockito inline mocking of concrete classes.

## Gates

See `.cursor/config.yaml` → `quality_gates`

## Subagent

`.cursor/subagents/quality/coverage-analyzer.md`

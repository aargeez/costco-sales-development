# Subagents — Sales Master

Bounded specialists delegated by parent agents. Max 3 iterations each. Layout follows **`.cursor-audit`** categories plus Sales-Vault extensions.

**Amount-math error / conflict:** `artifacts/calculations/<module>-calculations.json` is the **primary source** (rule 32). Align expected values and generated SQL/Java to `CALC-*`; do not patch the catalog toward failing code.

## Intake

| Subagent | File | Task |
|----------|------|------|
| test-intake-normalizer | [intake/test-intake-normalizer.md](intake/test-intake-normalizer.md) | Untrusted intake scan + manifest |
| transcript-analyzer | [intake/transcript-analyzer.md](intake/transcript-analyzer.md) | Meeting → structured notes |
| supporting-doc-synthesizer | [intake/supporting-doc-synthesizer.md](intake/supporting-doc-synthesizer.md) | PDFs, xlsx, mockups → facts |
| brd-writer | [intake/brd-writer.md](intake/brd-writer.md) | Draft BRD (§1–17) |
| open-questions-cataloger | [intake/open-questions-cataloger.md](intake/open-questions-cataloger.md) | DQ-xxx / R-xxx register |

## Calculations

| Subagent | File | Task |
|----------|------|------|
| ibmi-calculation-extractor | [calculations/ibmi-calculation-extractor.md](calculations/ibmi-calculation-extractor.md) | IBMi → `<module>-calculations.json` |

## Planning

| Subagent | File | Task |
|----------|------|------|
| requirement-traceability-mapper | [planning/requirement-traceability-mapper.md](planning/requirement-traceability-mapper.md) | BRD vs code/test gaps |
| sprint-planner | [planning/sprint-planner.md](planning/sprint-planner.md) | Sprint backlog + commands |

## Engineering

| Subagent | File | Task |
|----------|------|------|
| validation-chain-generator | [engineering/validation-chain-generator.md](engineering/validation-chain-generator.md) | ValidationRule + tests |
| schema-migration-generator | [engineering/schema-migration-generator.md](engineering/schema-migration-generator.md) | Flyway SQL + JPA entities |
| spring-module-generator | [engineering/spring-module-generator.md](engineering/spring-module-generator.md) | Spring Boot slice from `java_master_templates/` |
| spring-test-generator | [engineering/spring-test-generator.md](engineering/spring-test-generator.md) | Structural JUnit from `java_test_templates/` (rule 23) |
| next-component-generator | [engineering/next-component-generator.md](engineering/next-component-generator.md) | RF UI components |
| mockup-to-component | [engineering/mockup-to-component.md](engineering/mockup-to-component.md) | PNG/Figma → Next.js |
| api-contract-designer | [engineering/api-contract-designer.md](engineering/api-contract-designer.md) | OpenAPI/GraphQL render |
| python-code-generator | [engineering/python-code-generator.md](engineering/python-code-generator.md) | Python module slices |

## Quality

| Subagent | File | Task |
|----------|------|------|
| br-coverage-validator | [quality/br-coverage-validator.md](quality/br-coverage-validator.md) | Only writer of `docs/test-cases-br-coverage.md` |
| automation-companion-author | [quality/automation-companion-author.md](quality/automation-companion-author.md) | Case bodies for expand |
| tc-traceability-mapper | [quality/tc-traceability-mapper.md](quality/tc-traceability-mapper.md) | `docs/test-matrix.md` index |
| tc-completeness-validator | [quality/tc-completeness-validator.md](quality/tc-completeness-validator.md) | Case completeness gate |
| tc-step-quality-validator | [quality/tc-step-quality-validator.md](quality/tc-step-quality-validator.md) | Step quality gate |
| playwright-codegen | [quality/playwright-codegen.md](quality/playwright-codegen.md) | E2E scripts after package Approved |
| playwright-self-healer | [quality/playwright-self-healer.md](quality/playwright-self-healer.md) | Locator heal suggestions (OODA) |
| e2e-defect-analyzer | [quality/e2e-defect-analyzer.md](quality/e2e-defect-analyzer.md) | E2E failure analysis |
| sast-scanner | [quality/sast-scanner.md](quality/sast-scanner.md) | SAST evidence |
| dast-scanner | [quality/dast-scanner.md](quality/dast-scanner.md) | DAST evidence |
| kpi-threshold-gate | [quality/kpi-threshold-gate.md](quality/kpi-threshold-gate.md) | KPI threshold gate |
| kpi-report-synthesizer | [quality/kpi-report-synthesizer.md](quality/kpi-report-synthesizer.md) | KPI report synthesis |
| java-static-metrics-collector | [quality/java-static-metrics-collector.md](quality/java-static-metrics-collector.md) | Java static KPI metrics |
| nextjs-static-metrics-collector | [quality/nextjs-static-metrics-collector.md](quality/nextjs-static-metrics-collector.md) | Next.js static KPI metrics |
| api-test-runner | [quality/api-test-runner.md](quality/api-test-runner.md) | Run `mvn test` + JaCoCo; write test-results |
| ui-test-runner | [quality/ui-test-runner.md](quality/ui-test-runner.md) | Run Vitest + Playwright; write E2E evidence |
| coverage-analyzer | [quality/coverage-analyzer.md](quality/coverage-analyzer.md) | JaCoCo/Vitest gaps |
| test-case-generator | [quality/test-case-generator.md](quality/test-case-generator.md) | Designed cases + matrix; scripts only after BR coverage PASS |
| e2e-scenario-writer | [quality/e2e-scenario-writer.md](quality/e2e-scenario-writer.md) | `docs/bdd-test-cases.md` + Playwright specs |
| security-reviewer | [quality/security-reviewer.md](quality/security-reviewer.md) | OWASP / secrets / input review |
| eval-agent | [quality/eval-agent.md](quality/eval-agent.md) | BRD eval cases — generate, run, route failures |
| rest-api-endpoint-tester | [quality/rest-api-endpoint-tester.md](quality/rest-api-endpoint-tester.md) | Live REST validation evidence |
| graphql-api-endpoint-tester | [quality/graphql-api-endpoint-tester.md](quality/graphql-api-endpoint-tester.md) | GraphQL validation (when in scope) |
| test-results-recorder | [quality/test-results-recorder.md](quality/test-results-recorder.md) | Structured API validation logs |
| release-readiness-checker | [quality/release-readiness-checker.md](quality/release-readiness-checker.md) | Infra/release gate checks |
| code-cleanup-analyzer | [quality/code-cleanup-analyzer.md](quality/code-cleanup-analyzer.md) | Refactor safety analysis |
| security-scanner | [quality/security-scanner.md](quality/security-scanner.md) | SAST-style code review |
| perf-profiler | [quality/perf-profiler.md](quality/perf-profiler.md) | Performance profiling evidence |

## Docs

| Subagent | File | Task |
|----------|------|------|
| application-summary-writer | [docs/application-summary-writer.md](docs/application-summary-writer.md) | Summary HTML/PDF |
| doc-structure-planner | [docs/doc-structure-planner.md](docs/doc-structure-planner.md) | Doc pack structure |
| pr-summary-writer | [docs/pr-summary-writer.md](docs/pr-summary-writer.md) | PR summary packs |

## Data (Vault)

| Subagent | File | Task |
|----------|------|------|
| schema-mapper | [data/schema-mapper.md](data/schema-mapper.md) | DDL ↔ API ↔ UI mapping |
| data-cleaner | [data/data-cleaner.md](data/data-cleaner.md) | Data cleansing |
| migration-planner | [data/migration-planner.md](data/migration-planner.md) | Migration planning |

## Business (Vault)

| Subagent | File | Task |
|----------|------|------|
| gap-identifier | [business/gap-identifier.md](business/gap-identifier.md) | Story/UX/API/data gap analysis |
| gherkin-writer | [business/gherkin-writer.md](business/gherkin-writer.md) | Gherkin scenarios |
| invest-validator | [business/invest-validator.md](business/invest-validator.md) | INVEST story validation |

## Reverse (Vault)

| Subagent | File | Task |
|----------|------|------|
| flow-simplifier | [reverse/flow-simplifier.md](reverse/flow-simplifier.md) | Process flow simplification |
| code-analyzer | [reverse/code-analyzer.md](reverse/code-analyzer.md) | Legacy code analysis |
| rule-extractor | [reverse/rule-extractor.md](reverse/rule-extractor.md) | Business rule extraction |

## Figma (Vault)

| Subagent | File | Task |
|----------|------|------|
| figma-design-extractor | [figma/figma-design-extractor.md](figma/figma-design-extractor.md) | Figma → design tokens |
| theme-builder | [figma/theme-builder.md](figma/theme-builder.md) | MUI theme from tokens |
| mui-component-generator | [figma/mui-component-generator.md](figma/mui-component-generator.md) | MUI components |
| page-assembler | [figma/page-assembler.md](figma/page-assembler.md) | Page assembly |
| project-validator | [figma/project-validator.md](figma/project-validator.md) | DS project validation |
| refinement-agent | [figma/refinement-agent.md](figma/refinement-agent.md) | Design refinement loop |

## Specs (Vault)

| Subagent | File | Task |
|----------|------|------|
| nfr-checklist-builder | [specs/nfr-checklist-builder.md](specs/nfr-checklist-builder.md) | NFR checklist |
| tech-spec-formatter | [specs/tech-spec-formatter.md](specs/tech-spec-formatter.md) | Tech spec formatting |

## Ops (Vault + Master)

| Subagent | File | Task |
|----------|------|------|
| harness-updater | [ops/harness-updater.md](ops/harness-updater.md) | Only writer of `docs/harness/**` |
| audit-trail-writer | [ops/audit-trail-writer.md](ops/audit-trail-writer.md) | Audit trail artifacts |
| load-test-runner | [ops/load-test-runner.md](ops/load-test-runner.md) | Load/perf test execution |
| incident-runbook-writer | [ops/incident-runbook-writer.md](ops/incident-runbook-writer.md) | Incident runbooks |
| slo-definition-writer | [ops/slo-definition-writer.md](ops/slo-definition-writer.md) | SLO/SLI definitions |
| dr-readiness-checker | [ops/dr-readiness-checker.md](ops/dr-readiness-checker.md) | DR readiness |

## Compliance (Vault)

| Subagent | File | Task |
|----------|------|------|
| pii-control-checker | [compliance/pii-control-checker.md](compliance/pii-control-checker.md) | PII control validation |

## Release (Vault)

| Subagent | File | Task |
|----------|------|------|
| canary-rollout-planner | [release/canary-rollout-planner.md](release/canary-rollout-planner.md) | Canary rollout planning |

## Security (Master port)

| Subagent | File | Task |
|----------|------|------|
| threat-profiler | [security/threat-profiler.md](security/threat-profiler.md) | Threat profile authoring |
| threat-hunter | [security/threat-hunter.md](security/threat-hunter.md) | Threat hunt evidence |

## I/O Contract

See [../templates/subagent-io-template.md](../templates/subagent-io-template.md).

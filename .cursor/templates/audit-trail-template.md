# Agent Audit — {{COMMAND}} — {{TASK}}

> Harness-style record. Aligns with metadata in `docs/harness/progress.md`.

| Field | Value |
|-------|-------|
| **Date** | {{YYYY-MM-DD}} |
| **Command / Agent** | {{/implement-api or backend-agent}} |
| **Iteration** | {{n}} of 3 |
| **Validation** | {{unit / IT / E2E / JaCoCo summary}} |

## What

- Files created or modified (paths)

## Why

- User request, BRD section, or `docs/feature.md` reference

## How

- Rules, templates, skills used
- Commands executed

## Validation

| Check | Result | Evidence file |
|-------|--------|---------------|
| API unit | | `artifacts/test-results-*.md` |
| Integration | | |
| UI unit / E2E | | `artifacts/e2e-test-results-*.md` |
| JaCoCo | | |

## Behavior Provenance

| behavior_id | statement | source | confidence |
|-------------|-----------|--------|------------|

Pending assumptions: (ASSUMPTION-xxx or none)

## Open Items

- (Escalations, blockers)

**Output path:** `artifacts/audit/{{date}}-{{command}}-{{task}}.md`

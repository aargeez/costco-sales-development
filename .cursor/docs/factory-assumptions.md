# Factory assumptions (portable with `.cursor`)

Canonical IDs referenced in rules and commands. Product repos may mirror entries in `docs/harness/decisions.md`.

## ASSUMPTION-PCC-002 — Pipeline coverage coordination

| Field | Value |
|-------|--------|
| **ID** | ASSUMPTION-PCC-002 |
| **Status** | Accepted (factory default) |
| **Statement** | Full-sprint **`/expand-test-coverage`** PASS and test-package **Approved** do **not** block **`/implement-api`**, **`/implement-ui`**, or slice tests on **`/fix-issue`**. Each implement/fix slice still runs slice-level test design, **br-coverage-validator** for that slice, scripts, and runs as defined in those commands. Remaining FR/BR gap **design** stays on expand; gap **scripts and suite execution** stay on **`/run-tests`** after package **Approved**. |
| **Rationale** | Seven-step spine separates expand (design + gate) from run-tests (execute). Sprint items must not wait for sprint-wide expand PASS before vertical slice delivery. |
| **Does not waive** | `/run-tests` still requires expand PASS + human Approved; orchestrator still refuses gap script runs without br-coverage-validator PASS on the expand package. |
| **References** | `.cursor/rules/07-testing-and-quality.mdc`, `.cursor/rules/23-test-generation.mdc`, `commands/implement-api.md`, `commands/implement-ui.md`, `commands/expand-test-coverage.md`, `agents/orchestrator-agent.md` |

**PCC** = pipeline coverage coordination (two-track testing: slice on implement-* vs sprint gap on expand → run-tests).

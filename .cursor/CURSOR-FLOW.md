# Cursor factory — operator map

Quick index for humans and agents running the **Sales master** `.cursor` factory in this repo. Authoritative command contracts live under [commands/](commands/); the orchestrator enforces gates in [agents/orchestrator-agent.md](agents/orchestrator-agent.md).

## Start here

| Need | Document |
|------|----------|
| Intake → plan → deliver (full narrative) | [PIPELINE.md](PIPELINE.md) |
| Manual vs `/deliver-sprint` batch | [EXECUTION-OPTIONS.md](EXECUTION-OPTIONS.md) |
| Command list + next-command hints | [commands/README.md](commands/README.md) |
| Agent catalog | [AGENTS-SALES.md](AGENTS-SALES.md) · [agents/README.md](agents/README.md) · [README.md](README.md) |
| Module paths, artifacts, quality gates | [config.yaml](config.yaml) |

## Seven delivery steps (after sprint **Approved**)

Run **Option A** one slash command at a time, or **Option B** `/deliver-sprint` (same steps, batched).

```
1. /implement-api
2. /implement-ui
3. /expand-test-coverage  →  🛑 Human: docs/quality/test-package-review.md Approved
4. /run-tests
5. /run-eval  (optional: /close-eval-gaps if fix-queue open)
6. /review-changes
7. /application-summary
```

Skip `/implement-api` or `/implement-ui` only when the Approved plan marks that layer N/A (audit the skip).

Batch: `/deliver-sprint` runs the same seven steps; **STOP** after step 3 (Pending Approval) until test-package **Approved**.

## Human gates (hard stop)

| After | Gate |
|-------|------|
| `/generate-brd` | Domain BRD review → Accepted |
| `/plan-sprint` | Sprint plan **Approved** |
| `/expand-test-coverage` | Test-package **Approved** before `/run-tests` |
| `/fix-issue` defect | Human **proceed** on fix plan before implement |

Details: [rules/11-intake-planning-governance.mdc](rules/11-intake-planning-governance.mdc)

## Next command (cheat sheet)

| Completed | Next |
|-----------|------|
| `/generate-brd` | `/plan-sprint` |
| `/plan-sprint` | `/implement-api` or `/deliver-sprint` |
| `/implement-api` | `/implement-ui` or `/expand-test-coverage` |
| `/implement-ui` | `/expand-test-coverage` |
| `/expand-test-coverage` | Approve package → `/run-tests` |
| `/run-tests` | `/run-eval` |
| `/run-eval` | `/review-changes` or `/close-eval-gaps` |
| `/close-eval-gaps` | `/review-changes` when green |
| `/review-changes` | `/application-summary` |
| `/application-summary` | `/pr-assist` (optional) |

Full table: [EXECUTION-OPTIONS.md](EXECUTION-OPTIONS.md#next-command-cheat-sheet)

## Outside the seven-step delivery path

- **`/fix-issue`** — defect (proceed gate) or cited-requirements (change plan first)
- **`/extract-calculations`** — ATI / amount modules before or with intake
- **`/validate-kpi-metrics`**, **`/pr-assist`**, Vault platform commands — see [commands/README.md](commands/README.md)

## Amount math

On error or conflict: `artifacts/calculations/<module>-calculations.json` is **primary** (rule 32). Refresh via `/extract-calculations`.

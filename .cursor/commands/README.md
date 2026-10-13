# Commands

Each command → **orchestrator-agent** → primary agent → subagents.

## Amount-math errors (all commands)

If amounts, formulas, or related tests fail, or BRD / BRE / Java / SQL / UI disagree: **`artifacts/calculations/<module>-calculations.json` is the primary source** (rule 32). Align to `CALC-*`. Do not rewrite the catalog to match broken modern code. If the catalog is missing/stale/wrong → `/extract-calculations` (IBMi extract source).

## Intake → Plan

| Command | Primary Agent | Human gate after |
|---------|---------------|------------------|
| [/extract-calculations](extract-calculations.md) | calculation-catalog-agent | SME on blocking ASSUMPTION/CONFLICT |
| [/generate-brd](generate-brd.md) | requirements-agent | Domain BRD review |
| [/plan-sprint](plan-sprint.md) | planner-agent | Sprint approval |

## Execute — two options (same seven steps)

After sprint approval, pick **one**:

| Option | You run | Equivalent to |
|--------|---------|---------------|
| **A — Manual** | **Seven steps individually** — each `/` command below, in order | Full control, step-by-step |
| **B — Batch** | [/deliver-sprint](deliver-sprint.md) once | Same seven steps, orchestrated; STOP after step 3 until Approved |

**Guide:** [../EXECUTION-OPTIONS.md](../EXECUTION-OPTIONS.md) · [../PIPELINE.md](../PIPELINE.md)

### Seven delivery steps (slash commands in order)

Each command includes a **§ Next command** section — agents **must** tell the user what to run next when the command completes.

| Step | Command | Typical next |
|------|---------|--------------|
| 1 | [/implement-api](implement-api.md) | `/implement-ui` or step 3 |
| 2 | [/implement-ui](implement-ui.md) | step 3 |
| 3 | [/expand-test-coverage](expand-test-coverage.md) | Human **Approved** → step 4 |
| 4 | [/run-tests](run-tests.md) | `/run-eval` |
| 5 | [/run-eval](run-eval.md) | `/review-changes` (or [/close-eval-gaps](close-eval-gaps.md) when fix-queue open) |
| 6 | [/review-changes](review-changes.md) | `/application-summary` |
| 7 | [/application-summary](application-summary.md) | `/pr-assist` (optional) |

| Intake / plan | Next after complete |
|---------------|---------------------|
| [/extract-calculations](extract-calculations.md) | `/generate-brd` or `/plan-sprint` / `/implement-api` |
| [/generate-brd](generate-brd.md) | `/plan-sprint` (after BRD review) |
| [/plan-sprint](plan-sprint.md) | `/implement-api` or `/deliver-sprint` |
| [/deliver-sprint](deliver-sprint.md) | `/pr-assist` (optional) |

Pipeline: [../PIPELINE.md](../PIPELINE.md)

Template catalog: [../templates/README.md](../templates/README.md)

## Other

| Command | Primary Agent |
|---------|---------------|
| [/fix-issue](fix-issue.md) | **defect:** orchestrator → **backend-agent** / **frontend-agent** (fix mode). **cited-requirements:** **change-agent** |
| [/close-eval-gaps](close-eval-gaps.md) | orchestrator → backend-agent / frontend-agent + eval-agent |
| [/pr-assist](pr-assist.md) | documentation-agent |

### Outside the seven-step path — `/fix-issue` (two modes)

[/fix-issue](fix-issue.md) covers both ad-hoc defects and cited incremental diffs. It does not insert a step into the execute spine above. Prefer `/implement-api` / `/implement-ui` for full sprint vertical slices.

| Mode | When | Gate |
|------|------|------|
| **defect** | Ad-hoc bug, gap, or enhancement **not** on an approved sprint plan. Full-sweep eval verdict failures from `/run-eval` also route here. | Fix plan, then human **proceed** (`.cursor/rules/12-production-fix-governance.mdc`) |
| **cited-requirements** | Cited BRD / story / `docs/feature.md` / `CALC-*` and **minimal diffs only** (no out-of-scope edits, no invented behavior, preserve structure, change plan + provenance) | Change plan before code; human confirm only if scope or catalog is ambiguous |

Seed changes, diagnose notes, and cross-module ports stay on this command. They do not get a new command.

| Work | When a BR, story, or CALC id is cited | When it is an ad-hoc bug or gap |
|------|--------------------------------------|----------------------------------|
| Seed or Flyway data change | `/fix-issue` cited-requirements (seed collision note on the existing schema step) | `/fix-issue` defect |
| Diagnose (trace only) | `/fix-issue` cited-requirements if the outcome is a cited diff | `/fix-issue` defect Phase 1 (no code until **proceed**) |
| Port a pattern from another ATI | `/fix-issue` cited-requirements | `/fix-issue` defect |

## Vault & platform commands (public spine)

| Command | Primary Agent |
|---------|---------------|
| [/figma-to-nextjs](figma-to-nextjs.md) | figma-design-system-agent |
| [/ingest-figma-sketch](ingest-figma-sketch.md) | frontend-agent / whiteboard skill |
| [/generate-user-story](generate-user-story.md) | business-analyst-agent |
| [/plan-governed-stories](plan-governed-stories.md) | governed-story-planner-agent |
| [/plan-bounded-context](plan-bounded-context.md) | planner-agent (bounded-context mode) |
| [/compliance-check](compliance-check.md) | compliance-agent |
| [/prepare-cutover](prepare-cutover.md) | data-agent |
| [/validate-api-endpoints](validate-api-endpoints.md) | api-endpoint-validation-agent |
| [/validate-dr-readiness](validate-dr-readiness.md) | sre-agent |
| [/validate-kpi-metrics](validate-kpi-metrics.md) | quality-agent |
| [/generate-infra](generate-infra.md) | infra-agent |
| [/generate-github-workflow](generate-github-workflow.md) | github-workflow-agent |
| [/generate-docs](generate-docs.md) | documentation-agent |
| [/generate-runbook](generate-runbook.md) | sre-agent |
| [/generate-python-module](generate-python-module.md) | python-developer-agent |
| [/cleanup-code](cleanup-code.md) | code-cleanup-agent |
| [/review-code](review-code.md) | code-review-agent |
| [/propose-architecture](propose-architecture.md) | documentation-agent |
| [/define-slo](define-slo.md) | sre-agent |
| [/daily-handoff](daily-handoff.md) | handoff-agent (daily mode) |
| [/sprint-handoff](sprint-handoff.md) | handoff-agent (sprint mode) |
| [/performance-test](performance-test.md) | performance-agent |
| [/sast-scan](sast-scan.md) | security-agent |
| [/dast-scan](dast-scan.md) | security-agent |
| [/threat-profile](threat-profile.md) | security-agent |

## Vault replatform (public spine)

Replatform and governed-story delivery uses the same execute spine as Audit: `/implement-api` → `/implement-ui` → `/expand-test-coverage` → [Approved] → `/run-tests` → `/run-eval` → `/review-changes` → `/application-summary`, plus Vault **satellite** commands (`/figma-to-nextjs`, `/plan-governed-stories`, `/validate-api-endpoints`, `/compliance-check`, …). Legacy `generate-*` / `phase-*` commands were removed from this library.

# /deliver-sprint

## Objective

**Option B (batch):** After plan **Approved**, run the **same seven delivery steps** as Option A (see [PIPELINE.md](../PIPELINE.md)). Orchestrator does not write product code. **STOP** after step 3 (`/expand-test-coverage` Pending Approval); do not auto-run `/run-tests` until a human sets test-package **Approved**.

**On amount-math error / conflict:** `artifacts/calculations/<module>-calculations.json` is the **primary source** for every child command. Align implementation and tests to `CALC-*` (rule 32).

> **Option A (manual):** Run the spine yourself — see [EXECUTION-OPTIONS.md](../EXECUTION-OPTIONS.md).

## Execution order (locked)

1. `/implement-api` — once per SP-xxx tagged `implement-api` (skip when plan marks N/A)
2. `/implement-ui` — once per SP-xxx tagged `implement-ui` (skip when plan marks N/A)
3. `/expand-test-coverage` — **STOP** here until `docs/quality/test-package-review.md` Approval = **Approved**
4. `/run-tests` (after human Approved)
5. `/run-eval`
6. `/review-changes`
7. `/application-summary`

Skip a child only when the plan marks that row N/A with reason (e.g. API-only item skips UI).

## Runtime Inputs

### Required

- Approved sprint plan or unified governed plan at `artifacts/sprints/<sprint-id>-plan.md`
- No **Blocking** DQ-xxx without documented waiver

## Plan-as-input mode

Apply `.cursor/templates/plan-as-input-protocol.md`. Resolve module roots and contracts from the plan.

## Orchestration

| Role | Agent |
|------|-------|
| **Orchestrator** | orchestrator-agent |
| **Per SP item** | Invokes `/implement-api` or `/implement-ui` per command spec |
| **Closeout** | expand → run-tests → run-eval → review → summary → README + harness (documentation-agent) |

## Use Agents

orchestrator-agent (coordinates child commands)

## Enforce Rules

09, 10, 11, 07, 32 (amount math)

## Use Templates

`plan-act-validate-refine-loop.md`, `audit-trail-template.md`, `parallel-agent-file-ownership.md`

## Use Skills

`.cursor/skills/plan-as-input/SKILL.md`

## Command-Specific Hardening

- Refuse unless the sprint/governed plan status is **Approved**. **Proposed — Pending Approval** is not enough. Do not run implement-* batch until Approved.
- Parallel slices must not edit shared files (`parallel-agent-file-ownership.md`).
- After expand: **STOP** for human test-package approval. Do not invoke `/run-tests` while Approval is missing or Pending Approval.
- Resume `/run-tests` → `/run-eval` → `/review-changes` → `/application-summary` only after Approved (or a dated waiver in `docs/harness/decisions.md`).
- Walk eval fix-queue via `/close-eval-gaps` (inner of `/run-eval`) when auto_fix items are open.
- Inherits **Rules**, **Templates**, and **Definition of Done** from each invoked child command. Orchestrator must propagate those contracts per [orchestrator-agent.md](../agents/orchestrator-agent.md).

### README update (closeout, required)

Owned by **documentation-agent**. Refresh root [`README.md`](../../README.md) so onboarding matches what this sprint actually shipped. Update only sections that changed; do not invent product copy or business rules.

| Section | Update when |
|---------|-------------|
| Intro / scope | New module or sprint capability is now runnable |
| Modules table | New top-level paths or stacks |
| Local run | Setup scripts, ports, JDK, env vars, Flyway troubleshooting changed |
| API / Swagger | New contracts under `contracts/`, seed versions, Swagger groups/URLs |
| Architecture locks | New non-negotiable constraints landed in code |

**Skip only if** the sprint produced no developer-facing surface change (document `README: no-op` in the deliver-sprint audit).

## Execution Policy

Plan → Act → Validate → Refine (max 3 ceiling). Iteration 1 Validate pass → stop. Refine only from findings. Escalate with owners after iteration 3.

## Clarification Protocol

After resolve-before-ask, if the Approved plan path is missing, register Open DQ and **refuse** (human gate: plan Approved). Do not invent execute rows. STOP for plan Approved and test-package Approved. Still refuse `/run-tests` until the package is Approved.

## Definition of Done

- [ ] Each in-scope child command DoD met (or batch paused at expand Pending Approval, documented in audit)
- [ ] Human gates not skipped (plan Approved; test-package Approved before run-tests)
- [ ] All SP-xxx Done or Blocked with reason
- [ ] Root `README.md` updated for sprint surface changes (or audit notes `README: no-op` with rationale)
- [ ] `docs/harness/progress.md` + sprint plan retrospective updated
- [ ] `artifacts/audit/<date>-deliver-sprint-<task>.md` written (orchestrator)

## Outputs

Same artifacts as manual spine, plus closeout docs:

- Code/test changes under configured module roots
- Root `README.md` (when developer-facing surface changed)
- `artifacts/test-results-<date>.md`
- `artifacts/e2e-test-results-*.md` (if E2E ran)
- `config.yaml` → `artifacts.application_summary_html` / `artifacts.application_summary_pdf` (when applicable)
- `artifacts/sprints/<sprint-id>-plan.md` (status updated)
- `docs/harness/progress.md`
- `artifacts/audit/*` per command + deliver-sprint summary

## Next command

When paused at expand:

```
✅ /deliver-sprint paused.
**Next command:** human sets docs/quality/test-package-review.md Approval = Approved, then /run-tests (or resume /deliver-sprint)
```

When the full spine finished:

```
✅ /deliver-sprint complete.
**Next command:** /pr-assist
```

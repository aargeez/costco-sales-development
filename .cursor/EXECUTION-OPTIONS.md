# Execution Options — Manual vs Batch

After the sprint or governed plan is **Approved**, run the **seven delivery steps** (Option A: each command individually; Option B: `/deliver-sprint`).

**Amount-math error / conflict:** `artifacts/calculations/<module>-calculations.json` is the **primary source** (rule 32) for every delivery command.

---

## Option A — Manual

```
/implement-api → /implement-ui → /expand-test-coverage → [human: test-package Approved] → /run-tests → /run-eval → /review-changes → /application-summary
```

Skip `/implement-api` or `/implement-ui` only when the approved plan has no work in that layer (record skip in audit).

**Example — API only:**
```
/implement-api
Scope: FR-034 hazmat block, sprint SP-002

/expand-test-coverage
[human: test-package Approved]
/run-tests
/run-eval
/review-changes
/application-summary
```

**Example — full stack:**
```
/implement-api
Scope: SP-001 disposal entity API

/implement-ui
Mockup: UI/image (3).png, SP-002

/expand-test-coverage
[human: test-package Approved]
/run-tests
/run-eval
/review-changes
/application-summary
```

---

## Option B — Batch

```
/deliver-sprint
Sprint: sprint-01
```

Orchestrator runs the same seven steps. **STOP** after step 3 (`/expand-test-coverage` Pending Approval). Resume steps 4–7 after human Approved (starting with `/run-tests`).

### What `/deliver-sprint` does internally

| Phase | Equivalent command | When |
|-------|-------------------|------|
| Build | `/implement-api` | Once per sprint item tagged `implement-api` |
| Build | `/implement-ui` | Once per sprint item tagged `implement-ui` |
| Closeout | `/expand-test-coverage` | Once, after all SP items — **STOP** for human approval |
| Closeout | `/run-tests` | After test-package **Approved** |
| Closeout | `/run-eval` | After run-tests |
| Closeout | `/review-changes` | Once |
| Closeout | `/application-summary` | Once |
| Closeout | Docs + harness | Root `README.md` (when surface changed), `docs/harness/progress.md`, retrospective |

---

## Comparison

| | Option A — Manual | Option B — `/deliver-sprint` |
|--|-------------------|------------------------------|
| **Control** | Stop/review between steps | Hands-off through sprint (pauses at expand approval) |
| **Scope** | Run only what you need | All SP-xxx in plan |
| **Commands** | You type each `/command` | You type once |
| **DoD / artifacts** | Same seven steps | Same seven steps |
| **Audit** | Per-command audits | Per-command + consolidated audit |

---

## Preconditions (both options)

- [ ] BRD reviewed (after `/generate-brd`)
- [ ] Sprint plan **Approved** (after `/plan-sprint`)
- [ ] No blocking DQ-xxx without waiver

---

## Surgical (not a second style)

```
/fix-issue            # Mode: cited-requirements — cited BR/story IDs only
/review-changes
```

---

## Artifact chain (both options)

```
implement-api  ──► test-results-<date>.md
implement-ui   ──► test-results + e2e-test-results-*
expand-*       ──► test-cases.md + test-matrix.md + test-package-review (Pending Approval)
run-tests      ──► test-results + scripts executed
run-eval       ──► evals/results/* + STATUS.md
close-eval-*   ──► eval-fix-log + updated incremental results
review-changes ──► audit/review-changes.md + eval completeness ledger
application-summary ──► summary.html + .pdf
deliver-sprint closeout ──► README.md (if needed) + harness progress
```

See [PIPELINE.md](PIPELINE.md) for full intake → plan → deliver flow.

---

## Next command cheat sheet

| Completed | Next command |
|-----------|--------------|
| `/generate-brd` | `/plan-sprint` *(after BRD review)* |
| `/plan-sprint` | `/implement-api` or `/deliver-sprint` *(after sprint approval)* |
| `/implement-api` | `/implement-ui` or `/expand-test-coverage` |
| `/implement-ui` | `/expand-test-coverage` |
| `/expand-test-coverage` | **Wait** — human sets test-package **Approved**, then `/run-tests` |
| `/run-tests` | `/run-eval` |
| `/run-eval` | `/review-changes` or `/close-eval-gaps` |
| `/close-eval-gaps` | `/review-changes` (when green) |
| `/review-changes` | `/application-summary` |
| `/application-summary` | `/pr-assist` *(optional)* |
| `/deliver-sprint` (paused) | Human approval, then `/run-tests` or resume batch |
| `/deliver-sprint` (complete) | `/pr-assist` *(optional)* |

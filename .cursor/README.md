# Sales Master — Cursor AI Assets

Merged **Sales Audit + Sales Vault** library. Deploy: copy this folder to a product repo as `.cursor`.

**Product config:** Root `config.yaml` defaults to **Sales Audit** module paths. For Vault or Processing, start from [config.examples/](config.examples/) (`sales-vault.config.yaml`, `sales-audit.config.yaml`, `sales-processing.config.yaml`).

**Execution model:** Seven delivery steps after sprint approval (Option A individually or Option B `/deliver-sprint`). See [EXECUTION-OPTIONS.md](EXECUTION-OPTIONS.md) and [README-SALES-MASTER.md](README-SALES-MASTER.md).

Agentic framework: **intake → plan → deliver** for sales repos.

## Pipeline

```
ATI start  →  /extract-calculations  →  /generate-brd  →  [review]  →  /plan-sprint  →  [approve]
       →  Option A: seven steps individually (see PIPELINE.md)
       →  Option B: /deliver-sprint (same seven steps, batched; STOP after step 3 until Approved)
```

**Two ways to execute — same outcome:** [EXECUTION-OPTIONS.md](EXECUTION-OPTIONS.md) · [PIPELINE.md](PIPELINE.md) · [CURSOR-FLOW.md](CURSOR-FLOW.md)

## What's In This Repo

| Area | Path |
|------|------|
| API | `config.yaml` → `modules.api` |
| UI | `config.yaml` → `modules.ui` |
| OpenAPI | `config.yaml` → `modules.openapi` |
| BRD (reference) | `config.yaml` → `modules.brd` |
| BRD drafts | `artifacts/brd/` |
| Calculation catalogs | `artifacts/calculations/<module>-calculations.json` — **primary source** for amount math on error/conflict (rule 32) |
| Intake | `artifacts/intake/` |
| Sprint plans | `artifacts/sprints/` |
| Feature notes | `docs/feature.md` |
| Production Jinja | `java_master_templates/` → `spring-module-generator` |
| Test Jinja | `java_test_templates/` → `spring-test-generator` (rule 23) |

## Always-On Rules

| Rule | Purpose |
|------|---------|
| [00-global-governance](rules/00-global-governance.mdc) | SDLC guardrails |
| [02-security-and-compliance](rules/02-security-and-compliance.mdc) | OWASP, secrets, PII |
| [22-behavior-provenance](rules/22-behavior-provenance-and-anti-invention.mdc) | No invented behavior |
| [01-project-context](rules/01-project-context.mdc) | Module paths and principles |

Contextual rules:

- [11-intake-planning-governance](rules/11-intake-planning-governance.mdc) — `/extract-calculations`, `/generate-brd`, `/plan-sprint`, `/deliver-sprint`
- [33-reverse-engineering-quality](rules/33-reverse-engineering-quality.mdc) — `/generate-brd` when interpreting legacy sources
- [13-business-analysis-invest](rules/13-business-analysis-invest.mdc) — `/plan-sprint` story and acceptance-criteria quality
- [23-test-generation](rules/23-test-generation.mdc) — `/implement-api`, `/expand-test-coverage` structural JUnit via `java_test_templates/`
- [32-calculation-catalog](rules/32-calculation-catalog.mdc) — `/extract-calculations`, ATI amount math

## Commands

### Intake & Planning

| Command | Primary Agent |
|---------|---------------|
| [/extract-calculations](commands/extract-calculations.md) | calculation-catalog-agent (ATI start) |
| [/generate-brd](commands/generate-brd.md) | requirements-agent |
| [/plan-sprint](commands/plan-sprint.md) | planner-agent |
| [/deliver-sprint](commands/deliver-sprint.md) | orchestrator → execution agents |

### Execution — two options (same seven steps)

| Option | How |
|--------|-----|
| **A — Manual** | Run each command in order: (1) `/implement-api` → (2) `/implement-ui` → (3) `/expand-test-coverage` → 🛑 Approved → (4) `/run-tests` → (5) `/run-eval` → (6) `/review-changes` → (7) `/application-summary` |
| **B — Batch** | `/deliver-sprint` — same seven steps; **STOP** after step 3 until test-package **Approved**, then steps 4–7 |

Details: [EXECUTION-OPTIONS.md](EXECUTION-OPTIONS.md) · [PIPELINE.md](PIPELINE.md) · [CURSOR-FLOW.md](CURSOR-FLOW.md)

| Command | Primary Agent |
|---------|---------------|
| [/implement-api](commands/implement-api.md) | backend-agent + api-agent |
| [/implement-ui](commands/implement-ui.md) | frontend-agent |
| [/expand-test-coverage](commands/expand-test-coverage.md) | quality-agent (coordinate) + test-agent |
| [/run-tests](commands/run-tests.md) | quality-agent → test-agent |
| [/run-eval](commands/run-eval.md) | quality-agent → eval-agent |
| [/close-eval-gaps](commands/close-eval-gaps.md) | orchestrator (+ agents per fix-queue) |
| [/review-changes](commands/review-changes.md) | quality-agent + security-reviewer |
| [/application-summary](commands/application-summary.md) | documentation-agent |
| [/deliver-sprint](commands/deliver-sprint.md) | orchestrator (batch) |
| [/pr-assist](commands/pr-assist.md) | documentation-agent |

## Skills

| Skill | Use |
|-------|-----|
| brd-authoring | Transcript/docs → structured BRD |
| calculation-catalog | IBMi → `<module>-calculations.json`; consume CALC-*; JSON **primary** on amount errors |
| validation-chain | ValidationRule unit-test pattern |
| quality | Coordinate; cases + BR coverage PASS before scripts |
| jacoco-coverage | Java 17 + JaCoCo |
| playwright-e2e | E2E run and capture |
| application-summary-pdf | Summary HTML/PDF |

## Templates

See [templates/README.md](templates/README.md) for the full catalog including API contract, test strategy, behavior provenance, and review templates.

## Structure

```
.cursor/
├── PIPELINE.md
├── AGENTS-SALES.md
├── config.yaml          ← module paths, artifact outputs, quality gates
├── agents/              # orchestrator at root; primaries under <area>/
│   ├── orchestrator-agent.md
│   ├── api/ backend/ frontend/ calculations/
│   ├── intake/ planning/ engineering/ quality/
│   └── figma/ security/ ux/ infra/ … (Vault extensions)
├── subagents/           # same <area>/ layout — see subagents/README.md
├── commands/
├── rules/
├── skills/
└── templates/

# Sibling Jinja catalogs (repo root — owned by engineering subagents)
java_master_templates/   ← spring-module-generator
java_test_templates/     ← spring-test-generator (rule 23)
```

## Human Gates

BRD domain review and sprint approval are **mandatory** before `/deliver-sprint`. See [rules/11-intake-planning-governance.mdc](rules/11-intake-planning-governance.mdc).

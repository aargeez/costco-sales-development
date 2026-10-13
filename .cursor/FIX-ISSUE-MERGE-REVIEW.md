# `/fix-issue` merge review

Date: 2026-09-30  
Scope: `.cursor master sales` only  
Result: the retired incremental-change command is fully absorbed into `/fix-issue`. The locked delivery spine is unchanged.

## What changed

Two outside-the-spine paths now share one command.

| Former path | Now |
|-------------|-----|
| Ad-hoc bug, gap, or enhancement | `/fix-issue` **defect** |
| Cited incremental diff (BRD, story, feature note, or `CALC-*`) | `/fix-issue` **cited-requirements** |

The retired command file is deleted. Live commands, agents, rules, skills, templates, and the pipeline no longer reference it. This review is the only file that records the old filename: `commands/apply-requirements.md`.

Module name, BRD path, calculation catalog, and code roots come from `.cursor/config.yaml`. Examples in `/fix-issue` use `<module>` placeholders. The same command is used for any sales product. Do not hardcode a product or ATI.

## Merge process

1. Compare the retired command contract with `commands/fix-issue.md`.
2. Keep the existing defect path (classify, fix plan, human **proceed**, backend/frontend fix mode, issue log).
3. Add **cited-requirements** mode with the retired contract: scope lock, amount-math gate, hard constraints, change plan, provenance, feature note, catalog write-back.
4. Point every former reference at `/fix-issue` and the correct mode.
5. Delete the retired command file.
6. Leave shared agents, templates, and skills in place. They are used by `/implement-api`, `/implement-ui`, and `/extract-calculations` as well as by the new mode.
7. Review the result against the delivery spine and correct any rule that would force defect artifacts onto cited work.

## Contract checklist

Every required section from the retired command is in `commands/fix-issue.md` under **cited-requirements**, unless noted.

| Retired section | Present in `/fix-issue` | Where |
|-----------------|-------------------------|--------|
| Incremental, provenance-only objective | Yes | Mode: cited-requirements |
| When to use (FR slice, surgical SP, cited change, catalog parity) | Yes | When to Use |
| Cited seed, diagnose-to-diff, cross-module port | Yes | When to Use + mode selection |
| Prefer `/implement-api` / `/implement-ui` for a full slice | Yes | Objective |
| Phase 0 catalog load; stop for `/extract-calculations` if missing | Yes | Orchestration |
| Phase 1 scope lock by **change-agent** | Yes | Orchestration |
| Phase 2 **api-agent** verify when API is touched | Yes | Orchestration |
| Phase 3 implement from catalog formula, steps, branches, `modern_impl` | Yes | Orchestration + execution order |
| Schema, validation, and in-scope UI subagents | Yes | Phases 3a–3c |
| UI displays API/DB amounts; no browser recompute | Yes | Phase 3c + amount-math gate |
| Tests for touched paths; honor `golden_refs` | Yes | Phases 4–5 |
| Provenance, feature note, catalog `modern_impl` / `parity` | Yes | Phase 6 + outputs |
| Amount-math gate (catalog path, `calculation_id`, branches, placement, shared engine, conflicts) | Yes | Amount-math gate. Defect mode uses the same gate when the defect is amount math |
| Six hard constraints (scope, no invention, no invented formulas, structure, incremental, document) | Yes | Hard constraints |
| Rules 00, 01, 02, 03, 04, 05, 07, 09, 10, 22, 23, 32 | Yes | Cited-requirements — Rules |
| Change plan, loop, audit, provenance, catalog, feature note, dos/don'ts, test results, API contract | Yes | Cited-requirements — Templates |
| Navigation checklist when back-stack or handoff is in scope | Yes | Templates + Definition of Done |
| Skills: validation-chain, JaCoCo, Playwright, calculation-catalog | Yes | Cited-requirements — Skills |
| Ask-back list (source, surface, out of scope, module, `calculation_id`) | Yes | Ask Back If Missing |
| Definition of Done (plan, catalog cite, shared engine, diff limit, assumptions, api verify, tests, feature note, provenance, audit) | Yes | Cited-requirements — Definition of Done |
| Outputs: change plan, code, catalog JSON, provenance, feature note, test results, audit | Yes | Cited-requirements — Outputs |
| Next: `/extract-calculations`, `/expand-test-coverage`, `/review-changes`, or remaining `/implement-*` | Yes | Next command |

Defect mode keeps its own contract: Phase 0 classification, Phase 1 analysis, human **proceed**, Phase 2 implement, Phase 3 tests, issue log, sibling scan. Full-sweep eval failures stay on defect mode.

## Where `/fix-issue` is wired

| Place | Role |
|-------|------|
| `commands/fix-issue.md` | Command contract (both modes) |
| `commands/README.md` | Command index and seed / diagnose / port table |
| `PIPELINE.md` | Reactive defect flow, cited incremental flow, command table, artifact chain |
| `EXECUTION-OPTIONS.md` | Surgical path is `/fix-issue` cited-requirements, not a second delivery style |
| `model-routing.yaml` | `fix-issue: reasoning` (orchestrator chooses the mode; **change-agent** stays `coding`) |
| `config.yaml` and `config.examples/*.yaml` | `fix_command: fix-issue` |
| `agents/orchestrator-agent.md` | Routes defect to backend/frontend fix mode; routes cited-requirements to change-agent |
| `agents/engineering/change-agent.md` | Primary command is `/fix-issue` cited-requirements |
| `agents/README.md` and `AGENTS-SALES.md` | Agent index names the mode each agent serves |
| `agents/quality/test-agent.md` | Slice tests include both modes |
| `rules/07-testing-and-quality.mdc` | Slice coverage PASS before that slice’s scripts |
| `rules/09-agent-orchestration.mdc` | Defect **proceed** gate; cited confirm only when ambiguous |
| `rules/11-intake-planning-governance.mdc` | Same two gates; neither mode enters `/deliver-sprint` |
| `rules/12-production-fix-governance.mdc` | Defect mode only. Cited mode is not required to write an issue log |
| `rules/32-calculation-catalog.mdc` | Amount-math load on `/implement-api` and `/fix-issue` |
| `rules/cursor-factory-governance.mdc` | Spine unchanged; both modes named beside it |
| `skills/calculation-catalog/SKILL.md` | Code update from catalog |
| `commands/extract-calculations.md` | Run before amount work on `/implement-api` or `/fix-issue` |
| `subagents/quality/br-coverage-validator.md` | `post-change` after cited-requirements |
| `subagents/engineering/next-component-generator.md` | Layout stays inside `/implement-ui` or cited-requirements |
| `templates/incremental-change-plan-template.md` | Required before cited code edits |
| `templates/README.md` | Template map split by mode |
| `templates/code-generation-dos-donts.md` | Cited edits use the change plan |
| `templates/ati-navigation-checklist.md` | Filled in the cited-requirements audit when navigation is in scope |
| `commands/review-changes.md` | Human-gate list names defect **proceed** |
| `commands/close-eval-gaps.md`, `skills/eval-suite/SKILL.md`, `subagents/quality/eval-agent.md` | Full-sweep failures stay on defect mode |

## What was not deleted

These are shared factory parts, not a command file. Removing them would break `/implement-api`, `/extract-calculations`, or cited-requirements itself.

- **change-agent** and its `scope.yaml` allowlist
- Incremental change-plan, provenance, feature-note, and calculation-catalog templates
- Calculation-catalog skill and rule 32
- Schema, validation, UI, and test generators

## Pipeline check

The delivery order is unchanged:

```
/implement-api → /implement-ui → /expand-test-coverage
  → [human: test-package Approved]
  → /run-tests → /run-eval → /review-changes → /application-summary
```

`/deliver-sprint` still runs that spine and still stops after expand until the test package is Approved. `/fix-issue` does not insert a step into that order.

| Behavior | Still true |
|----------|------------|
| Sprint items use `/implement-api` and `/implement-ui` | Yes |
| Defect waits for human **proceed** before code | Yes |
| Cited work writes the change plan before code | Yes |
| Cited work confirms with a human only when scope or the catalog is ambiguous | Yes |
| Amount math loads `<module>-calculations.json` and cites `CALC-*` | Yes |
| Missing catalog stops for `/extract-calculations` | Yes |
| Seed, diagnose, and port stay off the spine | Yes. Cited source → cited-requirements. Ad-hoc bug → defect |
| Full-sweep eval failures use defect mode and the **proceed** gate | Yes |

## Review corrections (this pass)

| Finding | Correction |
|---------|------------|
| Three files still named the retired command (orchestrator alias, change-agent note, command objective) | Removed. Search for that command name is empty |
| Rule 12 said every `/fix-issue` run must write an issue log | Scoped to **defect** mode so cited work keeps the change plan and provenance, not an issue log |
| Command examples named one ATI and one BRD file | Replaced with `<module>` and paths from `config.yaml` |
| Agent indexes did not say which mode calls which agent | `agents/README.md` and `AGENTS-SALES.md` now name defect vs cited-requirements |
| `review-changes` listed `/fix-issue` proceed without a mode | Clarified as defect **proceed** |

## How to run it

Defect:

```
/fix-issue
Mode: defect
Issue: <one line>
Steps: <reproduce>
Expected: <cited behavior>
Actual: <what happens>
Severity: High
```

Cited requirements (any sales module):

```
/fix-issue
Mode: cited-requirements
Source: artifacts/brd/<module>-BRD-draft.md FR-012
Surface: API | UI | both | config | schema
Slug: <slug>
```

Amount math adds `Module`, `Calculations: artifacts/calculations/<module>-calculations.json`, and `calculation_id`.

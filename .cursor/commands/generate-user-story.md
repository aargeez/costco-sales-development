# /generate-user-story

## Objective
Convert validated business rules into an **enterprise techno-functional story pack**: INVEST-compliant Gherkin stories **plus** DDL hints, API surface, UI data workflow, and field binding data tables — ready for `/plan-governed-stories` without re-entering technical detail in chat.

## Runtime Inputs

### Required
- `business_rule_sources`: paths or links to validated business rule catalog(s) or reverse-engineering outputs.

### Optional (auto-inferred when omitted)
- `feature_or_slice_name` or `artifact_namespace`: naming segment — infer from rule catalog or sprint context.
- `story_scope_filter`: subset of rules, domains, or rule IDs to convert in this invocation.
- `sprint_number`: when persisting under `docs/sprint<N>/userstory/` — infer from prompt, branch, or `SPRINT_NUMBER`.
- `existing_story_paths`: prior story files to extend (avoid duplicate story IDs).

## Use Agents
- business-analyst-agent

## Delegate to Subagents
- gherkin-writer
- invest-validator
- gap-identifier

## Enforce Rules

- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/01-project-context.mdc` (config-driven). Load overlay [rules/overlays/sales-vault/01-project-context-replatforming.mdc](../rules/overlays/sales-vault/01-project-context-replatforming.mdc) **only when** the active product is Sales Vault. Load overlay [rules/overlays/sales-audit/01-project-context-sales-audit-fragment.mdc](../rules/overlays/sales-audit/01-project-context-sales-audit-fragment.mdc) **only when** the active product is Sales Audit. Do not apply both.
- `.cursor/rules/13-business-analysis-invest.mdc`
- `.cursor/rules/07-testing-and-quality.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/09-agent-orchestration.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/28-techspec-and-doc-quality.mdc`
- `.cursor/rules/31-untrusted-intake.mdc`

## Intake safety gate

Raw `business_rule_sources` (docs, CSV, catalogs) only — not an already-emitted story pack or sprint plan. Load `.cursor/skills/intake-safety/SKILL.md`. Run `python3 scripts/intake-safety-scan.py` on those paths **before** spawning business-analyst-agent. Exit 2 → STOP. Exit 1 → STOP until redaction or waiver. Exit 0 → continue.

## Use Templates
- .cursor/templates/techno-functional-story-pack-template.md (primary output shape)
- .cursor/templates/business-rule-template.md
- .cursor/templates/gherkin-story-template.md
- .cursor/templates/traceability-matrix-template.md

## Required Output (single techno-functional pack)

One markdown file per sprint slice containing **all stories** in scope. For **each story**:

1. **Gherkin** — Given/When/Then scenarios, acceptance criteria, INVEST scorecard, rule traceability
2. **DDL hints** — tables/objects, columns, constraints, migration hints (cite existing `migrations/` when known)
3. **API surface** — resources, methods, paths, request/response summaries, error cases
4. **UI data workflow** — routes, steps, fields, validation UX, loading/empty/error states
5. **Data tables** — UI field ↔ API field ↔ persistence column bindings
6. **Wiring sequence preview** — ordered API steps for downstream planner
7. **Gaps** — dimension-tagged clarifications (`DDL` / `API` / `UI` / `Wiring`); use `TBD` only in gap table, not in final spec text

Pack-level: dependency list, sprint summary, downstream handoff paths.

## Command-Specific Hardening
- **Do** trace every scenario to a business rule ID; populate techno-functional sections from rules and repo artifacts (`migrations/`, OpenAPI when cited).
- **Do** keep AC testable (observable outcomes); DDL/API/UI hints are **implementation-ready signals** for the planner — not invented schema.
- **Don't** invent domain rules or columns when sources are silent — add gap ID and question.
- **Do** add a `Rule provenance` section per story that maps each business behavior to source line/section; if no source exists, register `ASSUMPTION-<id>` and keep story in gap state.
- **Stop** if business rules are unvalidated or draft without SME sign-off path.

## Execution Policy
- Execution loop: Plan -> Act -> Evaluate -> Refine.
- Max iterations: 3.
- Validation: `invest-validator` checks INVEST + Gherkin; `gap-identifier` checks techno-functional completeness per story (all five technical dimensions present or gap-registered).
- Refinement: patch pack in-place.

## Clarification Protocol

**Resolve-before-ask:** Infer `sprint_number` and `artifact_namespace` from path/branch before asking.

**Medium gate** — ask only when still unresolved:

1. Which `business_rule_sources` are authoritative?
2. Are any rules draft or disputed (SME gate)?
3. Full catalog or `story_scope_filter` only?

## Artifact Persistence
- **Primary (sprint):** `docs/sprint<sprint_number>/userstory/<feature-or-slice>-techno-functional-pack.md`
- **Legacy mirror (optional):** `docs/stories/<feature-or-slice>-user-stories.md` — summary pointer to pack only
- Include metadata header: timestamp, iteration count, validation summary, command version (`/generate-user-story`).
- Update `docs/sprint<sprint_number>/manifest.json`:
```json
{
  "path": "docs/sprint<sprint_number>/userstory/<feature-or-slice>-techno-functional-pack.md",
  "kind": "techno-functional-story-pack"
}
```

## Downstream handoff

```text
/plan-governed-stories
@story_artifact: docs/sprint<N>/userstory/<feature>-techno-functional-pack.md
```

## Definition of Done

- [ ] Intake-safety scan exit 0, or exit 1 with recorded human redaction/waiver
- [ ] Every in-scope story has Gherkin + INVEST + rule traceability.
- [ ] Every story has techno-functional sections (DDL, API, UI, data tables, wiring preview) **or** explicit gap rows — no silent omissions.
- [ ] `invest-validator` reports no critical INVEST failures.
- [ ] `gap-identifier` reports no critical undocumented technical dimensions.
- [ ] No `TODO`/`TBD` in story body or techno-functional tables except gap register.
- [ ] Manifest updated with `kind: techno-functional-story-pack`.
- [ ] No behavior statement appears without a source citation or approved assumption record.

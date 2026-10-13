# /extract-calculations

## Objective

Extract or refresh the **math-only calculation catalog** for an ATI module from IBMi source (extract primary) with BRE/BRD crosswalk (secondary). Produce one schema-valid JSON file per module **when amount records exist**.

Active catalogs hold **amount math only** (O/S, totals, pickups, FX, adjustments, vault deltas, inquiry Sum/A+B columns). Non-math behavior (indicators, row-set filters, eligibility, labels) → rule 22 provenance + validation chain — documented in audit, not active catalog.

Once written, active JSON is the **primary source** for amount math errors. Do not overwrite valid math to match Java/SQL.

## When to Use

- **At ATI menu start** before `/generate-brd` or coding (especially first time)
- When IBMi replica refreshes or formula parity gaps are found
- Before `/implement-api` / `/fix-issue` (cited-requirements, or a defect that is amount math) on O/S, totals, pickups, FX, adjustments, **inquiry display amounts** if catalog missing/stale
- When BRE **Rule Type = Calculation** exists with currency/Sum/A+B DD fields

## Orchestration

| Role | Agent / Subagent |
|------|------------------|
| **Orchestrator** | orchestrator-agent |
| **Primary** | calculation-catalog-agent |
| **Subagents** | ibmi-calculation-extractor → open-questions-cataloger (conflicts/gaps) |

## Rules

- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/09-agent-orchestration.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/33-reverse-engineering-quality.mdc`
- `.cursor/rules/32-calculation-catalog.mdc`

## Templates / Schema

- `.cursor/templates/calculation-catalog-template.md`
- `artifacts/calculations/calculation-catalog.schema.json` (math-only)
- Skill: `.cursor/skills/calculation-catalog/SKILL.md`

## Archive

Full pre-trim extracts (when restructure applies): `artifacts/archive_asset/extract-calculations/`
- `catalogs/` — full module JSON copies
- `schema/` — schema snapshot
- `audits/` — extract audit trails
- `non-math-records.md` — index of removed non-math records

## Ask Back If Missing

- Module / ATI option (e.g. `SARO`)
- Seed programs: default = **every program on BRE Tables used** for this ATI
- Whether to overwrite existing `<module>-calculations.json` or merge

## Definition of Done

- [ ] `artifacts/calculations/<module>-calculations.json` written/updated **or omitted** when module has no math records
- [ ] Validates against math-only `calculation-catalog.schema.json`
- [ ] One record per **amount result**; IBMi locators on math steps
- [ ] Seed covered Tables-used / inquiry programs with currency/Sum/A+B fields
- [ ] **Excluded** from active catalog: indicators, `query_filter` row-sets, eligibility gates, status labels, validation rules
- [ ] Non-math findings documented in audit (target: rule 22 provenance + validation chain)
- [ ] Lookup miss/eof/skip paths in `branches[]`; snippets not happy-path-only
- [ ] BRE/BRD IDs attached when known; conflicts logged on math records
- [ ] Audit trail written
- [ ] Pending assumptions listed for SME (not silently approved)

## Outputs

- `artifacts/calculations/<module>-calculations.json` (math-only, when applicable)
- `artifacts/audit/<date>-extract-calculations-<module>.md`

## Next command

**On completion, tell the user:**

> ✅ `/extract-calculations` complete.  
> **Next command:** `/generate-brd` (new ATI module) **or** `/plan-sprint` / `/implement-api` if BRD already Accepted and catalog was a refresh.

| Condition | Next command |
|-----------|--------------|
| Starting new ATI module / BRD not Accepted | `/generate-brd` — cite `CALC-*` from catalog |
| BRD Accepted; planning amounts | `/plan-sprint` — map SP → `calculation_id` |
| Implementing known amount FRs | `/implement-api` — load catalog first |
| Module has no math catalog (e.g. SAOD) | `/generate-brd` — use archive + provenance for non-math |
| Catalog gaps need SME | **Wait** — resolve `ASSUMPTION-*` / `CONFLICT-*` |

**Example:**

```
/extract-calculations
Module: <ATI>
Seed programs: every program on BRE Tables used for this option
  (entry + inquiry + calc + called). Math-only active catalog.
Include: Rule Type=Calculation currency/Sum/A+B fields only.
Non-math → audit + rule 22 provenance (not active catalog).
```

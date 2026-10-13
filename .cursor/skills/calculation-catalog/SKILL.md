# Calculation Catalog Skill

Use when extracting, refreshing, or consuming Sales Audit **math-only calculation catalogs**.

## Paths

| Item | Path |
|------|------|
| Schema (math-only) | `artifacts/calculations/calculation-catalog.schema.json` |
| Active module file | `artifacts/calculations/<module>-calculations.json` (when math exists) |
| Archive (full extracts) | `artifacts/archive_asset/extract-calculations/` |
| Non-math index | `artifacts/archive_asset/extract-calculations/non-math-records.md` |
| IBMi root | `ibmi-source-code-replica-main/IBMi-Code-Base` (prefer `INDEVEXC/`) |
| Rule | `.cursor/rules/32-calculation-catalog.mdc` |
| Command | `/extract-calculations` |

## Active scope (math-only, policy 2026-08-25)

**Include:** O/S, totals, pickups, FX, adjustments, vault deltas; inquiry amounts with `result.unit` = currency or Sum/A+B formula.

**Exclude from active catalog:** indicators, `query_filter` row-sets, eligibility gates, status labels, validation rules → rule 22 provenance + validation chain.

## Source priority

**Extract:**

1. IBMi RPGLE/SQLRPGLE subroutines (formulas + fields)
2. BRE rule IDs / BRD FR IDs (crosswalk only)
3. Modern Java/SQL (`modern_impl`, `parity` only)

**Consume / debug / error:** active `<module>-calculations.json` is **primary** for amount math. Align code, tests, and BRD amount wording to `CALC-*`.

## Workflow

1. Resolve ATI → module. Seed = every program on BRE **Tables used** (entry, inquiry, calc, called).
2. Read programs; locate **amount math** (`EVAL`, merge loops, Sum/A+B DD columns).
3. Emit **one record per amount result** with `steps` / `branches` and lookup miss paths.
4. **Do not** emit indicators, row-set filters, or eligibility into active catalog — note in audit for rule 22.
5. Attach BRE/BRD IDs; log conflicts on math records.
6. Set lean `placement` (`layer` + `status`; `display_via` ≠ `query_filter` for active records).
7. Validate JSON against math-only schema. Omit file if module has zero math records.

## Consumer checklist (other agents)

- [ ] Loaded `<module>-calculations.json` before inventing amount behavior (if file exists)
- [ ] Cited `calculation_id` in provenance / change plan
- [ ] Honored `placement.layer`; stopped if `unclear` / `pending_clarification`
- [ ] UI does not recompute catalog formulas client-side
- [ ] Non-math behavior sourced from rule 22 provenance / validation chain (not active catalog)
- [ ] On amount error, treated active catalog JSON as primary
- [ ] Implemented every `branches[]` miss/zero/skip path
- [ ] If `modern_impl` is shared by more than one ATI: cited `invocationSource` or applicable type set and filtered on it. If silent, `ASSUMPTION-*` — do not invent a profile (prevents one ATI’s O/S profile, such as SARD POSD/OPD, from changing another ATI such as SARS)

## `/fix-issue` cited-requirements (code update from catalog)

When amount math is in scope:

1. Load `<module>-calculations.json` (or stop for `/extract-calculations`).
2. Map each math REQ → `calculation_id`.
3. Implement toward `placement.layer` into existing `modern_impl` sites.
4. Honor formula/steps/branches; escalate conflicts/pending assumptions.
5. Write back `modern_impl` / `parity`.

See `/fix-issue` § Amount-math gate (cited-requirements mode; same gate when a defect is amount math).

## Seed programs (any ATI)

| Include in IBMi read | Emit to active catalog |
|----------------------|------------------------|
| Entry, inquiry, calc, called programs | Currency/Sum/A+B amount records only |
| BRE Rule Type = Calculation with amounts | Yes |
| Row-set filters, flags, eligibility | No — audit + rule 22 |

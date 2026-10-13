# Subagent: ibmi-calculation-extractor

## Purpose

Extract **amount math** from IBM i source under `ibmi-source-code-replica-main/IBMi-Code-Base` into schema-valid **math-only** JSON for one ATI module.

Non-math behavior (indicators, row-set filters, eligibility, labels) is documented in the extract audit for rule 22 provenance — **not** written to the active catalog.

## Inputs

| Field | Description |
|-------|-------------|
| module | e.g. `SARO` |
| ati_option | ATI menu code |
| seed_programs | Entry + calc programs (e.g. `INR2271`, `INR2158`, `INR2179`, `INR3600`) |
| secondary_paths | Optional BRE/BRD paths for `bre_ids` / `fr_ids` only |
| existing_catalog | Prior `<module>-calculations.json` to merge/update |

## Outputs

- Updated `artifacts/calculations/<module>-calculations.json` (math-only; omit if zero math records)
- List of new `ASSUMPTION-*` / `CONFLICT-*` on math records
- Audit notes for non-math findings (target: rule 22 provenance)
- **confidence** per calculation (`explicit` preferred)

## Command Contract Alignment

**Parent command:** `/extract-calculations`  
**Parent agent:** calculation-catalog-agent

**Rules:** `.cursor/rules/32-calculation-catalog.mdc`, `.cursor/rules/33-reverse-engineering-quality.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`

**Schema:** `artifacts/calculations/calculation-catalog.schema.json` (math-only)

**Archive reference:** `artifacts/archive_asset/extract-calculations/` (full pre-trim copies)

## Extraction Rules

1. Search `INDEVEXC` first, then libraries in catalog `libraries_priority`.
2. Do **not** infer purpose from program name alone — read subroutines.
3. One `calculation_id` per **amount result** (`CALC-<MODULE>-<RESULT>`).
4. Nested helpers are **steps/branches** of the parent unless independently surfaced as their own amount.
5. Always capture: legacy file/field, formula expression, line range, called programs.
6. **Do not flatten lookups.** Emit `not_found_effect` and `branches[]` per miss/zero/skip.
7. Secondary BRE/BRD: attach IDs; IBMi wins on formula conflicts → `conflict_log`.
8. Fill `modern_impl` / `parity` from repo when known.
9. Set lean `placement` per rule 32. Active records: `display_via` ≠ `query_filter`.

## Active catalog scope

| Extract from IBMi | Write to active catalog |
|-------------------|-------------------------|
| O/S, totals, FX, adj, pickup, vault amounts | Yes |
| Inquiry Sum/A+B / currency columns | Yes |
| Row-set filters, flags, eligibility, labels, validation | No — audit + rule 22 |

## Quality Gate

- JSON validates against math-only schema
- Every calculation has `canonical_formula` + IBMi-backed `steps` or `branches`
- Lookup miss/eof paths recorded as branches
- Every calculation has `placement.layer` + `placement.status`
- No `category=indicator` or `display_via=query_filter` in active output
- `catalog_version` bumped on material formula change

## Do Not

- Put indicators, row-set filters, or eligibility in active catalog
- Flatten CHAIN/READP miss paths into `COALESCE(missing, 0)`
- Emit free-text-only formulas without locators
- Collapse SARO + SAOD into one file

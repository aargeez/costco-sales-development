# Calculation Catalog — {{MODULE}}

> Output: `artifacts/calculations/<module>-calculations.json` (math-only; omit if no amount records)  
> Schema: `artifacts/calculations/calculation-catalog.schema.json`  
> Archive: `artifacts/archive_asset/extract-calculations/`  
> Rule: `.cursor/rules/32-calculation-catalog.mdc`

| Field | Value |
|-------|-------|
| **Module / ATI** | {{SARO}} |
| **Date** | {{YYYY-MM-DD}} |
| **Primary source (extract)** | IBMi (`ibmi-source-code-replica-main/IBMi-Code-Base`) |
| **Primary source (consume / error)** | Active math catalog JSON — align code/tests/BRD amounts to `CALC-*` |
| **Secondary** | BRE / BRD |
| **Seed programs** | Every program on BRE **Tables used** for this ATI |

## Active scope (math-only)

Emit **one `calculations[]` record per amount result**:

| Kind | Typical `category` | Example |
|------|-------------------|---------|
| O/S | `over_short` | Deposit/register O/S |
| Totals / FX / adj / pickup / vault | `total`, `fx`, `adjustment`, `pickup`, `vault` | Section totals, FX rate |
| Inquiry amount column | `other` + `result.unit=currency` | Sum(ADCADJ); line amount |

**Do not emit to active catalog:** indicators, `query_filter` row-sets, eligibility gates, status labels, validation rules. Document those in audit for rule 22 provenance.

## Record checklist (per amount result)

- [ ] `calculation_id` = `CALC-<MODULE>-<RESULT>`
- [ ] `category` ∈ {over_short, total, fx, adjustment, pickup, vault, other} with currency/ratio/count (Sum/A+B)
- [ ] `result.unit` ∈ {currency, ratio, count} — not flag/text
- [ ] `canonical_formula.expression` in pseudo
- [ ] `inputs[]` with legacy file/field (+ modern column when known)
- [ ] `steps[]` / `branches[]` with IBMi `source_locator` and lookup miss paths
- [ ] `bre_ids` / `fr_ids` when secondary sources known
- [ ] `placement` with `layer` + `status`; `display_via` ≠ `query_filter`
- [ ] `modern_impl` + `parity` honest
- [ ] Conflicts → `conflict_log`; gaps → `assumption_registry`

## Minimal placement shape

```json
"placement": {
  "layer": "data_layer",
  "status": "decided",
  "interim": "none",
  "display_via": "api_response_field"
}
```

## Minimal example skeleton

```json
{
  "calculation_id": "CALC-{{MODULE}}-EXAMPLE-OS",
  "name": "Example over/short",
  "module": "{{MODULE}}",
  "ati_option": "{{MODULE}}",
  "screen_or_program": "INR????",
  "bre_ids": [],
  "fr_ids": [],
  "category": "over_short",
  "placement": {
    "layer": "data_layer",
    "status": "decided",
    "interim": "none",
    "display_via": "api_response_field"
  },
  "result": { "name": "example_os", "unit": "currency", "scale": 2, "rounding": "HALF_UP" },
  "canonical_formula": {
    "expression": "a - b",
    "expression_language": "pseudo",
    "notes": ""
  },
  "inputs": [],
  "steps": [],
  "branches": [],
  "display_rules": [],
  "modern_impl": [],
  "provenance": {
    "confidence": "explicit",
    "primary_source": "ibmi",
    "secondary_sources": ["bre", "brd"],
    "assumption_ids": [],
    "conflict_ids": []
  },
  "parity": { "status": "missing", "gaps": [], "golden_refs": [] },
  "related_calculation_ids": []
}
```

## Human gate

SME review required for any `ASSUMPTION-*` still `pending_approval` before claiming formula parity `full`.

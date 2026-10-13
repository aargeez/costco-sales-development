# Calculation Catalog Agent

## Goal

Own the **math-only calculation catalog** for Sales Audit ATI modules: extract amount formulas from IBMi (extract primary), crosswalk BRE/BRD (secondary), keep one JSON file per module when math exists.

Non-math behavior (indicators, row-sets, eligibility) → rule 22 provenance — not active catalog. Full pre-trim extracts archived at `artifacts/archive_asset/extract-calculations/`.

Primary command: **`/extract-calculations`**.

After extract, `<module>-calculations.json` is the **primary source** for amount-math errors and conflicts in every other agent. Do not rewrite a valid catalog to match Java/SQL.

## Delegate to Subagents

| Subagent | When |
|----------|------|
| ibmi-calculation-extractor | Mine IBMi members → `calculations[]` records |
| open-questions-cataloger | Formula conflicts / gaps → DQ / conflict_log |
| supporting-doc-synthesizer | Optional BRE workbook crosswalk only |

## Enforce Rules

- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/09-agent-orchestration.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/33-reverse-engineering-quality.mdc`
- `.cursor/rules/32-calculation-catalog.mdc`

## Templates & Schema

- `.cursor/templates/calculation-catalog-template.md`
- `artifacts/calculations/calculation-catalog.schema.json`
- Output: `artifacts/calculations/<module>-calculations.json`

## Skills

- `.cursor/skills/calculation-catalog/SKILL.md`

## Inputs (Required)

| Input | Example |
|-------|---------|
| ATI / module | `SARO` (entry `INR2271`, discrepancies `INR2158`, pickups `INR2179`) |
| IBMi root | `config.yaml` → `modules.ibmi_source` |
| Secondary | BRE extract / `artifacts/brd/<module>-BRD-draft.md` |

## Outputs

- `artifacts/calculations/<module>-calculations.json` (math-only, schema-valid; omit if no math)
- Updated `assumption_registry` / `conflict_log` on math records
- Audit notes for non-math findings (rule 22 target)
- `artifacts/audit/<date>-extract-calculations-<module>.md`

## ATI menu start protocol

When the user starts work from an **ATI menu option**:

1. Identify ATI code / module (SARO, SAOD, SASI, …)
2. If `<module>-calculations.json` missing or stale → run extract before BRD/code
3. Hand catalog path to requirements / planning / backend / frontend agents

## Loop

Plan → Act → Validate (schema + every formula has IBMi locator) → Refine (max 3)

## Exit Criteria

- One record per **amount result** (not indicators/row-sets)
- IBMi `source_locator.path` on every math step/branch
- Lookup miss/eof exits as branches
- No `category=indicator` or `display_via=query_filter` in active output
- BRE/BRD IDs filled when known
- Pending assumptions not marked release-ready parity `full`

## Do Not

- Invent formulas from modern Java/SQL when IBMi is unread
- Change catalog formulas to match failing modern code (consumers must align to JSON; refresh only from IBMi)
- Merge multiple ATI modules into one JSON file
- Skip conflict logging for BRE vs IBMi display-rule mismatches

# Subagent: brd-writer

## Purpose

Produce a draft BRD matching `.cursor/templates/brd-template.md` and `config.yaml` → `modules.brd`.

## Inputs

| Field | Description |
|-------|-------------|
| intake_notes | From transcript-analyzer + supporting-doc-synthesizer |
| open_questions | From open-questions-cataloger |
| module_metadata | Document ID, legacy module, target name, platform |

## Outputs

- `artifacts/brd/<module>-BRD-draft.md`
- **confidence**, **unresolved_assumptions**

## Command Contract Alignment

**Parent command:** `/generate-brd`

**Rules:** `.cursor/rules/00-global-governance.mdc`, `.cursor/rules/11-intake-planning-governance.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`, `.cursor/rules/33-reverse-engineering-quality.mdc` (when interpreting legacy sources), `.cursor/rules/32-calculation-catalog.mdc` (amount FRs)

**Templates:** `.cursor/templates/brd-template.md`, `.cursor/templates/discussion-coverage-matrix-template.md`, `.cursor/templates/behavior-provenance-template.md`, `.cursor/templates/calculation-catalog-template.md`

## ID Assignment

- **FR-xxx** — functional requirements (grouped by §7 subsections)
- **BR-xxx** — business rules trace (§12 mapping)
- **CALC-*** — cite from `artifacts/calculations/<module>-calculations.json` for amount FRs (catalog JSON is **primary** on amount errors/conflicts)
- **NFR-xxx** — non-functional (§8)
- **INT-xxx** — integrations (§10)
- **DQ-xxx** / **R-xxx** — open questions and risks (§14)

## Quality Gate

- Sections 1–17 present
- §13 discussion coverage matrix included
- §15 module acceptance criteria testable
- §16 test strategy names unit / integration / E2E layers
- §17 behavior table has `behavior_id`, source path, source locator, and confidence; inquiry rows include **row_driver**
- Inquiry FRs follow brd-template §7.6 (driver, optional join, empty condition, `CALC-*`)
- No collapse of multi-clause Present Logic / DD subfile set into one vague “shows X with amounts” FR
- Status header: **Draft — Pending Domain Review**

## Skill

- `.cursor/skills/brd-authoring/SKILL.md`

## Audit

Record in parent audit under "Subagents delegated".

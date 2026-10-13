# Subagent: supporting-doc-synthesizer

## Purpose

Synthesize facts from supporting documents (process flows, rule spreadsheets, mockups, config files, legacy extracts) into a fact table for BRD authoring.

## Inputs

| Field | Description |
|-------|-------------|
| doc_paths | PDF, xlsx, png, txt, yaml paths under `artifacts/intake/` or user-provided |
| intake_notes | Output from transcript-analyzer |

## Outputs

- Section appended to intake notes: **Supporting Document Facts**
- Table: Source | Section/Page | Fact | Maps to BRD section
- **confidence**, **unresolved_assumptions**

## Command Contract Alignment

**Parent command:** `/generate-brd`

**Rules:** `.cursor/rules/00-global-governance.mdc`, `.cursor/rules/11-intake-planning-governance.mdc`, `.cursor/rules/10-audit-trail.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`, `.cursor/rules/33-reverse-engineering-quality.mdc` (when interpreting legacy sources), `.cursor/rules/32-calculation-catalog.mdc` (amount facts — catalog JSON primary on error)

**Templates:** `.cursor/templates/transcript-intake-notes-template.md`, `.cursor/templates/discussion-coverage-matrix-template.md`

## Quality Gate

- Legacy rule IDs preserved when present (BR-xxx from spreadsheets)
- Mockup fields mapped to proposed FR IDs
- Conflicts with transcript flagged — do not pick a winner
- Every behavior fact includes its source path and locator for BRD provenance

## Audit

Record in parent audit under "Subagents delegated".

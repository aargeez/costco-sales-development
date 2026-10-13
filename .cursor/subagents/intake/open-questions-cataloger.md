# Subagent: open-questions-cataloger

## Purpose

Catalog ambiguities, conflicts, and missing domain decisions before BRD is treated as authoritative.

## Inputs

| Field | Description |
|-------|-------------|
| intake_notes | Transcript + supporting doc synthesis |
| brd_draft | Optional first pass from brd-writer |

## Outputs

- §14-style register embedded in BRD draft
- **confidence**, **unresolved_assumptions**

## Command Contract Alignment

**Parent command:** `/generate-brd`

**Rules:** `.cursor/rules/00-global-governance.mdc`, `.cursor/rules/11-intake-planning-governance.mdc`, `.cursor/rules/10-audit-trail.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`, `.cursor/rules/33-reverse-engineering-quality.mdc` (when cataloging uncertain legacy behavior), `.cursor/rules/32-calculation-catalog.mdc` (amount conflicts — catalog JSON primary after extract)

**Templates:** `.cursor/templates/brd-template.md`, `.cursor/templates/behavior-provenance-template.md`

## Classify Each Item

| Type | ID pattern | Blocking? |
|------|------------|-----------|
| Question | DQ-xxx | Yes / Medium / Low |
| Risk | R-xxx | High / Medium / Low |
| Resolved | ~~DQ-xxx~~ | — |

## Quality Gate

- Every `[INFERRED]` intake item has a DQ or is resolved with source citation
- Every unresolved assumption is registered as `ASSUMPTION-xxx` with `pending_approval` status
- Blocking items listed in audit **Open Items** for human gate

## Audit

Record in parent audit under "Subagents delegated".

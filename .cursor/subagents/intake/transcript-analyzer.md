# Subagent: transcript-analyzer

## Purpose

Extract structured facts, decisions, conflicts, and action items from meeting transcript(s).

## Inputs

| Field | Description |
|-------|-------------|
| transcript_paths | One or more markdown/text transcript files |
| meeting_metadata | Date, attendees, module name (optional) |

## Outputs

- `artifacts/intake/<date>-intake-notes.md` per template
- **confidence**: high | medium | low
- **unresolved_assumptions**: list

## Command Contract Alignment

**Parent command:** `/generate-brd`

**Rules:** `.cursor/rules/00-global-governance.mdc`, `.cursor/rules/11-intake-planning-governance.mdc`, `.cursor/rules/10-audit-trail.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`, `.cursor/rules/33-reverse-engineering-quality.mdc` (when interpreting legacy sources)

**Template:** `.cursor/templates/transcript-intake-notes-template.md`

## Extract

- User journeys described (entry, reversal, errors)
- Business rules stated explicitly or by example
- Integrations and systems named (UDP, IBM i, Apigee, etc.)
- UI/UX decisions (screens, fields, modes)
- Conflicts between speakers → flag for open-questions-cataloger
- Quotes with approximate location (speaker + paragraph) for traceability

## Quality Gate

- No requirement added without transcript evidence or an `ASSUMPTION-xxx` entry pending approval
- All conflicts listed, not silently resolved

## Audit

Record in parent audit under "Subagents delegated".

# BRD Authoring

Use when generating or updating a BRD from intake materials.

## Reference Format

Mirror structure of `config.yaml` → `modules.brd` and `.cursor/templates/brd-template.md`.

## Workflow

1. **Intake first** — transcript-analyzer + supporting-doc-synthesizer before writing prose
2. **Assign IDs** — FR (functional), BR (business rule trace), NFR, INT, DQ, R
3. **Write sections 1–17** — do not skip; use N/A with reason if truly absent
4. **§13 coverage matrix** — every major transcript topic → BRD section
5. **§14 open questions** — unresolved conflicts; mark Blocking explicitly
6. **§15 acceptance criteria** — testable, module-level
7. **§16 test strategy** — unit / integration / E2E mapping
8. **§17 provenance** — record behavior sources, assumptions, conflicts, and approvals

## Traceability Rules

- Each FR row: Priority (Must/Should), Source (transcript date, doc name, legacy BR-xxx)
- Validation chain (FR-030+) in **execution order** with error messages en-US and fr-CA where known
- Mermaid for user journeys in §6 when flows are discussed
- **Inquiry screens:** follow `.cursor/templates/brd-template.md` §7.6 — row driver, optional joins, empty condition, `CALC-*` on amount columns. One BRE **Present Logic** block (or DD subfile field set) must not become a single vague FR. Do not attach list-membership to a sibling maintenance FR.
- **Tables used / DD:** every Input row and display-file field for in-scope programs → FR, `CALC-*`, or explicit Out of Scope (§9.1).

## Status Header

| Status | Meaning |
|--------|---------|
| Draft — Pending Domain Review | After `/generate-brd` — needs PO/domain |
| Accepted | Human approved — safe for `/plan-sprint` |
| Superseded | Replaced by newer version |

## Output Path

`artifacts/brd/<module>-BRD-draft.md` until accepted; then may promote to `artifacts/<Module>-BRD.md` per user direction.

## Do Not

- Copy legacy BRD verbatim without mapping to new module names
- Close DQ-xxx without cited resolution source
- Publish **Accepted** status without user confirmation
- Map `BR-n`–`BR-n+2` → one FR when Present Logic names a file, a formula, or an empty path
- Treat an empty inquiry as “sparse sample” without stating the **row-driver** file and empty condition

## Subagent

`.cursor/subagents/intake/brd-writer.md`

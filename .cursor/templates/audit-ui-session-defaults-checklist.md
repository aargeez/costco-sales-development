# Audit UI session defaults checklist

Fill inside `/implement-ui` or `/fix-issue` audit when the slice **adds or changes** entry, search/criteria, or session fields on an ATI screen. Do not add a command.

**Skill:** `.cursor/skills/ati-session-defaults/SKILL.md`

**Output:** copy into `artifacts/audit/<date>-implement-ui-<task>.md` or `artifacts/audit/<date>-fix-issue-<task>.md`. Blank rows = Open DQ with owner.

## Provenance table (paste from plan/BRD)

| Field | Default when URL empty? | Default rule | source_path | source_locator | confidence |
|-------|-------------------------|--------------|-------------|----------------|------------|
| | | | | | |

If none: **No session defaults — user must select all criteria.**

## Gate checks

| Check | Cite (plan SP, FR/BR) | Result |
|-------|------------------------|--------|
| Plan/BRD cites each defaulted field (table above) | | |
| Lookups separated from `get*Defaults` / field prefill | | |
| `hasRestorable*` hydrates before any defaults (single branch) | | |
| No uncited `useState` seeds for session fields | | |
| WH-user lock cites FR/BR only | | |
| Vitest: empty URL → only cited defaults visible | | |
| Sibling scan summary (skill § Session defaults validate) | | |

Navigation: `.cursor/templates/ati-navigation-checklist.md`  
Layout: `.cursor/templates/audit-ui-layout-checklist.md`

# Audit UI list / table checklist

Fill inside `/implement-ui` or `/fix-issue` audit when the slice **adds or changes** list/results tables, grids with footers, or shared pagination molecules. Do not add a command.

**Skill:** `.cursor/skills/shared-ui-molecules/SKILL.md` (§ Molecule validate + § Sibling scan)

**Output:** copy into `artifacts/audit/<date>-implement-ui-<task>.md` or `artifacts/audit/<date>-fix-issue-<task>.md`. Blank rows = Open DQ with owner.

| Check | Cite (peer screen, manifest, API contract) | Result |
|-------|---------------------------------------------|--------|
| Pagination mode: client `useTablePagination` vs server API pages | | |
| API/domain totals mapped to molecule `count` (if server-side) | | |
| `onPageChange` / `onRowsPerPageChange` match MUI two-arg contract | | |
| `data-testid` on pagination when table is user-visible | | |
| Vitest or E2E asserts pagination label or row total | | |
| In-slice manifest components: required props present, forbidden absent | | |
| Sibling scan table attached (skill § Sibling scan) | | |

Layout (entry/criteria): `.cursor/templates/audit-ui-layout-checklist.md`  
Navigation: `.cursor/templates/ati-navigation-checklist.md`

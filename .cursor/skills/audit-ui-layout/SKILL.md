# Audit UI layout (Sales Audit RF)

Use during `/implement-ui`, `/fix-issue` (frontend leg), and any slice that adds or changes **entry**, **search/criteria**, or session filters on an ATI screen.

Bind UI module from `config.yaml` → `modules.ui`. Complements `.cursor/skills/ati-navigation-state/SKILL.md` (URL restore) — layout is orthogonal.

## Screen classes

| Class | Examples | Structure |
|-------|----------|-----------|
| **Entry / criteria** | SASE, SARS, SART, SATE, SASOI entry | Column-first session (below) |
| **Search** | SAST IND2135 | Criteria rows + include panel below |
| **List / results** | Exception list, SAST results | `ScreenShell`, context header, grid, pagination |
| **Detail / satellite** | Flash detail, explanations | Header + grid/form; Back per nav skill |

## Entry / criteria contract

Reference: `sales-audit-nextjs/src/screens/sase/SaseEntryScreen.tsx`, `sars/SarsEntryScreen.tsx`.

1. `ScreenShell` + `ActionButtonBar` (actions or headerActions per module convention).
2. `DatePickerProvider` wrapping criteria when date pickers are used.
3. Root criteria container:

   ```tsx
   <Stack spacing={2} sx={{ maxWidth: 720 }} data-testid="<module>_entry">
   ```

4. **Single vertical flow** for session: location scope → dates → filters/checkboxes. Do not split criteria and selectable options into **left/right columns** at `md`/`lg`.

### Location XOR (when cited)

When BRD or feature doc requires **location group or warehouse** (not both):

- Bordered `Box` with hint typography (`Enter Location Group or Warehouse`).
- Inner row: `direction={{ xs: 'column', sm: 'row' }}` for **group | or | warehouse only**.
- `data-testid="<module>_location_xor"`.

Do not add XOR when BRD specifies a single locked warehouse only.

**Session defaults:** pre-filled criteria on first paint require Plan/BRD cites — `.cursor/skills/ati-session-defaults/SKILL.md`. Lookups for dropdowns are not defaults.

### Dates

- From/to pair: `Stack direction={{ xs: 'column', sm: 'row' }} spacing={2}` with `fullWidth` date pickers on `sm+`.
- On **360×640** (`xs`), dates stack vertically.

### Selectable options (checkboxes, include flags, thresholds)

- Place **below** the date block in the same outer column stack.
- Use `data-testid="<module>_session_filters"` or `<module>_close_types`.
- **Anti-pattern:** `Stack direction={{ xs: 'column', md: 'row' }}` with criteria in one child and `Checkbox` / `FormControlLabel` in a sibling `Box`/`Stack`.

## Search screens

Reference: `sales-audit-nextjs/src/screens/sast/SastSearchScreen.tsx`.

- Label + control rows (`CriteriaRow` or equivalent).
- Warehouse, date range, transaction criteria, then **Include** panel below the criteria section.
- RF 360×640: sections stack; avoid side-by-side criteria vs include at narrow widths unless mockup/ATI cites it.

## List / detail

- Reuse module `ScreenShell`, context header, selectable-row pattern (rule 04 — no Option column).
- Amount columns: display API values only (`calculation-catalog` skill).
- **Pagination / table footers:** `.cursor/skills/shared-ui-molecules/SKILL.md` — map API totals to shared molecule props; client lists use `useTablePagination`; fill `.cursor/templates/audit-ui-list-table-checklist.md` when the slice changes list tables.

## Provenance

- Mockup or ATI PNG wins for layout when present.
- Otherwise align with peer entry in the same module family (SASE-like inquiry).
- Deviation from this skill requires BRD/FR cite or Open DQ in the audit; do not invent layout from chat.

## Tests

When checklist applies:

- Vitest: entry root `data-testid` present.
- Optional: filters/checkbox section appears **after** date fields in DOM order (`compareDocumentPosition` or test ids).
- Designed UAT: RF 360×640 — filters visible below dates without horizontal scroll for session block.

## Sibling scan (skill-only — no repo script)

Run when `/implement-ui` Layout validate applies or fix-issue label **layout / criteria column drift** applies. Scope: `src/screens/**/*EntryScreen.tsx` and `*SearchScreen.tsx`.

1. **Criteria vs filters column split** — search for `direction={{ xs: 'column', md: 'row' }}` or `lg: 'row'` in those files. For each hit, read the next ~80 lines: if a sibling `Stack`/`Box` contains `Checkbox` or `FormControlLabel` while dates/dropdowns sit in another sibling, flag **`criteria_vs_filters_row_split`** (SASOI-style drift).
2. **Missing entry width** — in entry screens with location group / close-type / session filters (`locationGroup`, `closeTypes`, `_session_filters`, `_close_types`), confirm root criteria `Stack` uses `maxWidth: 720` (or cite exception in checklist). Flag **`entry_missing_maxWidth_720_with_filters`** when absent.
3. **Peer check** — compare touched module to `sase/SaseEntryScreen.tsx`: filters in vertical `*_session_filters` / below dates, not a second column at `md+`.
4. **Record** — table in audit or issue log: path, line, finding kind, fix vs backlog.

## Layout validate (`/implement-ui`)

1. Fill `.cursor/templates/audit-ui-layout-checklist.md` in the command audit.
2. Run **Sibling scan** (above) for the module in scope.
3. Review findings; fix in scope or log Open DQ.

## Related

- `.cursor/rules/04-frontend-nextjs.mdc`
- `.cursor/templates/audit-ui-layout-checklist.md`
- `.cursor/templates/ui-spec-template.md` (Layout section)

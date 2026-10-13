# Shared UI molecules

Use during `/implement-ui`, `/fix-issue` (frontend leg), and any slice that adds or changes **list/results tables**, pagination footers, or imports from `modules.ui` → `src/components/**`.

Bind paths from `config.yaml` → `modules.ui`. Complements `.cursor/skills/audit-ui-layout/SKILL.md` (layout) and `.cursor/skills/ati-navigation-state/SKILL.md` (URL restore).

## Principle

**Domain and API field names stop at the screen boundary.** Shared molecules expose **component prop names** (read the exported `*Props` type). Do not pass OpenAPI/JSON property names as JSX props unless they match the molecule contract.

Example class of bug (not module-specific): API `totalCount` → molecule `count` at the call site.

## Pagination

### Client-side (in-memory rows)

Prefer `useTablePagination` from the UI module’s hooks. Spread into the shared pagination molecule:

- `page`, `pageSize`, `count`, `onPageChange`, `onRowsPerPageChange`

Peer pattern: list screens that paginate `items.slice` locally.

### Server-side (API page + total)

- Map API totals: e.g. `count={response.totalCount ?? 0}` (names differ by design).
- `onPageChange={(_event, newPage) => …}` — **two parameters** (MUI contract).
- Provide `onRowsPerPageChange` (no-op acceptable when server `pageSize` is fixed).
- Keep `page` / `pageSize` in URL or state consistent with the inquiry API.

## Molecule validate (command gate — no repo script)

Required when `/implement-ui` **Shared molecule validate** runs or `/fix-issue` touches list tables. This gate lives in the skill and command audit only.

### 1. Load contract

- Read `config.yaml` → `quality_gates.ui_shared_component_manifest` or `.cursor/templates/ui-shared-component-manifest.yaml`.
- For each in-slice molecule, also open its `*Props` type under `modules.ui` → `src/components/**`.

### 2. Verify in-slice usages

For every JSX usage of a manifest-listed component in files **changed in this slice**:

| Check | Pass criteria |
|-------|----------------|
| Required props | Every `required_jsx_props` from manifest present on the tag (e.g. `count`, `page`, `pageSize`, `onPageChange`, `onRowsPerPageChange`). |
| Forbidden props | No `forbidden_jsx_props` on the tag (e.g. `totalCount` on `AuditTablePagination`). |
| Handler shape | `onPageChange` uses **two** parameters before `=>` (e.g. `(_event, newPage)`), not a single `(page) =>`. |
| Boundary mapping | API/DTO totals mapped at call site (`count={data?.totalCount ?? 0}`), not passed as wrong prop name. |

Record **file:line** and PASS/FAIL per usage in the audit.

### 3. Sibling scan (repo-wide, read-only)

Scope: `modules.ui` → `src/**/*.tsx`. Do not edit out-of-scope files in `/fix-issue`.

For **each** manifest component:

1. Search usages: `<ComponentName` (ripgrep or IDE).
2. Flag any tag with a **forbidden** prop from the manifest.
3. Flag **single-argument** `onPageChange` on pagination molecules.
4. Attach a findings table to the audit or issue log (file, line, finding, PASS/FAIL).

Any FAIL in slice files → **FAIL** refine loop (max 3). Sibling FAIL outside slice → record in audit; fix only if in scope.

### 4. Checklist and tests

1. Fill `.cursor/templates/audit-ui-list-table-checklist.md` in the command audit (include sibling scan summary).
2. When pagination is user-visible: Vitest with `src/test/paginationLabel.ts` or document gap.

## Tests

When pagination is user-visible:

- Vitest: render list with mocked API totals; assert displayed range via `src/test/paginationLabel.ts` helper, or document gap in audit.
- Optional `data-testid="*_table_pagination"` on the shared pagination molecule.

## References

- Operator doc: `.cursor/docs/ui-shared-component-gates.md`
- Manifest template: `.cursor/templates/ui-shared-component-manifest.yaml`
- Rule: `.cursor/rules/04-frontend-nextjs.mdc` (shared molecule bullet)

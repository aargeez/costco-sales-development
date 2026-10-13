# UI shared component gates (operator guide)

Portable factory guidance for **list/results tables** and other wrappers under the UI module’s `src/components/**`. Not tied to any single product screen or ATI.

## Incident class: pagination label `1–NaN of undefined`

**Symptoms:** MUI table footer shows a broken range while rows render correctly.

**Root causes (generic):**

1. **API vs molecule prop names** — Paginated APIs often expose `totalCount`. Shared pagination wrappers align with MUI and use **`count`**. Passing `totalCount` as a JSX prop on the wrapper is ignored at runtime; `count` stays undefined.
2. **MUI handler shape** — `onPageChange` must be `(event, newPage) => void`. A one-argument handler receives the **event** as the first parameter, breaking page navigation.
3. **Missing factory gates** — Layout/navigation skills did not cover molecule wiring; Vitest did not assert pagination labels; audit-time checks were not required.

**Prevention:** **Shared molecule validate** on `/implement-ui` and `/fix-issue` when list tables change — procedure in `.cursor/skills/shared-ui-molecules/SKILL.md` § Molecule validate.

## When gates apply

| Command | Trigger |
|---------|---------|
| `/implement-ui` | Slice adds or changes list/results table, grid footer, or shared molecule usage |
| `/fix-issue` | Frontend leg touches list table or pagination; sibling-scan label **shared molecule / pagination drift** |

## Artifacts

| Artifact | Purpose |
|----------|---------|
| `.cursor/skills/shared-ui-molecules/SKILL.md` | Contracts, **Molecule validate** gate, sibling scan |
| `.cursor/templates/ui-shared-component-manifest.yaml` | Wrapper rules (required/forbidden props) |
| `.cursor/templates/audit-ui-list-table-checklist.md` | Paste into command audit |

## Running the gate (agents)

No standalone repo script. Follow **Molecule validate** in the shared-ui-molecules skill:

1. Load manifest + molecule `*Props` types.
2. Verify changed files (required/forbidden props, handler shape).
3. Sibling scan all manifest components under `modules.ui`.
4. Complete list-table checklist; record findings table in audit **Validation**.

Any FAIL on in-slice usages blocks Done until refined.

## Extending the manifest

Add a `components.<ExportName>` entry:

- `required_jsx_props` — must appear on each usage.
- `forbidden_jsx_props` — common mistaken aliases (e.g. API field names).
- `notes` — human rationale for implementers.

Keep entries **molecule-centric**, not module-centric. Point `quality_gates.ui_shared_component_manifest` in `config.yaml` at the template path.

## Testing expectation

When a slice introduces `*_table_pagination` or a manifest-listed molecule on a list screen, Vitest should assert the displayed range (see `modules.ui` → `src/test/paginationLabel.ts`) or document an explicit gap in the audit.

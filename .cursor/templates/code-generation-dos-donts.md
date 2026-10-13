# Dos and Don'ts

## Do

- Follow output formats in `.cursor/templates/README.md`
- Update `docs/test-matrix.md` when adding rule coverage
- Record test runs in `artifacts/test-results-{date}.md`
- Update `docs/harness/progress.md` after significant slices
- Use existing ADR pattern in `docs/adr/` for architecture decisions
- For requirements-scoped edits, use `/fix-issue` cited-requirements + `incremental-change-plan-template.md` before coding
- Keep diffs minimal; match existing package/layer/naming patterns in the touched area
- For amount math, load `artifacts/calculations/<module>-calculations.json`, cite `CALC-*`, implement formula/steps, update `parity` / `modern_impl`
- On amount errors or source conflict, treat that JSON as the **primary source** (rule 32)

## Don't

- Invent new report formats not already in `docs/` or `artifacts/`
- Add domain-specific skills — use rules + existing code as reference
- Create user stories or reverse-engineering artifacts unless asked
- Change out-of-scope files “while you’re here”
- Invent business rules, UI copy, error semantics, auth decisions, or **formulas** without a cited source (rule 22 / rule 32)
- Rewrite modules or introduce new architecture when a localized edit satisfies the requirement
- Recompute calculation-catalog math in the UI
- Code amount changes when the module calculation catalog is missing — run `/extract-calculations` first
- Rename OpenAPI query or JSON names in the client (`from`/`to` vs `fromDate`/`toDate`, `rows` vs `lines`, amount field aliases such as `overShort` vs `overShortAmount`)
- Strip `returnQs` to a subset when navigating Back or on cross-screen handoff — keep the full cited session, selection, and filters/inputs per rule 04 and the navigation checklist
- Add a green-screen Option column for row selection — use the module’s existing selectable-row pattern
- Write a new screen Vitest with bare `render(` when the UI module already has a themed render helper (`renderAudit` only if that export already exists)
- Add a Flyway version that already exists under `db/migration/`, or a seed that overwrites another module’s golden keys without a collision note in the audit

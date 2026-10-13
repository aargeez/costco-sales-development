# ATI session defaults (provenance)

Use during `/plan-sprint`, `/plan-governed-stories`, `/implement-ui`, `/fix-issue` (frontend leg), and any slice that adds or changes **entry**, **search/criteria**, or session fields on an ATI screen.

Bind paths from `config.yaml` → `modules.ui`. Works with `.cursor/skills/ati-navigation-state/SKILL.md` (URL hydrate) and `.cursor/skills/audit-ui-layout/SKILL.md` (layout). Pagination API totals: `.cursor/skills/shared-ui-molecules/SKILL.md`.

## Definitions

| Term | Meaning | Plan/BRD cite? |
|------|---------|----------------|
| **Lookups** | `listWarehouses`, location groups, code lists — options in dropdowns | No |
| **Hydrate** | Restore criteria from `useSearchParams()` when `hasRestorable*` | Cite `returnQs` keys (navigation checklist) |
| **Role-lock** | WH user home warehouse read-only / scoped | Yes — WH scope FR/BR |
| **Session default** | Pre-filled date, loc grp, checkbox, threshold, or any `get*Defaults` response field applied when URL is empty | **Yes** — FR/BR or approved plan SP |
| **Client seed** | Non-empty `useState` initial for session fields (e.g. `default*CloseTypes()`, literal threshold) | **Yes** — same as session default |

**Silence in Plan/BRD = no default** — leave empty/null until the user selects (except lookups and cited role-lock).

**Precedence:** approved sprint/governed plan SP → module BRD FR/BR → approved story AC.

## Entry load recipe

Single async branch (same `then` as lookups); do not split defaults and hydrate across competing effects.

```text
Promise.all([lookups..., optional get*Defaults when BRD cites defaults endpoint])
  .then(() => {
    load lookup rows into state
    if (hasRestorable*(searchParams)) {
      apply read*EntryHydration(searchParams)  // all cited keys
    } else {
      apply ONLY fields listed in provenance table (below)
      // do not spread full defaults response
    }
  })
```

- Call `get*Defaults` only when the approved plan/BRD cites that endpoint or default rule for at least one field.
- From the defaults response, apply **only** fields that appear in the provenance table.

## Provenance table (required in audit for in-slice entry/search)

| Field | Default when URL empty? | Default rule | source_path | source_locator | confidence |
|-------|-------------------------|--------------|-------------|----------------|------------|
| (example) fromDate | yes | system date | artifacts/brd/... | FR-011 | explicit |

If the slice has **no** cited defaults, state explicitly: **“No session defaults — user must select all criteria.”**

## Session defaults validate (command gate — no repo script)

Required when `/implement-ui` **Session defaults validate** runs or `/fix-issue` touches entry/search/criteria.

### 1. In-slice review

For each changed `*EntryScreen*`, `*SearchScreen*`, `*CriteriaScreen*`:

| Check | PASS |
|-------|------|
| Provenance table complete in audit | Every applied default has a row |
| Lookups only | Lists load without pre-filling uncited criteria |
| `hasRestorable*` | Hydrate runs in same branch before any defaults |
| No blind `else { setFromDate(defaults...); setEverything...)` | Else applies only cited fields |
| `useState` seeds | No uncited non-empty session literals |

Any FAIL on in-slice files → refine loop (max 3).

### 2. Sibling scan (read-only)

Scope: `modules.ui` → `src/screens/**/*Entry*.tsx`, `*Search*.tsx`, `*Criteria*.tsx`.

1. Search `get\w+Defaults` — note file; flag if `else` branch applies uncited fields (backlog unless in slice).
2. Search `useState(` with session-related initial literals (`true`, `'50'`, `default*CloseTypes()`, date literals) without module cite in audit.
3. Record table in audit: file, finding kind (`uncited defaults else`, `useState seed`, `defaults API uncited`), in-slice vs backlog.

Do not edit out-of-scope modules in `/fix-issue`.

## Tests

When checklist applies:

- Vitest: render entry with **empty** `URLSearchParams` → visible values match provenance table only; uncited fields empty/disabled.
- With restorable URL → hydrate values visible (navigation skill).

## References

- Operator doc: `.cursor/docs/ati-session-defaults-gates.md`
- Checklist: `.cursor/templates/audit-ui-session-defaults-checklist.md`
- Rule: `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`

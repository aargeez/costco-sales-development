# ATI navigation state (URL round-trip)

Use this skill during `/implement-ui`, `/fix-issue` (frontend leg), and any ATI screen with **Back**, row selection that must survive return, or cross-screen handoff.

Bind UI module root from `config.yaml` → `modules.ui`.

## Three-legged contract

1. **Forward** — Put session criteria (warehouse, dates, filters, checkboxes) on the query string when navigating to list/detail/satellite. Carry `returnTo` and nested `returnQs` when leaving a hub screen.
2. **Back** — Use explicit `build*EntryHref`, `build*SearchHrefFromParams`, or `resolve*BackHref` from the module’s `*ReturnTo.ts`. Do **not** use bare `router.push('/module')` for Back. Do **not** use `router.back()` for ATI criteria restore (SATA protect-mode caller exit is the documented exception).
3. **Land** — Entry and search/criteria screens **hydrate** controlled fields from `useSearchParams()`. When there are **no** restorable params, apply **only** Plan/BRD-cited session defaults (not blind `get*Defaults` apply-all). See `.cursor/skills/ati-session-defaults/SKILL.md`.

Reference implementations:

- Entry: `sales-audit-nextjs/src/screens/sase/saseReturnTo.ts` + `SaseEntryScreen.tsx`
- Search Back: `sales-audit-nextjs/src/screens/sast/sastReturnTo.ts` + `SastSearchResultsScreen.tsx`

## Entry hydration recipe

In `*ReturnTo.ts` for the module:

- Export `*_ENTRY_KEYS` (or reuse inquire keys)
- `hasRestorable*EntryParams(source: URLSearchParams): boolean`
- `read*EntryHydration(source): Partial<criteria>`
- `build*EntryHref(source: URLSearchParams | string): string`

In the entry screen’s **single** async load (`Promise.all` / defaults fetch):

```text
if (hasRestorable*(searchParams)) {
  apply read*EntryHydration(searchParams) to state
} else {
  apply ONLY session fields cited in Plan/BRD (see .cursor/skills/ati-session-defaults/SKILL.md)
}
```

Do **not** apply defaults in one effect and URL restore in another unless the restore effect runs **after** defaults and wins — prefer one branch inside the same `then` block.

## Search / criteria screens

- Initialize `useState` from `searchParams` where practical.
- Add `useEffect(() => { ... }, [searchParams])` to re-apply **all** cited restore keys when the URL changes (client component may stay mounted across `router.push`).
- Export restore key list from `*ReturnTo.ts` when shared (see `SEARCH_RESTORE_KEYS` in `sastReturnTo.ts`).

## Anti-patterns

| Anti-pattern | Why it fails |
|--------------|--------------|
| `router.push('/sasoi')` on Back from results | Clears criteria |
| `strip*ReturnParams` dropping session or `selected` | Back loses row selection |
| Restore gated on `!returnTo` while WH/from/to are on URL | Summary Back with handoff context skips hydrate |
| Vitest only asserts `router.push` URL | Parent never re-rendered; UI still blank |
| List `load()` depends on `selected` in URL | Spinner clears grid on row click |

**Out of scope for Back restore:** Exit / Cancel to `/saro` or audit menu — intentional leave.

## Tests

**Vitest (required when checklist documents Back):**

1. Child screen: click Back → assert `push` target includes query string with cited keys.
2. Entry/search: render with that query (simulate return) → assert **visible** field values (dates, WH, checkboxes), not only URL.

**Playwright:** Follow `.cursor/skills/playwright-e2e/SKILL.md` — assert checklist **returnQs shape** keys on URL or in controls after Back.

## Module discovery

1. Search `src/screens/<module>/*ReturnTo.ts` or `*HandoffParams.ts`.
2. If missing, add helpers before editing screens (mirror `saseReturnTo` / `sastReturnTo`).
3. Fill `.cursor/templates/ati-navigation-checklist.md` in the command audit.

## Sibling scan (skill-only — no repo script)

Run this procedure before closing `/implement-ui` (navigation validate) or when fix-issue sibling scan label **nav / bypass / returnQs** applies. Scope: `config.yaml` → `modules.ui` → `src/screens/**/*.tsx`.

1. **Find Back handlers with bare paths** — search for `router.push('/` under `src/screens` where the handler is Back (not Exit/Cancel):
   - Match lines with `id: 'back'` or `label: 'Back'` within ±5 lines of `router.push(`.
   - Flag `router.push('/<path>')` with **no** `?` in the string (query restore missing).
   - **Allowlist (not findings):** targets `/saro`, `/audit`, `/` menu root when labeled Exit/Cancel or intentional leave.
2. **Find `router.back()` on ATI criteria** — search `router.back()` in `*EntryScreen.tsx`, `*SearchScreen.tsx`, `*ResultsScreen.tsx`, `*ListScreen.tsx`; flag unless cited SATA protect-mode caller exit or documented exception in module `*ReturnTo.ts`.
3. **Strip helpers** — in the touched module and siblings, grep `strip*ReturnParams`; confirm `selected`, session keys, and cited `returnQs` keys are not dropped on forward handoff.
4. **Record** — table in the command audit or `artifacts/issues/<date>-nav-returnqs-sibling-scan.md`: file, line, finding kind (`Back bare path`, `router.back`, `strip returnQs`), in-scope fix vs backlog.

Review findings labeled **Back bare path**; fix in-scope screens or log backlog; do not edit out-of-scope ATIs in the same slice unless the command expands scope.

## Related rules and templates

- `.cursor/rules/04-frontend-nextjs.mdc`
- `.cursor/templates/ati-navigation-checklist.md`
- `.cursor/rules/12-production-fix-governance.mdc` (sibling scan label: nav / bypass / returnQs)

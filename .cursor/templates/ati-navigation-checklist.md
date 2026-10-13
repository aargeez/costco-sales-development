# ATI navigation checklist

Fill this inside the existing `/implement-ui` or `/fix-issue` cited-requirements audit when the screen has a back stack, row selection that must survive Back, or a cross-screen handoff. Do not add a command. Do not invent routes, query names, or bypass rules.

**Skill:** `.cursor/skills/ati-navigation-state/SKILL.md`

**Output:** copy the table into `artifacts/audit/<date>-implement-ui-<task>.md` or `artifacts/audit/<date>-fix-issue-<task>.md`. Leave a row blank only as an Open DQ with an owner.

| Check | Cite (BRD, feature note, or existing code) | Result |
|-------|-----------------------------------------------|--------|
| Back levels (how many screens Back restores) | | |
| `returnQs` shape (each key, cite, and kind: session / selection / filter or input) | | |
| `returnQs` keeps the full session, not a stripped subset | | |
| Selected row or record id is in `returnQs` or `withSelected` | | |
| Back handler uses `build*Href` / `resolve*BackHref` (not bare `router.push('/module')`) | | |
| Entry/search hydrates **all** keys listed in `returnQs shape` | | |
| API defaults gated on `hasRestorable*` (single-branch load; no async overwrite) | | |
| Uncited session defaults not applied — only Plan/BRD-cited fields (`ati-session-defaults` skill) | | |
| `searchParams` change re-syncs controlled fields when component can stay mounted | | |
| Vitest proves **UI restore** after return URL (not only `router.push` argument) | | |
| E2E asserts checklist keys when journey includes Back (Playwright skill) | | |
| List fetch is not tied to the selection URL unless the cited screen already does that | | |
| Bypass runs only when `invocationSource` (or the cited catalog/BRD field) is present | | |
| Cross-ATI handoff target (source screen → destination route) | | |
| Helpers reused (`*ReturnTo`, `withSelected`) when the module already has them | | |
| Query names on the handoff URL match OpenAPI (Phase 0 name table) | | |
| Sibling scan (skill § Sibling scan) recorded for this module | | |

If no source states the destination, register `ASSUMPTION-*` or an Open DQ. Do not guess `/sast` versus `/sast/results`.

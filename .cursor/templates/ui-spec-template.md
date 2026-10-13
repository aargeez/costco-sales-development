# UI Specification Template

## Screen / Route
## User Goals
## Component Hierarchy
## State Matrix (loading/empty/error/success)
## Accessibility Requirements
## API Dependencies
## Acceptance Criteria Mapping

## Layout (fill when entry, search/criteria, or session filters change)

Cite BRD FR, ATI PNG, or peer screen. Skill: `.cursor/skills/audit-ui-layout/SKILL.md`

| Field | Value |
|-------|-------|
| Screen class | entry / search / list / detail |
| Entry `maxWidth: 720` | yes / cited exception |
| Location XOR | present / N/A per cite |
| Column-first session (360×640) | filters below dates |
| Test ids | `<module>_entry`, `*_session_filters` or `*_close_types` |
| Peer reference | e.g. SASE entry |

Checklist: `.cursor/templates/audit-ui-layout-checklist.md`

## Navigation (fill only when the screen has back-stack or handoff)

Cite BRD, feature notes, or an existing route. Do not invent a destination.

| Field | Value |
|-------|-------|
| Back levels | |
| `returnQs` fields kept (session + selection + filters/inputs) | List each key with cite and kind — same as checklist row **returnQs shape**; do not invent a global schema |
| Back handler | `build*Href` / `resolve*BackHref` — not bare module path |
| Entry/search hydrate | all keys in `returnQs shape`; defaults gated on `hasRestorable*` |
| Session defaults on empty URL | Plan/BRD table per `ati-session-defaults` skill — or “no defaults” |
| searchParams re-sync | criteria screen re-applies fields when URL changes |
| Tests | Vitest UI restore; E2E when journey has Back |
| Bypass vs restore | bypass only with cited `invocationSource` |
| Cross-ATI handoff target | source screen → destination route (cited) |
| Helpers reused | `*ReturnTo` / `withSelected` if the module already has them |

Checklist: `.cursor/templates/ati-navigation-checklist.md` — skill: `.cursor/skills/ati-navigation-state/SKILL.md`

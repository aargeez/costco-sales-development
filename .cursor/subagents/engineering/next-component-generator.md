# next-component-generator

## Purpose
UX/mockup → Next pages/components/hooks.

## Parent
frontend-agent / change-agent

## Must / Must NOT
- Must: complete the bounded task with structured IO and confidence.
- Must NOT: Do not invent; return gaps to parent.

## Inputs
- UI spec
- OpenAPI types

## Outputs
- TSX/TS
- Confidence: high | medium | low
- Unresolved assumptions list

## Failure modes
Incomplete input → partial result with explicit gaps.

## Quality gate
Loading/empty/error states. No Option column for row selection; use the module’s existing selectable-row pattern. Layout and wrap go through `sx` on the MUI component that owns them. Entry/search/criteria: follow `.cursor/skills/audit-ui-layout/SKILL.md`; name peer reference screen in parent audit. List/results tables: read shared molecule `*Props` before emitting JSX; follow `.cursor/skills/shared-ui-molecules/SKILL.md` (map API totals at screen boundary; MUI two-arg pagination handlers). Entry/search: follow `.cursor/skills/ati-session-defaults/SKILL.md` — do not emit blind `else { apply get*Defaults }`; apply only Plan/BRD-cited fields; no uncited `useState` seeds. When the screen has Back or handoff: follow `.cursor/skills/ati-navigation-state/SKILL.md`; reuse the module’s `*ReturnTo` / `withSelected` helpers; build handoff URLs with the full cited `returnQs` (do not strip keys); hydrate entry/search from URL with defaults gated on `hasRestorable*`. List fetch must not depend on the selection URL unless the cited screen already does that. Layout work stays inside the parent `/implement-ui` or `/fix-issue` cited-requirements step.

## Ask-backs
Register Open DQ for parent if unclear. Do not invent. Do not STOP the parent except human gates.

## Loop
Plan → Act → Validate → Refine (max 3) unless parent is intake (then Observe → Reflect).

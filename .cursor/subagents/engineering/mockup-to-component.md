# Subagent: mockup-to-component

## Purpose

Convert UI mockups, screenshots, or Figma references into Next.js RF components matching repo conventions.

## Inputs

| Field | Description |
|-------|-------------|
| mockup_path | `UI/image (*.png)`, `artifacts/intake/supporting/`, or Figma URL |
| feature_ref | `docs/feature.md` section or BRD FR-xxx |
| screen | Scan (Screen 1) or Detail (Screen 2) |

## Approach (pick one)

1. **Local mockup** — read PNG from repo; match layout, spacing, labels to existing MUI patterns; map screen class and column order per `.cursor/skills/audit-ui-layout/SKILL.md` (default inquiry entry → SASE column unless mockup shows otherwise)
2. **Figma MCP** — if user provides figma.com URL, use Figma MCP `get_design_context` then adapt to project stack (Next.js 14, MUI 6, existing `src/components/rf/`)
3. **Feature doc only** — implement from `docs/feature.md` when no visual available

## Outputs

- TSX/TS under `config.yaml` → `modules.ui` (`src/` as in that module)
- i18n keys in `src/i18n/messages.ts` (en-US + fr-CA)
- `data-testid` on inputs, buttons, alerts
- **confidence**, **unresolved_assumptions**

## Enforce

- `.cursor/skills/audit-ui-layout/SKILL.md` (entry/search structure)
- `.cursor/rules/04-frontend-nextjs.mdc`
- `.cursor/rules/05-api-contracts.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- Reuse existing components (`NumericStepper`, scan input, mode toggle) before creating new ones

## Command Contract Alignment

**Parent command:** `/implement-ui`

**Rules:** `.cursor/rules/04-frontend-nextjs.mdc`, `.cursor/rules/05-api-contracts.mdc`, `.cursor/rules/10-audit-trail.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`, `.cursor/rules/32-calculation-catalog.mdc` (amount columns — catalog JSON primary; display only)

**Templates:** `.cursor/templates/plan-act-validate-refine-loop.md`, `.cursor/templates/audit-trail-template.md`

## Quality Gate

- 360×640 RF viewport
- No hardcoded user-visible strings — i18n only
- Matches OpenAPI client types for API calls
- List mockups: note pagination mode (client in-memory vs server API pages) for parent shared-molecule checklist
- Entry mockups: mark each field **empty on first paint** vs **pre-filled**; pre-filled requires Plan/BRD cite for parent session-defaults checklist
- UI copy and workflow behavior cite the supplied mockup, BRD, or feature source

## Delegates to

- `next-component-generator` for implementation details after layout is resolved

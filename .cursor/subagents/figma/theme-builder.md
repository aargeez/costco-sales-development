# Subagent: Theme Builder

## Purpose
Transform extracted Figma design tokens into a production MUI v6 theme — palette, typography, component overrides, and shadow definitions — ready for `ThemeProvider` integration.

## Required Inputs
- `design_tokens`: from figma-design-extractor (colors, typography, shadows, spacing).
- `component_inventory`: from figma-design-extractor (to derive component-level overrides).

## Outputs
- `src/theme/palette.ts` — color palette mapping Figma color styles to MUI palette + custom `designTokens` export.
- `src/theme/typography.ts` — `TypographyOptions` mapping Figma type scale (H1-H6, body, caption, overline, button).
- `src/theme/components.ts` — `Components<Theme>` overrides for MUI components used in the design (Button, TextField, Chip, Table, AppBar, etc.).
- `src/theme/index.ts` — `createTheme()` entry point re-exporting palette, typography, components.

## Construction Rules
1. **Palette**: Map every named Figma color to MUI palette slots (`primary`, `secondary`, `text`, `background`, `grey`, `info`, etc.) or custom `designTokens` for non-standard colors.
2. **Typography**: Map each Figma text style to the closest MUI variant. Font family MUST be `"Roboto", sans-serif` unless design specifies otherwise.
3. **Component Overrides**: For each MUI component used in the design, generate `styleOverrides` matching Figma's default styling (border-radius, padding, colors, hover states).
4. **Shadows**: Map Figma elevation effects to MUI-compatible box-shadow strings in `designTokens.shadows`.
5. **No hardcoded values in components**: All theme values MUST be defined here so components reference `theme.palette.*` or `theme.typography.*`.

## Validation Checks (self-verify before returning)
- `createTheme()` call compiles without type errors.
- Every Figma color token appears in palette or designTokens.
- Typography variants cover at minimum: h3, h5, body1, body2, caption, overline, button.
- At least 3 component overrides exist in `components.ts`.

## Failure Modes
- Missing font-weight mapping → default to `400` (regular) and flag.
- Unknown Figma color name → place in `designTokens.colors` with descriptive key and flag.
- Escalation: if design tokens are incomplete, return partial theme with explicit TODOs.

## Quality Gate
Parent accepts when:
- All four theme files exist and export correctly.
- Zero TypeScript errors in theme files.
- Palette covers all colors from the Figma extraction.
- Typography covers the primary type scale.

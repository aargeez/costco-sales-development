# Subagent: MUI Component Generator

## Purpose
Generate production-ready, reusable MUI v6 React components from the Figma component inventory, following atomic design hierarchy, using `styled()` API, and consolidating all variants into single components with typed props.

## Required Inputs
- `component_inventory`: from figma-design-extractor (names, variants, properties, hierarchy).
- `design_tokens`: resolved theme reference (palette, typography, component overrides).
- `screenshots`: Figma screenshots for visual accuracy reference.
- `asset_manifest` (optional): SVG/icon asset URLs for inline icons.

## Outputs
- `src/components/atoms/<Name>/index.tsx` — primitive components (Button, TextField, Chip, IconButton, ToggleButton, etc.).
- `src/components/molecules/<Name>/index.tsx` — composed components (SearchField, FilterDropdown, Pagination, etc.).
- `src/components/organisms/<Name>/index.tsx` — complex sections (NavBar, SubNav, EntryForm, WorklistTable, etc.).
- `src/components/templates/<Name>/index.tsx` — full page composition templates.
- `src/components/index.ts` — barrel exports for all components.
- `src/types/index.ts` — re-exported prop interfaces.

## Generation Rules

### Variant Consolidation
For each Figma component with variants (e.g., Button with Primary/Secondary/Disabled/Loading states):
1. Create ONE React component, not separate files per variant.
2. Define a TypeScript `interface <Name>Props` with discriminated props for variants.
3. Map Figma variant properties to union types: `variant: 'contained' | 'outlined' | 'text'`.
4. Map Figma boolean toggles to boolean props: `showIcon`, `isLoading`, `disabled`.
5. Handle CSS states (hover, active, focus) via `styled()` `&:hover` / `&:active` selectors.

### Styling
- Use `styled(MuiComponent)(({ theme }) => ({...}))` pattern — theme-aware.
- Reference `theme.palette.*`, `theme.typography.*`, `designTokens.*` — never hardcode hex.
- Use `shouldForwardProp` when custom props would leak to DOM.

### State Management
- Interactive components MUST use `useState` for internal state.
- Implement controlled component pattern: accept `value` + `onChange`, fall back to internal state.
- Use `useEffect` only when side effects are genuinely needed.

### Prop Mapping Table (Figma → React)
| Figma Property | React Prop | Type |
|---|---|---|
| Show Icon | `showIcon` | `boolean` |
| Icon Position | `iconPosition` | `'start' \| 'end'` |
| State: Loading | `isLoading` | `boolean` |
| State: Disabled | `disabled` | `boolean` |
| Variant | `variant` | union of variant strings |
| Size | `size` | `'small' \| 'medium' \| 'large'` |
| Label / Text | `children` or `label` | `ReactNode \| string` |

### File Conventions
- `'use client'` directive on every component with hooks.
- Default export + named export for each component.
- Props interface exported alongside component.

## Validation Checks (self-verify)
- Every component has a `<Name>Props` interface.
- No raw `className="..."` with utility classes.
- No inline `style={{}}` except for dynamic computed values.
- `styled()` used at least once per component file.
- All imports resolve to `@/theme`, `@/components`, or MUI packages.

## Failure Modes
- If a Figma component has no clear MUI equivalent → build from `Box` or `Paper` + `styled()` and flag.
- If variant logic is ambiguous → implement the most common variant as default, document alternatives.
- Escalation: return partial component set with explicit `// TODO: resolve variant` markers.

## Quality Gate
Parent accepts when:
- All atoms compile without TypeScript errors.
- Each component has typed props, `styled()` usage, and `'use client'` where needed.
- Barrel export in `src/components/index.ts` covers all generated components.
- Atomic hierarchy is correct (no organisms in atoms folder).

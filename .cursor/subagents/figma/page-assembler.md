# Subagent: Page Assembler

## Purpose
Wire generated components into Next.js pages, layouts, and app-level configuration — producing a runnable application with mock data that visually matches the Figma design.

## Required Inputs
- `page_structure`: from figma-design-extractor (hierarchical layout of which organisms compose the page).
- `component_manifest`: list of generated components from mui-component-generator.
- `theme_path`: path to generated theme (for ThemeProvider wiring).
- `screenshots`: Figma page-level screenshots for layout validation reference.

## Outputs
- `src/pages/_app.tsx` — ThemeProvider + CssBaseline wrapper.
- `src/pages/_document.tsx` — Roboto font link, emotion insertion point, HTML lang.
- `src/pages/index.tsx` — main page composing organisms with mock data and interactive state.
- `public/assets/` — placeholder SVGs/images for logos and icons.
- `package.json` — with correct dependencies (MUI v6, emotion, Next.js, React 19, TypeScript).
- `tsconfig.json` — strict mode, path aliases, Next.js plugin.
- `next.config.ts` — transpile packages, strict mode.
- `.eslintrc.json` — Next.js lint config.

## Assembly Rules

### App Configuration
1. `_app.tsx` wraps `Component` with `ThemeProvider` + `CssBaseline`.
2. `_document.tsx` adds Roboto font from Google Fonts CDN and emotion insertion meta tag.
3. No `getInitialProps` on `_app` unless SSR data is required.

### Page Composition
1. Read the `page_structure` tree to determine organism ordering.
2. Create page component that composes NavBar → SubNav → Content Area → organisms.
3. Generate realistic mock data matching the Figma design (dates, item numbers, descriptions, prices).
4. Wire `useState` for interactive elements (form inputs, table pagination, view toggles).
5. Connect event handlers between organisms (e.g., EntryForm.onAddToWorklist → updates WorklistTable items).

### Mock Data Contract
- Mock data MUST match the visual structure shown in Figma screenshots.
- Use realistic values — not "Lorem ipsum" or empty placeholders.
- At least 5-10 rows for table data to demonstrate pagination.

## Validation Checks
- `npm run dev` starts without errors.
- `npx tsc --noEmit` passes.
- All imports resolve (no missing modules).
- ThemeProvider wraps the entire app.

## Failure Modes
- If component imports fail → check barrel exports and path aliases.
- If mock data shape doesn't match component props → adjust data or flag prop mismatch.
- Escalation: return page with working layout but flagged data gaps.

## Quality Gate
Parent accepts when:
- `_app.tsx`, `_document.tsx`, and `index.tsx` all exist and render.
- TypeScript compiles cleanly.
- Page visually composes all major sections from the Figma design.
- Mock data is populated and realistic.

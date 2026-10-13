# Figma Design System Agent (Orchestrator)

## Goal
Transform a Figma Design System into a production-ready, reusable Next.js project with MUI v6, orchestrating extraction, theming, component generation, validation, and iterative refinement until quality gates pass.

## Runtime Configuration (from command or human)
- `figma_url`: required; Figma file URL with `node-id` parameter.
- `file_key`: extracted from URL; Figma file key.
- `node_id`: extracted from URL; target frame/page node ID.
- `project_name`: required; output project folder name.
- `reference_project` (optional): path to existing project for folder structure reference only (never copy code).
- `model_label`: required; LLM model name used (for validation report labeling).
- `skip_llm_eval` (optional): boolean; skip LLM-based validation pass (default: false).

## Core Policy
- This agent acts as Senior Frontend Architect and Design Systems Engineer.
- ALL output MUST comply with `.cursor/rules/21-figma-to-nextjs-mui.mdc`.
- Reference projects are for folder structure inspiration ONLY — never clone code.
- Design system is a customized MUI v6 — use `styled()` API and theme overrides exclusively.

## Delegated Subagents (execution order)

### Phase 1: Extraction
- **figma-design-extractor** (`.cursor/subagents/figma/figma-design-extractor.md`)
  - Input: `figma_url`, `file_key`, `node_id`
  - Output: design tokens, component inventory, asset URLs, screenshots
  - Tools: Figma MCP (`get_metadata`, `get_design_context`, `get_variable_defs`)

### Phase 2: Theme Construction
- **theme-builder** (`.cursor/subagents/figma/theme-builder.md`)
  - Input: design tokens from Phase 1
  - Output: `src/theme/` files (palette.ts, typography.ts, components.ts, index.ts)

### Phase 3: Component Generation
- **mui-component-generator** (`.cursor/subagents/figma/mui-component-generator.md`)
  - Input: component inventory, theme tokens, Figma screenshots
  - Output: atomic components (atoms → molecules → organisms → templates), barrel exports, type exports

### Phase 4: Page Assembly
- **page-assembler** (`.cursor/subagents/figma/page-assembler.md`)
  - Input: component tree, page layout from Figma frame
  - Output: `src/pages/` files (_app.tsx, _document.tsx, index.tsx), mock data, page templates

### Phase 5: Validation & Refinement
- **project-validator** (`.cursor/subagents/figma/project-validator.md`)
  - Input: generated project path, model label
  - Output: validation report (static + LLM scores), findings list
  - Tool: `validator/validate.py`
- **refinement-agent** (`.cursor/subagents/figma/refinement-agent.md`)
  - Input: validation findings, source files
  - Output: patched source files addressing findings

## Decision Logic
- Proceed through phases sequentially; each phase depends on prior output.
- After Phase 5 validation: if combined score < 7.0 OR any critical finding exists, trigger refinement loop.
- Refinement loops: max 3 iterations. After 3 iterations, escalate remaining findings to human.
- If Figma MCP returns "too large" for a node, automatically split into sub-nodes and fetch individually.

## Execution Loop
```
1. EXTRACT  → Figma MCP → design tokens + component inventory
2. THEME    → Build MUI theme from tokens
3. GENERATE → Create atomic components (atoms → organisms)
4. ASSEMBLE → Wire pages, templates, _app, mock data
5. VALIDATE → Run static + LLM validation
6. REFINE   → Fix findings (if score < 7.0 or critical issues)
7. REPEAT   → Steps 5-6 (max 3 refinement cycles)
8. EXPORT   → Final project + validation report JSON
```

## Memory (retained across iterations)
- Figma design tokens and component inventory
- Generated file manifest
- Validation scores per iteration (for trend tracking)
- Open findings and resolution status
- Refinement patch history

## Exit Criteria
- `npx tsc --noEmit` passes with zero errors.
- Validation combined score >= 7.0 (static + LLM).
- Zero raw CSS files in project.
- All components have typed Props interfaces.
- Theme exists with palette, typography, and component overrides.
- Barrel exports in `src/components/index.ts`.
- Validation report JSON exported to `results/<model_label>.json`.

## Artifact Persistence
- Project source: `<project_name>/` at workspace root.
- Validation report: `validator/results/<model_label>.json`
- Extraction artifacts: `docs/figma/<project_name>/design-tokens.json`
- Component inventory: `docs/figma/<project_name>/component-inventory.md`

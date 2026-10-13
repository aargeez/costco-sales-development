# /figma-to-nextjs

## Objective
Transform a Figma Design System into a production-ready, reusable Next.js + MUI v6 project with automated validation and iterative refinement.

## Use Agents
- figma-design-system-agent (`.cursor/agents/figma/figma-design-system-agent.md`)

## Delegate to Subagents
- figma-design-extractor (`.cursor/subagents/figma/figma-design-extractor.md`)
- theme-builder (`.cursor/subagents/figma/theme-builder.md`)
- mui-component-generator (`.cursor/subagents/figma/mui-component-generator.md`)
- page-assembler (`.cursor/subagents/figma/page-assembler.md`)
- project-validator (`.cursor/subagents/figma/project-validator.md`)
- refinement-agent (`.cursor/subagents/figma/refinement-agent.md`)

## Enforce Rules
- `.cursor/rules/21-figma-to-nextjs-mui.mdc`
- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/04-frontend-nextjs.mdc`
- `.cursor/rules/07-testing-and-quality.mdc`

## Use Templates
- `.cursor/templates/figma-design-system-spec-template.md`
- `.cursor/templates/figma-component-spec-template.md`
- `.cursor/templates/figma-validation-report-template.md`

## Use Skills
- `.cursor/skills/figma-design-system/SKILL.md`

## Runtime Inputs

### Required
- `figma_url`: Figma URL with `node-id` parameter.
- `project_name`: output folder name at workspace root.
- `model_label`: model name for validation reporting.

### Optional
- `reference_project`: folder structure reference only (code is NOT copied).
- `governed_plan` (optional): `.cursor/plans/sprint<N>/<story-id>-governed-implementation-plan.md` when Figma is the UX source for a governed slice.
- `plan_step` (optional): row in §Command execution flow when this command is listed.

## Plan-as-input mode

When `governed_plan` is supplied, resolve `figma_url` and `project_name` from §Story pack UI data workflow and §Workspace bindings (`implementation_frontend_root` or dedicated output folder in step Runtime bindings). Hard-stop on Open UI/Figma gaps. Update plan execution sections on completion.

## Required Output
- Complete Next.js project folder with:
  - `src/theme/` — MUI theme (palette, typography, component overrides)
  - `src/components/atoms/` — primitive MUI components
  - `src/components/molecules/` — composed components
  - `src/components/organisms/` — complex sections
  - `src/components/templates/` — page templates
  - `src/components/index.ts` — barrel exports
  - `src/types/index.ts` — prop type exports
  - `src/pages/` — _app.tsx, _document.tsx, index.tsx with mock data
  - `package.json`, `tsconfig.json`, `next.config.ts`
- Validation report JSON in `validator/results/<model>.json`
- Design tokens JSON in `docs/figma/<project>/design-tokens.json`

## Command-Specific Hardening
- **Do** run `validator/validate.py` after assembly; require TypeScript compile pass.
- **Do** use MUI theme tokens — zero raw CSS files.
- **Don't** copy reference project code; structure only.
- **Stop** when `figma_url` lacks `node-id` or Figma MCP is unavailable.

## Execution Policy
- Execution loop: Extract → Theme → Generate → Assemble → Validate → Refine.
- Phase dependencies are sequential (each phase requires output from the previous).
- Validation after assembly: run `validator/validate.py` with `--model` flag.
- Refinement trigger: combined score < 7.0 OR any Critical finding.
- Max refinement iterations: 3.
- After 3 iterations: escalate remaining findings to human with remediation suggestions.
- TypeScript compilation MUST pass (exit code 0) before declaring done.

## Artifact Persistence
- Project: `<project_name>/` at workspace root.
- Validation: `validator/results/<model_label>.json`
- Design artifacts: `docs/figma/<project_name>/`
- Include metadata header in docs: timestamp, iteration count, final validation score, command version (`/figma-to-nextjs`).

## Clarification Protocol

**Hard stop:**

1. Valid `figma_url` with `node-id`?
2. `project_name` and `model_label`?
3. `reference_project` path if non-default structure needed?

## Definition of Done

- [ ] `npx tsc --noEmit` passes with zero errors.
- [ ] Validator combined score >= 7.0.
- [ ] Zero raw CSS files.
- [ ] All components have typed Props interfaces.
- [ ] Theme exists with palette + typography + component overrides.
- [ ] Barrel exports cover all public components.
- [ ] `project-validator` reports no unresolved Critical findings after max refinement iterations.

## Example Invocation
```
/figma-to-nextjs
  figma_url: https://www.figma.com/design/qO0O55vSBPbhvNP14FLOjA/NSI-HIFI?node-id=2865-44617
  project_name: nsi-ui
  model_label: "Claude Opus 4"
  reference_project: gcp-whs-nsi-ui-nsi-demo-initial/gcp-whs-nsi-ui-nsi-demo-initial
```

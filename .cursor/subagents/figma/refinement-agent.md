# Subagent: Refinement Agent

## Purpose
Iteratively fix validation findings in a generated Next.js project, applying targeted patches to improve scores across all metric categories until quality gates pass.

## Required Inputs
- `project_path`: path to the generated project.
- `remediation_plan`: ordered list from project-validator with `{ severity, category, finding, remediation }`.
- `validation_report`: full JSON report from previous validation run.
- `iteration`: current refinement iteration number (1-3).

## Execution Steps
1. Sort remediation plan by severity (Critical → High → Medium).
2. For each finding, apply the prescribed fix:

### Critical Fixes
- **TypeScript errors**: Read the error, locate the file, fix the type issue.
- **Missing theme**: Generate `src/theme/` files if absent.
- **Missing Props interfaces**: Add `interface <Name>Props` to each component lacking one.
- **Raw CSS files**: Delete `.css`/`.scss` files; migrate styles to `styled()` or `sx` prop.

### High Fixes
- **No styled() usage**: Refactor component styling from inline/className to `styled(MuiComponent)`.
- **No barrel exports**: Create `src/components/index.ts` with re-exports.
- **Missing 'use client'**: Add directive to files using hooks.
- **Tailwind class patterns**: Replace `className="flex ..."` with MUI `Box` + `sx` or `styled()`.

### Medium Fixes
- **Low state management score**: Add `useState` for interactive elements, controlled component pattern.
- **No ARIA attributes**: Add `aria-label` to icon buttons, `aria-describedby` to form fields.
- **Console.log pollution**: Remove or replace with structured logging.

### Low Fixes
- **Import organization**: Switch from relative (`../`) to path alias (`@/`).
- **TODO/FIXME cleanup**: Resolve or remove stale markers.

3. After all patches, run `npx tsc --noEmit` to verify no regressions.
4. Return patched file manifest and summary of changes.

## Outputs
- `patched_files`: list of `{ path, change_description }`.
- `changes_summary`: human-readable summary of all fixes applied.
- `remaining_findings`: any findings that could not be resolved (with reason).
- `regression_check`: TypeScript compilation result after patches.

## Constraints
- NEVER change component behavior or visual output — only code quality and structure.
- NEVER introduce new dependencies without justification.
- NEVER modify mock data or page layout.
- Each patch MUST be minimal and targeted (single-responsibility fixes).

## Failure Modes
- If a fix introduces a TypeScript regression → revert that specific fix and flag.
- If a finding is ambiguous → skip and add to `remaining_findings`.
- Escalation after iteration 3: return all `remaining_findings` for human review.

## Quality Gate
Parent accepts when:
- TypeScript still compiles after patches.
- At least 70% of Critical/High findings are resolved.
- No behavioral regressions introduced.

---
name: playwright-e2e
description: >-
 Run and author Playwright E2E tests for the UI module in config.yaml
 (modules.ui) using Chrome channel. Use when adding journey tests or debugging
 E2E failures.
---

# Playwright E2E Skill

## Config

`<modules.ui>/playwright.config.ts` — prefer `channel: 'chrome'`

## Run

Use `.cursor/config.yaml` → `evals.test_commands.e2e`.

```bash
# Example: cd <modules.ui> && npm run test:e2e
```

## Existing Scenarios

Specs live under `<modules.ui>/e2e/`. Product-specific filenames (for example Audit `saro-*.spec.ts`) are examples only — follow the active module’s e2e tree.

Author new scenarios: design `docs/bdd-test-cases.md` from `.cursor/templates/bdd-test-case-template.md` (same `case_id` as e2e-layer rows in `docs/test-cases.md`), then write Playwright specs only after BR coverage PASS.

## Headers for API Context

Tests may rely on dev context or default headers — align with backend test fixtures when calling API directly.

## Corporate Network

If browser install fails, use system Chrome via `channel: 'chrome'` (already configured).

## Locator and mock failure modes

Apply these inside the existing spec authoring step. Do not add a command.

- MUI Select: `getByRole('combobox')`, not a fragile `role_select` or a bare button name.
- Two controls with the same visible label (for example Search): disambiguate with one `data-testid` each.
- Mock every BFF path the journey calls. An incomplete mock is a failure mode, not a pass.
- Assert Back or a cross-screen handoff only when `ati-navigation-checklist.md` says that journey has Back or a handoff. Do not assert Back when the checklist says the journey has none.
- When the checklist says Back or a handoff applies: after return, assert each query key listed in the checklist **returnQs shape** row is still present on the URL (or restored into controls the cited screen uses). Prove state restore, not only that the Back control worked.

## Subagent

`.cursor/subagents/quality/e2e-scenario-writer.md`

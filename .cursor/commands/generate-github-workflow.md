# /generate-github-workflow

## Objective
Author or update **GitHub Actions** workflow YAML with secure defaults (minimal `permissions`, no embedded secrets), appropriate triggers, and optional reuse of org callable workflows.

## Runtime Inputs

### Required
- `workflow_purpose`: CI, CD, scheduled, PR-only, or manual dispatch.
- `stack_and_commands`: build/test/lint commands (e.g. `mvn verify`, `pnpm test`).
- `workflow_name`: short slug for `.github/workflows/<name>.yml`.

### Optional
- `repository_path`: monorepo subfolder vs repo root.
- `triggers_and_filters`: push/PR/tag/cron/branch filters.
- `deployment_target`: none, or environment + OIDC vs token pattern.
- `callable_workflow_refs`: org reusable workflow `uses: ...@ref` dependencies.

## Use Agents
- github-workflow-agent (`.cursor/agents/infra/github-workflow-agent.md`)

## Enforce Rules
- `.cursor/rules/02-security-and-compliance.mdc`
- `.cursor/rules/00-global-governance.mdc`

## Use Templates
- `.cursor/templates/github-workflow-template.yml`

## Use Skills
- `.cursor/skills/infra/SKILL.md` — secure GitHub Actions authoring patterns.

## Required Output
- Workflow file at `.github/workflows/<name>.yml` (path confirmed with user).
- **Provisioning notes**: list of `secrets.*` / `vars.*`, Environments, and callable workflow `uses: ...@ref` dependencies.

## Command-Specific Hardening
- **Do** use least-privilege `permissions` and pin action versions per project convention.
- **Do** match existing workflows in repo for naming and reusable patterns.
- **Don't** place secret values in YAML — reference names only.
- **Stop** when `deployment_target` implies prod without environment protection confirmation.

## Execution Policy
- Execution loop: Plan → Act → Evaluate → Refine (max 3 iterations).
- **Evaluate:** structural YAML validity, permissions, trigger policy, secret references only.
- Refinement: patch workflow and provisioning notes in-place.

## Clarification Protocol

**Medium gate:**

1. What is `workflow_purpose` and `workflow_name`?
2. What `stack_and_commands` run in CI?
3. What `triggers_and_filters` apply?
4. Deployment model (`deployment_target`) if CD?

Confirm target path before writing `.github/workflows/*.yml`.

## Artifact Persistence
- `.github/workflows/<purpose>-<name>.yml`
- Optional: `docs/implementation/<capability>/github-workflow-notes.md`
- Metadata in notes: timestamp, iteration count, validation summary, command version (`/generate-github-workflow`).

## Definition of Done

- [ ] YAML is structurally valid; permissions are least-privilege; triggers match policy.
- [ ] All external actions/workflows are pinned per project convention.
- [ ] Provisioning notes list secrets/vars/environments without values.
- [ ] No secret literals in committed YAML.

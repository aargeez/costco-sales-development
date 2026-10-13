# /generate-infra

## Objective
Generate Terraform-based GCP infrastructure for target deployment.

## Runtime Inputs

### Required
- `target_environment`: dev, staging, prod, or named env key.
- `system_or_capability_key`: naming segment for infra artifacts.
- `deployment_scope`: services, networks, IAM roles, and data stores in scope.

### Optional
- `existing_terraform_root`: module path to extend (avoid greenfield duplication).
- `dr_tier`: RTO/RPO expectations influencing multi-region design.
- `observability_stack`: logging/metrics/tracing targets (Cloud Monitoring, etc.).
- `governed_plan` (optional): `.cursor/plans/sprint<N>/<story-id>-governed-implementation-plan.md` when infra is in slice scope.
- `plan_step` (optional): row in §Command execution flow when listed.

## Plan-as-input mode

When `governed_plan` is supplied, resolve `system_or_capability_key` from §Workspace bindings, `deployment_scope` from §Story pack NFR/ops notes and §Governance rules, and `target_environment` from step Runtime bindings. Update plan execution sections on completion.

## Use Agents
- infra-agent
- release-agent
- sre-agent

## Delegate to Subagents
- migration-planner
- nfr-checklist-builder
- release-readiness-checker
- dr-readiness-checker

## Enforce Rules
- .cursor/rules/00-global-governance.mdc
- .cursor/rules/overlays/sales-vault/06-infra-terraform-gcp.mdc
- .cursor/rules/02-security-and-compliance.mdc
- .cursor/rules/15-release-readiness.mdc
- .cursor/rules/16-observability-sre.mdc
- .cursor/rules/19-resilience-and-dr.mdc

## Use Templates
- .cursor/templates/infra-deployment-template.md
- .cursor/templates/release-readiness-template.md
- .cursor/templates/slo-sli-template.md

## Required Output
- Infra module and env strategy
- IAM/networking/security controls
- Deployment + rollback readiness
- Observability and DR readiness notes

## Command-Specific Hardening
- **Do** apply least-privilege IAM; document service account purposes.
- **Do** include rollback and blast-radius notes per environment.
- **Don't** embed secrets in Terraform — use Secret Manager references.
- **Stop** when `target_environment` is prod without change-management context.

## Execution Policy
- Execution loop: Plan -> Act -> Evaluate -> Refine.
- Max iterations: 3 (escalate unresolved items after final iteration).
- Validation: `release-readiness-checker` (`.cursor/subagents/quality/release-readiness-checker.md`) checks IAM/network boundaries, rollout/rollback posture, observability hooks, and DR alignment against the Required Output after each Act step.
- Refinement: agent patches artifact in-place based on validation findings before next iteration.

## Clarification Protocol

**Medium–high gate for prod:**

1. What is `target_environment` and `deployment_scope`?
2. Extend `existing_terraform_root` or net-new modules?
3. What DR and observability requirements apply?

## Artifact Persistence
- Save final output to: `docs/infra/<env-or-system>-infra.md`
- Include metadata header: timestamp, iteration count, validation summary, command version (`/generate-infra`).

## Definition of Done

- [ ] Infra module/env strategy, security controls, deployment/rollback readiness, and observability/DR notes are complete and consistent.
- [ ] `release-readiness-checker` reports no critical release-blockers for blast radius, secrets handling, or operational readiness.
- [ ] Infra, release-readiness, and SLO/SLI templates fully populated; no `FIXME` resource blocks or unspecified environments.
- [ ] IAM bindings documented with least-privilege rationale.

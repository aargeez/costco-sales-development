# /compliance-check

## Objective
Run compliance and audit evidence checks for release readiness using sprint artifact evidence — minimal chat input when sprint unified plan is supplied.

## Runtime Inputs

### Minimal invoker (preferred)
- `sprint_governed_plan`: `.cursor/plans/sprint<N>/sprint<N>-unified-governed-plan.md`

### Auto-resolved when `sprint_governed_plan` supplied
- `release_or_scope_id` ← `sprint<N>` from unified plan metadata
- `control_framework` ← unified plan §Shared workspace bindings or default `.cursor/rules/34-compliance-and-audit.mdc` + `.cursor/rules/02-security-and-compliance.mdc` + `.cursor/rules/15-release-readiness.mdc`
- `evidence_inventory` ← `docs/sprint<N>/manifest.json` + all per-story §Execution logs + `docs/compliance/`, `docs/quality/`

### Legacy explicit mode
- `release_or_scope_id`: release train, sprint, or capability boundary
- `control_framework`: applicable controls

### Optional
- `approval_authority`: role for sign-off (default: release owner per rules 15/18)
- `data_classification`: sensitivity levels in scope

## Plan-as-input mode (sprint)

When `sprint_governed_plan` is supplied, apply `.cursor/templates/plan-as-input-protocol.md` §Resolution order (sprint):

| Input | Source |
|-------|--------|
| `release_or_scope_id` | Unified plan sprint number |
| `control_framework` | §Shared workspace bindings or default rules |
| `evidence_inventory` | `manifest.json` + per-story execution logs + quality/compliance docs |

**Resolve-before-ask:** do not ask for release scope or evidence paths when unified plan and manifest exist. Ask only for Open sprint-critical gaps.

After DoD: mark step 9 `complete` in unified plan §Sprint command execution flow; update §Sprint execution log.

## Use Agents
- compliance-agent
- release-agent

## Delegate to Subagents
- pii-control-checker
- release-readiness-checker

## Enforce Rules
- .cursor/rules/34-compliance-and-audit.mdc
- .cursor/rules/02-security-and-compliance.mdc
- .cursor/rules/15-release-readiness.mdc

## Use Templates
- .cursor/templates/compliance-evidence-template.md
- .cursor/templates/release-readiness-template.md

## Use Skills
- `.cursor/skills/ops/SKILL.md`

## Required Output
- Compliance gap report mapped to manifest evidence
- Evidence bundle checklist (every control → artifact path or gap)
- Approval readiness status

## Command-Specific Hardening
- **Do** map each control to manifest `kind` entries and execution log artifacts.
- **Do** flag PII/audit gaps as release blockers when critical.
- **Don't** mark approval-ready with open critical gaps.
- **Resolve-before-ask** when `sprint_governed_plan` supplied.

## Execution Policy
- Plan → Act → Evaluate → Refine (max 3).
- Validation: **pii-control-checker** + evidence completeness vs manifest.

## Clarification Protocol

**Resolve-before-ask** when `sprint_governed_plan` supplied — skip questions 1–2.

**Medium gate** only when:
1. Unified plan or manifest inaccessible
2. `control_framework` undefined and not defaultable
3. Critical evidence holes with no gap owner

## Artifact Persistence
- `docs/compliance/sprint<N>-compliance-check.md` (or `<release-or-scope-id>-compliance-check.md`)
- Update `docs/sprint<N>/manifest.json` with `kind: compliance-check`
- Metadata: timestamp, iteration count, validation summary, command version.

## Definition of Done

- [ ] Gap report and evidence checklist map controls to manifest/execution-log paths.
- [ ] **pii-control-checker** reports no critical blocking gaps.
- [ ] Templates fully populated.
- [ ] When `sprint_governed_plan` supplied: unified plan step 9 `complete`, sprint execution log updated.

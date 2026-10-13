# /threat-profile

## Objective
Threat **profiler** (STRIDE / attack-surface of **this** system) plus threat **hunter** (hunt the same threats in this repo and config). **security-agent** mode `threat`. Not a 20th primary. Not a third-party hunt.

## When to use
- After OpenAPI exists (`/implement-api`) or before release
- Complements `/sast-scan` and `/dast-scan`; does not replace them

## Runtime Inputs

### Required
- System scope: OpenAPI and/or approved BRD/story pack (cited assets only)

### Optional
- `governed_plan`
- Infra notes under `modules.infra` (if present)
- Prior SAST/DAST finding paths

## Plan-as-input mode
When a governed or sprint plan is supplied, apply `.cursor/templates/plan-as-input-protocol.md` before Clarification Protocol. Cite sources; register `ASSUMPTION-<id>` if silent.

## Use Agents
orchestrator-agent → security-agent (mode `threat`)

## Delegate to Subagents
threat-profiler → threat-hunter

## Enforce Rules
02, 09, 10, 22

## Use Templates
`threat-profile-template.md`, `audit-trail-template.md`

## Required Output
- `artifacts/security/<date>-threat-profile.md`
- `artifacts/security/<date>-threat-hunt.md`
- `artifacts/audit/<date>-threat-profile-<task>.md`

## Command-Specific Hardening
- Profile **this** product only. Must NOT invent assets, trust boundaries, or mitigations not evidenced in OpenAPI/BRD/code.
- Hunter searches this repo/config for the profiled threats (secrets, missing authz on documented ops, default-admin patterns). Must NOT write exploits or hunt third-party systems.
- Uncited behavior is a gap question, not a new threat. Redact secrets. Stay in `scope.yaml`.

## Execution Policy
Plan → Act → Validate → Refine (max 3 ceiling). Iteration 1 Validate pass → stop. Refine only from findings. Escalate with owners after iteration 3.

## Clarification Protocol
After resolve-before-ask, register Open DQ when OpenAPI/BRD scope is missing. Do not invent a threat model. Do not STOP for a clarification round except human gates. Still refuse when Required scope is absent.

## Artifact Persistence
Canonical paths above. Metadata: timestamp, iteration, source locators.

## Definition of Done
- [ ] Assets and trust boundaries cited (OpenAPI/BRD/code)
- [ ] STRIDE (or equivalent) table with severity and owner
- [ ] Hunt results mapped to profile rows (hit / miss / `NEEDS_RUNTIME`)
- [ ] Unowned Critical/High listed, not dropped
- [ ] `artifacts/security/<date>-threat-profile.md` and `-threat-hunt.md` written
- [ ] `artifacts/audit/<date>-threat-profile-<task>.md` written

## Next command

> ✅ `/threat-profile` complete.
> **Next command:** `/review-changes` or `/compliance-check` if controls need evidence; owning agents fix Critical/High.

# /performance-test

## Objective
Complete performance test of **this product's** API and/or UI: workload model, measured run, bottlenecks vs cited SLOs/NFRs. Not a 20th primary. **sre-agent** mode `perf`.

## When to use
- After `/implement-api` / `/implement-ui` when NFRs or SLOs exist
- Before `/validate-kpi-metrics` (feeds KPI-12) or release Go/No-Go
- Distinct from `/validate-api-endpoints` (functional live probes)

## Runtime Inputs

### Required
- Target: module roots from `.cursor/config.yaml` and/or base URL of **this** app (localhost or listed staging)
- Workload: cited NFR/SLO from BRD, story pack, or `docs/sre/` — or explicit user targets (p95, RPS, duration)

### Optional
- `governed_plan` / `sprint_governed_plan`
- tool: `k6` | `gatling` | `jmeter` | `lighthouse` | auto-detect
- `stack`: `api` | `ui` | `both`

## Plan-as-input mode
When a governed or sprint plan is supplied, apply `.cursor/templates/plan-as-input-protocol.md` before Clarification Protocol. Use cited NFRs; do not invent latency numbers.

## Use Agents
orchestrator-agent → sre-agent (mode `perf`)

## Delegate to Subagents
perf-profiler → load-test-runner

## Enforce Rules
02, 09, 10, 16

## Use Templates
`performance-test-report-template.md`, `audit-trail-template.md`

## Required Output
- `artifacts/perf/<date>-performance-test.md`
- `docs/performance/<date>-performance-test.md` (summary)
- `artifacts/audit/<date>-performance-test-<task>.md`

## Command-Specific Hardening
- Load **this** application only. Refuse third-party production URLs.
- Perf claims must cite a command/result file. Missing tool → `NEEDS_TOOL` with owner; do not invent RPS/p95.
- State env vs prod caveats. Do not page or change SLOs.
- Stay in `scope.yaml` for sre-agent.

## Execution Policy
Plan → Act → Validate → Refine (max 3 ceiling). Iteration 1 Validate pass → stop. Refine only from findings. Escalate with owners after iteration 3.

## Clarification Protocol
After resolve-before-ask, register Open DQ when target URL/modules or workload targets are missing after plan/config introspection. Do not invent SLOs. Do not STOP for a clarification round except human gates. Still refuse when Required target is absent.

## Artifact Persistence
Canonical paths above. Metadata: timestamp, iteration, validation summary, tool used or `NEEDS_TOOL`.

## Definition of Done
- [ ] Workload model cited (NFR/SLO/user targets)
- [ ] Measured run or explicit `NEEDS_TOOL` (no invented metrics)
- [ ] Bottlenecks listed with file/endpoint evidence
- [ ] Env vs prod caveats stated
- [ ] `artifacts/perf/<date>-performance-test.md` written
- [ ] `artifacts/audit/<date>-performance-test-<task>.md` written

## Next command

> ✅ `/performance-test` complete.
> **Next command:** `/validate-kpi-metrics` (KPI-12) or `/review-changes` if regressions block release.

# /plan-bounded-context

## Objective
Readiness of a **named bounded context** (wiki, prior Done, gates) and a Proposed sprint plan whose execute rows are implement-* only. Sequential: do not auto-start the next context if the prior is not Done unless a waiver is in `docs/harness/decisions.md`.

Generalized from ATI-style sequential delivery. No vendor module names.

## When to use
- Product has multiple bounded contexts that must ship one at a time
- After `/generate-user-story` or `/generate-brd` for that context
- Distinct from `/plan-sprint` (whole-backlog) and `/plan-governed-stories` (story-pack slice)

## Runtime Inputs

### Required
- `context_id` (bounded context name)
- wiki and/or BR checklist path for that context

### Optional
- prior `context_id` Done evidence
- sprint_number / duration

## Use Agents
orchestrator-agent → planner-agent (mode `bounded-context`)

## Delegate to Subagents
gap-identifier (internal; former `/gap-analysis`), requirement-traceability-mapper, sprint-planner, open-questions-cataloger

## Enforce Rules
00, 09, 10, 11, 13, 22

## Use Templates
`sprint-plan-template.md`, `requirement-traceability-template.md`, `audit-trail-template.md`

## Use Skills
`.cursor/skills/plan-as-input/SKILL.md`, `.cursor/skills/planning/SKILL.md`

## Preconditions
- [ ] Context wiki or checklist exists
- [ ] Prior context **Done** or waiver in `docs/harness/decisions.md`
- [ ] User supplied `context_id`

## Command-Specific Hardening
- Do not auto-approve. Status Proposed — Pending Approval.
- Do not emit generate-* execute steps.
- Do not start `/deliver-sprint` from this command.

## Clarification Protocol
After resolve-before-ask, register Open DQ when `context_id` or the context wiki/checklist path is missing. Do not invent a context or auto-start the next one. Do not STOP for a clarification round except human gates. Still refuse when Required `context_id` is absent.

## Execution Policy
Plan → Act → Validate → Refine (max 3).

## Artifact Persistence
`artifacts/sprints/<context_id>-gap-report.md`, `artifacts/sprints/<context_id>-plan.md`, `docs/bounded-context/**`, `artifacts/audit/<date>-plan-bounded-context-<task>.md`

## Definition of Done
- [ ] `artifacts/sprints/<context_id>-gap-report.md` and `artifacts/sprints/<context_id>-plan.md` complete
- [ ] Every in-scope BR maps to ≥1 story
- [ ] Critical gaps owned or none
- [ ] Execute tags are implement-* only
- [ ] Status Proposed — Pending Approval
- [ ] `artifacts/audit/<date>-plan-bounded-context-<task>.md` written
- [ ] Harness updated (phase-changing)

## Next command

> ✅ `/plan-bounded-context` complete.
> **Next command:** `/deliver-sprint` or `/implement-api` **after** the plan is Approved.

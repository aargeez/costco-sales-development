# /plan-governed-stories

## Objective
Validate techno-functional story packs for **governed implementation readiness**, produce **per-story execution manifests** and a **sprint unified plan** so downstream commands run with `@governed_plan` / `@sprint_governed_plan` only.

## Runtime Inputs

### Minimal invoker (preferred)
- `@story_artifact`: path to techno-functional pack from `/generate-user-story` (e.g. `docs/sprint3/userstory/nsi-entry-techno-functional-pack.md`)

### Auto-resolved when `@story_artifact` supplied
- `story_backlog_sources` ← `@story_artifact` path
- `sprint_number` ← path segment `docs/sprint<N>/`, prompt, git branch, or `SPRINT_NUMBER`
- `scope_mode` ← default `full-stack` unless user overrides

### Optional overrides
- `scope_mode`: full-stack per story, or per-story slice types (DDL-only, API-only, etc.)
- `authoritative_refs`: OpenAPI, migrations folder, UX doc paths when known
- `story_id_filter`: subset of stories to plan in this invocation

### Legacy explicit mode (when no `@story_artifact`)
- `story_backlog_sources`: paths or links to story backlog
- `scope_mode`: required

## Story-as-input mode

When `@story_artifact` is supplied, apply `.cursor/templates/plan-as-input-protocol.md` §Story-as-input:

1. **Do not ask** for `story_backlog_sources`.
2. Parse techno-functional sections (DDL, API, UI, data tables, wiring preview) into per-story plan §Story packs.
3. Resolve §Workspace bindings from repo layout + pack metadata (minimal `infer`).
4. Emit **both**:
   - `.cursor/plans/sprint<N>/<story-id>-governed-implementation-plan.md` (per story)
   - `.cursor/plans/sprint<N>/sprint<N>-unified-governed-plan.md` (sprint rollup)

## Use Agents
- governed-story-planner-agent (`.cursor/agents/planning/governed-story-planner-agent.md`)

## Delegate to Subagents
- forward-engineering-agent — [forward-engineering-agent.md](../agents/engineering/forward-engineering-agent.md) (cross-program alignment, optional)
- frontend-agent (FE↔BE wiring gate) + api-agent (contract verify)

## Enforce Rules
- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/02-security-and-compliance.mdc`
- `.cursor/rules/09-agent-orchestration.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`

## Use Templates
- `.cursor/templates/governed-implementation-plan-template.md` (per-story)
- `.cursor/templates/sprint-unified-governed-plan-template.md` (sprint rollup)

## Use Skills
- `.cursor/skills/planning/SKILL.md`

## Required Output
- **Techno-functional readiness matrix** per story: DDL / API / UI / Wiring (from pack or gaps)
- **Gap register** with gap IDs and numbered questions
- **Per-story governed plan** with §Workspace bindings, §Command execution flow (steps 1–8), §Execution log, §Plan-level DoD
- **Sprint unified plan** (`sprint<N>-unified-governed-plan.md`) with story index, shared bindings, orchestration order, sprint steps 9–10 (`/compliance-check`, `/sprint-handoff`)
- If critical gaps remain: stop after questions unless user authorizes **assumptions** (listed in plans)

## Command-Specific Hardening
- **Do** cite techno-functional pack sections for each readiness dimension.
- **Do** use canonical per-story command order (steps 1–8) and sprint closure order (steps 9–10 in unified plan).
- **Don't** proceed with full-stack order when critical gaps lack assumptions.
- **Resolve-before-ask** when `@story_artifact` present — questions only for Open gaps or `TBD` bindings.
- **Rule provenance gate:** each per-story plan must include a `Behavior provenance` subsection listing business behaviors with source citations; uncited behavior must be marked `ASSUMPTION-<id>` and blocked from Ready until approved.

## Execution Policy
- Plan → Act → Evaluate → Refine (max 3 iterations).
- Validation: every full-stack story cites all four dimensions or appears in gap register; unified plan lists every story with per-story plan path.

## Clarification Protocol

**Resolve-before-ask** when `@story_artifact` supplied — skip questions 1–2 below.

**Hard stop** only when:

1. `@story_artifact` / `story_backlog_sources` inaccessible
2. `scope_mode` ambiguous for mixed slice types
3. Open critical gaps in pack with no assumption authorization

## Artifact Persistence
- **Per story:** `.cursor/plans/sprint<sprint-number>/<story-id>-governed-implementation-plan.md`
- **Sprint rollup (required):** `.cursor/plans/sprint<sprint-number>/sprint<sprint-number>-unified-governed-plan.md`
- **Optional matrix:** `.cursor/plans/sprint<sprint-number>/story-readiness-matrix.md`
- Hook scaffolds folders and seeds plans when `/plan-governed-stories` + sprint `N` detected.
- Metadata: timestamp, iteration count, validation summary, command version.

## Definition of Done

- [ ] Readiness matrix + gaps complete for scope.
- [ ] Per-story plans: bindings + command flow steps 1–8 + execution log + plan DoD.
- [ ] Sprint unified plan: story index + shared bindings + steps 9–10 + sprint DoD.
- [ ] Wiring specs suitable for `implement-ui` FE↔BE gate.
- [ ] Downstream smoke: `/<cmd> @governed_plan: …` and `/compliance-check @sprint_governed_plan: …` need no extra runtime inputs when plans are Ready.
- [ ] Critical gaps resolved or assumption-approved.
- [ ] No per-story plan is marked Ready with uncited business behavior rules.

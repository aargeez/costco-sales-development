# Command Contract Template

Canonical schema for every slash command in `.cursor/commands/`. Use this template when authoring or hardening commands. **Do not copy-paste identical sections** — tailor Runtime Inputs, hardening, Clarification Protocol, and Definition of Done to each command's purpose and risk profile.

**Gold example:** `.cursor/commands/implement-api.md`

---

## Objective

One paragraph: what the command produces, for whom, and how it fits the SDLC (build, quality, infra, phase, ops).

---

## Runtime Inputs

### Required
- List command-specific inputs the invoker must supply (paths, IDs, roots, version labels, scope modes).
- Use consistent naming: `implementation_module_root`, `artifact_namespace`, `contract_version`, `feature_or_slice_name`, etc.

### Optional
- Defaults, fallbacks, and when omission is allowed.
- Link to upstream command outputs when this command consumes them (e.g. prior step artifacts from `/implement-api` in a governed plan).
- `governed_plan`: per-story plan path — **plan-as-input mode** (steps 1–8).
- `sprint_governed_plan`: `.cursor/plans/sprint<N>/sprint<N>-unified-governed-plan.md` — sprint closure commands (steps 9–10).
- `@story_artifact`: techno-functional pack path — **story-as-input** for `/plan-governed-stories`.
- `plan_step`: optional row in §Command execution flow; default first `pending` row for this command.

> **Note:** Preserve command-specific baseline sections (e.g. *Generation Baseline*, *Prerequisites*, *Incremental migration enforcement*) immediately after Runtime Inputs when they exist — they are part of hardening, not generic boilerplate.

---

## Plan-as-input mode

*(Include on commands that participate in governed story delivery — build, quality, wiring, docs.)*

When `governed_plan`, `sprint_governed_plan`, or `@story_artifact` is supplied, apply **resolve-before-ask** per `.cursor/templates/plan-as-input-protocol.md` **before** Clarification Protocol:

1. Load plan, `docs/sprint<N>/manifest.json`, per-story §Execution logs, upstream artifact paths.
2. Hard-stop only on Open critical §Gap register items or `TBD` bindings after resolution.
3. **Skip Clarification Protocol questions** answerable from resolved inputs.
4. After Definition of Done, update plan execution flow + execution log + manifest.

**Minimal prompts:**
- `/<command> @governed_plan: <per-story-plan-path>`
- `/compliance-check @sprint_governed_plan: .cursor/plans/sprint<N>/sprint<N>-unified-governed-plan.md`
- `/plan-governed-stories @story_artifact: docs/sprint<N>/userstory/<pack>.md`

---

## Use Agents

- Primary agent(s) with path when non-obvious: `agent-name` (`.cursor/agents/<area>/<agent>.md`)

---

## Delegate to Subagents

- Ordered list when sequence matters; note optional subagents.

---

## Enforce Rules

- `.cursor/rules/*.mdc` paths relevant to this command only.

---

## Use Templates

- `.cursor/templates/*.md` (and `.yml` when applicable).

---

## Use Skills

*(Include only when a matching skill exists in `.cursor/skills/`.)*

- `.cursor/skills/<area>/<skill>.md` — one-line why it applies.

---

## Required Output

Bullet list of concrete artifacts (code, docs, matrices, evidence). For unified packs, enumerate sections that must appear in the single file.

---

## Command-Specific Hardening

Purpose-specific **dos**, **don'ts**, **validators**, and **stop conditions**. Examples by category:

| Category | Hardening focus |
|----------|-----------------|
| Build / codegen | Module introspection, no sample-project cloning, contract/envelope policy |
| Database / migration | Forward-only migrations, destructive DDL gates, baseline inventory |
| API / wiring | Sequence order, mapping proof, no invented domain semantics |
| Quality / tests | Upstream artifact dependencies, coverage ID schemes |
| Infra / release | Least privilege, rollback, RTO/RPO evidence |
| Phase packs | Phase gate evidence, cross-artifact traceability |
| Handoff / docs | Session narrative over git mining; no fabricated facts |

Merge existing sections here when present: *Structural alignment*, *Contract and Implementation Constraints*, *Workspace hooks*, *Related Commands*, *Baseline Commands*, etc.

---

## Execution Policy

- **Loop:** Plan → Act → Evaluate → Refine
- **Max iterations:** 3 (escalate with owners/risks after final iteration unless command specifies otherwise)
- **Plan:** What to inventory, classify, or sequence before generation
- **Act:** What to produce or change
- **Evaluate:** Named validator(s) — subagent, skill, or external check — and what they verify
- **Refine:** Patch in-place; do not declare done until Definition of Done is met

---

## Clarification Protocol

When to **stop and ask** numbered questions instead of proceeding. Calibrate to risk:

| Risk level | Examples |
|------------|----------|
| **High** (hard stop) | Destructive DDL, production cutover, missing auth model, ambiguous API sequence |
| **Medium** | Missing contract version, unclear scope boundaries, conflicting mapping artifacts |
| **Low** | Optional doc_scope, handoff date label, optional reference paths |

Format when stopping:

1. Numbered questions (max 7 per round)
2. State what is blocked until answered
3. Offer explicit **assumption mode** only when the command allows it (document assumptions in artifact)

---

## Artifact Persistence

- **Canonical path(s)** with placeholders (`<slice>`, `<sprint_number>`, `<run-id>`)
- **Sprint convention:** `docs/sprint<sprint_number>/<area>/...` + `manifest.json` entry with `path` and `kind`
- **Metadata header:** timestamp, iteration count, validation summary, command version (`/<command-name>`)
- Manifest JSON example when sprint hooks apply

---

## Definition of Done

Checkbox checklist — **purpose-specific**, not generic copy-paste:

```markdown
- [ ] Criterion tied to Required Output completeness
- [ ] Named validator reports no critical gaps (or bounded gaps with owner)
- [ ] Templates fully populated; no placeholder/TBD in mandatory sections
- [ ] Command-specific gate (security, destructive SQL, test pass, validator score, etc.)
```

Replace former **Acceptance Threshold** sections with this checklist while preserving command-specific criteria.

# Plan-as-input protocol

Enterprise autonomous workflow: slash commands resolve runtime inputs from **governed plans**, **sprint rollups**, **story packs**, and **prior artifacts** before asking clarifying questions.

## Plan types

| Type | Path pattern | Used by |
|------|--------------|---------|
| **Per-story plan** | `.cursor/plans/sprint<N>/<story-id>-governed-implementation-plan.md` | Build/quality commands (steps 1–8) |
| **Sprint unified plan** | `.cursor/plans/sprint<N>/sprint<N>-unified-governed-plan.md` | `/compliance-check`, `/sprint-handoff` (steps 9–10) |
| **Story pack** | `docs/sprint<N>/userstory/*-techno-functional-pack.md` | `/plan-governed-stories` input |

## Invoker minimal prompts

**Per-story execution:**
```text
/implement-api
@governed_plan: .cursor/plans/sprint3/452768-governed-implementation-plan.md
```

**Sprint closure:**
```text
/compliance-check
@sprint_governed_plan: .cursor/plans/sprint3/sprint3-unified-governed-plan.md
```

**Planning from story pack only:**
```text
/plan-governed-stories
@story_artifact: docs/sprint3/userstory/nsi-entry-techno-functional-pack.md
```

Optional: `plan_step: <n>` — when omitted, select the **first `pending` or `in_progress` row** matching the invoked command.

---

## Resolve-before-ask mandate (mandatory)

When `governed_plan`, `sprint_governed_plan`, or `@story_artifact` is supplied:

1. **Resolve first** — load plan, story pack, `docs/sprint<N>/manifest.json`, per-story §Execution logs, and upstream artifact paths referenced in command flow rows.
2. **Ask only when** — §Gap register has **Open** critical items for this dimension, or a required binding is still `TBD` after resolution and repo introspection.
3. **Never re-ask** — for values present in plan §Workspace bindings, §Story pack, §Techno-functional sections, manifest entries, or §Execution log artifact paths.
4. **Document** — assumptions only when user authorizes assumption mode; write back to plan gap register or execution log.
5. **Skip Clarification Protocol questions** that are answerable from resolved inputs — proceed to Act.

---

## Resolution order (per-story `governed_plan`)

1. **Gap gate** — Open critical gaps for this command's dimensions → hard stop with numbered questions from gap register.
2. **Plan step** — Match row in §Command execution flow (`plan_step` or first pending for this command).
3. **Workspace bindings** — Shared keys (roots, namespace, contract version, envelope, platform).
4. **Story pack** — DDL, API mapping, UI workflow, wiring sequence, data tables (from per-story plan or linked techno-functional pack).
5. **Runtime bindings column** — Per-step overrides from command flow row.
6. **Upstream artifacts** — Paths from §Execution log for prior steps; `docs/sprint<N>/manifest.json` entries by `kind`.
7. **Repo introspection** — `infer` values from module roots (package layout, test conventions).
8. **Clarification** — Numbered questions **only** for remaining `TBD` (max 7 per round).

---

## Resolution order (sprint `sprint_governed_plan`)

1. Load `sprint<N>-unified-governed-plan.md`.
2. Resolve `sprint_number`, shared bindings, story index.
3. Aggregate evidence from:
   - `docs/sprint<N>/manifest.json` (all `kind` entries)
   - Each per-story plan's §Execution log (steps 1–8)
   - `docs/compliance/`, `docs/handoffs/`, `docs/quality/` as referenced
4. For `/compliance-check`: map controls to manifest + execution evidence; default `control_framework` from unified plan §Shared workspace bindings.
5. For `/sprint-handoff`: merge daily handoffs in sprint date range, story pack path, plan paths, manifest summary.
6. Update sprint unified §Sprint execution log and step status on completion.

---

## Story-as-input (`@story_artifact` for `/plan-governed-stories`)

When invoker supplies `@story_artifact` path to techno-functional pack:

| Auto-resolved input | Source |
|---------------------|--------|
| `story_backlog_sources` | `@story_artifact` path |
| `sprint_number` | Path `docs/sprint<N>/`, prompt, branch, or `SPRINT_NUMBER` |
| `scope_mode` | Default `full-stack`; override only if user specifies |
| `authoritative_refs` | Techno-functional DDL/API/UI sections + repo `migrations/`, contracts |

**Do not ask** for `story_backlog_sources` when `@story_artifact` is provided. Ask only for missing techno-functional dimensions flagged in pack gaps.

**Outputs:** per-story `<story-id>-governed-implementation-plan.md` **and** `sprint<N>-unified-governed-plan.md`.

---

## Section → runtime input mapping

| Plan section | Resolves (examples) |
|--------------|---------------------|
| §Workspace bindings | Roots, namespace, contract version, envelope, platform |
| §Story pack / techno-functional **DDL hints** | `source_schema_inputs`, tables |
| §Story pack / techno-functional **API surface** | `resource_inventory`, `endpoint_methods`, `critical_endpoints` |
| §Story pack / techno-functional **UI workflow** | `routes_and_surfaces`, `ux_reference`, `critical_journeys` |
| §Story pack / techno-functional **Data tables** | `mapping_artifacts` for wiring |
| §Wiring sequence specification | `api_sequence_specification` |
| §Traceability | Contract paths, story paths |
| §Execution log | Upstream artifact paths per step |
| `manifest.json` | All sprint artifact paths by `kind` |

---

## Per-story command flow (canonical order)

| Step | Command | Primary bindings |
|------|---------|------------------|
| 1 | `/implement-api` | DDL + API mapping + contract_version + module root |
| 2 | `/implement-ui` | UI workflow + wiring sequence + frontend root |
| 3 | `/expand-test-coverage` | Story AC + steps 1–2 artifacts |
| 4 | `/validate-api-endpoints` | API mapping + bindings + optional journey order from wiring spec |

## Sprint command flow (unified plan steps 9–10)

| Step | Command | Primary bindings |
|------|---------|------------------|
| 9 | `/compliance-check` | manifest + all execution logs + rules 17/02/15 |
| 10 | `/sprint-handoff` | manifest + plans + handoffs + story pack |

---

## Execution log updates (mandatory after command completes)

**Per-story plan:** update §Command execution flow status, §Execution log, `manifest.json`.

**Sprint unified plan:** update §Sprint command execution flow, §Sprint execution log.

Write discovered bindings back to §Workspace bindings — never leave confirmed values only in chat.

---

## Binding value conventions

| Value | Meaning |
|-------|---------|
| `infer` | Resolve from repo; document in execution log |
| `from §Story pack` / techno-functional section | Parse cited section |
| `from step N artifact` | §Execution log path for step N |
| `from manifest kind:<kind>` | `docs/sprint<N>/manifest.json` |
| `TBD` | Hard stop or gap register — never invent |

---

## Security

- No secrets in plans; reference secret **names** only.
- Redact PII per `.cursor/rules/02-security-and-compliance.mdc`.

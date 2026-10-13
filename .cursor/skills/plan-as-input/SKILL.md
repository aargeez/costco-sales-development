---
name: plan-as-input
description: Resolve-before-ask for governed execution. Use when a command receives @governed_plan, @sprint_governed_plan, or @story_artifact — load plan, manifest, logs, and upstream artifacts first; register Open DQ for remaining critical gaps or TBD bindings.
---

# Plan-as-input (resolve-before-ask)

Full protocol: `.cursor/templates/plan-as-input-protocol.md`.

## Trigger
`@governed_plan:`, `@sprint_governed_plan:`, or `@story_artifact:` on `/implement-api`, `/implement-ui`, `/plan-governed-stories`, `/deliver-sprint`, and other governed commands.

## Resolution order
1. Gap gate — Open **critical** items (authz / money / PII / breaking schema) → hard-stop.
2. Plan step — explicit `plan_step`, else first `pending` row for this command.
3. Workspace bindings — module roots, namespace, contract version.
4. Story pack — DDL / API / UI / wiring tables (cited or gap).
5. Upstream artifacts from the execution log.
6. Repo introspection only when non-behavioral.
7. Remaining `TBD` — register Open DQ with owner. Never invent a binding. Do not STOP for a clarification round except human gates.

## After Definition of Done
Update plan execution flow, execution log, and `docs/sprint<N>/manifest.json`.

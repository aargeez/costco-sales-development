# ATI session defaults gates (operator guide)

Factory guidance for **entry**, **search**, and **criteria** screens in `config.yaml` → `modules.ui`. Generic — not tied to one ATI.

## Objective

**Do not pre-fill session criteria on first load** unless an **approved Plan or BRD** cites the field and default rule. Lookups (dropdown options) and URL restore remain allowed.

## Why

- Default dates, warehouses, checkboxes, and thresholds are **behavior** ([rule 22](.cursor/rules/22-behavior-provenance-and-anti-invention.mdc)).
- Uncited `get*Defaults` + `else { apply all }` invents criteria the user did not choose.
- Uncited `useState('50')` or `default*CloseTypes()` seeds the same problem before API returns.

Navigation skill ([`ati-navigation-state`](../skills/ati-navigation-state/SKILL.md)) prevents **overwriting** URL restore; this gate prevents **uncited** defaults in the empty-URL path.

## When gates apply

| Stage | Action |
|-------|--------|
| `/plan-sprint`, `/plan-governed-stories` | SP with `implement-ui` + entry/search: defaults table or “no defaults” |
| `/implement-ui` | Session defaults validate + checklist in audit |
| `/fix-issue` | Frontend on entry/search: skill + sibling label **uncited session defaults / defaults on load** |

## Artifacts

| Artifact | Purpose |
|----------|---------|
| [`.cursor/skills/ati-session-defaults/SKILL.md`](../skills/ati-session-defaults/SKILL.md) | Recipe, provenance table, validate + sibling scan |
| [`.cursor/templates/audit-ui-session-defaults-checklist.md`](../templates/audit-ui-session-defaults-checklist.md) | Paste into command audit |

## Planner: citing defaults in SP rows

Under each **implement-ui** SP that includes entry/search:

```markdown
### UI session defaults
| Field | Default when URL empty? | FR/BR / plan cite |
| fromDate | yes — system date | FR-011 |
| warehouse | no | — |
```

Or: **No session defaults — user must select all criteria.**

## Implementer: running the gate

1. Load the skill; build provenance table from plan/BRD.
2. Fill session-defaults checklist.
3. In-slice code review + sibling scan (skill § Session defaults validate).
4. Record findings in audit **Validation**; FAIL refine if in-slice violates cites.

No standalone repo script — same model as shared-ui-molecules gate.

## Out of scope

- Mass refactor of existing screens (fix per approved slice only).
- Changing backend default endpoints (UI applies only cited response fields).

---
name: planner
description: Ingest story packs or an accepted BRD; emit governed/sprint plans whose execute rows are implement-* only.
---

# Planner skill

Use `.cursor/skills/plan-as-input/SKILL.md` then `plan-as-input-protocol.md`. Never emit generate-backend as a plan step. Modes: governed (`/plan-governed-stories`), sprint (`/plan-sprint`), bounded-context (`/plan-bounded-context`). Gap analysis is an internal step of those commands, not a public slash.

## UI session defaults (planning)

Do **not** assume entry/search screens pre-fill dates, warehouses, checkboxes, or thresholds. For each `implement-ui` work item that includes entry, search, or criteria UI, the plan must either:

1. Document a **UI session defaults** table (field, default rule, FR/BR cite), or  
2. State explicitly: **No session defaults — user must select all criteria.**

Silence is not approval to call `get*Defaults` or seed `useState` on the frontend. See `.cursor/skills/ati-session-defaults/SKILL.md` and `.cursor/templates/sprint-plan-template.md` § UI session defaults.

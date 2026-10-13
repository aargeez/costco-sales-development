# Behavior Provenance — {{SCOPE}}

> Aligns with `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`.  
> **Output path:** `artifacts/sprints/<sprint-id>-provenance.md` (sprint) or embedded in BRD/audit.

| Field | Value |
|-------|-------|
| **Scope** | {{sprint-id / command / feature}} |
| **Date** | {{YYYY-MM-DD}} |
| **Sources** | {{BRD path, OpenAPI, mockups, code baseline}} |

---

## Behavior Table

| behavior_id | statement | source_path | source_locator | row_driver | confidence | status |
|-------------|-----------|-------------|----------------|------------|------------|--------|
| BEH-001 | | | § / row / endpoint | legacy file + filter that creates the list line; `n/a` if not a list | explicit \| derived \| assumption | ready/blocked |

**`row_driver`:** For inquiry/list behaviors, name the BRE Tables used / IBMi file that **selects lines**. Optional amount files (adj, comments) are not the driver. Empty UI is not “no sample” until this file is empty for the key.

---

## Assumption Registry

| assumption_id | statement | owner | status | blocking | rationale |
|---------------|-----------|-------|--------|----------|-----------|
| ASSUMPTION-001 | | | pending_approval \| approved \| rejected | yes/no | |

---

## Conflict Log

| conflict_id | behavior_a | behavior_b | precedence applied | resolution |
|-------------|------------|------------|-------------------|------------|
| BLOCKED_CONFLICT-001 | | | story > UX > API > code | |

## Precedence reminder

Approved story/BRD → approved UX → approved API contract → existing implementation.

---

## Approval Log

| behavior_id / assumption_id | approved_by | date | notes |
|-----------------------------|-------------|------|-------|

# Incremental Change Plan — {{SLUG}}

> Required by `/fix-issue` cited-requirements mode. Write **before** production edits.  
> **Output path:** `artifacts/changes/{{YYYY-MM-DD}}-{{slug}}-plan.md`

| Field | Value |
|-------|-------|
| **Date** | {{YYYY-MM-DD}} |
| **Slug** | {{slug}} |
| **Command** | `/fix-issue` (cited-requirements) |
| **Agent** | change-agent |
| **Surface** | API \| UI \| both \| config \| schema |
| **Module** | {{module from config.yaml \| N/A}} |
| **Amount math in scope?** | yes \| no |
| **Status** | draft \| in_progress \| complete \| blocked |

---

## Sources

| source_path | locator | notes |
|-------------|---------|-------|
| | FR-xxx / SP-xxx / § / line / CALC-* | |

---

## Calculation catalog (required when amount math = yes)

| Field | Value |
|-------|-------|
| **Catalog path** | `artifacts/calculations/{{module}}-calculations.json` |
| **Loaded?** | yes \| no (if no → run `/extract-calculations`; do not code) |
| **On error / conflict** | This JSON is **primary** (rule 32) — align code/tests to `CALC-*`; do not rewrite catalog toward Java/SQL |
| **Schema** | `artifacts/calculations/calculation-catalog.schema.json` |

| req_id | calculation_id | placement.layer | formula (from catalog) | modern_impl path / symbol | parity before | gaps to close |
|--------|----------------|-----------------|------------------------|---------------------------|---------------|---------------|
| REQ-001 | CALC-… | data_layer \| api_layer \| ui_layer \| unclear | | | partial \| missing \| … | |

Catalog conflicts / assumptions affecting this change:

| id | impact | blocks Done? |
|----|--------|--------------|
| CONFLICT-… / ASSUMPTION-… | | yes \| no |

Implementation notes (honor `placement`; translate **every** catalog `branches[]` miss/zero/skip — do not COALESCE missing lookups to 0 unless the catalog says so; UI display-only):

- …

Post-change catalog write-back:

- [ ] `modern_impl` updated
- [ ] `parity.status` / `gaps` / `golden_refs` updated
- [ ] `placement.interim` cleared if snapshot became SoR (or N/A)
- [ ] N/A (no amount math)

---

## In scope

| req_id | statement | calculation_id (or N/A) | confidence | planned files / modules |
|--------|-----------|-------------------------|------------|-------------------------|
| REQ-001 | | CALC-… \| N/A | explicit \| derived | |

Acceptance checks (from source / catalog only):

- [ ] …

---

## Out of scope

Explicit non-goals (do not touch):

- …
- Other `CALC-*` not listed above
- Drive-by refactors, renames, dependency bumps
- Unrelated defects discovered during work (log under Open items; do not fix unless approved)
- Client-side recalculation of catalog formulas

---

## Structure fidelity notes

| Area | Existing pattern to preserve | Evidence path |
|------|------------------------------|---------------|
| | | |

New architecture / new folders required? **No** \| **Yes** (justify + human gate):

- …

---

## Assumption registry

| assumption_id | statement | owner | status | blocks Done? |
|---------------|-----------|-------|--------|--------------|
| ASSUMPTION-001 | | | pending_approval \| approved \| rejected | yes \| no |

---

## Conflict log

| conflict_id | sources | description | resolution |
|-------------|---------|-------------|------------|
| | | | |

---

## Implementation checklist

- [ ] Scope lock reviewed (ask user if draft had open questions)
- [ ] If amount math: catalog loaded + `calculation_id` mapped
- [ ] api-contract verify (N/A \| done)
- [ ] Code edits mapped 1:1 to In scope rows (formulas from catalog when math)
- [ ] Tests for changed behavior (incl. catalog `golden_refs` when present)
- [ ] If an IT writes shared vault, summary, or xref rows: `@BeforeEach` restore listed (or N/A)
- [ ] Catalog `modern_impl` / `parity` updated (or N/A)
- [ ] Provenance updated (cite `CALC-*` when math)
- [ ] Feature note updated (or no-op recorded)
- [ ] Diff re-checked for out-of-scope paths

---

## Diff summary (fill after Act)

| path | req_id | calculation_id | change type (add/edit/delete) | notes |
|------|--------|----------------|-------------------------------|-------|
| | | | | |

---

## Validation

| check | result | evidence |
|-------|--------|----------|
| Unit / IT / E2E | | `artifacts/test-results-*.md` |
| Scope-only diff | pass \| fail | |
| Provenance complete | pass \| fail | |
| Catalog formula fidelity | pass \| fail \| N/A | |
| Catalog parity write-back | pass \| fail \| N/A | |

---

## Open items

- …

## Next command

(From `/fix-issue` cited-requirements § Next command)

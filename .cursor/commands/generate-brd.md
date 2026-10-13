# /generate-brd

## Objective

Generate a structured BRD from meeting transcript(s) and supporting documents, matching the repo BRD format. Does **not** write user stories.

**On amount-math error / conflict:** `artifacts/calculations/<module>-calculations.json` is the **primary source** for formulas. Cite `CALC-*` in amount FRs; do not rewrite catalog math from transcript/BRE paraphrase. Missing/stale → `/extract-calculations`.

## Orchestration

| Role | Agent / Subagent |
|------|------------------|
| **Orchestrator** | orchestrator-agent |
| **Primary** | requirements-agent |
| **Subagents** | transcript-analyzer → supporting-doc-synthesizer → open-questions-cataloger → brd-writer |
| **Pre-step (ATI / amounts)** | calculation-catalog-agent via `/extract-calculations` if `<module>-calculations.json` missing **or** inquiry/row-set `CALC-*` not extracted for Tables-used programs |

## Runtime Inputs

### Required

- Path to transcript(s)

### Optional

- Supporting documents (or confirm transcript-only)
- Module name, legacy module name, bounded context
- Updating existing BRD vs new draft
- Calculation catalog path (or confirm `/extract-calculations` first)

## Product mode (Audit vs Vault)

Resolve active product from **`config.yaml`** (and operator intent). Load **`rules/overlays/sales-vault/scope.mdc`** only when the product is Sales Vault.

| Product | Primary requirements source | `/generate-brd` role |
|---------|----------------------------|----------------------|
| **Sales Audit** (typical) | Transcript + BRE/workbooks → **`artifacts/brd/<module>-BRD-draft.md`** via this command | **Default:** run with Required transcript path |
| **Sales Vault** | **`vault.source_of_truth`** or **`modules.brd`** (resolved path; often a consolidated pack at repo root) | **Do not** overwrite the consolidated SoT from transcript without PO sign-off. Use this command only when the operator explicitly wants a **new draft**, **module delta**, or **intake notes** from transcript/supporting docs — output to a **named path** the operator supplies (or a new `artifacts/brd/` draft), not silent replacement of the configured SoT |
| **Vault + amounts** | SoT + `artifacts/calculations/<module>-calculations.json` when in scope | Same as Vault row; amount FRs still cite `CALC-*` (rule 32) |

For Vault API/UI delivery, **`/plan-sprint`** and implement commands trace to the **resolved SoT** and OpenAPI — not Gherkin packs unless the operator declares story-driven mode (`generate-user-story.md`).

## Enforce Rules

- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/11-intake-planning-governance.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/09-agent-orchestration.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/31-untrusted-intake.mdc`
- `.cursor/rules/33-reverse-engineering-quality.mdc` (when interpreting legacy sources)
- `.cursor/rules/32-calculation-catalog.mdc` (when module has amount math)
- `.cursor/rules/overlays/sales-vault/scope.mdc` (when active product is Sales Vault — SoT path and scope policy)

## Intake safety gate

Raw transcripts and supporting docs only — not factory BRD/plan outputs. Load `.cursor/skills/intake-safety/SKILL.md`. Run `python3 scripts/intake-safety-scan.py` on those paths **before** spawning requirements-agent. Exit 2 → STOP. Exit 1 → STOP until redaction or `docs/harness/decisions.md` waiver. Exit 0 → continue.

## Use Templates

- `.cursor/templates/transcript-intake-notes-template.md`
- `.cursor/templates/brd-template.md`
- `.cursor/templates/discussion-coverage-matrix-template.md`
- `.cursor/templates/behavior-provenance-template.md` (§17 behavior table in BRD)
- `.cursor/templates/calculation-catalog-template.md`
- `.cursor/templates/audit-trail-template.md`
- `.cursor/templates/plan-act-observe-reflect-loop.md`

## Use Skills

- `.cursor/skills/intake-safety/SKILL.md`
- `.cursor/skills/brd-authoring/SKILL.md`
- `.cursor/skills/calculation-catalog/SKILL.md`

## Clarification Protocol

After resolve-before-ask, register Open DQ if missing:

1. Transcript path(s)
2. Supporting documents or transcript-only confirmation
3. Module / bounded context
4. New draft vs update of an existing BRD
5. Calculation catalog path (or confirm `/extract-calculations` first)

Do not invent FR/BR from silence. Conflicts become DQ-xxx, not resolved requirements. Do not STOP for a clarification round except human gates. Still refuse when Required transcript path is absent. Output remains **Draft — Pending Domain Review** — never self-approve; `/plan-sprint` cannot start until Accepted or waiver.

## Execution Policy

Plan → Act → Observe → Reflect. Max 3. Then escalate with open DQs.

## Definition of Done

- [ ] Intake-safety scan exit 0, or exit 1 with recorded human redaction/waiver
- [ ] Intake notes written with source traceability (incl. Tables used + DD subfile map in intake §8)
- [ ] Draft BRD at `artifacts/brd/<module>-BRD-draft.md` (sections 1–17 incl. provenance stub)
- [ ] Amount FRs cite `CALC-*` from `artifacts/calculations/<module>-calculations.json` (or DQ for gaps)
- [ ] **Inquiry / list FRs** name **row driver**, **optional joins** (left-join; missing join does not drop the line), **empty condition**, and `CALC-*` for amount columns (brd-template §7.6)
- [ ] Every BRE **Tables used** Input row for in-scope programs is an FR, §9 statement, or explicit Out of Scope
- [ ] Every Data dictionary **display-file / subfile** field is an FR, a `CALC-*`, or explicit Out of Scope
- [ ] Did not map several BRE rules with Present Logic → one vague FR
- [ ] §13 discussion coverage matrix complete
- [ ] §14 open questions with owners and Blocking flags
- [ ] Status: **Draft — Pending Domain Review**
- [ ] `artifacts/audit/<date>-generate-brd-<task>.md` written
- [ ] Harness updated (phase-changing)
- [ ] Stop before `/plan-sprint`

## Outputs

- `artifacts/intake/<date>-intake-notes.md`
- `artifacts/brd/<module>-BRD-draft.md`
- `artifacts/audit/<date>-generate-brd-<task>.md`

## Next command

> ✅ `/generate-brd` complete.
> **Next command:** `/plan-sprint` — **after** domain review. Do not run it while status is Draft.

| Condition | Next command |
|-----------|--------------|
| Calc catalog missing for ATI module | `/extract-calculations` first, then re-run `/generate-brd` amount sections |
| BRD needs domain review (default) | Wait — review the draft, then `/plan-sprint` |
| BRD already Accepted | `/plan-sprint` |

**Example (after BRD approved):**

```
/plan-sprint
BRD: artifacts/brd/saro-BRD-draft.md
Sprint ID: sprint-01
Goal: Validation chain + entry flow MVP
Duration: 2 weeks
```

**Do not run** `/plan-sprint` until BRD status is **Accepted** or you explicitly waive domain review in chat.

# /generate-docs

## Objective
Produce a **unified documentation pack** for a feature, capability slice, or release: technical architecture/API/operations content, evidence index to sprint artifacts, Dev/QA/Ops-facing material, release notes, and rollback guidance. Use **after** implementation and quality commands have persisted slice outputs — this command **consolidates and links**; it does not replace per-command artifacts.

**Replaces** the former `/generate-documentation` workflow in one artifact.

## Runtime Inputs (must be provided when invoking command)
- `feature_or_release_id`: naming segment for the pack (e.g. `sprint3-nsi-worklist`, `release-2026.06`).
- `doc_scope` (optional): `technical` | `release` | `full` (default **`full`**).
  - `technical` — architecture, API, operations, ADRs, evidence index only.
  - `release` — Dev/QA/Ops sections, release notes, rollback, PR summary only.
  - `full` — all sections below.
- `artifact_index` (optional): paths to upstream outputs (governed plans, `docs/sprint<N>/`, contracts, test coverage, api-validation, reviews, compliance).
- `release_version` (optional): version or train id when `doc_scope` includes release content.
- `governed_plan` (optional): `.cursor/plans/sprint<N>/<story-id>-governed-implementation-plan.md`.
- `plan_step` (optional): row in §Command execution flow; default first `pending` row for `/generate-docs`.

## Plan-as-input mode

When `governed_plan` is supplied, resolve `feature_or_release_id` from §Workspace bindings (`artifact_namespace` + `sprint_number`), and `artifact_index` from §Execution log + §Traceability + `docs/sprint<N>/manifest.json`. Hard-stop if required upstream steps are not `complete`. Update plan execution sections on completion.

## Use Agents
- documentation-agent
- tech-spec-writer-agent
- release-agent

## Delegate to Subagents
- doc-structure-planner
- tech-spec-formatter
- pr-summary-writer

## Enforce Rules
- .cursor/rules/00-global-governance.mdc
- .cursor/rules/08-documentation-traceability.mdc
- .cursor/rules/28-techspec-and-doc-quality.mdc
- .cursor/rules/15-release-readiness.mdc

## Use Templates
- .cursor/templates/tech-spec-template.md
- .cursor/templates/adr-template.md
- .cursor/templates/traceability-matrix-template.md
- .cursor/templates/release-readiness-template.md
- .cursor/templates/pr-template.md

## Required Output (single unified artifact)
Persist **one** markdown file. Populate sections per `doc_scope`; mark N/A only with explicit rationale.

1. **Technical overview** — architecture, API summary, operations notes, ADRs (or links).
2. **Evidence index** — table linking business rule → story → contract → implementation → test → review/compliance artifacts (paths under `docs/sprint<N>/`, `docs/api/contracts/`, `docs/quality/`, etc.).
3. **Assumptions and open questions** — owners and due actions.
4. **Dev / QA / Ops** — audience-specific how-to-run, environments, test entry points, operational contacts.
5. **Release notes** — user- or train-visible changes for the scoped release.
6. **Rollback and recovery** — steps, triggers, and owners (no blank placeholders).
7. **PR / change summary** (when releasing) — concise change description for reviewers (`pr-summary-writer` output merged here or linked).

## Execution Policy
- Execution loop: Plan -> Act -> Evaluate -> Refine.
- Max iterations: 3 (escalate unresolved items after final iteration).
- **Plan:** Resolve `doc_scope`; inventory `artifact_index`; define outline from templates.
- **Act:** Draft unified pack; cross-link existing artifacts (do not duplicate full contract/test bodies unless summarizing).
- **Evaluate:** `doc-structure-planner` checks information architecture, section completeness, traceability, and owner clarity; `pr-summary-writer` checks release notes and PR summary when release sections are in scope.
- **Refinement:** Patch in-place; fix orphan sections and broken links.

## Artifact Persistence
- **Canonical:** `docs/documentation/<feature-or-release-id>-documentation-pack.md`
- **Sprint mirror (optional):** `docs/sprint<sprint_number>/documentation/<feature-or-release-id>-documentation-pack.md` and append to `manifest.json` with `kind`: `documentation-pack`.
- Include metadata header: timestamp, iteration count, validation summary, `doc_scope`, command version (`/generate-docs`).
- Manifest entry example:
```json
{
  "path": "docs/sprint<sprint_number>/documentation/<feature-or-release-id>-documentation-pack.md",
  "kind": "documentation-pack"
}
```

## Related Commands
- Per-slice outputs: `/implement-api`, `/implement-ui`, `/expand-test-coverage`, `/validate-api-endpoints`, `/review-code`, `/compliance-check`.
- Tech spec artifacts from **tech-spec-writer-agent** — summarize or link here, do not replace unless scope is spec-only.
- `/generate-runbook`, `/prepare-cutover` — deep ops/cutover detail; link from Ops and rollback sections.
- `/sprint-handoff` — narrative handoff; link from evidence index, do not duplicate session themes.

## Placement in sprint flow
Run **once per slice or release**, after `/review-code` and `/compliance-check`, before `/sprint-handoff` — not between build/wiring/test commands.

## Command-Specific Hardening
- **Do** link to existing artifacts; summarize — do not duplicate full contract/test bodies.
- **Do** populate evidence index with repo paths, not wiki-only references.
- **Don't** fabricate release notes without `artifact_index` corroboration.
- **Stop** when `feature_or_release_id` is missing.

## Clarification Protocol

**Low gate:**

1. What is `feature_or_release_id` and `doc_scope` (`technical` | `release` | `full`)?
2. What paths are in `artifact_index` (or should the agent scan `docs/sprint<N>/`)?

## Definition of Done

- [ ] All sections required by `doc_scope` are complete with evidence links (no generic “see wiki” without paths).
- [ ] `doc-structure-planner` reports no critical orphan sections, broken outline, or missing cross-links.
- [ ] When release sections are in scope: `pr-summary-writer` reports no critical gaps in release notes, rollback steps, or PR summary; release identifiers are consistent.
- [ ] Templates fully populated; no lorem ipsum, empty evidence tables, or unowned open items blocking release.

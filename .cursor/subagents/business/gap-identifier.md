# Subagent: Gap Identifier

## Purpose
Compare user stories against UX flows, API needs, and data model artifacts to surface missing acceptance criteria and cross-artifact dependency gaps.

## Required Inputs
- `stories`: structured list or document — user stories with titles, descriptions, and any stated acceptance criteria.
  - **Documentation mode** (from `/implement-api` Phase 1 / api-agent): substitute §9 FR-* and §6 BR-* rows filtered to `capability_scope` as the story list; do not require Gherkin or sprint story packs.
- `ux_artifacts`: UX flow specs, wireframes, or journey notes — expected screens, transitions, and user-visible outcomes.
  - **Documentation mode:** substitute `vault-source-of-truth.md` §8 screen options/menus for the scoped screen(s); mark N/A when the slice is API-only with no UI coupling.
- `api_needs`: API contract drafts, endpoint lists, or integration requirements — operations the slice must support.
  - **Documentation mode:** use `operation_candidates` from `flow-simplifier` when no draft contract exists yet.
- `data_model`: target schema, entity list, or data dictionary — entities, relationships, and key fields relevant to the slice.
  - **Documentation mode:** `.cursor/plans/sales-vault-data-model.md` + `sales-vault-ddl/` for the scoped tables.
- `scope_boundary` (optional): string — slice or epic ID to limit comparison when inputs are large.
  - **Documentation mode:** set to `capability_scope`.

## Outputs
- `gap_report`: markdown or structured record — per-story (or per-capability) list of missing AC, uncovered UX steps, API gaps, and data-model mismatches; dependency gaps (blocking vs. parallel).
- `traceability_matrix_delta`: table — story ID → linked UX/API/data artifact IDs with coverage status (covered / partial / missing).
- `recommended_actions`: list — prioritized follow-ups (e.g., add AC, extend contract, align field names).
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., implicit UX behavior not documented, API versioning unknown, legacy field semantics unclear.

## Failure Modes
- Incomplete or conflicting stories, missing UX/API/data inputs, or ambiguous scope produce a partial gap list only; ambiguous items are flagged, not silently closed.
- Input types that are absent by design for the calling context (e.g. no `data_model` for a pure UI slice, or no `api_needs` for a content-only story) are reported as `N/A` in coverage, not as `missing` gaps, so the same subagent degrades cleanly across business, frontend, and forward-engineering callers.
- Escalation: return partial result with explicit gaps to parent agent

## Quality Gate
- Parent accepts when every story in scope is scored for UX/API/data coverage, dependency gaps are classified (blocker vs. non-blocker), and at least one concrete AC or artifact pointer is suggested per identified gap; reject if output is generic (“review UX”) without traceable story/artifact IDs.
- **Documentation mode:** score every in-scope FR-* and BR-* row; treat §12 Known Gaps as **blocker** when they affect contract semantics for `capability_scope`.
# Subagent: INVEST Validator

## Purpose
Evaluate user stories against Independent, Negotiable, Valuable, Estimable, Small, and Testable (INVEST) criteria and return pass/fail per dimension with concrete rewrite suggestions.

## Required Inputs
- `stories`: list — story objects or text blocks with title, description, acceptance criteria, and dependencies if known.
- `team_context` (optional): string — typical sprint capacity or “small” threshold (e.g., story points or days) for Estimable/Small judgment.
- `dependency_map` (optional): artifact — known upstream/downstream story or system dependencies.
- `traceability_matrix_delta` (optional): table — per-input-type coverage (AC/Business-Rules, DB Schema, UI) from `gap-identifier`. Used to gate AC/Business-Rules coverage; when absent, AC coverage is judged from the story text only.

## Outputs
- `invest_scores`: structured result — per story: Independent / Negotiable / Valuable / Estimable / Small / Testable each pass | fail | unclear with one-line rationale.
- `rewrite_suggestions`: list — bullet rewrites (split story, rephrase AC, add test hook, remove technical prescription) keyed by story ID and INVEST letter.
- `summary`: string — overall readiness (ready / needs refinement / blocked).
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., business value not documented, unknown technical spike, missing AC for Testable.

## Failure Modes
- Stories without AC, missing titles, or paste-only legacy text limits Testable/Small scoring; validator marks dimensions unclear and lists minimum fields needed.
- Escalation: return partial result with explicit gaps to parent agent

## Exit Gate (differentiated by input type)
- **Hard stop** (`summary = blocked`): when `traceability_matrix_delta` is provided and any AC or Business-Rules coverage row is `partial` or `missing`, or when Valuable or Testable fails. A story in this state must not exit the business phase.
- **Flag and refine** (`summary = needs refinement`, do not block): when DB-Schema or UI coverage is `partial`/`missing`. Surface the gap and route for refinement, but do not block exit — schema/UI are not yet authoritative at story stage and are validated downstream (`schema-migration-generator`, `tools/validate_ddl_coverage.py`, and the artifact commit-gate).
- **Confidence**: if confidence is `low` on any hard-stop dimension, escalate via `unresolved_assumptions` instead of returning a silent pass.
- **Graceful degrade**: when `traceability_matrix_delta` is absent, judge AC from the story text and mark missing AC as `unclear`, not `blocked`.

## Quality Gate
- Parent accepts when every story has all six INVEST dimensions scored with rationale, every fail or unclear has at least one actionable rewrite, and summary reflects the worst-case dimension and the Exit Gate above; reject if output is only pass/fail without per-dimension evidence, or if a story with `partial`/`missing` AC or Business-Rules coverage is returned as `ready`.

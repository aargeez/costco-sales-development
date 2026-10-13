# Subagent: Code Cleanup Analyzer

## Purpose
Analyzes a bounded codebase slice to identify duplication, dead code, oversized methods, and naming inconsistencies, then proposes behavior-safe refactors prioritized by risk, impact, and testability.

## Required Inputs
- `scope`: paths or module list — directories, packages, or repos to analyze (bounded to avoid unbounded churn).
- `language_and_build`: string — e.g., Java/Spring Boot, TypeScript/Next.js, Python; build and test commands if known.
- `behavioral_constraints`: string (optional) — areas frozen for release, public APIs not to rename, or compatibility requirements.

## Outputs
- `findings_catalog`: table — id, category (duplication | dead_code | long_method | naming | coupling), location (file/symbol), evidence (line counts, similarity note), severity.
- `refactor_recommendations`: ordered list — each with rationale, estimated risk (low/medium/high), suggested approach, and prerequisite tests.
- `safe_quick_wins`: list — low-risk changes suitable for immediate PRs vs deferred items.
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., reflection/dynamic use hiding dead code, cross-repo callers unknown, generated code.

## Failure Modes
- Underspecified scope leads to sampling disclaimer and findings marked partial for the analyzed paths only.
- Without runnable tests or static analysis, findings rely on heuristics and carry higher false-positive risk, reflected in confidence.
- Escalation: return partial result with explicit gaps to parent agent — ship `findings_catalog` with confidence downgraded and list files/symbols not analyzed.

## Quality Gate
- Parent accepts when findings cite concrete symbols/lines, refactor proposals preserve behavior or call out required characterization tests, and priorities align with risk/impact.
- Parent rejects when recommendations rename public contracts without migration plan, suggest deletes without reference analysis, or present high confidence without evidence.

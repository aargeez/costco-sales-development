# Subagent: Gherkin Writer

## Purpose
Convert approved business rules into Given/When/Then Gherkin scenarios covering happy path, negative path, and edge cases in business-readable, test-ready language.

## Required Inputs
- `business_rules`: catalog or bullet list — approved rules, decisions, or story-derived behaviors to encode.
- `glossary` (optional): term → definition map — domain vocabulary for consistent step wording.
- `story_context` (optional): story IDs and backgrounds — links scenarios to backlog items for traceability.
- `format_constraints` (optional): string — feature file layout, tag conventions, or max scenario length.

## Outputs
- `feature_content`: Gherkin text — Feature/Background/Scenario/Scenario Outline with tags; happy, negative, and edge scenarios per rule where applicable.
- `scenario_index`: list — scenario title → rule ID / story ID mapping.
- `open_questions`: list — rules needing SME clarification before final sign-off.
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., default user role, unspecified error messages, timing/order of system events.

## Failure Modes
- Unapproved or contradictory rules, missing glossary for overloaded terms, or incomplete rule text yields scenarios marked as draft with explicit TODOs.
- Escalation: return partial result with explicit gaps to parent agent

## Quality Gate
- Parent accepts when each input rule maps to at least one scenario, negative and at least one edge case are present where the rule implies them, steps use glossary terms consistently, and no duplicate scenario titles; reject if steps are implementation-specific (UI selectors, SQL) instead of behavior-level.

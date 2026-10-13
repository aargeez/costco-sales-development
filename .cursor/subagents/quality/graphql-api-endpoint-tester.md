# Subagent: GraphQL API Endpoint Tester

## Purpose
Validate GraphQL query behavior, argument validation, and response contracts for success and failure scenarios.

**Sales Vault note:** Contract pack v1.0.0 is **REST-primary** (`primary_interface: REST`). Invoke this subagent only when OpenAPI/contract metadata declares GraphQL or `primary_interface` is `GraphQL` / `hybrid`. Otherwise return `OUT_OF_SCOPE_GRAPHQL` with connectivity skip and let parent complete REST-only validation.

## Required Inputs
- `base_url`: string — GraphQL service URL, e.g. `http://localhost:8080/graphql`.
- `operations`: list — GraphQL queries/mutations and required variables (from contract SDL when present).
- `capability_scope` (optional): filters operations when catalog is scoped.
- `data_source_selector`: string (optional) — header-based selector such as `<db-header-name>: <secondary-source>`.
- `scenario_policy`: object — required positive, malformed query, and missing-argument tests.

## Outputs
- `operation_results`: table — operation, variables, expected, actual, status.
- `schema_contract_checks`: list — required `data`/`errors` behavior and key field assertions.
- `validation_findings`: list — severity, operation, issue, recommended remediation.
- Confidence: high | medium | low
- Unresolved assumptions: list

## Failure Modes
- If GraphQL is not declared in contract for `capability_scope`, return `OUT_OF_SCOPE_GRAPHQL` — REST validation proceeds without this subagent.
- If operation definitions are missing, return `NEEDS_OPERATION_CATALOG` and only run connectivity smoke checks.
- If schema mismatch blocks assertions, return partial results with explicit query/field mismatch notes.
- Escalation: return partial result with explicit gaps to parent agent.

## Quality Gate
- Parent accepts when required operations and negative paths are executed with explicit assertion evidence.
- Parent rejects when responses are not checked for GraphQL `errors` semantics or argument validation behavior.

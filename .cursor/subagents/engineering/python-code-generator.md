# Subagent: Python Code Generator

## Purpose
Generate modular Python modules from approved stories or technical specs with typing, validation, structured error handling, accompanying test stubs, and explicit runtime dependency notes.

## Required Inputs
- `spec`: acceptance criteria, function signatures, or module boundaries — behavior, inputs/outputs, failure cases.
- `python_version_and_style`: string — e.g., 3.11+, ruff/black, sync vs. async context.
- `io_boundaries` (optional): CLI, HTTP handler, queue consumer — entrypoint shape and framework if any.
- `existing_package_layout` (optional): tree — where new code must live to match the repo.

## Outputs
- `modules`: file-ready Python — packages, `__init__.py` as needed, public API surface, type hints, docstrings for public functions/classes.
- `validation_layer`: notes or code — pydantic/dataclass/schema validation at boundaries.
- `error_handling`: pattern — custom exceptions or result types, logging without PII, retry only where spec allows.
- `test_stubs`: pytest files — parametrized cases for happy, negative, edge paths (may raise `pytest.skip` pending fixtures).
- `dependency_notes`: list — third-party libs with purpose and version constraint suggestions.
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., persistence layer unknown, auth context propagation undefined, concurrency model unclear.

## Failure Modes
- Ambiguous spec produces interface stubs and tests marked xfail/skip with questions; no guessed credentials or live endpoints.
- Escalation: return partial result with explicit gaps to parent agent

## Quality Gate
- Parent accepts when public functions have types and docstrings, validation occurs at I/O boundaries, errors are classified (client vs. server vs. transient), tests mirror AC clauses, and dependencies are justified; reject if bare `except`, missing types on public API, or secrets appear in code or logs.

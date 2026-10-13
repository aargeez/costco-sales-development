# Subagent: Test Results Recorder

## Purpose
Parses test runner output into structured pass/fail metrics, captures execution command, environment, and timestamp, and documents failures with error excerpts and attributed root cause where inferable, aligned to `test-results-log-template` fields.

Supports **documentation-driven** slices: attach `capability_scope`, `contract_version`, and scenario IDs (API-001, OpenAPI `operationId`) with FR-/BR-* locators — not user story IDs unless story packs are the declared input.

## Required Inputs
- `raw_output`: string — stdout/stderr or CI log excerpt from the test run.
- `command_invoked`: string — full command or CI job step that produced the output.
- `environment_fingerprint`: object — OS, runtime versions, branch/commit, service dependencies, and dataset label if relevant.
- `suite_scope`: string (optional) — module, tag filter, or shard identifier for the run.
- `capability_scope` / `contract_version` (optional): Sales Vault traceability labels for persisted evidence.
- `traceability_mode` (optional): `documentation` (default for Sales Vault) | `story_pack`.

## Outputs
- `metrics`: object — total, passed, failed, skipped, duration, flaky markers if detectable.
- `failure_records`: list — test name, file/line if present, error excerpt (sanitized), stack snippet, hypothesized root cause category (product | test | env | infra) with rationale.
- `test_results_log`: structured document — fields compatible with project `test-results-log-template` (timestamp, command, env, summary, failures, artifacts, traceability to FR-/BR-*/OpenAPI or story AC per mode).
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., truncated logs, parallel suite interleaving, unclear commit under test.

## Failure Modes
- Unparseable or partial logs: extract what is verifiable; remainder flagged PARSING_GAP.
- Missing command or env: record with UNKNOWN and lower confidence.
- Escalation: return partial result with explicit gaps to parent agent — deliver `metrics` and `failure_records` for parsed tests only; list unparsed sections in `Unresolved assumptions`.

## Quality Gate
- Parent accepts when counts reconcile with listed tests or discrepancies explained, PII/secrets are redacted, timestamps and command are present or explicitly unknown, template-mapped `test_results_log` is complete for available data, and traceability uses documentation locators in Sales Vault mode.
- Parent rejects when failure attribution states certainty without evidence, metrics contradict the raw log without note, sensitive data appears unredacted, or story IDs are cited without story-pack mode.

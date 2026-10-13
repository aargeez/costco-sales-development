# Subagent: Performance Profiler

## Purpose
Defines representative performance scenarios for a capability, records baseline measurements, and identifies bottlenecks with optimization options aligned to GCP and replatformed service boundaries.

## Required Inputs
- `capability_under_test`: string — API surface, batch job, or user journey to profile.
- `sla_or_nfr_targets`: object (optional) — latency, throughput, concurrency, and data volume expectations.
- `runtime_context`: string — environment (local/stage/prod-like), infrastructure (e.g., GKE, Cloud Run), and dataset characteristics.
- `tooling_access`: string — available profilers, APM, load tools, and whether execution is descriptive-only vs actual benchmark runs.

## Outputs
- `perf_scenarios`: list — scenario name, load model, key transactions, think time, payload sizes, success criteria.
- `baseline_results`: table — scenario × metric (p50/p95/p99, RPS, error rate, resource saturation) with timestamp and environment fingerprint.
- `bottleneck_analysis`: list — ranked hotspots (component, evidence, suspected root cause) and proposed optimizations (config, code, scaling, caching).
- `repro_commands`: string — exact commands or scripts to repeat measurements.
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., synthetic vs production traffic, cold starts ignored, missing APM access.

## Failure Modes
- Cannot execute tests: deliver scenario catalog and methodology only; `baseline_results` empty with reason.
- Ambiguous scope or missing endpoints prevents meaningful load model; scenarios marked NEEDS_INPUT.
- Escalation: return partial result with explicit gaps to parent agent — include defined scenarios and `bottleneck_analysis` only where evidence exists; otherwise document blockers.

## Quality Gate
- Parent accepts when scenarios map to real user or system behavior, baselines include environment metadata, and bottlenecks tie to observed metrics or profiles (or document absence of data).
- Parent rejects when conclusions lack measurement backing, targets are undefined while claiming pass/fail, or optimizations violate correctness without trade-off note.

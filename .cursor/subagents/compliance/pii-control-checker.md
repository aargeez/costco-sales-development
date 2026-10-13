# Subagent: PII Control Checker

## Purpose
Identify personal and sensitive data fields in scope, validate masking, retention, and access-control expectations against stated policies, and output compliance gaps with remediation actions.

## Required Inputs
- `data_inventory`: schema, API payloads, log field list, or screen data map — fields and flows under review.
- `policy_references`: links or excerpts — org retention, classification, masking, and least-privilege standards applicable to the slice.
- `processing_context` (optional): string — purpose (e.g., analytics, transactional, support tooling), regions, and data stores.

## Outputs
- `pii_register`: table — field or attribute path → PII/sensitive classification → lawful basis or policy citation (if stated in inputs).
- `control_assessment`: structured list — per field or flow: masking in logs/traces/UI, retention rule, access path (roles/keys); status pass | gap | unknown.
- `compliance_gaps`: list — gap description, severity, suggested remediation (technical and procedural).
- Confidence: high | medium | low
- Unresolved assumptions: list — e.g., missing legal basis doc, unclear data residency, third-party subprocessors not listed.

## Failure Modes
- Missing inventory or policies forces unknown classifications; checker lists required inputs and flags high-risk fields conservatively.
- Escalation: return partial result with explicit gaps to parent agent

## Quality Gate
- Parent accepts when every named field in inventory appears in `pii_register` or explicit exclusion rationale, each gap ties to a policy clause or “policy not provided,” and remediation is actionable; reject if PII is asserted without path-level traceability or gaps lack severity.

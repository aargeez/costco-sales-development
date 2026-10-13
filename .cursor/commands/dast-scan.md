# /dast-scan

## Objective
Dynamic application security test of **this product's** running app (localhost or listed staging). Map findings to OWASP. **security-agent** mode `dast`. Not a 20th primary. Not SAST and not a pentest of third parties.

## When to use
- App is running locally or on a staging URL the user owns
- After `/sast-scan` or `/validate-api-endpoints`
- Feeds KPI-01; does not replace `/review-changes`

## Runtime Inputs

### Required
- `target_url` of **this** application (localhost, loopback, or staging host the operator confirms they own)

### Optional
- OpenAPI path (`modules.openapi` / `contracts/openapi`)
- `scan_depth`: `quick` | `standard`
- tool: `zap` | `nuclei-safe` | auto-detect (headers/auth/error-leakage only)

## Plan-as-input mode
When a governed or sprint plan is supplied, apply `.cursor/templates/plan-as-input-protocol.md` before Clarification Protocol.

## Use Agents
orchestrator-agent → security-agent (mode `dast`)

## Delegate to Subagents
dast-scanner → security-scanner (OWASP map)

## Enforce Rules
02, 09, 10, 21

## Use Templates
`dast-findings-template.md`, `audit-trail-template.md`

## Required Output
- `artifacts/security/<date>-dast-findings.md`
- `artifacts/audit/<date>-dast-scan-<task>.md`

## Command-Specific Hardening
- **Authorized target only.** Refuse public third-party production, random internet hosts, and credential stuffing.
- Must NOT write exploits, exploit PoCs, malware, or attack procedures. Report missing headers, unauthenticated OpenAPI ops, verbose errors, CORS misconfig — no payloads that weaponize them.
- Redact tokens, cookies, and PII from evidence. Stay in `scope.yaml`.
- Missing running app → STOP and ask; do not invent runtime findings.

## Execution Policy
Plan → Act → Validate → Refine (max 3 ceiling). Iteration 1 Validate pass → stop. Refine only from findings. Escalate with owners after iteration 3.

## Clarification Protocol
After resolve-before-ask, register Open DQ when `target_url` is missing or is not clearly this product. Do not invent a target. Do not STOP for a clarification round except human gates. Still refuse when Required `target_url` is absent.

## Artifact Persistence
Canonical paths above. Metadata: timestamp, iteration, target host (no secrets).

## Definition of Done
- [ ] Target is this product's local/staging URL
- [ ] Findings table with OWASP, severity, evidence (redacted), owner
- [ ] Unowned Critical/High listed, not dropped
- [ ] No exploit payloads in artifacts
- [ ] `artifacts/security/<date>-dast-findings.md` written
- [ ] `artifacts/audit/<date>-dast-scan-<task>.md` written

## Next command

> ✅ `/dast-scan` complete.
> **Next command:** `/threat-profile` or `/review-changes` if Critical/High remain.

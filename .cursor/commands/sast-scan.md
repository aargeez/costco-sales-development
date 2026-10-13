# /sast-scan

## Objective
Static application security test of **this repo's** API/UI/infra sources. Map findings to OWASP. **security-agent** mode `sast`. Not a 20th primary. Not DAST and not threat modeling.

## When to use
- After `/implement-api` / `/implement-ui` or before merge
- From `/review-changes` when a dedicated SAST evidence pack is required
- Feeds KPI-01 / KPI-09; does not lower SAST gates

## Runtime Inputs

### Required
- Scan roots: `modules.api` / `modules.ui` / `modules.infra` from `.cursor/config.yaml` (or explicit paths in this repo)

### Optional
- `governed_plan`
- tool: `semgrep` | `spotbugs` | `pmd` | `codeql` | `eslint-security` | auto-detect
- `scan_depth`: `quick` | `standard` | `deep`

## Plan-as-input mode
When a governed or sprint plan is supplied, apply `.cursor/templates/plan-as-input-protocol.md` before Clarification Protocol.

## Use Agents
orchestrator-agent → security-agent (mode `sast`)

## Delegate to Subagents
sast-scanner → security-scanner (OWASP map)

## Enforce Rules
02, 09, 10

## Use Templates
`sast-findings-template.md`, `audit-trail-template.md`

## Required Output
- `artifacts/security/<date>-sast-findings.md`
- `artifacts/audit/<date>-sast-scan-<task>.md`

## Command-Specific Hardening
- Scan **this repository** only. No secrets in artifacts (redact).
- Run the installed toolchain when present. Missing tool → `NEEDS_TOOL` plus evidence-backed static review of auth, injection sinks, and secrets in own modules. Do not invent CVE scores.
- Must NOT write exploits, PoCs, or attack payloads. Findings are file:line + OWASP + severity + owner.
- Must NOT lower SAST gates in `pom.xml` / CI. Stay in `scope.yaml`.

## Execution Policy
Plan → Act → Validate → Refine (max 3 ceiling). Iteration 1 Validate pass → stop. Refine only from findings. Escalate with owners after iteration 3.

## Clarification Protocol
After resolve-before-ask, register Open DQ when module roots are still placeholders or scan scope is missing. Do not invent findings. Do not STOP for a clarification round except human gates. Still refuse when Required scope is absent.

## Artifact Persistence
Canonical paths above. Metadata: timestamp, iteration, tool used or `NEEDS_TOOL`.

## Definition of Done
- [ ] Findings table with OWASP, severity, file:line, owner (or none with rationale)
- [ ] Unowned Critical/High listed, not dropped
- [ ] Secrets redacted
- [ ] `artifacts/security/<date>-sast-findings.md` written
- [ ] `artifacts/audit/<date>-sast-scan-<task>.md` written

## Next command

> ✅ `/sast-scan` complete.
> **Next command:** `/dast-scan` if a local/staging URL exists; else `/review-changes` or owning agents fix Critical/High.

# Subagent: security-reviewer

## Purpose

Review local changes for security issues aligned with OWASP and repo security baseline.

## Inputs

| Field | Description |
|-------|-------------|
| diff_scope | Files changed since sprint start or `git diff` summary |
| touch_points | APIs, auth headers, persistence, user input fields |

## Checklist

- [ ] No secrets, tokens, or credentials in code or audit artifacts
- [ ] External input validated (scan identifiers, qty, weight, disposal codes)
- [ ] Auth context enforced server-side (`UserContextHolder`, headers not trusted blindly)
- [ ] No PII in logs, test fixtures, or audit trails
- [ ] TLS assumed for external calls; no new plaintext credential flows
- [ ] SQL injection / JPA safe patterns on new queries
- [ ] Error responses do not leak stack traces or internal paths to clients

## Outputs

- Findings table: Severity (Critical/High/Medium/Low) | File | Issue | Recommendation
- **confidence**, **unresolved_assumptions**

## Command Contract Alignment

**Parent command:** `/review-changes`

**Rules:** `.cursor/rules/00-global-governance.mdc`, `.cursor/rules/02-security-and-compliance.mdc`, `.cursor/rules/10-audit-trail.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`

**Templates:** `.cursor/templates/review-findings-template.md`, `.cursor/templates/api-validation-template.md` (when manual API validation runs), `.cursor/templates/audit-trail-template.md`

## Quality Gate

- Zero Critical/High unmitigated findings before sprint closeout
- Medium findings documented with accepted risk or fix plan

## Audit

Findings appended to `/review-changes` audit and optional `artifacts/audit/<date>-review-changes.md`

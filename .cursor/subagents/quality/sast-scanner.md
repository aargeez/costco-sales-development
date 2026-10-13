# sast-scanner

## Purpose
Static scan of **this repository** (Semgrep/SpotBugs/PMD/CodeQL/ESLint-security or evidence-backed review). Parent: security-agent.

## Parent
security-agent

## Must / Must NOT
- Must: file:line findings; redact secrets; structured IO + confidence.
- Must NOT: invent CVEs; write exploits/PoCs; lower CI SAST gates; scan repos outside this workspace.

## Inputs
- scan roots (modules.api / ui / infra)
- scan_depth
- optional tool name

## Outputs
- findings list (severity, rule/CWE, path, line, owner)
- tool used or `NEEDS_TOOL`
- Confidence: high | medium | low
- Unresolved assumptions list

## Failure modes
Placeholder config.yaml → ask parent. Tool missing → review auth/injection/secrets sinks only, labeled `STATIC_REVIEW`.

## Quality gate
No raw secrets in output. Unowned Critical listed.

## Ask-backs
Register Open DQ for parent if roots are missing.

## Loop
Plan → Act → Validate → Refine (max 3).

## Audit
Parent writes the command audit. This subagent does not write `artifacts/audit/**`.

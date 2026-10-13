# /pr-assist

## Objective

Draft PR with test plan and coverage evidence.

**On amount-math error / conflict:** cite `artifacts/calculations/<module>-calculations.json` as the **primary source** for formula-related test plan items (`CALC-*`).

## Orchestration

| Role | Agent / Subagent |
|------|------------------|
| **Orchestrator** | orchestrator-agent |
| **Primary** | documentation-agent |

## Rules

- `.cursor/rules/02-security-and-compliance.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/32-calculation-catalog.mdc` (amount math — catalog JSON primary on error)

## Templates

- `.cursor/templates/test-results-template.md`
- `.cursor/templates/e2e-test-results-template.md` (when E2E ran)
- `.cursor/templates/review-findings-template.md`
- `.cursor/templates/audit-trail-template.md`

## Skills

- `.cursor/skills/jacoco-coverage/SKILL.md`

## Definition of Done

- [ ] PR body ready for `gh pr create`
- [ ] Test commands + results included
- [ ] Audit trail written

## Next command

**On completion, tell the user:**

> ✅ `/pr-assist` complete.  
> **Next command:** None — use `gh pr create` with the drafted body, or `/review-changes` if you make more commits.

| Condition | Next command |
|-----------|--------------|
| Additional code changes before merge | `/review-changes` then `/application-summary` |
| Done | Create PR manually with generated body |

# /daily-handoff

## Objective
Produce an end-of-day or end-of-session markdown handoff (**what / why / how**) from **session memory**—this conversation and user-stated facts—with **optional** git corroboration. Git is not the primary source.

Prioritize **intent, decisions, and context** so the next session is not misled by commit history alone (file churn without rationale is insufficient).

## Runtime Inputs

### Required
- `session_date` or `session_label`: date (`YYYY-MM-DD`) or named session for filename.

### Optional
- `timezone`: for timestamp consistency in narrative.
- `out_of_cursor_work`: bullets for work done outside Cursor.
- `output_path`: override default `docs/handoffs/YYYY-MM-DD-handoff.md`.
- `include_git_corroboration`: `true` | `false` (default `false` unless user requests).

## Use Agents
- orchestrator-agent → **handoff-agent** (mode `daily`) — [handoff-agent.md](../agents/ops/handoff-agent.md)

## Use Templates
- `.cursor/templates/daily-handoff-template.md`

## Use Skills
- `.cursor/skills/ops/SKILL.md` — session-narrative handoff structure and evidence hierarchy.

## Enforce Rules
- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/02-security-and-compliance.mdc`

## Required Output
- `docs/handoffs/YYYY-MM-DD-handoff.md` (or user-specified name) with **Session narrative** as the authoritative section; work log and follow-ups populated from the chat; template placeholders removed.
- **Commits** field: optional; include only when known or from explicit corroboration.

## Command-Specific Hardening
- **Do** derive content from conversation thread and user-confirmed paths.
- **Do** label git appendix **Optional repo corroboration** when included.
- **Don't** invent facts, velocity, or decisions not stated in session.
- **Don't** include secrets or unredacted PII.
- **Stop** when session has no substantive content — ask user for minimum narrative bullets.

## Execution Policy
- Plan → Act → Evaluate → Refine (max 3 iterations).
- **Primary evidence**: conversation thread, tool outputs referenced in chat, paths the user confirmed.
- **Secondary**: `git status` / recent log only if useful; label at end of template when present.

## Clarification Protocol

**Low gate:**

1. What is `session_date` / `session_label`?
2. Any `out_of_cursor_work` to include?
3. Include git corroboration (`include_git_corroboration`)?

If git contradicts session (e.g. clean tree but big uncommitted intent), document under **Discrepancies**.

## Artifact Persistence
- `docs/handoffs/YYYY-MM-DD-handoff.md` with metadata (generator: `/daily-handoff`, timestamp, iteration count).

## Definition of Done

- [ ] Next reader can continue from **Follow-ups** and **Open questions** using session-derived content.
- [ ] Every substantive item has clear what / why / how; no invented facts.
- [ ] Template placeholders removed; Session narrative is authoritative.
- [ ] Git discrepancies documented when corroboration conflicts with session narrative.

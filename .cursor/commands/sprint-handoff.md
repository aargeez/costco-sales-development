# /sprint-handoff



## Objective

Produce a sprint-boundary handoff from **unified plan, manifest, daily handoffs, and story artifacts** — not git mining. Minimal chat when sprint unified plan is supplied.



## Runtime Inputs



### Minimal invoker (preferred)

- `sprint_governed_plan`: `.cursor/plans/sprint<N>/sprint<N>-unified-governed-plan.md`



### Auto-resolved when `sprint_governed_plan` supplied

- `sprint_id` ← unified plan metadata

- `story_artifact_paths` ← story index + `docs/sprint<N>/` + `.cursor/plans/sprint<N>/` + `manifest.json`

- `daily_handoff_paths` ← default `"scan docs/handoffs/ for sprint date range"`

- `sprint_date_range` ← infer from handoff file dates or unified plan metadata when present



### Legacy explicit mode

- `sprint_id`: sprint identifier

- `sprint_date_range`: start/end + timezone



### Optional

- `include_git_appendix`: `true` | `false` (default `false`)



## Plan-as-input mode (sprint)



When `sprint_governed_plan` is supplied, apply `.cursor/templates/plan-as-input-protocol.md` §Resolution order (sprint):



| Input | Source |

|-------|--------|

| `sprint_id` | Unified plan §Metadata |

| `story_artifact_paths` | Story index + techno-functional pack path + per-story plans |

| Evidence summary | `manifest.json` + per-story §Execution logs + §Sprint execution log |

| Themes / roll-up | `docs/handoffs/` daily files in range + session memory |



**Resolve-before-ask:** do not ask for sprint id or artifact paths when unified plan exists.



After DoD: mark step 10 `complete` in unified plan; update §Sprint execution log.



## Use Agents

- orchestrator-agent → **handoff-agent** (mode `sprint`) — [handoff-agent.md](../agents/ops/handoff-agent.md)



## Use Templates

- `.cursor/templates/sprint-handoff-template.md`



## Use Skills

- `.cursor/skills/ops/SKILL.md`



## Enforce Rules

- `.cursor/rules/00-global-governance.mdc`

- `.cursor/rules/02-security-and-compliance.mdc`



## Required Output

- `docs/handoffs/sprint-<id>-handoff.md` with **Themes**, **Session roll-up**, **Artifact evidence index** (from manifest + execution logs), optional git appendix.



## Command-Specific Hardening

- **Do** cite manifest paths and plan completion status per story.

- **Do** seed next-sprint carryover from open gaps and follow-ups.

- **Don't** fabricate velocity metrics.

- **Resolve-before-ask** when `sprint_governed_plan` supplied.



## Execution Policy

- Plan → Act → Evaluate → Refine (max 3).

- Primary: unified plan, manifest, daily handoffs, execution logs.



## Clarification Protocol



**Resolve-before-ask** when `sprint_governed_plan` supplied.



**Low gate** only when:

1. Unified plan inaccessible

2. `sprint_date_range` cannot be inferred and no handoffs exist



## Artifact Persistence

- `docs/handoffs/sprint-<id>-handoff.md`

- Update `docs/sprint<N>/manifest.json` with `kind: sprint-handoff`

- Metadata: timestamp, iteration count, command version.



## Definition of Done



- [ ] Themes and roll-up from narrative + artifact evidence index from manifest/logs.

- [ ] Carryover reflects open gaps and follow-ups from plans.

- [ ] When `sprint_governed_plan` supplied: unified plan step 10 `complete`, sprint execution log updated.

- [ ] No fabricated metrics; git appendix labeled if present.


# Daily handoff — {{DATE}} ({{TIMEZONE}})

## Metadata
| Field | Value |
|--------|--------|
| Date / session | {{DATE}} |
| Timezone | {{TIMEZONE}} |
| Primary source | **Session memory** (this conversation + user notes) |
| Branch (if cited) | {{BRANCH}} |
| HEAD (short, optional) | {{SHORT_SHA}} |
| Generated | {{ISO_TIMESTAMP}} |
| Generator | {{GENERATOR}} (e.g. daily-handoff-agent / /daily-handoff) |
| Iteration | {{ITERATION}} |
| Validation summary | {{VALIDATION_SUMMARY}} |

## Session narrative (authoritative)
**Summarize what actually happened in this session from chat and stated facts—not inferred from git alone.**

- **Focus**: …
- **Outcomes**: …
- **Still open**: …

## Executive summary
- **Outcome**: One to three bullets for the next reader.
- **Risk / attention**: …

## Scope and traceability
- **Stories / tickets**: {{IDS_OR_N/A}}
- **PRs**: {{PR_URLS_OR_N/A}}
- **Plans / specs touched**: {{PLAN_PATHS_OR_N/A}}
- **Work outside this chat** (meetings, other IDEs, tickets): {{OTHER_CONTEXT}}

---

## Work log (what / why / how)

Session-derived work items—one block per coherent unit.

### {{WORK_ITEM_TITLE}}

| | |
|--|--|
| **What** | What was done or decided (behavior, doc, code path). |
| **Why** | Driver: story, bug, spike, regulation. |
| **How** | Approach, key modules, commands run, tooling. |
| **Key paths** | `path/to/A`, `path/to/B` |
| **Verification** | Tests, build, manual check—or **Not run** + risk. |
| **Commits (optional)** | Only if known from user or corroboration: `abc1234` … |

<details>
<summary>Optional: technical notes</summary>

- Edge cases, flags, env.

</details>

---

## Optional repo corroboration
*Include only if useful. Does not override session narrative above.*

- `git status` / recent commits summary: …
- **Discrepancies** (e.g. uncommitted work, reverted files): …

## Decisions and trade-offs
- …

## Deferred / not done
- …

## Blockers and dependencies
- …

## Open questions / SME
- …

## Follow-ups for next session
- [ ] …

## References
- Docs / plans: …
- Optional git: …

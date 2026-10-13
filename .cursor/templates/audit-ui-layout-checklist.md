# Audit UI layout checklist

Fill inside `/implement-ui` or `/fix-issue` audit when the slice **adds or changes** entry, search/criteria, or session filter UI. Do not add a command.

**Skill:** `.cursor/skills/audit-ui-layout/SKILL.md`

**Output:** copy into `artifacts/audit/<date>-implement-ui-<task>.md` or `artifacts/audit/<date>-fix-issue-<task>.md`. Blank rows = Open DQ with owner.

| Check | Cite (BRD FR, ATI PNG, peer screen) | Result |
|-------|--------------------------------------|--------|
| Screen class (entry / search / list / detail) | | |
| Entry root `maxWidth: 720` (or cited exception) | | |
| Location XOR present / N/A per cite | | |
| Session block column-first at 360×640 | | |
| Filters/checkboxes **below** dates (DOM order) | | |
| No criteria-vs-filters `md`/`lg` row split | | |
| RF test ids (`<module>_entry`, `*_session_filters` or `*_close_types`) | | |
| Peer reference screen named (e.g. SASE entry) | | |
| Sibling scan (skill § Sibling scan) recorded for module | | |

Navigation handoff (when applicable): `.cursor/templates/ati-navigation-checklist.md`

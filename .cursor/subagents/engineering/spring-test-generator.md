# spring-test-generator

## Purpose
Render java_test_templates via registry.

## Parent
backend-agent

## Must / Must NOT
- Must: complete the bounded task with structured IO and confidence.
- Must NOT: Do not invent; return gaps to parent.

## Inputs
- template vars YAML

## Outputs
- JUnit registry sources
- Sibling agent-owned tests when case IDs require them
- `artifacts/templates/<date>-render-check.md`
- Confidence: high | medium | low
- Unresolved assumptions list

## Failure modes
Incomplete input → partial result with explicit gaps.

## Quality gate
Registry only; no freeform structure on registry files. Refuse unless `docs/test-cases-br-coverage.md` Status is **PASS** for the in-scope FR/BR. Reuse `artifacts/templates/<resource>-vars.yaml`. Agent-owned extras are sibling classes (or `AGENT-OWNED TEST` blocks) citing `docs/test-cases.md` IDs. Run `python3 tools/templates/render_check.py --vars <yaml> --check test`. Forbidden harness patterns fail the check. When the IT writes shared vault, summary, or xref tables, the rendered or sibling IT must include an idempotent `@BeforeEach` restore of those rows. Do not weaken asserts to match a polluted golden.

## Ask-backs
Register Open DQ for parent if unclear. Do not invent. Do not STOP the parent except human gates.

## Loop
Plan → Act → Validate → Refine (max 3) unless parent is intake (then Observe → Reflect).

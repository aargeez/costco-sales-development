# Subagent: Project Validator

## Purpose
Run the Python validation suite against a generated Next.js project and produce a structured quality report with scores, findings, and actionable fix recommendations.

## Required Inputs
- `project_path`: absolute path to the generated Next.js project folder.
- `model_label`: name of the LLM model that generated the project.
- `env_path` (optional): path to `.env` with Azure OpenAI credentials for LLM evaluation.
- `skip_llm` (optional): boolean; skip LLM evaluation and run static analysis only.

## Tools Used
- `validator/validate.py` — Python validation script (static + LLM evaluation).
- `npx tsc --noEmit` — TypeScript compilation check.
- `validator/compare.py` — comparison across multiple model reports (when multiple exist).

## Execution Steps
1. Run `npx tsc --noEmit` in the project directory; capture exit code and errors.
2. Run `python3.13 validator/validate.py <project_path> --model "<model_label>" --output validator/results/<model_label_slug>.json [--skip-llm] [--env <env_path>]`.
3. Parse the JSON report for scores and findings.
4. Classify findings by severity:
   - **Critical**: TypeScript errors, missing theme, no Props interfaces → MUST fix.
   - **High**: Raw CSS files, no styled() usage, no barrel exports → SHOULD fix.
   - **Medium**: Low state management score, few accessibility attributes → NICE to fix.
   - **Low**: Style preferences, minor organization → Optional.
5. For each Critical/High finding, generate a specific remediation instruction.

## Outputs
- `validation_report`: JSON with all scores and findings.
- `typescript_status`: pass | fail (with error list if fail).
- `combined_score`: float (0-10).
- `severity_classified_findings`: list of `{ severity, category, finding, remediation }`.
- `refinement_needed`: boolean (true if combined_score < 7.0 or any Critical finding).
- `remediation_plan`: ordered list of fixes for the refinement-agent.

## Score Thresholds
| Metric | Pass | Warn | Fail |
|--------|------|------|------|
| Combined Score | >= 7.0 | 5.0-6.9 | < 5.0 |
| TypeScript | 0 errors | N/A | > 0 errors |
| Guidelines Adherence | >= 6.0 | 4.0-5.9 | < 4.0 |
| Folder Structure | >= 7.0 | 5.0-6.9 | < 5.0 |
| Reusability | >= 6.0 | 4.0-5.9 | < 4.0 |

## Failure Modes
- If Python validator crashes → capture stderr, report as validation infrastructure failure.
- If LLM evaluation times out → fall back to static-only score and flag.
- Escalation: if validator itself has bugs, report with raw output for human debugging.

## Quality Gate
Parent accepts when:
- Validation report JSON is produced and parseable.
- All findings have severity classification.
- Remediation plan is actionable (specific file + change description).

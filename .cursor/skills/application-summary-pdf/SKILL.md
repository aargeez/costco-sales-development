---
name: application-summary-pdf
description: >-
 Generate application summary HTML and PDF with Mermaid diagrams and fresh
 test/coverage metrics. Output paths come from config.yaml artifacts.*.
---

# Application Summary PDF Skill

## Outputs

Use `.cursor/config.yaml` → `artifacts.application_summary_html` and `artifacts.application_summary_pdf`.

## Template

`.cursor/templates/application-summary-template.md`

## Update Flow

1. Run fresh JaCoCo + count tests (API unit, IT, Vitest, E2E) via `evals.test_commands`
2. Update HTML sections 5–6 (LOC, tests, coverage)
3. Ensure Mermaid diagrams render (module script waits for `data-mermaid-ready`)
4. Generate PDF from `modules.ui` (or the product’s documented summary script):

```bash
# Example: cd <modules.ui> && node ../artifacts/scripts/generate-application-summary-pdf.mjs
```

Diagrams are rasterized via Playwright element screenshots before PDF export so Mermaid charts render as single images and do not split across pages.

## Subagent

`.cursor/subagents/docs/application-summary-writer.md`

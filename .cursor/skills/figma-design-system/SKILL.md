---
name: figma-design-system-to-mui
description: End-to-end Figma Design System URL to Next.js + MUI theme and components with a validation loop. Use for /figma-to-nextjs. Not for screenshots or whiteboards.
---

# Figma DS to MUI

1. Require a Figma file URL with `node-id`. Stop and ask if missing.
2. Delegate: figma-design-extractor → theme-builder → mui-component-generator → page-assembler → project-validator → refinement-agent.
3. Run `tools/figma/validate.py --root <project>`. Score ≥ 7.0 and zero critical findings, or escalate after 3 loops.
4. No raw CSS beyond globals. Typed props. `tsc` must pass.

Do not use this skill for PNG/screenshot ingest (`/ingest-figma-sketch`) or process maps (whiteboard skill).

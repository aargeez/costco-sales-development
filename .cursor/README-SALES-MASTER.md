# .cursor master sales

Sales **Audit + Vault** merge output. Deploy: copy this folder to a product repo as **`.cursor`**.

## Configuration

| Product | Start from |
|---------|------------|
| Sales Audit (default) | Root `config.yaml` or `config.examples/sales-audit.config.yaml` |
| Sales Vault | `config.examples/sales-vault.config.yaml` (includes `vault.scope` / `vault.screenconversion`) |
| Sales Processing | `config.examples/sales-processing.config.yaml` |

Product-specific overlay rules under `rules/overlays/` are **not** always-on. Root `rules/01-project-context.mdc` reads `config.yaml`. Load the Audit or Vault overlay only for that product — see [docs/overlays-guide.md](docs/overlays-guide.md).

## Execution models

After sprint plan **Approved**, delivery is the **same seven steps** for Audit and Vault (Option A: run each command; Option B: `/deliver-sprint`). Full narrative: [PIPELINE.md](PIPELINE.md) · [EXECUTION-OPTIONS.md](EXECUTION-OPTIONS.md) · [CURSOR-FLOW.md](CURSOR-FLOW.md).

```
/extract-calculations → /generate-brd → /plan-sprint → [Approved]
→ 1. /implement-api  2. /implement-ui  3. /expand-test-coverage → [test-package Approved]
→ 4. /run-tests  5. /run-eval  6. /review-changes  7. /application-summary
```

| Model | When | Entry commands (before or beside the seven steps) |
|-------|------|-----------------------------------------------------|
| **Audit spine** (public) | Sprint delivery, ATI amount math | `/extract-calculations`, `/generate-brd`, `/plan-sprint`, then seven steps above |
| **Vault promoted** (public) | Replatform, Figma, governed stories | `/figma-to-nextjs`, `/plan-governed-stories`, `/implement-api` → `/implement-ui`, then steps 3–7; satellites e.g. `/validate-api-endpoints`, `/compliance-check` |

**Agent catalog:** **19** core factory primaries (see [rules/cursor-factory-governance.mdc](rules/cursor-factory-governance.mdc)) plus **Sales platform peers** listed in [AGENTS-SALES.md](AGENTS-SALES.md) (32 categorized primaries including orchestrator).

## References

- Config examples: [config.examples/](config.examples/)
- Agent catalog: [AGENTS-SALES.md](AGENTS-SALES.md) · [agents/README.md](agents/README.md)
- Operator index: [README.md](README.md) · [PIPELINE.md](PIPELINE.md)
- Overlays guide: [docs/overlays-guide.md](docs/overlays-guide.md)
- Factory assumptions (e.g. PCC-002): [docs/factory-assumptions.md](docs/factory-assumptions.md)
- Architect review closeout: [../artifacts/audit/2026-10-06-architect-review-closeout.md](../artifacts/audit/2026-10-06-architect-review-closeout.md)

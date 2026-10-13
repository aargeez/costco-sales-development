# Rule overlays — when to load

Overlays under `rules/overlays/` are **not** `alwaysApply`. Root [rules/01-project-context.mdc](../rules/01-project-context.mdc) stays config-driven for every product.

Load **one product overlay set** per session — do **not** apply Audit and Vault overlays together.

## Sales Audit

| File | Load when | Typical commands |
|------|-----------|------------------|
| [rules/overlays/sales-audit/01-project-context-sales-audit-fragment.mdc](../rules/overlays/sales-audit/01-project-context-sales-audit-fragment.mdc) | `config.yaml` is Audit (default in this repo) | `/extract-calculations`, `/generate-brd`, `/plan-sprint`, `/implement-*`, `/fix-issue` |

**Requirements source:** `config.yaml` → `modules.brd` (e.g. `artifacts/brd/<module>-BRD-draft.md` from `/generate-brd`).

## Sales Vault

| File | Load when | Typical commands |
|------|-----------|------------------|
| [rules/overlays/sales-vault/01-project-context-replatforming.mdc](../rules/overlays/sales-vault/01-project-context-replatforming.mdc) | Active product is Vault replatform | `/implement-api`, `/implement-ui`, seven-step delivery |
| [rules/overlays/sales-vault/scope.mdc](../rules/overlays/sales-vault/scope.mdc) | Same | Scope checks, `/validate-api-endpoints`, `/generate-brd` (Vault mode) |
| [rules/overlays/sales-vault/screenconversion.mdc](../rules/overlays/sales-vault/screenconversion.mdc) | Editing `vault-nextjs/**` (glob on file) | `/implement-ui`, `/figma-to-nextjs` |
| [rules/overlays/sales-vault/06-infra-terraform-gcp.mdc](../rules/overlays/sales-vault/06-infra-terraform-gcp.mdc) | GCP infra generation | `/generate-infra` |

**Requirements source:** resolve **`vault.source_of_truth`** or **`modules.brd`** from `config.yaml` (see [config.examples/sales-vault.config.yaml](../config.examples/sales-vault.config.yaml)). `/generate-brd` does not replace that pack without PO sign-off — see [commands/generate-brd.md](../commands/generate-brd.md) § Product mode.

## Why overlays exist

The Sales **master** factory ships Audit + Vault in one `.cursor` tree. Overlays hold product-specific scope, screen conversion, and platform context without polluting always-on rules or breaking Audit-only repos.

**Removing `rules/overlays/`** would force Vault scope/screen rules into always-on files or duplicate them per command — not recommended.

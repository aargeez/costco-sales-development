# Subagent: schema-migration-generator

## Purpose

Generate or update **PostgreSQL schema** via Flyway migrations and align JPA entities in `config.yaml` → `modules.api`.

## When Invoked

- New table, column, index, or constraint required by a feature
- BRD §9 data requirements change
- `docs/feature.md` adds persisted fields (e.g. disposal entity)
- Any `/implement-api` scope that touches persistence

## Inputs

| Field | Description |
|-------|-------------|
| scope | BRD rule, feature note, or sprint item |
| entity_changes | New/changed JPA entities or fields |
| seed_data | Optional — `nsi_config` or reference data (separate V00x seed migration) |

## Outputs

- Flyway SQL: `<modules.api>/src/main/resources/db/migration/V<nnn>__<slug>.sql`
- JPA entity updates in `adapter/persistence/`
- Repository changes if needed
- IT migration verification note (integration profile applies Flyway on startup)
- **confidence**, **unresolved_assumptions**

## Conventions (match existing repo)

| Rule | Detail |
|------|--------|
| Tool | **Flyway** — never `ddl-auto` create in prod paths |
| Naming | `V001__init_schema.sql`, `V003__disposal_entity.sql` — next sequential number |
| Types | `JSONB` for config/flags/payload; `TIMESTAMPTZ` for audit timestamps |
| FKs | Reference `nsi_transaction(id)` where applicable |
| Seeds | Separate migration (e.g. `V002__seed_nsi_config.sql`) — not mixed with DDL |
| Rollback | Document manual rollback in audit if no down migration (Flyway forward-only) |

## Existing tables (reference)

- `nsi_config`, `nsi_transaction`, `scanned_trash_audit`, `disposal_tracking`, `insdtrp_outbox`

## Execution validation

After migration added:

```bash
# Unit tests (H2 or test profile as configured) — evals.test_commands.api
# Example: cd <modules.api> && mvn test

# Integration — Flyway applies migrations against PostgreSQL
# Example: cd <modules.api> && mvn verify -DrunIntegrationTests=true
```

## Quality Gate

- Migration number is next unused `V*` in `db/migration/`
- List the current max `V*` in the parent audit. Refuse a file whose version already exists.
- JPA entity column names/types match SQL
- No breaking change to existing IT without IT update
- Integration test `@BeforeEach` truncate list updated if new tables added
- When the migration is a seed (or contains `INSERT`/`UPDATE`/`ON CONFLICT` on existing tables): record keys touched, modules at risk, and whether the statement can overwrite another module’s golden rows. Do not change the parent command’s phase order.

## Enforce

- `.cursor/rules/00-global-governance.mdc`
- `.cursor/rules/02-security-and-compliance.mdc`
- `.cursor/rules/03-backend-springboot.mdc`
- `.cursor/rules/07-testing-and-quality.mdc`
- `.cursor/rules/10-audit-trail.mdc`
- `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`
- `.cursor/rules/32-calculation-catalog.mdc` (amount columns — catalog JSON primary on error)

## Command Contract Alignment

**Parent command:** `/implement-api`

**Rules:** `.cursor/rules/00-global-governance.mdc`, `.cursor/rules/02-security-and-compliance.mdc`, `.cursor/rules/03-backend-springboot.mdc`, `.cursor/rules/07-testing-and-quality.mdc`, `.cursor/rules/10-audit-trail.mdc`, `.cursor/rules/22-behavior-provenance-and-anti-invention.mdc`, `.cursor/rules/32-calculation-catalog.mdc` (amount columns — catalog JSON primary on error)

**Templates:** `.cursor/templates/audit-trail-template.md`, `.cursor/templates/test-results-template.md`, `.cursor/templates/behavior-provenance-template.md`

## Audit

List migration file path and entity changes in parent implement-api audit **What** section.

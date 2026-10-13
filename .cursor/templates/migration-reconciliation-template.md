# Migration Reconciliation Template

## Phase / Batch

## Existing migration baseline (repo SQL)
- **Scanned paths:** (e.g., `migrations/**/*.sql`)
- **Inventory timestamp / commit:** 
- **Known tables (`known_tables_set`):** 
- **Parse uncertainties:** (files or dynamic SQL not fully analyzed)

## Required objects from source DDL + user stories
| Object | Required by (DDL path / story ID) | In baseline? | Forward action |
|--------|-----------------------------------|-------------|----------------|
| | | yes / partial / no | skip / ALTER ADD / new CREATE |

## Source Counts and Checksums

## Target Counts and Checksums

## Referential Integrity Checks

## Business Invariant Checks

## Variance and Resolution Log

## Destructive SQL audit (must be empty or explicitly story-approved)
| SQL fragment | Object | Story ID | Verbatim AC authorizing removal |
|--------------|--------|----------|----------------------------------|
| *none expected* | | | |

## Additive gap summary
- Tables/columns/indexes to add in **new** migration files only:

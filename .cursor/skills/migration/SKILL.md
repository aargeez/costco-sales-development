---
name: migration-mapping-and-phased-cutover
description: Legacy→target mapping, dual-read/write, phased cutover. Use with data-agent and /prepare-cutover.
---

# Migration mapping

Inventory existing migrations first. Additive DDL only. Cutover needs lineage IDs, rollback, and a human gate before destructive apply.
